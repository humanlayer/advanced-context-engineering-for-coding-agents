[← Back to README](../README.md)

## From 12-Factor Agents to Context Engineering for Coding

You may remember me from April's [12-factor agents](https://hlyr.dev/12fa) post, as the coiner of the term "context engineering" or from the [AI Engineer talk on the topic](https://www.youtube.com/watch?v=8kMaTybvDUw).

Since then, we've been deep in the trenches figuring out how to make AI coding agents actually work in production. Not demos. Not greenfield projects. Real, messy, complex brownfield code.

### The Two Talks That Changed Everything

I have 2 favorite talks from AI Engineer 2025 (incidentally, the only two AIE talks with [more views than 12-factor agents](https://www.youtube.com/@aiDotEngineer/videos)):

1. **[Sean Grove's "Specs are the new code"](https://www.youtube.com/watch?v=8rABwKRsec4)** - The idea that chatting with an AI for hours then throwing away prompts while committing only code is like compiling a JAR and checking in the binary while throwing away the source.

2. **[The Stanford study on AI's impact on developer productivity](https://www.youtube.com/watch?v=tbDDYKRFjhk)** - Analyzed commits from 100k developers and found AI tools often lead to rework, diminishing gains.

### The Problem Everyone's Hitting

<img width="1326" height="751" alt="Stanford study results" src="https://github.com/user-attachments/assets/06f03232-f9d9-4a92-a182-37056bf877a4" />

This matched what I heard from founders everywhere:

* "Too much slop."
* "Tech debt factory."
* "Doesn't work in big repos."
* "Doesn't work for complex systems."

The general vibe on AI coding for hard stuff tends to be:

> Maybe someday, when models are smarter…

Even [Amjad](https://x.com/amasad) was on [Lenny's podcast](https://www.lennysnewsletter.com/p/behind-the-product-replit-amjad-masad) talking about how PMs use Replit agent to prototype, then hand off to engineers for production.

### The Context Engineering Answer

Whenever I hear "Maybe someday when the models are smart" I leap to exclaim: **that's what context engineering is all about** - getting the most out of *today's* models.

While 12-factor agents focused on building reliable agent systems, we discovered something even more powerful: applying these principles to how we USE coding agents.

The transformation from "maybe someday" to "shipping 35k LOC in 7 hours" isn't about waiting for GPT-5. It's about being intentional with context.

[← Back to README](../README.md) | [Stanford Study & Specs →](02-stanford-study-and-specs.md)