[← Back to README](../README.md)

## Our Weird Journey to Spec-Driven Development

### The 2000-Line PR Problem

I was working with one of the most productive AI coders I've ever met. Every few days they'd drop **2000-line Go PRs**.

And this wasn't a NextJS app or a CRUD API. This was complex, [race-prone systems code](https://github.com/humanlayer/humanlayer/blob/main/hld/daemon/daemon_subscription_integration_test.go#L45) that:
- Managed JSON RPC over Unix sockets
- Handled streaming stdio from forked Unix processes
- Dealt with Claude Code SDK process management
- Had all the fun concurrency bugs you'd expect

### The Breaking Point

The idea of carefully reading 2,000 lines of complex Go code every few days was simply not sustainable.

I had two choices:
1. Slow everything down
2. Fundamentally change how we work

We had no choice but to adopt **spec-driven development**.

### The Uncomfortable Transformation

It took about 8 weeks. It was incredibly uncomfortable for everyone involved, especially me.

I had to learn to:
- Let go of reading every line of PR code
- Trust specs as the source of truth
- Focus my attention on tests and interfaces
- Review plans instead of implementations

### The Results

But now we're flying:
- I shipped 6 PRs in a day
- Our intern shipped 10 PRs on day 8
- I can count on one hand the number of times I've opened a non-markdown file in an editor in the last two months

The transformation wasn't just about productivity. It was about sustainability. We went from drowning in code reviews to surfing on specifications.

### What This Means

When you can't possibly review all the code being produced, you have to move up a level of abstraction. You have to review the plans, the specs, the research.

This isn't giving up on code quality. It's recognizing that code quality comes from clear thinking, and clear thinking is best reviewed at the specification level.

[← Stanford Study & Specs](02-stanford-study-and-specs.md) | [The Naive Way →](04-the-naive-way.md)