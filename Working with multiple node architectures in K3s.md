I am in the process of rebuilding my home lab after neglecting it for the last few years. Originally it consisted of an old laptop and SBC running a kubernetes cluster using k3s, it was more for learning then actually hosting anything. But I recently got a new mini pc that makes the prospects of self hosting a few apps more viable. Now my cluster consists of 3 Nodes:

- TRIGKEY Ryzen 7 Mini PC (x86_64)
- Toshiba Satellite C855S5308 (x86_64)a
- Odroid XU4 A15 (armv7l)

I figured the best place to start with rebuilding my home lab would be with getting some sort of observability tool in place to make troubleshooting future deployments less of a pain. I decided to go with an [ELK stack](https://www.elastic.co/elastic-stack) as I have a lot of experience with it from my day job.

Now you'll notice the Odroid is using a different architecture then the other two nodes. This can occasionally cause some issues when an image is not available for the armv7l architecture. I ran into an example of this while setting up the ELK stack.

# The Setup

To get the ELK stack stood up I used the Helm charts provided by Elastic. Overall its a dead simple procedure:

1.  Add the repo

```bash
helm repo add elastic https://helm.elastic.co
helm repo update
```

2.  Create your value files

```bash
helm show values elastic/filebeat > filebeats/values.yml
helm show values elastic/logstash > logstash/values.yml
helm show values elastic/elasticsearch > elastic/values.yml
helm show values elastic/kibana > kibana/values.yml

# Edit the various values files as you see fit.
```

3.  Install the charts

```bash
helm install elasticsearch elastic/elasticsearch -n elk-stack --create-namespace -f elastic/values.yml
helm install filebeat elastic/filebeat -f filebeats/values.yml -n elk-stack
helm install logstash elastic/logstash -f logstash/values.yml -n elk-stack
helm install kibana elastic/kibana -f kibana/values.yml -n elk-stack
```

I configured my values so that each node in the cluster would have a filebeats pod that would watch the logs of each pod running on it. These filebeats pods would feed into a logstash pod that would run on the TrigKey node. The logstash would do a bit of formatting and then send everything along to Elasticsearch. For that part I setup a 2 node elastic cluster with a elastic pod running on both the TrigKey node and the Toshiba node. The Odroid does not have much storage attached to it at the moment so I left it out of the ES cluster. Finally to be able to read and visualize the logs I setup a Kibana pod on the TrigKey node. Once the installs complete I should have a fully functioning ELK stack setup to automatically capture the logs for any pod that I spin up on my cluster.

```bash
kubectl -n elk-stack get pods
NAME                            READY   STATUS             RESTARTS         AGE
elasticsearch-master-0          1/1     Running            16 (36h ago)     96d
elasticsearch-master-1          1/1     Running            2 (10d ago)      10d
filebeat-filebeat-929z2         0/1     ImagePullBackOff   0                75s
filebeat-filebeat-nlhn7         1/1     Running            747 (10d ago)    98d
filebeat-filebeat-q4g8r         1/1     Running            5 (10d ago)      98d
kibana-kibana-555ddb75f-sknz7   1/1     Running            7 (10d ago)      98d
logstash-logstash-0             1/1     Running            2390 (21m ago)   98d
```

Welp.

# ImagePullBackOff Loop

As the table above shows one of my filebeats pods is in a ImagePullBackOff state. To put that in human terms, the node this particular pod is running on is having trouble pulling the image for the pod. In this case the image in question is docker.elastic.co/beats/filebeat:8.5.1. By using the wide output option of get pods (`kubectl get pods -o wide`) I can trace this pod to the Odroid node. In order to pull this pod out of this crash loop I'll need to figure out why this node can't pull the filebeat image. I've found the best place to start with these issues is to just try pulling the image manually and seeing what breaks.

```bash
# from the odroid node's command line
$ docker image pull docker.elastic.co/beats/filebeat:8.5.1
8.5.1: Pulling from beats/filebeat
no matching manifest for linux/arm/v7 in the manifest list entries
```

That explains it. Since the odroid is using arm v7 architecture it will try to pull an image for that architecture, however in this case no such image exists. Which honestly is not surprising, can't imagine there is alot of demand for this type of image,  and if you take a look at [Elastic's support matrix page](https://www.elastic.co/support/matrix) you'll see they no longer support 32 bit starting with v8.

# The Fix

So we have a straight forward problem, we don't have an image available for the architecture that our node is running. The simplest solution would be to just pick an older version that has an arm v7 image. But that would mean a downgrade for the other two nodes, which doesn't sit right for me. Instead I went with a more involved solution:

1.  Clone the git repo for filebeats and check out the 8.5.1 tag
2.  Create a build of filebeats using the odroid architecture
3.  Write a image file that uses the newly built binary
4.  Add that image to the odroids local image repository

## Clone the repo and build the binary

The repo for filebeats is stored on Github so you can clone it as you would any other git repo. Here I'm specifying the branch 8.5.1 to make sure the version of the repo I checkout matches what's running on my other nodes.

```bash
# from the odroid node's command line
git clone --branch v8.5.1 https://github.com/elastic/beats.git
```

Now that we have the source code we can go ahead and build it. Filebeats is written in Go so building a binary for specific architectures or OSes is simple with the GOARCH and GOOS environment variables. Fortunately we don't have to worry about any of that as Elastic has setup a simple build process using mage. The steps are documented in their contribution guide [here](https://www.elastic.co/docs/extend/beats) but I've included the commands below:

```bash
cd beats
make mage
cd filebeat
mage build
```

## Creating an image with our binary

Now creating the image file is a bit trickier since we can't just copy Elastic's image file. But it would seem I am not the first person to run into this issue as I was able to find this [Medium post](https://edmondcck.medium.com/build-the-filebeat-docker-image-for-odroid-architecture-f9baec423d4b) going through a similar solution for odroid architecture. Now the article is using an older version of filebeats but we can rework it to fit our needs. First we need to copy a few files, including our new binary, into a directory so they can be easily copied to the docker image:

```bash
# while still in the beats/filebeat directory
mkdir -p filebeat-odriod/data filebeat-odroid/logs
cp -p filebeat ./filebeat-odroid
cp -p filebeat.docker.yml ./filebeat-odroid/filebeat.yml
cp -p filebeat.reference.yml ./filebeat-odroid
cp -p README.md ./filebeat-odroid
cp -pR module ./filebeat-odroid
cp -pR modules.d ./filebeat-odroid
```

Then we can setup our Dockerfile like so:

```Dockerfile
FROM ubuntu:latest
RUN apt update
RUN apt upgrade -y
RUN apt install -y ca-certificates curl gawk libcap2-bin xz-utils
RUN apt clean all
ENV PATH="/usr/share/filebeat:${PATH}"
COPY ./filebeat-odroid /usr/share/filebeat
WORKDIR /usr/share/filebeat
ENTRYPOINT ["filebeat"]
```

## Deploying image to local

Now this step will largely depend on what runtime you are using for your containers in your cluster. I am using containerd which is the default with K3s so I have the extra step of importing the the image into containerd's image repository.

```bash
# if you are using docker as your runtime this should be enough
docker build -t docker.elastic.co/beats/filebeat:8.5.1 .

# if you use containerd you'll have to import the image to its repo'
docker save docker.elastic.co/beats/filebeat:8.5.1 > image-tag.tar 
k3s ctr images import image-tag.tar
```

Once the is imported successfully we just need to wait for the pod to restart again and we should be good to go:

```bash
NAME                            READY   STATUS    RESTARTS         AGE
elasticsearch-master-0          1/1     Running   16 (38h ago)     96d
elasticsearch-master-1          1/1     Running   2 (10d ago)      10d
filebeat-filebeat-929z2         1/1     Running   0                115m
filebeat-filebeat-nlhn7         1/1     Running   747 (10d ago)    98d
filebeat-filebeat-q4g8r         1/1     Running   5 (10d ago)      98d
kibana-kibana-555ddb75f-sknz7   1/1     Running   7 (10d ago)      98d
logstash-logstash-0             1/1     Running   2395 (19m ago)   98d
```


#k3s #ELK #filebeats #armv7 #kubernetes #homelab
