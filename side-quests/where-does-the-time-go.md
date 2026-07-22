# Where does the time go

_A side quest carved out of [Why Software Factories Fail](https://github.com/humanlayer/advanced-context-engineering-for-coding-agents/blob/main/wsff.md)_

Even before AI, only 25-50% of the time to ship a feature was writing the code itself. The rest was aligning/planning, code-review/rework, and testing/verifying the solution.

<div align="center"><img src="../images/wheel-1-wm-shadow.png" width="50%" alt="Pre AI: a two-day feature where coding is only 2-4 hours -- planning, code review, and testing fill out the wheel"></div>

If you're only using AI to write the code, then you're taking the 2-4 hours of coding time down to 10-20 minutes, but you haven't accelerated anything else here.

<div align="center"><img src="../images/wheel-2-wm-shadow.png" width="50%" alt="Naive AI: coding shrinks to 20 minutes, but planning, review, and testing are unchanged"></div>

But if you use AI to help you plan and align, then you actually get closer to 2-3x faster. 

<div align="center"><img src="../images/wheel-3-wm-shadow.png" width="50%" alt="Model-assisted planning + alignment: every slice shrinks -- coding from AI, review and testing because we did a good job aligning"></div>

### The 80/20 rule in AI coding leverage

Lets assume if you yolo a two-sentence prompt into your factory, your chance of getting a fully-mergeable result is ~50%, the chance you have to rework it is 50%.

<div align="center"><img src="../images/rework-1-wm-shadow.png" width="50%" alt="Chance you'll have to rework it: a single point -- yolo a 2-sentence prompt (2 min) lands at ~50%"></div>

Now lets say you are a principal engineer with 10 years of experience. You have the whole codebase across 100 repos downloaded into your head. So you spend an afternoon writing a perfectly detailed spec by hand. Now your odds are better, but you probably still have about a 10% chance that you'll have to redo *something* significant.

<div align="center"><img src="../images/rework-2-wm-shadow.png" width="50%" alt="Add a second point: an afternoon hand-writing a detailed spec (5 hours) drops it to ~10%"></div>

And at the far end: write every line yourself. Nothing's left for the agent to get wrong, so the rework chance goes to zero.

<div align="center"><img src="../images/rework-3-wm-shadow.png" width="50%" alt="A third point: write all the code by hand (20 hours) -- ~0% rework, but you've given up all the leverage"></div>

**note** For this example I'm gonna blur "chance you'll have to change something" x "how painful the change will be" into a single %age number but obviously they're two separate variables. If the model is 50% likely to get a button style wrong, but the fix is one cheap prompt, then our combined "expected pain" is low.

$$\text{expected pain} = P(\text{you'll have to change it}) \times \text{how painful the change is}$$

If you draw this out, there's an inverse relationship between effort invested up front and expected pain.

<div align="center"><img src="../images/expected-pain-wm-shadow.png" width="50%" alt="Expected pain vs effort up front: the three points fall on an inverse-exponential curve that bottoms out at the write-it-all-by-hand extreme -- about 80% of the expected pain is gone in the first few minutes"></div>

What you don't want to do is spend 6 hours planning a task for which you could have eliminated 80% of the expected pain in the first 10 minutes.
