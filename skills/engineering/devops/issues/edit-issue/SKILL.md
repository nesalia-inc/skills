---
name: edit-issue
description: Edit GitHub issues following Marty conventions. Use this skill when the user asks to edit an issue, update an issue, change issue title, modify issue body, add or remove labels, reassign issues, or change milestone. Also use it when the user says things like "update the issue", "change the assignee", "add a label to issue", or "close this issue".
disable-model-invocation: false
context: fork
agent: general-purpose
permissionMode: bypassPermissions
allowed-tools: Bash
---

# Edit Issue Guide (Non-Interactive)

Follow this guide to edit GitHub issues using `gh issue edit`. All operations are command-line only — no interactive editors.

## Marty Issue Title Format

All issue titles MUST use the format: `[<Type>]: <Title>`

See [labels.md](../create-issue/labels.md) for the complete list of all Marty labels.

## Edit Commands

### Edit Title

```bash
gh issue edit 23 --title "[bug]: login fails on mobile"
```

### Edit Body

```bash
gh issue edit 23 --body "Updated description"

# From file
gh issue edit 23 --body-file "body.md"
```

### Add Labels

```bash
# Add single label
gh issue edit 23 --add-label "priority/p1"

# Add multiple labels
gh issue edit 23 --add-label "bug,priority/p1"

# Add and remove in same command
gh issue edit 23 --add-label "status/in-progress" --remove-label "status/triaged"
```

### Remove Labels

```bash
# Remove single label
gh issue edit 23 --remove-label "help wanted"

# Remove multiple labels
gh issue edit 23 --remove-label "bug,wontfix"
```

### Add Assignees

```bash
# Self-assign
gh issue edit 23 --add-assignee "@me"

# Assign Copilot
gh issue edit 23 --add-assignee "@copilot"

# Assign multiple people
gh issue edit 23 --add-assignee "username1,username2"
```

### Remove Assignees

```bash
# Unassign self
gh issue edit 23 --remove-assignee "@me"

# Remove multiple assignees
gh issue edit 23 --remove-assignee "username1,username2"
```

### Edit Milestone

```bash
# Set milestone
gh issue edit 23 --milestone "v1.0"

# Remove milestone
gh issue edit 23 --remove-milestone
```

### Add/Remove Projects

```bash
# Add to project
gh issue edit 23 --add-project "Roadmap"

# Remove from project
gh issue edit 23 --remove-project "Roadmap"
```

### Multiple Edits

```bash
# Multiple changes at once
gh issue edit 23 --title "[bug]: login fixed" --add-label "status/done" --remove-label "status/in-progress"
```

## Common Workflows

### Mark Issue as In Progress

```bash
gh issue edit 23 --add-label "status/in-progress" --add-assignee "@me"
```

### Mark Issue as Done

```bash
gh issue edit 23 --remove-label "status/in-progress" --add-label "status/done"
```

### Change Priority

```bash
gh issue edit 23 --add-label "priority/p0" --remove-label "priority/p2"
```

### Reassign Issue

```bash
gh issue edit 23 --add-assignee "newowner" --remove-assignee "oldowner"
```

### Update Status

```bash
# Triaged -> In Progress
gh issue edit 23 --remove-label "status/triaged" --add-label "status/in-progress"

# In Progress -> In Review
gh issue edit 23 --remove-label "status/in-progress" --add-label "status/in-review"
```

## Quick Reference

```
Edit title:       gh issue edit 23 --title "[type]: title"
Edit body:        gh issue edit 23 --body "body"
Add label:        gh issue edit 23 --add-label "label"
Remove label:     gh issue edit 23 --remove-label "label"
Self-assign:      gh issue edit 23 --add-assignee "@me"
Add milestone:    gh issue edit 23 --milestone "v1.0"
Remove milestone:  gh issue edit 23 --remove-milestone
Multiple edits:   gh issue edit 23 --title "..." --add-label "..." --remove-label "..."
```

## Related Skills

| Skill | When to Use |
|-------|-------------|
| `/create-issue` | Create new issues |
| `/close-issue` | Close issues |