[← Back to README](../README.md)

## Real World: Fixing BAML in 300k LOC

Let's see this in practice. I decided to test our techniques on [BAML](https://github.com/BoundaryML/baml), a programming language for working with LLMs.

- **The challenge**: 300k lines of Rust code
- **My experience**: Amateur Rust dev, never seen this codebase
- **The bug**: [Issue #1252](https://github.com/BoundaryML/baml/issues/1252) - assertion syntax validation
- **The result**: PR approved and merged same day

You can [watch the full episode](https://hlyr.dev/he-yt) to see the process.

### The Research Phase

**First attempt**: Claude decided the bug was invalid and the codebase was correct. I threw this research away.

**Second attempt**: With more steering, produced [this research doc](https://github.com/ai-that-works/ai-that-works/blob/main/2025-08-05-advanced-context-engineering-for-coding-agents/thoughts/shared/research/2025-08-05_05-15-59_baml_test_assertions.md).

Key insight: The research found the assertion validation was happening in the wrong phase of parsing.

### The Plan Phase

I created two plans in parallel:

1. **[Plan without research](https://github.com/ai-that-works/ai-that-works/blob/main/2025-08-05-advanced-context-engineering-for-coding-agents/thoughts/shared/plans/fix-assert-syntax-validation-no-research.md)** - Started while research was running
2. **[Plan with research](https://github.com/ai-that-works/ai-that-works/blob/main/2025-08-05-advanced-context-engineering-for-coding-agents/thoughts/shared/plans/baml-test-assertion-validation-with-research.md)** - Created after research completed

Both plans would have worked, but they differed significantly:
- Different fix locations
- Different testing approaches
- Different code organization

The plan with research fixed the problem in the *best* place and prescribed testing more aligned with codebase conventions.

### The Implementation Phase

Ran both plans in parallel overnight. By morning:

- **[PR with research](https://github.com/BoundaryML/baml/pull/2259#issuecomment-3155883849)**: Approved by maintainer (who didn't know this was for a demo!)
- **[PR without research](https://github.com/BoundaryML/baml/pull/2258/files)**: Closed as inferior approach

### The Deeper Test

Still skeptical? A few weeks later, Vaibhav and I spent 7 hours (3 on research/plans, 4 on implementation) and shipped **35k LOC** to add cancellation and WASM support to BAML.

[The cancellation PR got merged](https://github.com/BoundaryML/baml/pull/2357).

### What Made This Work

1. **Research quality mattered** - Bad research = bad plan = bad implementation
2. **Multiple attempts were needed** - First research was wrong, had to retry
3. **Parallel experimentation** - Running two plans revealed the quality difference
4. **Human judgment essential** - Knowing when to throw away bad research

This isn't magic. It's engineering.

[← Research, Plan, Implement](10-research-plan-implement.md) | [Human Leverage →](12-human-leverage.md)