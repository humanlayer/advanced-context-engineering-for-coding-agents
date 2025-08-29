[← Back to README](../README.md)

## Research, Plan, Implement: The Three-Step Dance

Here's exactly how we structure our workflow for maximum effectiveness.

### Phase 1: Research

**Goal**: Understand the codebase, find relevant files, trace information flow.

[Our research prompt](https://github.com/humanlayer/humanlayer/blob/main/.claude/commands/research_codebase.md) produces artifacts like:

```markdown
## Overview
The authentication system uses JWT tokens with refresh token rotation...

## Key Files
- `auth/jwt.go` - Token generation and validation
- `auth/middleware.go` - Request authentication middleware
- `auth/refresh.go` - Refresh token rotation logic

## Current Flow
1. User logs in with credentials
2. System generates access token (15min) and refresh token (7 days)
3. Middleware validates tokens on each request
4. Client refreshes when access token expires

## Potential Issues
- No rate limiting on refresh endpoint
- Refresh tokens not invalidated on logout
- Missing token revocation list
```

### Phase 2: Plan

**Goal**: Design exact steps to solve the problem.

[Our planning prompt](https://github.com/humanlayer/humanlayer/blob/main/.claude/commands/create_plan.md) produces artifacts like:

```markdown
## Implementation Plan: Add Refresh Token Revocation

### Phase 1: Add Revocation Storage
1. Create `auth/revocation.go`
   - Define RevokedToken struct
   - Implement storage interface
   - Add Redis implementation
2. Update `auth/middleware.go`
   - Check revocation list before accepting token
   - Add performance caching
3. Test with `auth/revocation_test.go`

### Phase 2: Implement Logout Revocation
1. Update `handlers/logout.go`
   - Add refresh token to revocation list
   - Clear client cookies
2. Add integration test

### Verification
- Run: `go test ./auth/...`
- Check: Revoked tokens are rejected
- Verify: Performance impact < 5ms
```

### Phase 3: Implement

**Goal**: Execute the plan phase by phase.

[Our implementation prompt](https://github.com/humanlayer/humanlayer/blob/main/.claude/commands/implement_plan.md) follows the plan exactly:

1. Read the plan
2. Execute current phase
3. Run verification steps
4. Update plan with progress
5. Move to next phase

### The Critical Insight

Each phase has a different context optimization:

- **Research**: Wide but shallow - see many files, extract patterns
- **Plan**: Narrow but deep - focus on specific approach
- **Implement**: Incremental - one phase at a time

### When to Deviate

Sometimes you:
- Skip research for simple bugs
- Do multiple research passes for complex systems
- Compact mid-implementation for long tasks
- Add a "debug" phase when stuck

The framework is flexible. The principle is constant: manage context intentionally.

[← Frequent Intentional Compaction](09-frequent-intentional-compaction.md) | [Real World: BAML →](11-real-world-baml.md)