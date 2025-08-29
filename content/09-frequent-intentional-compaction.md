[← Back to README](../README.md)

## Frequent Intentional Compaction: The Game Changer

The techniques that transformed our workflow fall under what I call "frequent intentional compaction".

### The Core Principle

Design your ENTIRE WORKFLOW around context management. Keep utilization in the 40%-60% range (depending on problem complexity).

Not 90%. Not 80%. **40-60%**.

This seems wasteful until you realize: the last 40% of your context is where everything falls apart.

### Ralph Wiggum as a Software Engineer

Geoff Huntley's [Ralph Wiggum technique](https://ghuntley.com/ralph/) takes this to the extreme:

```bash
while :; do
  cat PROMPT.md | npx --yes @sourcegraph/amp 
done
```

Run an agent in a loop forever with a simple prompt. Fresh context every time. "Hilariously dumb" but [surprisingly effective](https://ghuntley.com/content/images/size/w2400/2025/07/The-ralph-Process.png).

Our team used Ralph to [port BrowserUse to TypeScript overnight](https://github.com/repomirrorhq/repomirror/blob/main/repomirror.md) at the YC Agents Hackathon.

### The Three-Phase Workflow

We split every task into three phases (sometimes two, sometimes more, but usually three):

1. **Research** - Understand the codebase
2. **Plan** - Design the solution
3. **Implement** - Execute the plan

Each phase:
- Starts with minimal context
- Focuses on one goal
- Produces a structured artifact
- Feeds cleanly into the next phase

### Why 40-60%?

At 40-60% utilization:
- The model still has "room to think"
- Attention mechanisms work properly
- You can handle unexpected discoveries
- There's space for error recovery

At 90% utilization:
- The model struggles with basic tasks
- Simple instructions get missed
- Errors cascade instead of recovering
- You're one stack trace away from context overflow

### The Counterintuitive Truth

Using less context gives you more capability. It's not about the size of your context window. It's about how you use it.

Think of it like RAM in a computer. You don't want to use 100% of your RAM. You want headroom. Same with context.

[← Subagents](08-subagents-context-control.md) | [Research, Plan, Implement →](10-research-plan-implement.md)