# Advanced Context Engineering for Coding Agents

*Getting AI to solve complex problems in brownfield codebases*

<div align="center">
<a href="https://hlyr.dev/ace">
    <img src="https://img.shields.io/badge/YC_Talk-17m-orange" alt="YC Talk"></a>
<a href="https://hlyr.dev/12fa">
    <img src="https://img.shields.io/badge/12--factor_agents-context-blue" alt="12-factor agents"></a>
<a href="https://hlyr.dev/he-yt">
    <img src="https://img.shields.io/badge/live_coding-BAML_fix-red" alt="Live Coding Session"></a>
</div>

<p></p>

*This guide shares what we learned taking context engineering from agent design to practical coding workflows, why spec-driven development is the future, and how we ship 35k LOC in 7 hours.*

> [!TIP]
> Prefer video? [Watch the YC talk](https://hlyr.dev/ace) this is based on
>
> Want to see it in action? [Watch us fix a bug in 300k LOC Rust codebase](https://hlyr.dev/he-yt)

> [!WARNING]
> Hey - this is a WIP. In case you stumble on it.
>
> I will polish all the AI slop soon.
>
> -dex
<img width="947" height="252" alt="Screenshot 2025-08-29 at 3 58 11 PM" src="https://github.com/user-attachments/assets/8310c8fc-5b6d-4fda-9bf3-22e18d684ab2" />


Hi, I'm dex. You might remember me from [12-factor agents](https://hlyr.dev/12fa), coining "context engineering," or [the AI Engineer talk](https://www.youtube.com/watch?v=8kMaTybvDUw).

**I've been obsessed** with making AI coding agents actually work in production codebases. Not demos. Not greenfield projects. Real, messy, complex brownfield code.

**I've discovered** that the secret isn't waiting for smarter models. It's being intentional about context management.

**I've shipped** 6 PRs in a day without opening a single non-markdown file in an editor. Our intern shipped 10 PRs on day 8. We fixed complex race conditions in Go and added major features to 300k LOC Rust codebases we'd never seen before.

So, I set out to document:

> ### **How do we engineer context to make AI coding agents solve complex problems in brownfield codebases with zero slop?**

Welcome to Advanced Context Engineering for Coding Agents. Buckle up.

*Special thanks to [@vaibhav](https://github.com/vaibhav), [@sundeep](https://github.com/sundeep), [@geoffreyhuntley](https://github.com/geoffreyhuntley), [@simonfarshid](https://github.com/simonfarshid), [@boundaryml](https://github.com/boundaryml), and everyone who's suffered through early versions of these ideas.*

## The Short Version: The Core Concepts

Even as models [plateau in capability](content/01-from-12factor-to-context-engineering.md#maybe-someday-when-models-are-smarter), there are engineering techniques that make AI coding dramatically more reliable, scalable, and maintainable.

- [From 12-Factor Agents to Context Engineering for Coding](content/01-from-12factor-to-context-engineering.md)
- [The Stanford Study & Sean Grove's Revelation](content/02-stanford-study-and-specs.md)
- [Our Weird Journey to Spec-Driven Development](content/03-our-weird-journey.md)
- [The Naive Way: Chat Until You Apologize](content/04-the-naive-way.md)
- [Intentional Compaction: Your First Power Move](content/05-intentional-compaction.md)
- [What Exactly Are We Compacting?](content/06-what-are-we-compacting.md)
- [Why Obsess Over Context?](content/07-why-obsess-over-context.md)
- [Subagents: Context Control, Not Role Play](content/08-subagents-context-control.md)
- [Frequent Intentional Compaction: The Game Changer](content/09-frequent-intentional-compaction.md)
- [Research, Plan, Implement: The Three-Step Dance](content/10-research-plan-implement.md)
- [Real World: Fixing BAML in 300k LOC](content/11-real-world-baml.md)
- [Human Leverage: Where to Focus Your Attention](content/12-human-leverage.md)
- [Code Review in the Age of AI](content/13-code-review-mental-alignment.md)
- [What's Coming: The Post-IDE World](content/14-whats-coming.md)

## Why This Matters

The general vibe on AI coding for hard stuff tends to be:

> Maybe someday, when models are smarter…

Meanwhile, teams using these techniques are:
- Shipping 2000-line PRs of complex systems code
- Fixing bugs in codebases they've never seen
- Maintaining mental alignment while AI writes 99% of code
- Spending $12k/month on Opus and loving it

## The Problem We're Solving

Current AI coding tools have fundamental issues:

- **"Too much slop"** - Generated code that technically works but creates tech debt
- **"Doesn't work in big repos"** - Context windows explode, agents get lost
- **"Doesn't work for complex systems"** - Race conditions, distributed systems, etc.
- **"Tech debt factory"** - Rework outweighs productivity gains

## The Solution: Advanced Context Engineering

### What We Achieved

- ✅ **Works in Brownfield Codebases** - 300k LOC Rust, complex Go systems
- ✅ **Solves Complex Problems** - Race conditions, WASM support, cancellation
- ✅ **No Slop** - PRs merged by maintainers who didn't know it was AI
- ✅ **Maintains Mental Alignment** - Team stays in sync despite 10x velocity

### The Core Insight

<img width="1320" height="235" alt="Context equation" src="https://github.com/user-attachments/assets/a6ea98a6-665b-48af-983b-a1cb2c45e44c" />

> **Context Window Quality = (Correctness × Completeness) / Noise**

At any given point, a coding agent turn is a stateless function call. Context in, next step out. The ONLY lever you have is context quality.

## The Three-Step Workflow

### 1. Research
Understand the codebase, find relevant files, trace information flow. [See our research prompt](https://github.com/humanlayer/humanlayer/blob/main/.claude/commands/research_codebase.md).

### 2. Plan
Outline exact steps, files to edit, testing approach. [See our planning prompt](https://github.com/humanlayer/humanlayer/blob/main/.claude/commands/create_plan.md).

### 3. Implement
Execute the plan phase by phase, compact progress back into plan. [See our implementation prompt](https://github.com/humanlayer/humanlayer/blob/main/.claude/commands/implement_plan.md).

## Key Principles

### 1. Frequent Intentional Compaction
Keep context utilization at 40-60%. Design your ENTIRE workflow around context management.

### 2. Human Leverage at the Right Layer
- Bad line of code = bad line of code
- Bad line of plan = hundreds of bad lines
- Bad line of research = thousands

<img width="1331" height="745" alt="Human leverage pyramid" src="https://github.com/user-attachments/assets/305d3716-cb5c-4c1d-bb2b-bc035b35540b" />

### 3. Subagents Are About Context, Not Roles
[Don't play house](https://x.com/dexhorthy/status/1950288431122436597). Use subagents for context isolation and compaction.

### 4. This Is Not Magic
You MUST engage deeply. There's no magic prompt. This makes your performance better by building high-leverage human review into the pipeline.

## Real Results

- **Our team**: 3 engineers averaging $12k/month on Opus
- **Intern**: 2 PRs on day 1, 10 PRs on day 8
- **BAML bug**: Fixed in 300k LOC Rust, PR merged same day
- **Complex features**: 35k LOC shipped in 7 hours (cancellation + WASM)

## Getting Started

### For Individual Developers
Start with intentional compaction. When context fills up, write progress to a markdown file and start fresh.

### For Teams
1. Adopt research/plan/implement workflow
2. Review plans, not just code
3. Use specs as source of truth
4. Build shared context through markdown artifacts

### For Leaders
The hard part isn't the tech—it's the transformation. Everything about collaboration changes when AI writes 99% of code. If you don't figure this out, you'll get lapped by someone who did.

## What's Next

We're building tools to make this easier. Join the waitlist for CodeLayer, our "Superhuman for Claude Code": [https://hlyr.dev/code](https://hlyr.dev/code)

For engineering leaders ready to 10x productivity: we're forward-deploying to help teams make the culture/process/tech shift.

## Related Resources

- [12-Factor Agents](https://hlyr.dev/12fa) - The foundation for context engineering
- [AI That Works](https://github.com/ai-that-works/ai-that-works) - Weekly live coding sessions
- [Ralph Wiggum Technique](https://ghuntley.com/ralph/) - Hilariously simple context management
- [Sean Grove: Specs Are The New Code](https://www.youtube.com/watch?v=8rABwKRsec4)
- [Stanford Study on AI Developer Productivity](https://www.youtube.com/watch?v=tbDDYKRFjhk)
- [BAML](https://github.com/boundaryml/baml) - Where we tested these techniques

## Contributors

This guide exists because smart people shared their workflows, challenged assumptions, and pushed boundaries.

Thanks to everyone making AI coding actually work in production.

---

*Remember: The name of the game is ~170k context window. Use it wisely.*
