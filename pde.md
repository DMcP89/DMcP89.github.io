# My Personal Development Environment

If you follow any major tech influencers on youtube or twitter you've probably seen a few videos or tweets about their personal development environment. These are typically videos of their desktop setup, the tools they use, and the shortcuts they have setup. This is my take on the topic as well as a guide on how to setup your own personal development environment based on mine.

## How it started
If you had asked me a few years ago I would have advised against personal development environments. I would have told you that it would be better for every developer to work from a uniform environment, that way it would be easier to provide support, triage issues, reproduce bugs, and test features. Each developer should have an identical setup down to the IDE. The idea here was reducing friction while developers had to worked together or with each others code was more important than reducing friction when developers were working on their own. In hind sight I can see this was misguided for a few reasons:

1. Developers spend most of their time working on their own.
2. Even when developers do need to work with someone else's code it is in their own environment.
3. In the case of pair programming, typically one developer is driving while the other advises, so as long as the driver know's their environment it should be frictionless.

The sweat spot I've found is to have a set of standards that all developers should follow (what version to use for each tool, linting rules, pre-commit checks, things like that), but allow them to customize their environment to their liking. This allows developers to work in an environment that they are comfortable with, which in turn makes them more productive.

So right around the time the pademic hit I started building out my own personal development environment. This was mostly driven by working from home, I wanted to have a the same environment across all my devices (work laptop, personal laptop, home servers, etc..) to cut back on context switching. It started out as a pretty [basic setup](https://github.com/DMcP89/dotfiles/tree/e56dcecb8b14bce80753473c059589c2e740faef), some aliases for bash and a reduimetary vim setup. At the time I was not using vim as my main IDE, vscode filled that role, but it was my goto when viewing or editting file in the terminal.


//Gallery of images of my initial setup


## How it's going
Fast forward a year and a half and I've slowly started to transition to using neovim as my main IDE for all my projects. Vscode is still filling a role for me thanks to its debugger interface and I have enjoyed using its copilot features. I've transitioned from bash to zsh as my main shell and thanks to influencers like [the primeagen](#null) and [low level learning](#null) I've started to use tmux as a multiplexer, and tools like fzf and jq are now staples of my workflow. I've also switched daily driver from a windows environment to Pop Os and been experimenting with window managers like i3 and desktop environments like gnome. It took a bit to get used to it but I definitly can feel like its easier to move around.

//Gallery of images of my current setup


## How to use it
Since I use this on all my devices I've setup a suite of anisble playbooks to get everything setup automatically for me. Now I would highly recommend you use my configs as a starting point, see what works for you and what doesn't. As the name suggests your environment should be personal to you, the tools, shortcuts, and aliases I use might not make sense for you so experiment and iterate on it.




