# The vibes are off
<span class="center" >
<img src="https://cdn.arstechnica.net/wp-content/uploads/2025/03/karpathy_vibecode_screenshot.png" alt="Tweet by Andrej Karpathy describing what he calls vibe coding, letting an llm powered coding assistant control all aspects of code generation in a project" width="75%" />
</span>

<br/>

I've seen a number of posts around this idea of "vibe codig" over the past couple of months. The basic idea is you're handing over complete control of a program's code to an LLM agent and just dictating to it what changes to make. To his credit Andrej specifically states that he is using it for weekend projects, but as is the case with most things in tech these days, the hype got away from everyone. VS Code did a "vibe coding" session during one of their weekly [streams](https://www.youtube.com/watch?v=Pe8ghwTMFlg), plenty of [articles](https://arstechnica.com/ai/2025/03/is-vibe-coding-with-ai-gnarly-or-reckless-maybe-some-of-both/?utm_source=bsky&utm_medium=social) were posted about it, and whatever [this](https://www.itsavibe.ai/) is.


For the most part I would describe myself as a whatever floats your boat guy when it comes to software engineering. I don't really have a strong opinion on most of the common flame wars in the industry. You want to use tabs instead of spaces? Sure. Emcas, NeoVim, IDE? You do you. Even with the onset of AI coding assitants or agents, if it helps your workflow, go for it. I have copilot setup in both vs code and neovim, and find it useful. Vibe coding, however, is a hill I am willing to die on. It's more party trick than anything and if "vibe coding" was relegated just to the post above I probably would have never even bothered with this post.
# The nightmare scenario
Using "vibe coding" on anything more complex than a To Do app is not only asking for trouble, it's asking for unemployment in my opinion. Just imagine this scenario for a moment:

```
Its 2am. 
You've just finished a deployment on your company's banking app that processes millions of transactions. 
Its the second Friday of the month, and you know that means a lot of ACH transactions are going to be coming in due to payrolls.
You're tired, and you just finished all your post deployment checks an are ready to go to bed.
Suddenly, you get a page, every single ACH transaction is getting rejected as being suspected fraud.
After a few hours of debugging you trace the issue back to an update on fraud module, so you page out to the engineer who made the change and jump on a call.
You: "Hey, I see you made a change to the fraud module, for some reason it's considering every ACH transaction as fraud, any ideas on what might be causing this?"
Engineer: "Oh Ah, I'll have to check what the LLM did for that change"
```

Now you can say that "oh if you had proper tests in place this couldn't happen". But I'd venture to guess that if a LLM is writting the code, a LLM is writting the tests, it can just as easily come up with a test that passes for the wrong reasons or more likely it will miss edge cases. You can say "oh if you had proper code reviews in place this couldn't happen". But wouldn't that be just shifting the work to the code review? The old tried and true "LGTM" is not going to cut it if we're off loading everything to the LLM. After all if the situation above came to pass and your name is on the PR approval you'd be on the hook as well. 

And if you think that I'm over reacting and that no company would let anything resembling vibe coding near something as sensitive as a payment system, I'd encourage you to take a look at this job posting that made the rounds on [r/programminghumor](https://www.reddit.com/r/ProgrammerHumor/).


https://ca.linkedin.com/jobs/view/vibe-coder-at-bree-4186605629

<br/>

# The real trade off
A lot has been said about the productivity boosts that LLM powered coding assistants can provide. And I don't want to seem like I'm being a contrarian for the sake of it. I think the current crop of LLM powered tools do offer a lot of value, hell I have an entire [project](https://github.com/DMcP89/tinycare-tui) that I built partial to test them out so I can figure out how to best use them in my workflow. But the one thing I rarely see mentioned is what the actual trade off in using them is, especially when used in a "vibe coding" sense. Its not just that you'll need more rigiorous code review processes or CI/CD pipelines, or even the grueling debugging sessions to work out where your app is breaking. The real trade off is your skills. The more you offload to the LLM, the less you are actually developing. Spend time working through a problem yourself, reading library documentation, reviewing similar implementations, going over your logic and reasoning, that's what will keep you sharp. And if you are just getting started in your career, this is even more important. You need to build up your skills and knowledge base, and the best way to do that is to work through problems yourself. One recommendation that's gone a long way for me is to treat them like a pair programming partner. Use them to help you work through a problem, but don't just hand it over to them.
