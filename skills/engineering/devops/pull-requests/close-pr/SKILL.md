---
name: close-pr
description: Close GitHub pull requests following Marty conventions. Use this skill when the user asks to close a PR, close a pull request, cancel a PR, or abandon a pull request. Also use when the user says things like "close this PR", "cancel this pull request", or "I want to abandon this PR".
disable-model-invocation: false
context: fork
agent: general-purpose
permissionMode: bypassPermissions
allowed-tools: Bash
---

# Close PR Guide (Non-Interactive)

Follow this guide to close GitHub pull requests using `gh pr close`. All operations are command-line only — no interactive editors.

## Close Commands

### Close PR by Number

```bash
gh pr close 23
```

### Close PR by URL

```bash
gh pr close https://github.com/owner/repo/pull/23
```

### Close and Delete Branch

```bash
# Closes PR and deletes the head branch
gh pr close 23 --delete-branch
```

## When to Close PRs

| Scenario | Action |
|----------|--------|
| Changes no longer needed | Close PR |
| Duplicate of another PR | Close and reference original |
| Wontfix | Close with explanation |
| Merged (use merge-pr) | Use `/merge-pr` instead |

## Close Without Merging

```bash
# Close without deleting branch
gh pr close 23

# Close and delete branch
gh pr close 23 --delete-branch
```

## Close with Comment

```bash
# Add closing comment
gh pr close 23 --comment "Closing as duplicate of #45"
```

## Common Workflows

### Abandon Feature Branch PR

```bash
# Close PR and cleanup branch
gh pr close 23 --delete-branch
```

### Close Duplicate PR

```bash
# Close with reference
gh pr close 23 --comment "Duplicate of #45. Closing in favor of that one."
```

### Close Wontfix PR

```bash
# Explain why closing
gh pr close 23 --comment "Closing as wontfix - this approach doesn't align with our architecture."
```

## Quick Reference

```
Close PR:              gh pr close 23
Close and delete:      gh pr close 23 --delete-branch
Close with comment:    gh pr close 23 --comment "Reason"
Close by URL:           gh pr close https://github.com/owner/repo/pull/23
```

## Related Skills

| Skill | When to Use |
|-------|-------------|
| `/create-pr` | Create new PRs |
| `/merge-pr` | Merge PRs (if closing after merge) |
| `/update-pr` | Update PR before closing |