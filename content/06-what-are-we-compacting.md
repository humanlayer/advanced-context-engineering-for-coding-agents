[← Back to README](../README.md)

## What Exactly Are We Compacting?

### The Context Eaters

What fills up your context window?

* **Searching for files** - "Let me search for... let me look in... let me check..."
* **Understanding code flow** - Reading file after file to trace execution
* **Applying edits** - The back-and-forth of making changes
* **Test/build logs** - Walls of output from failed builds
* **Huge JSON blobs** - API responses, tool outputs, configuration dumps

Each of these can eat thousands of tokens. A single test failure can consume 10% of your context.

### Compaction = Structured Artifacts

**Compaction** is simply distilling these token-hungry activities into structured artifacts.

Instead of:
```
[5000 tokens of searching through files]
[3000 tokens of reading code]
[2000 tokens of failed attempts]
```

You get:
```markdown
## Key Files
- `src/daemon/handler.go` - Main request handler
- `src/daemon/pool.go` - Connection pool management
- `tests/integration/daemon_test.go` - Integration tests

## How It Works
1. Request comes in via Unix socket
2. Handler validates and routes to appropriate service
3. Service processes using connection from pool
4. Response serialized and sent back

## Current Issue
Race condition when connection pool is exhausted:
- Thread A gets last connection
- Thread B waits for connection
- Thread A returns connection
- Thread C steals it before B wakes up
```

### The Multiplication Effect

Every token you save in compaction gets multiplied across:
- Every subsequent turn with the agent
- Every restart or context refresh
- Every team member who reads the artifact

A 10x reduction in context size means 10x more room for actual problem-solving.

[← Intentional Compaction](05-intentional-compaction.md) | [Why Obsess Over Context? →](07-why-obsess-over-context.md)