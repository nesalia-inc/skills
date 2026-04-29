---
name: bug-report
description: Guide the bug reporting process following Marty conventions. Use this skill when the user asks to create a bug report, file a bug, report an issue, or document a bug. Also use when the user says things like "I found a bug", "there is a problem", "something is not working", or "log a bug report".
disable-model-invocation: false
context: fork
agent: general-purpose
permissionMode: bypassPermissions
allowed-tools: Bash
---

# Bug Report Guide (Non-Interactive)

Follow this guide to create bug reports following Marty conventions. Use `/create-issue` with the bug template.

## Bug Report Process

### Step 1: Verify it's a Bug

Before creating a bug report:
- Confirm the issue is reproducible
- Check if it was already reported
- Verify expected behavior

### Step 2: Create Issue with Bug Template

```bash
gh issue create -t "[bug]: brief description" --body-file "templates/bug.md" -l bug
```

### Step 3: Add Priority Label

```bash
# Critical bug
gh issue edit <number> --add-label "priority/p0"

# High priority
gh issue edit <number> --add-label "priority/p1"
```

## Bug Report Template

```markdown
## Summary
[Brief description of the bug]

## Steps to Reproduce
1. [Step 1]
2. [Step 2]
3. [Step 3]

## Expected Behavior
[What should happen]

## Actual Behavior
[What actually happens]

## Environment
- OS: [OS version]
- Tool version: [version]

## Possible Cause
[Your guess at what might be causing this]

Co-Authored-By: martyy-code <nesalia.inc@gmail.com>
```

## Bug Severity Levels

| Severity | Label | Response Time |
|----------|-------|---------------|
| Critical | `priority/p0` | Immediate |
| High | `priority/p1` | Within 24h |
| Medium | `priority/p2` | Within a week |
| Low | `priority/p3` | When convenient |

## Quick Reference

```
Create bug issue:    gh issue create -t "[bug]: title" --body-file "templates/bug.md" -l bug
Set priority:        gh issue edit <num> --add-label "priority/p1"
Assign self:         gh issue edit <num> --add-assignee "@me"
Add to canary:       gh issue edit <num> --add-label "status/triaged"
```

## Related Skills

| Skill | When to Use |
|-------|-------------|
| `/create-issue` | Create the issue |
| `/edit-issue` | Update with more info |
| `/triage` | Triage and categorize |