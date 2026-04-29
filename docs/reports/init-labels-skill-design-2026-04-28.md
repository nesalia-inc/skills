# Init Labels Skill Design Report

**Date**: 2026-04-28
**Status**: Draft
**Purpose**: Document the design for a skill that initializes GitHub issue labels in a new repository

---

## Purpose

When Marty starts working on a new repository, we need consistent labels across all repos. A `/init-labels` skill will set up the standard label system automatically.

---

## Why This Matters

Different repos currently use different label schemes:
- Some use `bug`, `enhancement`, `question`
- Others use `feature`, `fix`, `docs`
- No consistency across projects

A standard label system enables:
- **Filtering**: Find all bugs, all features easily
- **Automation**: GitHub Actions can trigger on label changes
- **Search**: `is:issue label:bug` works across repos
- **Reporting**: Track bug vs feature ratios

---

## Label Structure

### Type Labels

| Label | Color | Description |
|-------|-------|-------------|
| `bug` | Red (#d73a4a) | Bug fix |
| `feature` | Green (#008672) | New feature |
| `refactor` | Purple (#8b5cf6) | Code refactoring |
| `chore` | Gray (#6b7280) | Maintenance, tooling |
| `docs` | Blue (#2563eb) | Documentation only |
| `test` | Yellow (#f59e0b) | Tests only |
| `research` | Cyan (#06b6d4) | Investigation, spikes |

### Priority Labels

| Label | Color | Description |
|-------|-------|-------------|
| `priority/p0` | Red (#dc2626) | Critical - blocks everything |
| `priority/p1` | Orange (#ea580c) | High - important for current milestone |
| `priority/p2` | Yellow (#ca8a04) | Medium - planned |
| `priority/p3` | Gray (#9ca3af) | Low - nice to have |

### Status Labels

| Label | Color | Description |
|-------|-------|-------------|
| `status/triaged` | Blue (#3b82f6) | Issue has been triaged |
| `status/in-progress` | Purple (#9333ea) | Someone is working on it |
| `status/blocked` | Red (#ef4444) | Blocked by another issue |
| `status/in-review` | Yellow (#eab308) | PR is under review |
| `status/done` | Green (#22c55e) | Completed |

### RFC Labels

| Label | Color | Description |
|-------|-------|-------------|
| `rfc-needed` | Orange (#f97316) | Needs RFC before implementation |
| `rfc/draft` | Yellow (#eab308) | RFC in progress |
| `rfc/approved` | Green (#22c55e) | RFC approved |
| `rfc/rejected` | Red (#ef4444) | RFC rejected |

### Size Labels

| Label | Color | Description |
|-------|-------|-------------|
| `size/xs` | Gray (#6b7280) | Less than 1 hour |
| `size/s` | Gray (#6b7280) | 1-4 hours |
| `size/m` | Gray (#6b7280) | Half to full day |
| `size/l` | Gray (#6b7280) | 1-3 days |
| `size/xl` | Gray (#6b7280) | Week or more |

---

## What the Skill Does

### `/init-labels`

**Trigger**: `/init-labels` or `/init-labels [repo-owner/repo-name]`

**Actions**:
1. Creates all labels defined above in the repository
2. Sets correct colors (following GitHub conventional colors)
3. Creates a description for each label
4. Optionally removes any existing non-standard labels

**Scope**: Can work on:
- Current repo (default)
- Any repo specified: `/init-labels owner/repo`

---

## Implementation Approach

### GitHub API

The skill will use the GitHub API to create labels:

```
POST /repos/{owner}/{repo}/labels
{
  "name": "bug",
  "color": "d73a4a",
  "description": "Bug fix"
}
```

### Tools Needed

- `Bash` (for gh CLI or curl)
- `gh` CLI authenticated
- `Read` (to verify current labels)
- `Grep` (to check repo context)

### Dry Run Mode

Should support `--dry-run` to preview what would be created without making changes.

---

## Label Creation Order

1. Type labels first (bug, feature, etc.)
2. Priority labels
3. Status labels
4. RFC labels
5. Size labels

---

## What NOT to Do

- Do NOT delete existing labels automatically (too risky)
- Do NOT override colors if label already exists (unless --force)
- Do NOT create labels in repos without confirmation

---

## Open Questions

1. **Should the skill work on any GitHub repo or just repos where Marty is a collaborator?** Need proper permissions.

2. **Should existing labels be migrated?** E.g., `enhancement` → `feature`?

3. **Should this be a one-time setup or configurable?** (e.g., `/init-labels --config minimal` for fewer labels)

4. **Should labels include descriptions in French or English?** (Recommendation: English for consistency)

---

## Skill Skeleton

```markdown
---
name: init-labels
description: Initialize standard GitHub issue labels in a repository
---

# Init Labels Skill

## Purpose
Create standard labels for issue tracking.

## Usage
/init-labels [repo] [--dry-run] [--force]

## Labels Created
[List of all labels with colors and descriptions]
```

---

## Related Skills

- `/create-issue` — Uses these labels
- `/triage` — Updates status labels
- `/create-rfc` — Uses RFC labels

---

*This document is a design for a future skill. The skill itself has not been created yet.*
