[← Back to README](../README.md)

## Why Obsess Over Context?

### LLMs Are Stateless Functions

As we covered in [12-factor agents](https://hlyr.dev/12fa), LLMs are stateless functions. The only thing that affects the quality of your output is the quality of your inputs.

At any given point, a turn in a coding agent is a stateless function call. Context window in, next step out.

<img width="1334" height="747" alt="Context as stateless function" src="https://github.com/user-attachments/assets/471ecb31-5502-4100-8371-112dee75ac76" />

The contents of your context window are the ONLY lever you have to affect the quality of your output.

### The Context Optimization Hierarchy

You should optimize your context window for:

1. **Correctness** - Right information beats more information
2. **Completeness** - All necessary information present
3. **Size** - Minimal tokens for maximum signal
4. **Trajectory** - Guiding toward the solution

### The Three Deadly Sins

The worst things that can happen to your context window, in order:

1. **Incorrect Information** - Wrong file contents, outdated docs, failed approach still visible
2. **Missing Information** - Can't solve what you can't see
3. **Too Much Noise** - Signal drowned in irrelevance

### The Context Equation

<img width="1320" height="235" alt="Context equation" src="https://github.com/user-attachments/assets/a6ea98a6-665b-48af-983b-a1cb2c45e44c" />

Context Window Quality = (Correctness × Completeness) / Noise

### Geoff Huntley's Insight

As [Geoff Huntley](https://x.com/GeoffreyHuntley) puts it:

> The name of the game is that you only have approximately **170k of context window** to work with.
> So it's essential to use as little of it as possible.
> The more you use the context window, the worse the outcomes you'll get.

This isn't about being clever. It's about physics. Attention mechanisms degrade with length. The model literally pays less attention to everything when the context is full.

### Why Most Approaches Fail

Most approaches treat context as infinite. They assume you can just keep adding information. But every token you add makes every other token less likely to be properly attended to.

The solution isn't to wait for longer context windows. It's to be intentional about what goes in.

[← What Are We Compacting?](06-what-are-we-compacting.md) | [Subagents →](08-subagents-context-control.md)