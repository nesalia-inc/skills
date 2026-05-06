# Entry Points

Entry points are the triggers that tell the agent when to read or write to the context system. Without explicit entry points, the agent will never spontaneously consult or update the context files.

## The Problem

The Project Context System (PCS) is only effective if the agent actively uses it. Without guidance, the agent will:
- Never read `decisions/` before proposing tech changes
- Never update `progress.md` at end of session
- Never create ADRs when making architectural decisions
- Only read `map.md` if explicitly instructed

## Two Types of Entry Points

### Read Entry Points

When the agent encounters these triggers, it must read the specified context file.

| Trigger | File to Read | Reason |
|---------|-------------|--------|
| **Conversation starts** | `.context/map.md` | Get project overview and navigation |
| **Before writing code** | `[[project/definition/conventions.md]]` | Apply project coding rules |
| **Before installing/updating deps** | `[[project/definition/stack.md]]` | Understand target stack |
| **Before proposing tech/stack changes** | `[[project/definition/decisions/*.md]]` | Understand why current choices were made |
| **When navigating or adding files** | `[[project/definition/structure.md]]` | Respect folder organization |
| **At session start** | `[[project/status/current.md]]` | Know current sprint and phase |
| **At session start** | `[[project/status/progress.md]]` | Know ongoing work |
| **When encountering blockers** | `[[project/status/active-issues.md]]` | Check existing blockers |
| **After git operations** | `[[project/status/recent.md]]` | Track recent changes |

### Write Entry Points

When the agent encounters these triggers, it must offer to update the specified context file.

| Trigger | Action | File to Update |
|---------|--------|---------------|
| **Task/Feature completion** | Offer to summarize what was done | `[[project/status/recent.md]]` |
| **Before commit or PR** | Offer to update session progress | `[[project/status/progress.md]]` |
| **Major technical decision** | Create ADR to document rationale | `[[project/definition/decisions/000X-title.md]]` |
| **Blocker identified** | Offer to update blockers list | `[[project/status/active-issues.md]]` |
| **Architecture/stack change** | Update target definition first | Corresponding `[[project/definition/*.md]]` |
| **Detected structure drift** | Offer to update structure.md | `[[project/definition/structure.md]]` |

## Context Handshake

At the start of every conversation, after loading `map.md` and relevant context files, the agent must signal context activation with a brief confirmation:

```
Context Loaded: [Project Name] ([Sprint/Phase]).
- [X] active ADRs
- [Y] blockers in [[active-issues.md]]
- Status: [up-to-date/stale — offer sync if stale]

Ready to proceed.
```

This "handshake" confirms to the user that the context system is active and alerts them to any staleness issues.

## Agent Behavior: Context-First

Before coding, the agent must:

```
1. Detect what type of work is being done
2. Check corresponding Read Entry Points
3. Load necessary context files
4. Perform Context Handshake
5. Only then proceed with the work
6. On task completion, check Write Entry Points
7. Before commit/PR, verify context updates are done
```

## Entry Point Detection Rules

### Rule 1: Never Guess, Always Read

> "Before writing any code, read [[conventions.md]]. Before navigating, read [[structure.md]]. Never assume you know the project conventions."

### Rule 2: Document Decisions When Made

> "When you make a significant technical choice, create an ADR in [[decisions/]]. Don't wait to be asked."

### Rule 3: Task Completion = Context Update

> "On task or feature completion (not 'end of session'), offer to update [[status/recent.md]]. Before commit or PR, offer to update [[status/progress.md]]."

### Rule 4: Architecture Change = Context Change First

> "Before changing the architecture or stack, update the corresponding definition file. The code follows the context, not the other way around."

### Rule 5: Check ADRs Before Questioning Choices

> "If you want to replace a library, pattern, or approach, first read the related ADR in [[decisions/]]. Only propose changes after understanding the original reasoning."

## Trigger Detection Examples

| Agent Action | Trigger Detected | Entry Point Action |
|--------------|------------------|-------------------|
| User asks to replace Redux with Zustand | Tech change proposal | Read `[[decisions/*.md]]` to find why Redux was chosen |
| Agent finishes implementing feature | Task completion | Offer to update `[[status/recent.md]]` |
| User runs `git commit` | Pre-commit | Offer to update `[[status/progress.md]]` if work in progress |
| Agent detects `src/` structure has changed | Structure drift | Offer to compare with `[[structure.md]]` |
| User reports a blocker | Blocker encountered | Check and update `[[status/active-issues.md]]` |
| Agent starts working on a new task | Task start | Read `[[status/current.md]]` + `[[status/progress.md]]` |
| User asks to add new dependency | Dep installation | Read `[[stack.md]]` to verify compatibility |
| User asks to create PR | Pre-PR | Check if context updates needed before merge |

## Lazy Loading with Entry Points

Entry points do not mean loading everything at once. Based on the current task, the agent loads only what's relevant:

```
Task: Fix bug in user authentication
  → Read [[status/current.md]] (context)
  → Read [[structure.md]] (find auth files)
  → Read [[decisions/0003-auth-strategy.md]] (understand auth architecture)
  → Context Handshake
  → Fix the bug
  → Write [[status/recent.md]] (task completion)
  → Write [[status/progress.md]] (pre-commit)
```

## ADR Template

To reduce friction when creating ADRs, use this minimal template:

```markdown
---
created: 2024-01-15
---

# ADR [ID]: [Title]

## Context
[Problem being solved or why this decision was needed]

## Decision
[What was chosen and why]

## Alternatives Considered
[Why other options were rejected]

## Consequences
[Trade-offs, benefits, and risks of this choice]
```

Place this template in `[[project/definition/conventions.md]]` for easy reference.

## Operational Protocol (map.md integration)

Add this section to `map.md` so the agent knows how to manage itself:

```markdown
## Operational Protocol

1. **On Start**: Read this file + `status/current.md` + `status/progress.md`. Perform Context Handshake.

2. **On Code**: Read `definition/conventions.md` before writing.

3. **On Tech Change**: Check `definition/decisions/` before proposing any changes.

4. **On Task Completion**: Update `status/recent.md`.

5. **On Commit/PR**: Update `status/progress.md` if work is in progress.

6. **On Architecture/Stack Change**: Update target `definition/*.md` first, then code.
```

## Skills Integration

The `project-context` skill must encode these entry points as explicit rules that the agent follows.

The skill should:
1. Define the entry points clearly
2. Provide detection logic for each trigger
3. Specify the exact files to read/write
4. Include reminder prompts for the agent
5. Include the Context Handshake template
6. Include the ADR template

## Summary

| Entry Point Type | Count | Purpose |
|-----------------|-------|---------|
| Read Entry Points | 9 | When to load context files |
| Write Entry Points | 6 | When to update context files |
| Behavior Rules | 5 | Core agent behavior around context |

**Key Change:** "End of session" is replaced by "Task completion" and "Pre-commit/PR" — more reliable triggers that the agent can actually detect.

Without entry points, the PCS is documentation. With entry points, it becomes an active protocol the agent follows.