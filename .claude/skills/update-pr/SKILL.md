---
name: update-pr
description: Update GitHub pull requests following Marty conventions. Use this skill when the user asks to update a PR, modify PR description, change PR title, add reviewers to PR, add or remove labels, or update PR status.
disable-model-invocation: false
context: fork
agent: general-purpose
permissionMode: bypassPermissions
allowed-tools: Bash
---

# Update PR Guide (Non-Interactive)

Follow this guide to update GitHub pull requests using `gh pr edit`. All operations are command-line only — no interactive editors.

## PR Update Commands

### Update PR Title

```bash
gh pr edit 23 --title "[feat]: updated description"
```

### Update PR Body

```bash
# Set new body
gh pr edit 23 --body "New description"

# From file
gh pr edit 23 --body-file "body.md"
```

### Add Reviewers

```bash
# Add reviewer
gh pr edit 23 --add-reviewer username1

# Add multiple reviewers
gh pr edit 23 --add-reviewer username1,username2

# Self-assign
gh pr edit 23 --add-reviewer "@me"
```

### Remove Reviewers

```bash
gh pr edit 23 --remove-reviewer username1
```

### Add Labels

```bash
# Add single label
gh pr edit 23 --add-label "priority/p1"

# Add multiple labels
gh pr edit 23 --add-label "bug,priority/p1"
```

### Remove Labels

```bash
gh pr edit 23 --remove-label "wip"
```

### Add or Remove Milestone

```bash
# Set milestone
gh pr edit 23 --milestone "v1.0"

# Remove milestone
gh pr edit 23 --remove-milestone
```

### Add or Remove Project

```bash
# Add to project
gh pr edit 23 --add-project "Roadmap"

# Remove from project
gh pr edit 23 --remove-project "Roadmap"
```

### Update Reviewers and Labels Together

```bash
# Multiple updates in one command
gh pr edit 23 \
  --add-label "status/in-review" \
  --remove-label "status/in-progress" \
  --add-reviewer username1
```

## Common Workflows

### Mark PR as Ready for Review

```bash
# When WIP is done
gh pr edit 23 --remove-label "wip" --add-label "ready-for-review"
```

### Change PR Status

```bash
# Move to in-review
gh pr edit 23 --remove-label "status/in-progress" --add-label "status/in-review"

# Move to blocked
gh pr edit 23 --add-label "status/blocked"
```

### Add Reviewers After Creation

```bash
# Add team lead for review
gh pr edit 23 --add-reviewer team-lead
```

### Update Body with Test Results

```bash
gh pr edit 23 --body "## Summary

Description

## Test Plan
- [x] Unit tests pass
- [x] Integration tests pass
- [x] Manual verification completed

## Test Results
All tests passing. Ready for review.

Co-Authored-By: martyy-code <nesalia.inc@gmail.com>"
```

## Quick Reference

```
Update title:          gh pr edit 23 --title "[type]: new title"
Update body:           gh pr edit 23 --body "new body"
Add label:             gh pr edit 23 --add-label "label"
Remove label:          gh pr edit 23 --remove-label "label"
Add reviewer:          gh pr edit 23 --add-reviewer "@me"
Remove reviewer:        gh pr edit 23 --remove-reviewer user
Set milestone:         gh pr edit 23 --milestone "v1.0"
Remove milestone:      gh pr edit 23 --remove-milestone
Add project:           gh pr edit 23 --add-project "Roadmap"
```

## PR Number Discovery

```bash
# List open PRs for current branch
gh pr list --head feat/my-feature

# List your PRs
gh pr list --author "@me"

# View PR details
gh pr view 23
```

## Related Skills

| Skill | When to Use |
|-------|-------------|
| `/create-pr` | Create new PRs |
| `/close-pr` | Close PRs |
| `/merge-pr` | Merge PRs |
| `/review-pr` | Review PRs |