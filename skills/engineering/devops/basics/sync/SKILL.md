---
name: sync
description: Guide the sync process following Marty conventions - fetch, rebase, merge, and keeping branches up to date with main. Make sure to use this skill whenever the user asks to sync, sync branch, update branch, merge main, rebase, pull changes, or keep branch up to date. Also use it when the user says things like "sync with main", "update my branch", "rebase on main", or "merge main into my branch".
disable-model-invocation: false
context: fork
agent: general-purpose
permissionMode: bypassPermissions
allowed-tools: Bash
---

# Sync Process Guide (Non-Interactive)

Follow this guide to sync branches with main following Marty's workflow. All operations are command-line only — no interactive editors.

## Sync Strategy

Choose the sync method based on your situation:

| Situation | Method |
|-----------|--------|
| Keeping feature branch up to date | Rebase (`git rebase origin/main`) |
| Bringing main into feature branch | Merge (`git merge origin/main`) |
| Quick update for trivial changes | Pull (`git pull`) |
| Cleaning up local branches | Prune |

## Fetch Latest Changes

Always start by fetching the latest from remote:

```bash
# Fetch all remotes
git fetch --all

# Or fetch just origin
git fetch origin
```

## Rebase (Preferred for Feature Branches)

Rebase rewrites your commits on top of latest main. Creates cleaner history.

```bash
# Rebase current branch onto origin/main
git rebase origin/main

# Rebase with autosquash for fixup commits
git rebase -i --autosquash origin/main
```

### Handling Rebase Conflicts

```bash
# After resolving conflicts in affected files
git add <resolved-files>

# Continue rebase
git rebase --continue

# Abort if needed
git rebase --abort
```

### When to Use Rebase
- Feature branches with multiple commits
- Before creating PR
- When you want clean, linear history

## Merge (When Rebase Isn't Appropriate)

Merge preserves original commit history. Use for collaborative branches.

```bash
# Merge origin/main into current branch
git merge origin/main

# Merge with a custom commit message
git merge origin/main -m "Merge main into feat/my-feature"
```

### Handling Merge Conflicts

```bash
# After editing conflicted files
git add <resolved-files>

git commit
```

## Pull (Quick Updates)

For trivial syncing:

```bash
# Pull with rebase (preferred)
git pull --rebase origin main

# Standard pull (creates merge commit)
git pull origin main
```

## Sync Commands Reference

### Sync Feature Branch

```bash
git fetch origin && git rebase origin/main
```

### Sync Main Branch

```bash
git checkout main && git fetch origin && git merge origin/main && git push
```

### Sync All Branches

```bash
git fetch --all && for branch in $(git branch --format="%(refname:short)"); do git checkout "$branch" && git rebase origin/main 2>/dev/null || echo "Skipped $branch"; done
```

## Stash Before Sync

If you have uncommitted changes:

```bash
# Stash changes
git stash push -m "WIP: work in progress"

# Sync branch
git fetch origin && git rebase origin/main

# Restore changes
git stash pop
```

## Verify Sync

After syncing, verify with:

```bash
# Check commits ahead/behind
git log --oneline origin/main..HEAD

# Verify no divergences
git status

# Show recent commits on branch
git log --oneline -5
```

## Sync Before PR

Before creating a PR, ensure your branch is synced:

```bash
# 1. Fetch latest
git fetch origin

# 2. Rebase onto main
git rebase origin/main

# 3. Run tests
# <your test command>

# 4. Push updates
git push --force-with-lease
```

## Push After Sync

After rebasing, you'll need to force push because history changed:

```bash
git push --force-with-lease
```

**Important**: Only force push feature branches, never main.

## Quick Reference

```
Fetch:           git fetch --all
Rebase:          git rebase origin/main
Merge:           git merge origin/main
Pull rebase:     git pull --rebase origin main
Force push:     git push --force-with-lease
Stash:           git stash push -m "WIP"
Restore stash:   git stash pop
```

## Related Skills

| Skill | When to Use |
|-------|-------------|
| `/commit` | Make commits before syncing |
| `/push` | Push after syncing |