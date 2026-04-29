---
name: merge-pr
description: Merge GitHub pull requests following Marty conventions. Use this skill when the user asks to merge a PR, merge a pull request, complete a merge, or merge to main or canary. Also use when the user says things like "merge this PR", "I want to merge my changes", "complete the merge", or "squash and merge".
disable-model-invocation: false
context: fork
agent: general-purpose
permissionMode: bypassPermissions
allowed-tools: Bash
---

# Merge PR Guide (Non-Interactive)

Follow this guide to merge GitHub pull requests using `gh pr merge`. All operations are command-line only — no interactive editors.

## Merge Commands

### Squash Merge (Preferred)

```bash
# Squash and merge to canary
gh pr merge 23 --squash --delete-branch

# With commit message
gh pr merge 23 --squash --delete-branch --subject "feat(skills): add new feature"
```

### Merge Commit

```bash
# Regular merge commit
gh pr merge 23 --admin --delete-branch
```

### Rebase and Merge

```bash
# Rebase onto base branch
gh pr merge 23 --rebase --delete-branch
```

## Merge Options

```bash
# Squash merge (recommended)
gh pr merge 23 --squash --delete-branch

# With custom subject
gh pr merge 23 --squash --delete-branch --subject "feat(scope): description"

# Auto-delete branch after merge
gh pr merge 23 --squash --delete-branch

# No delete branch
gh pr merge 23 --squash
```

## Merge Strategy

| Strategy | When to Use | Preserves History |
|----------|-------------|-------------------|
| **Squash** | Most PRs | No - single commit |
| **Merge commit** | Collaborative PRs | Yes - full history |
| **Rebase** | Clean linear history | Yes - individual commits |

**Marty prefers squash merge** for cleaner history on `main` and `canary`.

## Before Merging

Verify the PR is ready:

```bash
# Check PR status
gh pr view 23

# Check if CI is passing
gh pr check 23

# View diff
gh pr diff 23
```

## Common Workflows

### Standard Squash Merge

```bash
# Verify CI passes first
gh pr check 23

# If all checks pass, merge
gh pr merge 23 --squash --delete-branch
```

### Merge and Set Commit Message

```bash
# Use PR title as commit subject (default)
gh pr merge 23 --squash --delete-branch

# Or provide custom message
gh pr merge 23 --squash --delete-branch --subject "feat(skills): add feature

Detailed description of what was done.

Co-Authored-By: martyy-code <nesalia.inc@gmail.com>"
```

### Merge Canary to Main

```bash
# From main branch
git checkout main && git pull
git merge origin/canary
git push
```

## Merge to Main Only

After verifying canary is stable, merge to main:

```bash
# Merge canary to main (squash)
gh pr merge 23 --squash --delete-branch
# Where PR 23 is canary -> main
```

## Quick Reference

```
Squash merge:           gh pr merge 23 --squash --delete-branch
Merge commit:           gh pr merge 23 --admin --delete-branch
Rebase merge:           gh pr merge 23 --rebase --delete-branch
Custom subject:         gh pr merge 23 --squash --delete-branch --subject "..."
Check PR:               gh pr view 23
Check CI:               gh pr check 23
```

## Common Mistakes to Avoid

1. **Merge without CI passing**: Always verify tests pass first
2. **Skip canary verification**: Don't merge to main without testing on canary
3. **Force push after merge**: Never force push main or canary
4. **Merge conflicts unresolved**: Always resolve conflicts before merging

## Related Skills

| Skill | When to Use |
|-------|-------------|
| `/create-pr` | Create PR before merging |
| `/close-pr` | Close without merging |
| `/sync` | Sync branches before merge |
| `/setup-canary` | Canary workflow details |