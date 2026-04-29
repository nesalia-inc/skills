# Development Process Discussion - 2026-04-28

## Context

This document captures a discussion about defining a development process for Marty that leverages agents effectively. The user is a solo developer with access to multiple agents, effectively operating like a small team.

---

## Current State

### The Problem

The user's workflow for new features follows a typical pattern:

1. Discussion with agent (research, learning, documentation)
2. Written reports in `docs/learnings/`, `docs/plans/`, etc.
3. Chaos and paralysis when transitioning to implementation

This "gap" between research/documentation and implementation is the main pain point.

### Root Cause

The user described thinking like a **solo dev**, not a lead with a team. With agents available, they effectively have a team, but:
- Massive plans are overwhelming
- Decomposition into actionable tasks is difficult
- Hard to know where to start

---

## Proposed Process Framework

### Tiered Approach by Change Type

Not all changes need the same process:

| Type | Process |
|------|---------|
| Hotfix (1-liner, trivial) | Direct commit on main |
| Bug fix (non-trivial) | Branch → PR → merge (light review) |
| Small feature | Branch → PR → self-review → merge |
| Large feature | Issue → RFC → Spec → Branch → PR → review → merge |
| Refactor (major) | Issue → RFC → Spec → Branch → PR → review → merge |
| Research/Learning | No code output — document in `docs/learnings/` |

### The RFC Phase

For research and planning, **RFC (Request for Comments)** is the preferred mechanism:

```
Idea emerges
    ↓
Pair with archi/tech lead agent (see below)
    ↓
RFC written in `docs/rfc/`
    ↓
Human (CTO/CEO) approves
    ↓
Implementation begins
```

#### Why RFC instead of Issues?

- Issues are too heavy for the research phase
- RFC enables structured thinking (motivation, spec, drawbacks, alternatives, prior art)
- Captures the "why" alongside the "what"
- Serves as documentation for future reference

### The RFC Document Structure

```markdown
# RFC-XXX: [Title]

## Status
[Proposed | Approved | Rejected | Deprecated]

## Motivation
Why are we doing this? What problem does it solve?

## Detailed Specification
What exactly will be built? Be precise.

## Drawbacks
What are the disadvantages? What might go wrong?

## Alternatives Considered
Why this approach over others? What else was considered?

## Prior Art
How have other projects/systems approached this?

## Implementation Plan
Phases, milestones, or tasks (if already defined)

## Open Questions
Things to resolve before/during implementation
```

---

## Agent Architecture

### The Vision: CTO/CEO with Specialized Agents

The user sees themselves as the **CTO/CEO** at the end of the chain:

```
CEO/CTO (Human)
    ↓ makes decisions, provides direction
Specialized Agents (The Team)
    ↓ execute, research, propose, implement
```

### Different Agent Types by Context

The key insight: **different conversations require different agents**:

| Context | Agent Role |
|---------|------------|
| New feature research | Archi/Tech Lead agent |
| Implementation | Task-specific subagent |
| Code review | Reviewer agent |
| Learning a package | `learn-package` skill |
| How-to question | `how-to` or `learn` skill |
| Writing RFC | Archi/Tech Lead agent + Human |

### The Archi/Tech Lead Agent

This agent pairs with the user during the research/planning phase.

**Ideal exchange pattern:**

```
Human: "I want to add auth to my app"
Agent: "What kind of app? Web, mobile, API only?"
Human: "Next.js web app + REST API"
Agent: "Do you want social OAuth, password, or both? Is 2FA needed?"
Human: "GitHub OAuth + password + TOTP"
Agent: "For sessions: stateless JWT or DB-backed sessions?"
... (continues until spec is complete)
```

**What this agent needs:**
- Context about the codebase (to avoid proposing incompatible architectures)
- Awareness of previous RFCs (for consistency)
- Knowledge of available skills (to know what's possible)

### The Implementation Agent (`/implement`)

After RFC is approved, a subagent executes the implementation based on:
- The RFC specification
- The approved plan
- Context from the codebase

---

## GitHub as Process Hub

### What GitHub Does Well

- Issues → bugs, features (with labels, milestones)
- PRs → code review, merge
- Actions → CI/CD, testing
- Projects → kanban boards
- Wiki/docs → documentation
- Discussions → async conversation

### What GitHub Doesn't Do

- Automatic issue → implementation flow
- Built-in for research/architecture phase
- AI/agents integration (manual orchestration)

### The Goal: OSS-Level CI/CD Pipeline

```
Issue opened
    ↓
Triage (labels, priority, assignment)
    ↓
RFC (if feature is complex)
    ↓
Branch created
    ↓
PR opened → CI runs (tests, lint, build)
    ↓
Code review
    ↓
Corrections
    ↓
Merge
    ↓
Auto-deploy (if checks pass)
```

---

## Key Decisions Made

1. **RFC lives in `docs/rfc/`** — not GitHub Issues — for the research/planning phase
2. **GitHub Issues only for approved work** — not for capturing ideas
3. **Tiered process** — not everything needs full PR → review → merge
4. **Multiple agent types** — based on context, not one-size-fits-all
5. **Human is CTO/CEO** — agents execute, human decides

---

## Open Questions / Next Steps

1. **Agent selection**: How to determine which agent type to use in a given moment?
2. **RFC approval**: What makes an RFC "approved" — human sign-off only?
3. **Context persistence**: How do agents maintain context across sessions?
4. **Integration with skills**: How do `learn-package`, `learn`, `deep-research` fit in?
5. **Tooling**: What needs to be built vs what exists today?

---

## Related Documents

- `docs/learnings/` — Package research and documentation
- `docs/plans/` — Feature plans and proposals
- `docs/internal/` — Internal project documentation

---

*This document captures the beginning of a process design discussion. It should be updated as the process evolves.*
