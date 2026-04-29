# Stale Skill Design Report

**Date**: 2026-04-28
**Status**: Draft

---

## Executive Summary

The `/stale` skill detects and cleans up stale issues, PRs, and branches. It prevents clutter from accumulating and keeps the project healthy.

---

## Purpose

Over time, projects accumulate:
- **Stale issues** — Old bugs, features never implemented
- **Stale PRs** — Abandoned work, draft PRs never completed
- **Old branches** — Merged but not deleted
- **Outdated discussions** — Threads no longer relevant

`/stale` identifies and cleans these up.

---

## When to Trigger

| Trigger | Description |
|---------|-------------|
| Manual | Human says `/stale` |
| Scheduled | Weekly automatic stale check |
| On merge | When PR is merged, check for cleanup |

---

## Stale Detection Process

```
/stale
    ↓
┌─────────────────────────────────────────────┐
│  1. Scan for Stale Items                    │
│     Issues: No activity for X days          │
│     PRs: No activity for X days            │
│     Branches: Merged or outdated            │
│     Discussions: No response for X days     │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  2. Categorize Stale Items                  │
│     → Issues (closeable)                   │
│     → Issues (needs response)              │
│     → Draft PRs (abandoned)                │
│     → PRs (outdated, conflicts)            │
│     → Branches (merged, deletable)          │
│     → Branches (stale, not merged)          │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  3. Take Action                             │
│     → Close/Archive stale items             │
│     → Notify authors                       │
│     → Delete merged branches               │
│     → Escalate if needed                   │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  4. Report Summary                           │
│     What was cleaned up                    │
│     What needs human attention              │
└─────────────────────────────────────────────┘
```

---

## Stale Criteria

### Issues

| Criteria | Value | Action |
|----------|-------|--------|
| No activity | 60 days | Add "stale" label |
| No activity | 90 days | Close (if labeled stale) |
| Has `status/blocked` | 30 days | Escalate |
| Has `waiting-on-user` | 14 days | Close |

### Pull Requests

| Criteria | Value | Action |
|----------|-------|--------|
| Draft, no update | 14 days | Add draft-stale label |
| Draft, no update | 30 days | Close/Archive |
| Has conflicts | 7 days | Notify to resolve |
| Review requested, no update | 14 days | Add review-stale label |
| Approved, no merge | 7 days | Notify to merge |

### Branches

| Criteria | Value | Action |
|----------|-------|--------|
| Merged to main | Immediately | Delete after 24h |
| Target branch deleted | Immediately | Delete |
| No activity | 30 days | Notify owner |
| Outdated (behind main) | 7 days | Notify to update |

---

## Stale Workflow

### Stage 1: Mark as Stale

```
Issue/PR detected as stale
    ↓
Add "stale" label
Add comment: "This hasn't had activity in X days. Will close in Y days unless updated."
    ↓
Wait Y days
```

### Stage 2: Close if Still Stale

```
Still no activity after Y days
    ↓
Close issue/PR
Add comment: "Closing due to inactivity."
If issue: Add "closed/stale" label
If PR: Archive instead of close
```

### Stage 3: Exception Handling

```
Stale item has special labels
    ↓
Check: "protected" label?
→ YES: Don't close, notify instead

Check: "enhancement" or "feature"?
→ YES: Don't auto-close, escalate to human

Check: Has recent comment (not by bot)?
→ YES: Remove stale label, reset timer
```

---

## Branch Cleanup

### Automatically Deletable

```markdown
Branches to delete:

| Branch | Reason | Status |
|--------|--------|--------|
| feature/old-login | Merged 5d ago | Ready |
| fix/typo-patch | Merged 2d ago | Ready |
| junk/debug-branch | Never merged, empty | Ready |
```

### Requires Notification

```markdown
Branches requiring attention:

| Branch | Reason | Owner | Action Needed |
|--------|--------|-------|---------------|
| feature/big-refactor | No activity 45d | @user | Update or close |
| feature/never-finished | Draft, abandoned | @user | Close or complete |
```

### Deletion Safety

```
Will NOT delete:
- Protected branches
- Branches with unmerged PRs
- Branches with recent activity
- Branch name matches main|master|develop|hotfix/*
```

---

## Notification Strategy

### Who Gets Notified

| Item | Owner | All Collaborators | Nobody |
|------|-------|-------------------|--------|
| Own stale issue | ✅ | ❌ | ❌ |
| Own stale PR | ✅ | ❌ | ❌ |
| Any draft PR | ✅ | ❌ | ❌ |
| Unresolved conflicts | ❌ | ✅ | ❌ |
| Merged branch | ❌ | ❌ | ✅ (silent delete) |

### Notification Template

```markdown
## Stale Item: Issue #42 - "Add GitHub OAuth"

👋 Hi @user!

This issue hasn't had activity in 60 days and has been marked as stale.

**Options:**
1. **Update**: Add new information or context
2. **Close**: If no longer needed
3. **Request un-stale**: If still relevant, explain why

If no response in 14 days, this issue will be closed.

---
*This is an automated message from Marty.*
```

---

## Stale Report

```markdown
## Stale Report - 2026-04-28

### Issues
| Action | Count |
|--------|-------|
| Marked stale | 3 |
| Closed | 5 |
| Removed stale label | 2 |
| Escalated | 1 |

### Pull Requests
| Action | Count |
|--------|-------|
| Marked stale | 2 |
| Closed (draft) | 1 |
| Archived | 3 |
| Notified for conflicts | 4 |

### Branches
| Action | Count |
|--------|-------|
| Deleted (merged) | 12 |
| Deleted (abandoned) | 5 |
| Notified for update | 8 |

### Total Cleaned Up
- Issues: 5 closed
- PRs: 4 cleaned
- Branches: 17 deleted
- Total: 26 items

### Items Needing Attention
| Item | Reason | Link |
|------|--------|------|
| #42 | Enhancement, 90d stale | [View](...) |
| #67 | Has waiting-on-user label | [View](...) |
```

---

## Integration with GitHub Actions

### Stale Action Config

```yaml
# .github/workflows/stale.yml
name: 'Stale'

on:
  schedule:
    - cron: '0 9 * * 1'  # Weekly Monday 9am

jobs:
  stale:
    runs-on: ubuntu-latest
    permissions:
      issues: write
      pull-requests: write
    steps:
      - uses: actions/stale@v9
        with:
          days-before-stale: 60
          days-before-close: 30
          stale-label: stale
          close-issue-message: "Closing due to inactivity."
          # ... more config
```

### Why Both Skill + Action?

| | Action | Skill |
|--|--------|-------|
| Automated marking | ✅ Yes | ✅ Yes |
| Automated closing | ✅ Yes | ✅ Yes |
| Custom logic | ❌ Limited | ✅ Full |
| Branch cleanup | ❌ No | ✅ Yes |
| Custom notifications | ❌ No | ✅ Yes |
| Escalation | ❌ No | ✅ Yes |

**Recommendation**: Use GitHub's stale action for basic marking, `/stale` skill for advanced cleanup.

---

## Skill Usage

### `/stale`

Run full stale check and cleanup.

### `/stale --issues`

Focus on stale issues only.

### `/stale --prs`

Focus on stale PRs only.

### `/stale --branches`

Focus on branch cleanup only.

### `/stale --dry-run`

Preview what would be cleaned up.

### `/stale --aggressive`

Lower thresholds, more aggressive cleanup.

---

## Thresholds Configuration

```yaml
# Customizable via flags or config
thresholds:
  issue:
    mark_stale: 60  # days
    close_stale: 90  # days
  pr:
    mark_stale_draft: 14  # days
    close_draft: 30  # days
    notify_conflicts: 7  # days
  branch:
    delete_merged: true  # immediately after merge
    notify_stale: 30  # days
    delete_abandoned: 90  # days
```

---

## Open Questions

1. **Auto-close vs manual close** — Auto-close by default? Or only after notification?

2. **Protected labels** — Which labels prevent auto-close? (e.g., `p0`, `security`)

3. **Draft PRs vs real PRs** — Treat drafts differently? (More lenient)

4. **Branch deletion safety** — How to prevent accidental deletion? (At least 24h after merge)

5. **Notification fatigue** — Limit notifications per user per week?

---

## Integration with Other Skills

| Skill | Integration |
|-------|-------------|
| `/repo-init` | Sets up stale workflow |
| `/create-issue` | Adds stale workflow label |
| `/pr-creation` | Tracks draft PRs for stale |

---

## Related Documents

- `docs/reports/repo-init-skill-design-2026-04-28.md` — Repo setup (includes stale workflow)
- `docs/reports/ci-fix-monitor-skill-design-2026-04-28.md` — CI health

---

*This document is a design for a future skill. The skill itself has not been created yet.*
