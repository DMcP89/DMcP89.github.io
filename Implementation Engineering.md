# The Missing Role: Implementation Engineer

The field of software engineering has no shortage of roles and titles. You have your standard "Software Engineer" role, which can sometimes have a classifier with it (Frontend, Backend, Full Stack). Then you have the more Ops focoused roles like "DevOps Engineer", "Site Reliability Engineer", "Platform Engineer", "System Engineer" the list goes on. There are even engineering roles in the Sales org now with titles like "Pre Sales Engineer" and "Solutions Architect". Each of these roles are fairly well represented in the software engineering zitegeist and in general have a fairly well defined set of responsibilities and skills. The exact details will vary from company to company but the gist is there for the most part. 

However there is one role that I have not seen a good definition for or much mention of, the "Implementation Engineer".

Having spent almost my entire career in implementations this always struct me as odd. Maybe its due that its primaryily present in B2B companies and can't be discussed in detail due to NDAs or that its simply just refered to as one of the roles I mentioned above or that it falls under some other org like Customer Success or Professional Services. In some cases the role can be refered to as an implementation consultant instead of an engineer which only adds to its obscurity. 

With this in mind I wanted to take a stab at defining what an Implementation Engineer is and what they do.

# What is an Implementation Engineer?
The in general we are tasked with getting the product(s) or service(s) up and running for the customers. This includes any and all integrations and customizations that are needed to make the product or service fit the usecase for the customer. Whatever gaps there are between what the product or service does out of the box and what the customer needs it to do, we are responsible for filling them.
However if you are looking for a more formal definition the best I have come up with so far is this:

> An Implementation Engineer is a software engineer who is responsible for all technical aspects of onboarding a product(s) or service(s) with a customer. This can include but is not limited to:
> - Deploying the product or service, including any 3rd party dependencies and infrastructure
> - Configuration of the product or service
> - Integration with other systems
> - Customization of the product or service
> - Monitoring and maintaining the product or service
> - Training of the customer on how to use and maintain the product or service
> - Providing technical support to the customer
> - Demoing the product or service to potential customers
> - Creating POCs or POVs for potential customers


# What does an Implementation Engineer do?
In my experience the most common thing you'll be working on is work arounds. Dev teams have a process, they need to make changes slowly and deliberately (for good reason), sometimes this can take weeks or months for a fix or feauture to be released. But the customer needs a fix now (or worse thinks a feature already exists), so you'll have to come up with a work around to buy the Dev team some time. A good work around can take many forms, sometimes its a plugin or a sidecar service that adds missing functionality, others it will be a simple cron job that keeps the systems cache from getting too big. On occasion it will consist of something hacky, like decompiling jar files to remove vulenerable code then recompiling them and deploying them to live environments (thanks [log4shell](https://unit42.paloaltonetworks.com/apache-log4j-vulnerability-cve-2021-44228/)). You know you've made it as an implementation engineer when one of your "temporary work arounds" is still in production years later as a part of the product's codebase.

<p align="center">
  <img src="https://www.monkeyuser.com/2018/workaround/88-workaround.png" alt="Workaround" width="400"/>
</p>

Other than work arounds it can be a little bit of everything. Sometimes its as straight forward as running some ansible playbooks on a customers infrastructure to get your app up and running (nothing like the thrill of the playbooks completing on the first try). Next thing you know you're writing an API bridge so that the customer's legacy system can speak your apps language. Then you'll have to do some ETL work to make sure that all the reference data from a previous system, perhaps a competitor's offering or something the client cooked up themselves and has no documentation for, is in your system. They don't have an ETL tool so you'll have to recommend one or roll your own process (just go with Logstash). And of course you'll be responsible for keeping up maintenance on all these things, which means upgrades and patches and monitoring. 

The biggest factor in what you'll be working on is the customer, some will have the resources and personel that can make your job a breeze, others won't. You'll have two different groups you'll be working with at most customers, the end users who are using your app and the technical team(s) who are supporting it. And you'll be working with both alot for the most part. Training calls to walk them through your cloud offering or the latest features in the new release. Getting paged from the support team asking for help debugging a Sev1 issue that they'd reach out to the Dev team for but it appears the error is coming from the bridge you wrote, it turns out however that its due to an inefficent database query that the customers DBAs forgot to make an index for. 


# Who should be an Implementation Engineer?
Anyone really, the obvious answer would be full stack engineers or sysadmins but as long as you have a good understanding of the basics of software engineer you can pickup the rest as you go with a little google-fu, or chatgpt-fu now I guess. As noted above it typically does require alot of cutomer interaction, so if that's not your strong suit or not something your interested in this type of role might not be for you. You also have to be able to work in some presure cooker situations. Weather its trying to reach a go live date on time or trying to troubleshsoot a Sev1, there will be times when the stress is high and you'll have to be able to keep your cool. 

# Why would I want to be an Implementation Engineer?
For me its a combination of being close to the action and the varity of technologies you get exposure to. In implementations you're working on live applications not just test environments, you get to actually see how your app is being used. Some customers will use it as is out of the box, others will have highly customized implementations. Each one will have opportunities for you to learn something you didn't know before. My first exposure to K8s was trying to troubleshoot an SSL cert issue in a customers environment. I got to learned python to create a library of VM automation scripts and once I found out about ansible I migrated them all to that. The most of my Spring knowledge comes from creating bridges using Spriing Integration. Because the role is so broad you have a lot of freedom with what tools you use, something you don't always get in traditional software engineering roles. And you'll quickly find that no two infrastructures are a like. One customer might be running everything in AWS, another might be running an on prem K8s cluster, and another still might be running a bunch of Windows VMs. All this means you'll get to be able to work with a wide variety of technologies and hopefully pick them up quickly. That last bit, picking things up quickly, is a skill that will serve you well in any role especially with an uncertain future with the current LLM and AI craze and the job marktet for software engineers being as competative as it is. 
