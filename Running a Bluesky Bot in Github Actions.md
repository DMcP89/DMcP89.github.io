# The Why
I've been using Bluesky a lot over the last few months and wanted to check out what the developer experience was like, building a simple bot felt like a good starting point. I've always like the old "Dumb" internet bots, the kind that just post a random saying or picture every so often and don't require their own data center and nuclear reactor to run. I also love horrible dad jokes. So creating a bot that posts a random dad joke every day checked all the boxes for me.

# How it started
I started by copying the [Bluesky example bot](https://github.com/bluesky-social/cookbook/tree/main/ts-bot) from their repository. It is a very simple setup, create an environment file with the username and password for the bot account, compile the typescript and just run the index.js file. That gets you a bot that will post the smile emoji every 3 hours.

```typescript
import { BskyAgent } from '@atproto/api';
import * as dotenv from 'dotenv';
import { CronJob } from 'cron';
import * as process from 'process';

dotenv.config();

// Create a Bluesky Agent 
const agent = new BskyAgent({
    service: 'https://bsky.social',
  })


async function main() {
    await agent.login({ identifier: process.env.BLUESKY_USERNAME!, password: process.env.BLUESKY_PASSWORD!})
    await agent.post({
        text: "🙂"
    });
    console.log("Just posted!")
}

main();


// Run this on a cron job
const scheduleExpressionMinute = '* * * * *'; // Run once every minute for testing
const scheduleExpression = '0 */3 * * *'; // Run once every three hours in prod

const job = new CronJob(scheduleExpression, main); // change to scheduleExpressionMinute for testing

job.start();
```

With most of the heavy lifting sorted out already it was just a matter of adding in some code to for getting a dad joke and updating the call to agent.post. Fortunately there is a [Dad Joke API](https://icanhazdadjoke.com/) that I used in another one of my [projects](https://github.com/DMcP89/tinycare-tui).I created an interface to handle the return from the API and added axios to handle the request.


```typescript
// Create interface for reponse from icanhazdadjoke
interface JokeResponse {
  id: string;
  joke: string;
  status: number;
}

async function postJoke(){
  try {
    // Fetch a joke from icanhazdadjoke
    const response = await axios.get<JokeResponse>('https://icanhazdadjoke.com/', {
      headers: {
        Accept: 'application/json',
      },
    });

    const jokeData = response.data;
    // Authenticate with Bluesky
    await agent.login({ identifier: process.env.BLUESKY_USERNAME!, password: process.env.BLUESKY_PASSWORD!})
    // Post the joke to Bluesky
    await agent.post({
        text: jokeData.joke,
    });
    console.log("Just posted: ", jokeData.joke);
  } catch (error) {
    console.error('Error fetching the joke:', error);
  }
}

```
And that was it, I had a bot that would post a dad joke every day at 9am. I ran it on my local machine for a few days and it worked perfectly.

![](/public/screenshots/firstpost-dadjoke.png)

# How its going
Originally I had this bot running as a background process on my local machine. This works fine but I like to experiment on my local machine so its not always on and I did not want to hassle with setting up a cron job or anything like that. Besides a local cron job wouldn't give me anything fancy like emails on failures or a history of the runs. And a small script like this? Perfect for Github Actions. 
```yaml
# Github Action to post a joke to Blueksy every day at 9:00 AM by running index.js with node, action should be able to run on demand as well.
name: Post Joke

on:
  schedule:
    - cron: '0 9 * * *'
  workflow_dispatch:

# Two secrets should be set in the repository settings: BLUESKY_USERNAME and BLUESKY_PASSWORD
jobs:
  build:
    runs-on: ubuntu-latest
    env:
      BLUESKY_USERNAME: ${{ secrets.BLUESKY_USERNAME }}
      BLUESKY_PASSWORD: ${{ secrets.BLUESKY_PASSWORD }}
    steps:
      - uses: actions/checkout@v2

      - name: Use Node.js
        uses: actions/setup-node@v1
        with:
          node-version: '20.x'

      - name: Install dependencies
        run: npm install

      - name: Run script
        run: node index.js
```
Now for simplicity I decided that I would handle building the index.js file locally and just push that to the repository, you could absolutely set up a build step in the Github Actions workflow and for anything more sophisticated than this I would recommend it. And since commiting a .env file would send dependabot into a tizzy I'm using Github Secrets to store the username and password for the bot account. 

The script itself does not change much. With Github handling the scheduling we can cut out the cron dependency and just call the postJoke function directly.
```typescript
import { BskyAgent } from '@atproto/api';
import * as dotenv from 'dotenv';
import * as process from 'process';
import axios from 'axios';


dotenv.config();

// Create a Bluesky Agent 
const agent = new BskyAgent({
    service: 'https://bsky.social',
})

// Create interface for reponse from icanhazdadjoke
interface JokeResponse {
  id: string;
  joke: string;
  status: number;
}

async function postJoke(){
  try {
    // Fetch a joke from icanhazdadjoke
    const response = await axios.get<JokeResponse>('https://icanhazdadjoke.com/', {
      headers: {
        Accept: 'application/json',
      },
    });

    const jokeData = response.data;
    // Authenticate with Bluesky
    await agent.login({ identifier: process.env.BLUESKY_USERNAME!, password: process.env.BLUESKY_PASSWORD!})
    // Post the joke to Bluesky
    await agent.post({
        text: jokeData.joke,
    });
    console.log("Just posted: ", jokeData.joke);
  } catch (error) {
    console.error('Error fetching the joke:', error);
  }
}
postJoke();
```
And there you have it, a Bluesky bot running in Github Actions. About as dead simple as it gets. Check out the repository if you want to see the full code.

https://github.com/DMcP89/bsky-jokeaday

