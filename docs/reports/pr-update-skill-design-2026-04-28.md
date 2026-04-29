# PR Update Skill Design Report

**Date**: 2026-04-28
**Status**: Draft

---

## Executive Summary

The `/pr-update` skill updates existing Pull Requests when relevant things change: new commits pushed, issues referenced, reviews addressed, or when CI fails and fixes are needed.

---

## Purpose

PRs are living documents. `/pr-update` keeps them current:
- Updates description when context changes
- Syncs with linked issues/RFCs
- Notifies when review comments are addressed
- Triggers re-review when needed
- Handles merge conflicts

---

## When to Trigger

| Trigger | Description |
|---------|-------------|
| After new commits | Automatically when branch is updated |
| Manual | Human says `/pr-update` or `/pr-update #42` |
| On comment | When Human or agent responds to review |
| On CI failure | When CI checks fail |
| Before merge | Final check before merging |

---

## Update Process

```
/pr-update #42
    ↓
┌─────────────────────────────────────────────┐
│  1. Fetch PR Current State                   │
│     - Latest commit SHA                       │
│     - Open issues referenced                 │
│     - Pending review comments               │
│     - CI status                             │
│     - Changes since last update              │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  2. Detect What Changed                      │
│     - New commits pushed?                    │
│     - New files changed?                     │
│     - Issues referenced?                     │
│     - PR description outdated?              │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  3. Handle Conflicts                          │
│     - Detect if main has diverged            │
│     - Offer to rebase or merge              │
│     - Notify if manual resolution needed    │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  4. Update PR if Needed                      │
│     - Update description with new changes    │
│     - Add latest commit info                │
│     - Sync linked issues                    │
│     - Update labels if needed               │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  5. Trigger Review if Needed                 │
│     - New changes = re-request review        │
│     - Only comment = no re-review           │
│     - CI failures = notify                  │
└─────────────────────────────────────────────┘
```

---

## Update Scenarios

### Scenario 1: New Commits Pushed

```
New commits detected on branch
    ↓
Compare: What files changed? What was the commit message?
    ↓
PR description already includes these changes?
    → YES: No update needed
    → NO: Add new changes to description
    ↓
Re-request review from reviewers
    ↓
Trigger /pr-review if all checks pass
```

### Scenario 2: Review Comments Addressed

```
Reviewer left comments on PR
    ↓
Human or agent addresses comment
    ↓
Developer marks comment as "resolved"
    ↓
/pr-update detects resolved comments
    ↓
Notify reviewer that comments are addressed
    ↓
Re-request review if all comments resolved
```

### Scenario 3: CI Failure

```
CI checks fail on PR
    ↓
/pr-update detects failure
    ↓
Analyze failure:
    → Test failure → Notify author to fix tests
    → Lint failure → Auto-fix if possible
    → Build failure → Notify with error details
    ↓
If auto-fixable: Apply fix, push, re-run CI
If not: Notify Human/agent to fix manually
```

### Scenario 4: Merge Conflicts

```
PR has merge conflicts with main
    ↓
/pr-update detects conflict
    ↓
Attempt auto-rebase:
    → Success → Push rebased branch
    → Failure → Notify: "Manual resolution needed"
    ↓
Update PR status to: "Has merge conflicts"
```

### Scenario 5: RFC Updated

```
RFC-0042 was updated after PR was created
    ↓
/pr-update detects: "This PR implements RFC-0042"
    ↓
Check: Does PR still match RFC?
    → YES: No change needed
    → NO: Flag potential scope creep
    ↓
Notify if RFC requirements changed
```

---

## PR Update Types

### Automatic Updates

| Update Type | Trigger | Action |
|-------------|---------|--------|
| Commit sync | New commits pushed | Update description |
| Label sync | Labels changed in issue | Update PR labels |
| CI status | Checks pass/fail | Notify relevant people |
| Review status | Comments resolved | Re-request review if all resolved |

### Manual Updates

| Command | Action |
|---------|--------|
| `/pr-update #42 --rebase` | Force rebase with main |
| `/pr-update #42 --merge` | Merge main into branch |
| `/pr-update #42 --recheck` | Re-run all CI checks |
| `/pr-update #42 --notify` | Send update notification to reviewers |

---

## Conflict Resolution

### Automatic Resolution

If conflict is simple (no overlapping changes):

```
Conflict detected
    ↓
Fetch both branch and main
    ↓
Analyze conflict markers
    ↓
If resolvable by keeping branch version:
    → Auto-resolve
    → Push with force-with-lease
    ↓
Notify: "Auto-resolved conflicts"
```

### Manual Resolution

If conflict is complex:

```
Conflict requires human decision
    ↓
Notify: "Merge conflicts need manual resolution"
    ↓
Provide:
    - List of conflicting files
    - Diff of conflicts
    - Instructions for resolution
    ↓
Human resolves → /pr-update --conflict-resolved
```

---

## Update Notification

When to notify reviewers:

| Event | Notify? | How |
|-------|---------|-----|
| New commits pushed | Yes | GitHub auto-notifies |
| All comments resolved | Yes | "@reviewer all comments addressed" |
| CI failures | Yes | "@author CI is failing" |
| Conflicts resolved | Yes | "@reviewer conflicts resolved" |
| Ready for final review | Yes | Re-request review |

---

## PR Status Updates

| Status | Label | When |
|--------|-------|------|
| Awaiting review | `status/awaiting-review` | Initial state |
| Changes requested | `status/changes-requested` | Reviewer requested changes |
| Awaiting approval | `status/awaiting-approval` | All comments resolved |
| Ready to merge | `status/ready-to-merge` | Approved, CI passing |
| Has conflicts | `status/has-conflicts` | Merge conflicts detected |
| Blocked | `status/blocked` | Blocked by another PR |

---

## Skill Usage

### `/pr-update`

Update current PR (from current branch).

### `/pr-update #42`

Update specific PR #42.

### `/pr-update --rebase`

Rebase current PR against main.

### `/pr-update --merge`

Merge main into current PR branch.

### `/pr-update --recheck`

Re-run all CI checks.

### `/pr-update --notify`

Send review notification.

### `/pr-update --force`

Force update even if nothing changed (refresh metadata).

---

## Integration with Other Skills

| Skill | Integration |
|-------|-------------|
| `/implement` | Calls `/pr-update` after pushing commits |
| `/pr-review` | Triggered when PR is updated |
| `/pr-creation` | PR created by this skill |
| `/create-issue` | Linked to PR |

---

## Update Frequency

### Don't Update When
- PR is draft
- PR is WIP
- Nothing has changed
- CI is still running

### Update When
- New commits pushed
- Review comments resolved
- CI failed
- CI passed after failure
- RFC requirements changed
- Branch has conflicts

---

## Open Questions

1. **Update frequency** — How often to check for changes? On every push? On demand?

2. **Notify vs Auto-re-request** — When all comments resolved, auto re-request or wait for Human?

3. **Merge strategy** — Default to squash? Or follow project convention?

4. **Stale PRs** — What to do with PRs that haven't been updated in X days?

5. **Multi-PR dependencies** — How to handle when PR #42 depends on PR #41?

6. **Force push handling** — Detect and handle force pushes appropriately?

---

## Related Documents

- `docs/reports/pr-creation-skill-design-2026-04-28.md` — Creating PRs
- `docs/reports/pr-review-skill-design-2026-04-28.md` — Reviewing PRs
- `docs/reports/issue-rfc-process-design-2026-04-28.md` — General workflow

---

*This document is a design for a future skill. The skill itself has not been created yet.*
