---
name: context-check
description: Validate context alignment with reality. Detect stale documents, structure drift, and report blockers status.
---

# Context Check

Validate that the `.context/` files align with the actual project state.

## What is the Project Context System?

The **Project Context System** solves a fundamental problem: when an AI agent starts a conversation, it has no context about the project. It must guess the domain, rebuild structure mentally, and rediscover conventions.

The system defines context in `.context/` files that the agent reads at startup. But if these files become stale or misaligned with reality, the agent works with wrong information.

This skill ensures the context remains accurate.

### How It Fits In

```
project-context (core skill)
    │
    └── Uses: context-check (stale detection, structure validation)

Skills workflow:
  /project-context → loads context, triggers context-check
  /context-check → validates alignment, reports issues
  /context-update → fixes issues found by context-check
```

## When to Run

| Trigger | When |
|---------|------|
| **Session start** | Check staleness before agent works |
| **On demand** | User asks "check context" or "validate context" |
| **Pre-commit** | Verify status before committing |
| **After major changes** | Architecture or stack modifications |

## Checks Performed

### 1. Stale Detection

Check `updated` frontmatter against threshold:

| Document Type | Stale After |
|---------------|-------------|
| Status docs | 14 days |
| Definition docs | 90 days |

Alert format:
> "I notice `[[status/current.md]]` was updated 20 days ago. It's stale. Should I sync it?"

### 2. Structure Drift

Compare actual project structure with `[[project/definition/structure.md]]`:

```bash
tree -I "node_modules|dist|.git|build" -L 3
```

Report discrepancies like:
- Directories in reality but not in `structure.md`
- Directories documented but no longer exist
- New top-level folders not accounted for

### 3. Git History vs recent.md

Compare recent commits with `[[project/status/recent.md]]`:
- What was merged that isn't documented?
- What changed since the last documented update?

### 4. Blocker Status Check

Read `[[project/status/active-issues.md]]` and report:
- Number of active blockers
- Any that have been resolved but still listed

## Output Format

Give a clear, concise report:

```
Context Check Results:
┌─────────────────────────────────────────────────────┐
│ Status:                                            │
│   - recent.md: up-to-date (2 days ago)             │
│   - current.md: STALE (18 days ago) ← needs sync   │
│   - active-issues.md: up-to-date                   │
│                                                     │
│ Structure: aligned (no drift detected)             │
│                                                     │
│ Recent Changes: synced with git history            │
│                                                     │
│ Blockers: 2 active                                 │
│   - Auth module: waiting for API keys              │
│   - DB migration: blocked by backup                │
└─────────────────────────────────────────────────────┘
```

## On Stale Detection

When documents are stale, offer to sync:

| File | Action |
|------|--------|
| `status/current.md` | "I notice it's 20 days old. Should I analyze recent commits to update the sprint/phase info?" |
| `status/recent.md` | "Recent changes aren't documented. Want me to check git log and update?" |
| `status/active-issues.md` | "Blockers list might be outdated. Should I review and update?" |

Never update automatically. Offer and let user confirm.

## What to Check vs What to Fix

| This Skill Does | Other Skills Do |
|-----------------|-----------------|
| Detect stale files | `context-update` fixes them |
| Report structure drift | `context-update` updates structure.md |
| Verify git history sync | `context-update` updates recent.md |
| List blockers | User resolves blockers |

This skill only validates and reports. It does not modify files.

## Real Dates

Always use system date (from OS), not training data cutoff:
- Staleness calculated from real current date
- File ages computed from real system time