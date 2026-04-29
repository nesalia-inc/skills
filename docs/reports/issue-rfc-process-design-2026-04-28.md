# Issue and RFC Process Design for Marty

**Date**: 2026-04-28
**Status**: Draft
**Related**: `docs/reports/rfc-process-design-2026-04-28.md`

---

## Executive Summary

This document defines the process for creating GitHub Issues and managing RFCs in Marty. It is designed for a solo developer (Human/CTO/CEO) assisted by agents. All issue creation is agent-driven; the Human only speaks/discusses.

---

## Core Principle

**Human speaks, agents execute paperwork.**

The Human discusses ideas, bugs, features. Agents handle all documentation, issue creation, RFC drafting, and implementation. The Human only makes decisions (approve/reject) and reviews outputs.

---

## GitHub Issues

### Purpose

GitHub Issues track all work items in Marty:
- Bugs
- Features (small to large)
- Tasks
- Refactors

### `/create-issue` Skill

Triggered by: `/create-issue [optional context]`

**What it does:**
1. Reads recent conversation context
2. Creates a GitHub Issue with appropriate labels
3. Determines if the issue is RFC-worthy (large/complex) or simple
4. If RFC-worthy: creates a draft RFC linked to the issue
5. If simple: assigns to appropriate agent for implementation

**The skill does ONLY issue creation** — nothing else.

### Issue Labels

| Label | Meaning |
|-------|---------|
| `bug` | Bug fix |
| `feature` | New feature |
| `refactor` | Code refactoring |
| `chore` | Maintenance task |
| `research` | Investigation needed |
| `rfc-worthy` | Needs RFC before implementation |
| `priority/p0` | Critical |
| `priority/p1` | High |
| `priority/p2` | Medium |
| `priority/p3` | Low |

### Issue Structure

```markdown
---
number: 42
title: Add GitHub OAuth authentication
type: feature
rfc: RFC-0042
status: proposed
priority: p1
created: 2026-04-28
---

## Summary

## Motivation

## Acceptance Criteria

## Related
- RFC-0042
```

---

## RFC Process

### Purpose

RFCs are for **large or complex changes** that need structured thinking before implementation. Not all issues become RFCs.

**RFC-worthy criteria:**
- Architectural impact
- Changes that constrain future development
- New features with multiple design choices
- Refactors that change behavior

**NOT RFC-worthy:**
- Bug fixes (unless architectural)
- Small, bounded changes
- Documentation improvements
- Hotfixes

### RFC Lifecycle

```
┌─────────────────────────────────────────────────────┐
│  1. Issue Created                                   │
│     Human discusses idea                            │
│     → /create-issue creates GitHub Issue #42       │
│     → Agent marks as "rfc-worthy" if applicable    │
└─────────────────┬───────────────────────────────────┘
                  ↓
┌─────────────────────────────────────────────────────┐
│  2. RFC Draft                                      │
│     /create-rfc #42                                │
│     → RFC-0042 created in docs/rfc/                │
│     → Linked to GitHub Issue #42                   │
│     → Document enriched over time (dynamic)        │
└─────────────────┬───────────────────────────────────┘
                  ↓
┌─────────────────────────────────────────────────────┐
│  3. Enrichment (Dynamic)                           │
│     Human and agents discuss, refine               │
│     → RFC document updated iteratively             │
│     → Linked GitHub Issue updated                 │
└─────────────────┬───────────────────────────────────┘
                  ↓
┌─────────────────────────────────────────────────────┐
│  4. Approval                                       │
│     Human reviews and approves                      │
│     → Issue status = Approved                      │
│     → RFC status = Approved                        │
└─────────────────┬───────────────────────────────────┘
                  ↓
┌─────────────────────────────────────────────────────┐
│  5. Implementation                                 │
│     /implement RFC-0042                            │
│     → Builder agent implements                      │
│     → References RFC in commits                    │
│     → Issue closed when complete                   │
└─────────────────────────────────────────────────────┘
```

### RFC vs Issue

| Aspect | GitHub Issue | RFC Document |
|--------|--------------|--------------|
| **Location** | GitHub | `docs/rfc/RFC-XXXX.md` |
| **Purpose** | Track work | Capture design decisions |
| **Format** | Structured (labels, status) | Markdown (flexible) |
| **Author** | Agent (from /create-issue) | Human + agents (enrich together) |
| **Evolution** | Status changes | Content grows over time |
| **Scope** | Always (bugs, features, etc.) | Large changes only |

### RFC = Dynamic Document

Unlike traditional RFCs (written once, then static), Marty RFCs are **enriched iteratively**:

1. Draft created by agent from issue context
2. Human discusses ideas with agents
3. RFC document updated to reflect decisions
4. When approved, RFC is "complete"

This allows the RFC to capture the **reasoning process**, not just the final decision.

### RFC Numbering

RFCs and Issues share the same number:
- GitHub Issue #42
- RFC-0042 in `docs/rfc/RFC-0042-title.md`

This creates a clear link between tracking (GitHub) and design (RFC doc).

---

## Skills

| Skill | Trigger | Purpose |
|-------|---------|---------|
| `/create-issue` | `/create-issue [ctx]` | Creates GitHub Issue, determines if RFC needed |
| `/create-rfc` | `/create-rfc #XX` | Creates RFC draft linked to issue |
| `/implement` | `/implement RFC-XXXX` | Implements approved RFC |
| `/triage` | `/triage #XX` | Triages existing issue |
| `/learn` | `/learn [pkg/concept]` | Learns a package/concept |

---

## Agent Roles

### Issue Creator Agent
- Runs `/create-issue`
- Creates GitHub Issues with proper labels
- Determines RFC-worthiness
- Assigns to agents

### RFC Enrichment Agent
- Assists Human during RFC discussion
- Updates RFC document with decisions
- Suggests alternatives, identifies gaps

### Builder Agent
- Implements approved RFCs
- References RFC number in commits
- Updates issue status

### Reviewer Agent
- Reviews RFC for completeness
- Checks alternatives, risks, prior art
- Suggests improvements

---

## Workflow Examples

### Example 1: Small Bug

```
Human: "j'ai un bug sur le login"
    ↓
/create-issue "login bug avec OAuth"
    ↓
Agent creates GitHub Issue #45
Labels: bug, priority/p1
rfc-worthy: false
    ↓
Agent assigned for fix
    ↓
Builder fixes, PR, merged
Issue #45 closed
```

### Example 2: Large Feature

```
Human: "je veux ajouter GitHub OAuth avec full repo access"
    ↓
/create-issue "GitHub OAuth with full repo access"
    ↓
Agent creates GitHub Issue #46
Labels: feature, rfc-worthy, priority/p1
    ↓
/create-rfc #46
    ↓
RFC-0046 created in docs/rfc/
    ↓
Human and agent enrich RFC over discussion
RFC updated iteratively
    ↓
Human approves RFC
Issue #46 status = Approved
    ↓
/implement RFC-0046
    ↓
Builder implements
PR → review → merge
Issue #46 closed
```

---

## Next Steps

1. Create `/create-issue` skill
2. Define `/create-rfc` skill
3. Define `/implement` skill
4. Update CLAUDE.md with new process
5. Test with next bug or feature

---

## Related Documents

- `docs/reports/rfc-process-design-2026-04-28.md` — RFC process details
- `docs/reports/process-discussion-2026-04-28.md` — Initial process discussion

---

*This document should be updated as the process evolves.*
