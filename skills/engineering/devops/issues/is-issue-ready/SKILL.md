---
name: is-issue-ready
description: Check if a GitHub issue is ready to be worked on. Verifies that the issue has the required triage labels. Use this before starting work on any issue. Example: "/is-issue-ready 45"
disable-model-invocation: false
context: fork
agent: general-purpose
permissionMode: bypassPermissions
allowed-tools: Bash, Read
---

# Is Issue Ready Guide

Checks if a GitHub issue is ready to be worked on. This is a **deterministic check** - no judgment, just verification of labels.

## Check Process

### Step 1: Get Issue Labels

```bash
gh issue view <number> --json number,title,labels
```

### Step 2: Verify Required Labels

The issue is **READY** if ALL of:
- ✅ Has `status/triaged` label

The issue is **NOT READY** if ANY of:
- ❌ Missing `status/triaged` label
- ❌ Has `status/blocked` label
- ❌ Has `rfc-needed` label
- ❌ Has `status/in-progress` label (someone else is working on it)
- ❌ Has `status/done` label (already completed)
- ❌ Has `status/duplicate` label (closed as duplicate)

## Output

### If READY

```
/is-issue-ready 45

Issue #45: "Login fails with special characters"
Labels: [bug, priority/p1, effort/s, area/auth, status/triaged]

✓ Issue is ready to be worked on.
```

### If NOT READY - Missing triage

```
/is-issue-ready 45

Issue #45: "Login fails with special characters"
Labels: [bug, priority/p1]

✗ Issue is NOT ready.

Missing required label:
- status/triaged

Run /triage 45 to triage this issue.
```

### If NOT READY - Blocked

```
/is-issue-ready 45

Issue #45: "Login fails with special characters"
Labels: [bug, priority/p1, status/triaged, status/blocked]

✗ Issue is blocked.

This issue cannot be worked on until the blocker is resolved.
```

### If NOT READY - RFC Needed

```
/is-issue-ready 45

Issue #45: "Complete auth system redesign"
Labels: [feature, priority/p2, effort/xl, rfc-needed]

✗ Issue requires RFC.

Effort is effort/xl - this issue needs planning before implementation.

Run /create-rfc 45 to create the RFC proposal.
```

### If NOT READY - In Progress

```
/is-issue-ready 45

Issue #45: "Login fails with special characters"
Labels: [bug, priority/p1, status/triaged, status/in-progress]

✗ Issue is already being worked on.

Assigned to: @username

Someone else is already working on this issue.
```

### If NOT READY - Already Done

```
/is-issue-ready 45

Issue #45: "Login fails with special characters"
Labels: [bug, priority/p1, status/done]

✗ Issue is already completed.

This issue has been closed as done.
```

## Quick Reference

```
/is-issue-ready <number>

✓ Ready:      status/triaged present, no blockers
✗ Not Ready:  Run /triage, /create-rfc, or wait
```

## Related Skills

| Skill | When to Use |
|-------|-------------|
| `/triage` | Issue needs triage (missing status/triaged) |
| `/create-rfc` | Issue has rfc-needed label |
| `/fix-issue` | After confirming issue is ready |
