[← Back to README](../README.md)

## The Naive Way: Chat Until You Apologize

Most of us start by using a coding agent like a chatbot. You talk (or shout) back and forth with it, vibing your way through a problem until you either:
- Run out of context
- Give up
- The agent starts apologizing

<img width="1328" height="741" alt="Naive chat approach" src="https://github.com/user-attachments/assets/51a46854-c542-4515-afbb-a2fe26970809" />

### The Slightly Smarter Restart

A slightly smarter approach is to just start over when you get off track. Discard your session and start fresh, perhaps with more steering:

> [original prompt], but make sure not to use XYZ approach, that won't work

<img width="1331" height="744" alt="Restart with steering" src="https://github.com/user-attachments/assets/c96f9b42-0801-428a-b366-af871d1f97af" />

### Why This Doesn't Scale

The naive approach has fundamental problems:

1. **Context Pollution** - Every failed attempt, every "oops", every correction stays in context
2. **Trajectory Lock-in** - Once the agent starts down a path, it's hard to redirect
3. **No Learning Transfer** - When you restart, you lose everything you learned
4. **Cognitive Load** - You're simultaneously debugging the problem AND managing the agent

The chat paradigm is great for exploration but terrible for execution. It's like using a REPL in production.

### The Missing Piece

What's missing is intentionality. When you're just chatting, you're not being deliberate about:
- What information goes into context
- When to preserve vs discard context
- How to structure information for reuse
- Where human review adds the most value

The solution isn't to chat better. It's to stop chatting and start engineering.

[← Our Weird Journey](03-our-weird-journey.md) | [Intentional Compaction →](05-intentional-compaction.md)