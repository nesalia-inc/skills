---
name: setup-canary
description: Guide the use of canary branch workflow following Marty conventions - test changes on canary branch before merging to main. Use this skill when the user asks to test on canary, use canary branch, merge to canary, or prepare changes for main.
disable-model-invocation: false
context: fork
agent: general-purpose
permissionMode: bypassPermissions
allowed-tools: Bash
---

# Canary Branch Workflow

Follow this guide to use the canary branch for testing before merging to main. The canary branch is a pre-main integration branch where changes are tested together before full deployment.

## What is Canary Branch?

The `canary` branch is a testing branch that sits between feature branches and `main`:
- Feature branches merge into `canary` for integration testing
- When `canary` is stable, it merges into `main`
- `main` is always deployable and reflects production

```
feature/xyz → canary → main
```

## Canary Workflow

### Step 1: Create Feature Branch

```bash
git checkout -b feat/my-feature
# Make your changes
git add -A
git commit -m "feat(skills): add new feature

Testing new capability before main.

Co-Authored-By: martyy-code <nesalia.inc@gmail.com>"
```

### Step 2: Push to Remote

```bash
git push -u origin HEAD
```

### Step 3: Create PR to Canary

```bash
gh pr create --title "feat(skills): add new feature" --body "## Summary
- New feature for testing

## Test Plan
- [ ] Unit tests pass
- [ ] Integration tests pass
- [ ] Manual testing completed

Co-Authored-By: martyy-code <nesalia.inc@gmail.com>" --base canary
```

### Step 4: Merge to Canary

After PR approval, merge to `canary`:

```bash
gh pr merge --squash --delete-branch
```

### Step 5: Verify on Canary

```bash
# Switch to canary and pull latest
git checkout canary && git pull

# Deploy canary or run tests
./scripts/deploy-canary.sh
# or
npm run test:integration
```

### Step 6: Monitor and Validate

```bash
# Check canary build status
gh run list --branch canary

# View canary logs
kubectl logs -l environment=canary
```

### Step 7: Merge Canary to Main

Once `canary` is stable:

```bash
# From main, merge canary
git checkout main && git pull
git merge origin/canary

# Push main
git push
```

## Branch Protection Rules

| Branch | Protection |
|--------|------------|
| `main` | Requires PR, require status checks, no force push |
| `canary` | Requires PR, require status checks, no force push |

## Canary vs Main

| Aspect | Canary | Main |
|--------|--------|------|
| Purpose | Integration testing | Production-ready |
| Stability | Testing/stable | Always deployable |
| Frequency | Regular merges from features | Merged from canary when stable |
| Deploys | Staging/beta environments | Production |

## Integration with CI/CD

Canary triggers CI pipelines to validate integration:

```yaml
# .github/workflows/canary-ci.yml
name: Canary CI

on:
  push:
    branches: [canary]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - checkout
      - run: npm ci
      - run: npm test
      - run: npm run build
```

## Quick Reference

```
Create feature branch:    git checkout -b feat/my-feature
Push feature:             git push -u origin HEAD
Create PR to canary:      gh pr create --base canary
Merge to canary:          gh pr merge --squash
Verify canary:            git checkout canary && git pull
Merge canary to main:     git checkout main && git merge origin/canary && git push
```

## Common Mistakes to Avoid

1. **Direct push to canary**: Always use PR for canary merges
2. **Skip testing on canary**: Never merge canary to main without validation
3. **Outdated canary**: Keep canary synced with main regularly
4. **Bypass CI**: Don't merge if CI is failing on canary
5. **Merge conflicts**: Resolve conflicts on feature branch before PR

## Related Skills

| Skill | When to Use |
|-------|-------------|
| `/commit` | Make commits following conventions |
| `/pr-creation` | Create PRs to canary or main |
| `/sync` | Keep branches up to date |
| `/push` | Push changes and create PRs |