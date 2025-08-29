[← Back to README](../README.md)

## Subagents: Context Control, Not Role Play

Subagents have been a feature of Claude Code and many coding CLIs since the early days. But most people misunderstand what they're for.

### What Subagents Are NOT

Subagents are not about [playing house and anthropomorphizing roles](https://x.com/dexhorthy/status/1950288431122436597). 

They're not about:
- "You are a senior Python developer"
- "You are a careful code reviewer"
- "You are a testing specialist"

That's cargo cult prompting.

### What Subagents ARE

**Subagents are about context control.**

<img width="1331" height="745" alt="Subagents for context control" src="https://github.com/user-attachments/assets/0bf24a03-522d-4f1d-8722-9e0d2250bd60" />

When you spawn a subagent:
1. It gets a fresh context window
2. It performs a focused task
3. It returns a compressed result
4. The parent continues with clean context

### The Ideal Subagent Response

The ideal subagent response looks like our compaction artifact:

<img width="1309" height="747" alt="Ideal subagent response" src="https://github.com/user-attachments/assets/a7d5946d-4e81-46e8-b314-d02dae1f00ee" />

### The Challenge

Getting a subagent to return this is not trivial:

<img width="1327" height="745" alt="Subagent challenges" src="https://github.com/user-attachments/assets/113e49eb-db7f-444d-b7f6-993112f87591" />

Generic subagents often:
- Return too much detail
- Miss critical information
- Don't structure their output well
- Fail to compress effectively

### Making Subagents Work

To make subagents effective:

1. **Be specific about output format** - Tell them exactly what structure you want
2. **Focus on one task** - Don't ask them to research AND plan AND implement
3. **Request compression** - Explicitly ask for concise, structured summaries
4. **Use custom subagents when possible** - Better prompts = better compression

The goal isn't delegation. It's isolation. You're not creating a team, you're managing context windows.

[← Why Obsess Over Context?](07-why-obsess-over-context.md) | [Frequent Intentional Compaction →](09-frequent-intentional-compaction.md)