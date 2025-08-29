[← Back to README](../README.md)

## Code Review in the Age of AI

### What Is Code Review For?

People have different opinions on what code review is for. I prefer [Blake Smith's framing](https://blakesmith.me/2015/02/09/code-review-essentials-for-software-teams.html):

<img width="500" height="647" alt="Code review purposes" src="https://github.com/user-attachments/assets/4c873d29-5dd7-4ed1-82e7-332e871b1d12" />

The most important part of code review is **mental alignment** - keeping team members on the same page about how the code is changing and why.

### The 2000-Line PR Problem (Revisited)

Remember those 2000-line Go PRs? The issue wasn't just review time. It was mental alignment.

I was losing touch with:
- What our product was
- How it worked
- Why it was built certain ways
- Where we were heading

Anyone who's worked with a productive AI coder knows this feeling. The code grows faster than understanding.

### The New Reality

When AI writes 99% of your code, a much larger proportion of your codebase is unfamiliar to any given engineer.

Traditional code review breaks down because:
1. Volume overwhelms reviewers
2. Context is missing
3. Intent is unclear
4. Changes are too large to grasp

### The Solution: Review the Specs

I can't read 2000 lines of Go daily.  
But I *can* read 200 lines of a well-written implementation plan.

I can't spelunk through 40+ files of daemon code for an hour when something breaks.  
But I *can* steer a research prompt to give me the speed-run on where to look and why.

### What This Means for Teams

You need an engineering process that:

1. **Keeps team members aligned** - Everyone knows what's changing and why
2. **Enables quick learning** - Anyone can understand unfamiliar code quickly
3. **Preserves intent** - The "why" is as accessible as the "what"
4. **Scales with velocity** - Works whether shipping 1 PR or 10 per day

For most teams, this is pull requests and internal docs.  
For us, it's specs, plans, and research.

### The Broader Implication

Code review as we know it is dying. Not because quality doesn't matter, but because the abstraction level is wrong.

When AI writes the code, humans should review the thinking. When implementation is automated, specification becomes paramount.

This isn't giving up on code quality. It's recognizing that code quality comes from clear thinking, and clear thinking is best reviewed at the specification level.

[← Human Leverage](12-human-leverage.md) | [What's Coming →](14-whats-coming.md)