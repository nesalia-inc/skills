# Branch Cleanup Skill Design Report

**Date**: 2026-04-28
**Status**: Draft

---

## Executive Summary

The `/branch-cleanup` skill manages Git branches: deletes merged branches, syncs stale branches with main, and keeps the repository tidy.

---

## Purpose

Branches accumulate over time:
- **Merged branches** — Should be deleted
- **Outdated branches** — Need rebase/sync
- **Abandoned branches** — Never merged, never updated
- **Duplicate branches** — Multiple for same feature

`/branch-cleanup` automates branch hygiene.

---

## When to Trigger

| Trigger | Description |
|---------|-------------|
| Manual | Human says `/branch-cleanup` |
| On merge | After PR merges, cleanup source branch |
| On PR close | When PR is closed (not merged) |
| Scheduled | Weekly branch hygiene |

---

## Cleanup Process

```
/branch-cleanup
    ↓
┌─────────────────────────────────────────────┐
│  1. Fetch All Branches                      │
│     - Local branches                        │
│     - Remote branches                       │
│     - Compare with main                     │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  2. Categorize Branches                    │
│     → Merged (safe to delete)              │
│     → Outdated (behind main)               │
│     → Stale (no activity)                  │
│     → Abandoned (never merged, old)        │
│     → Protected (never delete)            │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  3. Apply Cleanup                          │
│     → Delete merged branches                │
│     → Notify about outdated                 │
│     → Archive abandoned                    │
│     → Skip protected                       │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  4. Sync Protected Branches                │
│     → Rebase/sync with main                │
│     → Notify owners                        │
└─────────────────────────────────────────────┘
```

---

## Branch Categories

### 1. Merged Branches (Safe to Delete)

```markdown
## Merged Branches: 12

| Branch | Merged By | Date | Safe to Delete |
|--------|-----------|------|----------------|
| feature/login | #123 | 2d ago | ✅ |
| fix/typo | #124 | 5d ago | ✅ |
| refactor/auth | #125 | 1d ago | ✅ |
| feature/dark-mode | #127 | 3d ago | ✅ |

### Auto-Delete Policy
Delete branches merged to main if:
- Merged more than 24 hours ago
- No unmerged PRs targeting this branch
- Branch name doesn't match protected pattern
```

### 2. Outdated Branches (Need Sync)

```markdown
## Outdated Branches: 5

| Branch | Behind | Ahead | Last Update | Action |
|--------|--------|-------|-------------|---------|
| feature/oauth | 12 commits | 3 commits | 7d ago | Rebase |
| feature/new-api | 45 commits | 1 commit | 14d ago | Rebase |
| feature/reports | 3 commits | 0 commits | 3d ago | Merge main |

### Sync Instructions
```bash
# Rebase feature/oauth
git checkout feature/oauth
git fetch origin
git rebase origin/main

# Force push (if needed)
git push --force-with-lease
```
```

### 3. Stale Branches (No Activity)

```markdown
## Stale Branches: 3

| Branch | Last Commit | Days Idle | Owner | Status |
|--------|-------------|-----------|-------|--------|
| feature/old-login | 2026-03-01 | 58d | @user | ⚠️ Needs attention |
| feature/deprecated-ui | 2026-02-15 | 73d | @user | ⚠️ Needs attention |
| junk/debug-stuff | 2026-01-10 | 108d | @user | 🔴 Abandoned |

### Stale Definition
- No commits in 30+ days
- Or: PR closed without merge
```

### 4. Protected Branches (Never Delete)

```markdown
## Protected Branches: 3

These branches will NEVER be deleted:

| Branch | Reason |
|--------|--------|
| main | Default branch |
| master | Legacy default |
| develop | Development branch |
| release/* | Release branches |
| hotfix/* | Hotfix branches |
```

---

## Deletion Safety

### Safe to Delete Checklist

```
Before deleting a branch:
    ↓
Is branch merged to main?
    ↓ NO → STOP, don't delete
    ↓ YES ↓
Is branch older than 24h since merge?
    ↓ NO → Wait
    ↓ YES ↓
Does branch have unmerged PRs?
    ↓ YES → STOP, don't delete
    ↓ NO ↓
Is branch protected?
    ↓ YES → STOP, don't delete
    ↓ NO ↓
→ Safe to delete
```

### Protected Branch Patterns

```yaml
protected_patterns:
  - main
  - master
  - develop
  - release/*
  - hotfix/*
  - stable/*
  - legacy/*
```

### Force Delete

```bash
/branch-cleanup --force
```

Force delete even if checks fail (DANGEROUS).

---

## Notification Strategy

### Before Deletion

```markdown
## Branch Deletion Notice

@user,

The following branches will be deleted in 48 hours:

- feature/login (#123 merged)
- fix/typo (#124 merged)

If you want to keep any of these branches, comment on this PR or run:
/branch-cleanup --preserve branch-name

After 48 hours, these branches will be automatically deleted.
```

### After Deletion

```markdown
## Branches Deleted

The following branches were deleted:

- feature/login (merged in #123)
- fix/typo (merged in #124)
- refactor/auth (merged in #125)

Total deleted: 3
```

---

## Main Sync

### When Branches Drift

When `main` has new commits, dependent branches become outdated.

```
/branch-cleanup --sync
    ↓
For each non-main branch:
    ↓
Is branch behind main?
    YES ↓
Notify branch owner
    ↓
Suggest rebase or merge
    ↓
Offer to auto-sync if simple
```

### Auto-Sync Options

```bash
# Sync all outdated branches
/branch-cleanup --sync --auto

# Sync specific branch
/branch-cleanup --sync feature/oauth

# Sync with rebase (preferred)
/branch-cleanup --sync --rebase

# Sync with merge (if rebasing is complex)
/branch-cleanup --sync --merge
```

---

## Abandoned Branch Handling

### What Makes a Branch "Abandoned"

| Condition | Meaning |
|-----------|---------|
| 90+ days old | Very old |
| No commits in 60+ days | Unmaintained |
| PR closed without merge | Rejected or abandoned |
| No associated issue | Orphaned work |

### Handling Strategy

```markdown
## Abandoned Branches: 3

### Archive Instead of Delete
These branches will be archived (not deleted):

| Branch | Archived As | Reason |
|--------|-------------|---------|
| feature/old-login | archived/feature-old-login | 90d old |
| feature/deprecated-ui | archived/feature-deprecated-ui | Rejected PR |

### Permanent Deletion Candidates
These branches should be permanently deleted:

| Branch | Reason |
|--------|--------|
| junk/debug-stuff | Clearly garbage |
| temp/test-branch | Obviously temporary |
| experiment/throwaway | Clearly experimental |
```

### Archive Process

```bash
# Archive instead of delete
git checkout main
git branch -m feature/old-login archived/feature-old-login
git push origin archived/feature-old-login
git push origin --delete feature/old-login
```

---

## Branch Report

```markdown
## Branch Cleanup Report - 2026-04-28

### Summary
| Action | Count |
|--------|-------|
| Deleted (merged) | 12 |
| Archived (abandoned) | 5 |
| Synced (rebased) | 3 |
| Notified (outdated) | 8 |
| Protected (skipped) | 6 |

### Repository Health
| Metric | Value |
|--------|-------|
| Total branches | 34 |
| Active branches | 14 |
| Stale branches | 6 |
| Protected | 6 |
| Last cleanup | 7d ago |

### Recommendations
1. Auto-delete merged branches (enable in settings)
2. Set 30-day stale threshold
3. Archive abandoned branches monthly

### Estimated Savings
- Storage: ~50MB freed
- Confusion: Clear which branches matter
```

---

## Skill Usage

### `/branch-cleanup`

Full branch cleanup.

### `/branch-cleanup --dry-run`

Preview what would be deleted.

### `/branch-cleanup --merged`

Delete only merged branches.

### `/branch-cleanup --sync`

Sync outdated branches with main.

### `/branch-cleanup --stale`

Handle stale branches only.

### `/branch-cleanup --archive`

Archive abandoned branches instead of deleting.

### `/branch-cleanup --force`

Force delete without safety checks.

### `/branch-cleanup --preserve branch-name`

Preserve a specific branch from deletion.

---

## Integration with Other Skills

| Skill | Integration |
|-------|-------------|
| `/pr-merge` | Triggers cleanup after merge |
| `/stale` | Shares stale detection logic |
| `/pr-creation` | Creates branches with clean names |

---

## Configuration

### Config File

```yaml
# .claude/branch-cleanup.yml

auto_delete:
  enabled: true
  delay_hours: 24  # Wait before deleting

protection:
  patterns:
    - main
    - master
    - develop
    - release/*
    - hotfix/*

stale:
  threshold_days: 30
  archive_instead_of_delete: true

notifications:
  before_delete: true
  delay_hours: 48
  notify_owner: true

sync:
  auto_sync: false
  notify_outdated: true
```

---

## Open Questions

1. **Auto-delete merged** — Enable by default? Or manual only?

2. **Archive vs delete** — Always archive abandoned? Or some get deleted?

3. **Owner notification** — How to determine branch owner? (Last committer? PR assignee?)

4. **Force vs safe** — Default to safe mode? Only force with explicit flag?

5. **Remote vs local** — Clean both? Or just remote where CI/CD runs?

---

## Related Documents

- `docs/reports/stale-skill-design-2026-04-28.md` — General stale handling
- `docs/reports/pr-update-skill-design-2026-04-28.md` — Branch sync during PR updates

---

*This document is a design for a future skill. The skill itself has not been created yet.*
