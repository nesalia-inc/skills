# Triage Skill Design Report

**Date**: 2026-04-28
**Status**: Draft
**Related**: `docs/reports/issue-rfc-process-design-2026-04-28.md`, `docs/reports/bug-report-process-design-2026-04-28.md`

---

## Executive Summary

The `/triage` skill assesses and categorizes existing GitHub Issues. It determines priority, size, type, and whether an RFC is needed. This is the gatekeeper that decides what happens to each issue.

---

## Purpose

When an issue exists but hasn't been triaged, the `/triage` skill:
- Assesses the issue's nature
- Assigns appropriate labels
- Determines the workflow (simple fix vs RFC)
- Assigns to the right agent or queues for RFC

---

## When to Triage

| Trigger | Description |
|---------|-------------|
| Manual | Human says `/triage #42` or `/triage` (triages all open issues) |
| On creation | Automatically triggered after `/create-issue` |
| Scheduled | Agent runs triage on unstriaged issues weekly |

---

## Triage Process

```
/triage #42
    ↓
┌─────────────────────────────────────────────┐
│  1. Read the Issue                          │
│     - Title, description, comments           │
│     - Related RFCs if any                    │
│     - Previous similar issues                │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  2. Type Classification                       │
│     What kind of work is this?              │
│     → bug / feature / refactor / chore     │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  3. Size Estimation                          │
│     How long will this take?                │
│     → xs / s / m / l / xl                  │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  4. Priority Assessment                      │
│     How urgent is this?                     │
│     → p0 / p1 / p2 / p3                    │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  5. RFC Check                               │
│     Does this need an RFC?                  │
│     → rfc-needed / rfc-not-needed           │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  6. Action Recommendation                    │
│     What should happen next?                │
│     → Assign to Builder                     │
│     → Queue for RFC discussion              │
│     → Blocked by another issue              │
│     → Needs more information                │
└─────────────────────────────────────────────┘
```

---

## Type Classification

### Decision Tree

```
Is this a bug?
    YES → Type = bug
    NO ↓

Is this about existing code changing behavior?
    YES → Type = refactor
    NO ↓

Is this about adding something new?
    YES → Type = feature
    NO ↓

Is this about documentation?
    YES → Type = docs
    NO ↓

Is this about tests only?
    YES → Type = test
    NO ↓

Is this about tooling, CI, infrastructure?
    YES → Type = chore
    NO ↓

Is this about learning/investigating?
    YES → Type = research
    NO ↓

→ Default = chore (if unclear)
```

---

## Size Estimation

### Guidelines

| Size | Time | Criteria |
|------|------|----------|
| **xs** | < 1 hour | Typo fix, simple config change, 1-line fix |
| **s** | 1-4 hours | Small bug fix, simple feature addition |
| **m** | Half to full day | Standard bug fix, small feature |
| **l** | 1-3 days | Complex feature, multi-file changes |
| **xl** | Week+ | Large feature, major refactor, architectural change |

### Size Estimation Factors

- Number of files affected
- Complexity of the change
- Testing requirements
- Documentation needs
- Coordination with other changes

---

## Priority Assessment

### Based on Value vs Effort

| | Low Effort | High Effort |
|--|-----------|-------------|
| **High Value** | p1 (Quick wins) | p1 or p2 (Depends on scope) |
| **Low Value** | p3 (When convenient) | p3 or backlog |

### Priority Factors

- **Business impact**: How does this affect the user?
- **Blockers**: Does this block other work?
- **Tech debt**: Does this reduce future complexity?
- **Risk**: Does this fix a security or stability issue?

---

## RFC Decision

### RFC is Likely Needed When

- Size = xl (week+)
- Type = feature AND complexity = high
- Changes architectural decisions
- Constrains future development choices
- Multiple valid approaches exist

### RFC is NOT Needed When

- Size = xs, s, or m
- Type = bug (unless architectural)
- Clear, single solution
- Well-established pattern exists in codebase

---

## Action Recommendations

### After triage, the skill recommends ONE of:

| Action | When |
|--------|------|
| **Assign to Builder** | Ready for implementation, size ≤ m, no RFC needed |
| **Start RFC** | RFC-worthy, needs structured design first |
| **Needs more info** | Issue is unclear, request clarification |
| **Blocked** | Depends on another issue being resolved |
| **Duplicate** | Same as existing issue, suggest linking |
| **Wontfix** | Not worth doing, explain why |
| **Backlog** | Valid but not urgent, deprioritize |

---

## Triage Output

When `/triage` completes, it updates the issue:

```markdown
## Triage Summary

| Field | Value |
|-------|-------|
| Type | feature |
| Size | l |
| Priority | p2 |
| RFC Needed | yes |
| Confidence | high |

## Recommendation

This feature requires an RFC due to its architectural impact. The issue has multiple valid design approaches and will benefit from structured discussion.

## Next Steps

1. Create RFC draft using /create-rfc
2. Discuss design with Archi agent
3. Approval from Human
4. Implementation by Builder
```

---

## Triage Labels Applied

| Label | Applied When |
|-------|--------------|
| `type/bug` | Bug report |
| `type/feature` | New feature |
| `type/refactor` | Refactoring |
| `type/chore` | Maintenance |
| `type/docs` | Documentation |
| `type/test` | Tests |
| `type/research` | Investigation |
| `size/xs` through `size/xl` | Estimated size |
| `priority/p0` through `priority/p3` | Priority |
| `rfc-needed` | Needs RFC |
| `status/triaged` | Triage complete |
| `status/blocked` | Blocked by another issue |

---

## Skill Usage

### `/triage`

Triages the current conversation's issue context (if discussed recently).

### `/triage #42`

Triages specific issue #42.

### `/triage --all`

Triages all open issues without a `status/triaged` label.

### `/triage --force`

Re-triages an already-triaged issue (in case circumstances changed).

---

## Triage Confidence

The skill should report its confidence level:

| Confidence | Meaning |
|------------|---------|
| **High** | Clear issue, confident in assessment |
| **Medium** | Some ambiguity, recommend human review |
| **Low** | Unclear issue, needs human input |

---

## Open Questions

1. **Should triage run automatically on issue creation?** Or only when triggered?

2. **Should triage ever auto-assign?** Or always recommend and let human confirm?

3. **How to handle triage conflicts?** (e.g., Agent says p2, Human disagrees)

4. **Triage retention** — Should triage be re-done periodically? (e.g., annually)

5. **Batch triage** — `/triage --all` could take a long time. Progress indicators?

---

## Integration with Other Skills

| Skill | Integration |
|-------|-------------|
| `/create-issue` | Calls `/triage` after creating issue |
| `/create-rfc` | Linked when `rfc-needed` is set |
| `/implement` | Reads triage info before starting work |
| `/report-bug` | Bug-specific triage (severity assessment) |

---

## Related Documents

- `docs/reports/issue-rfc-process-design-2026-04-28.md` — General process
- `docs/reports/bug-report-process-design-2026-04-28.md` — Bug-specific triage
- `docs/reports/init-labels-skill-design-2026-04-28.md` — Labels used

---

*This document is a design for a future skill. The skill itself has not been created yet.*
