---
name: fix-issue
description: Fix a GitHub issue by reading it, implementing the fix, and creating a PR. Use this skill when the user wants to work on an issue. Also use when the user says "fix issue #X", "work on this issue", "implement issue", or "take this issue".
disable-model-invocation: false
context: fork
agent: general-purpose
permissionMode: bypassPermissions
allowed-tools: Bash, Read, Glob, Grep, Write, Edit, Task
---

# Fix Issue Guide

Fixes a GitHub issue that has been properly triaged. Creates branch, implements fix with tests, and creates PR.

## Prerequisites

**The issue MUST have `status/triaged` label before using this skill.**

If the issue is NOT triaged, use `/triage <number>` first.

## CRITICAL: Use Skills for Every Change

**You MUST use the following skills for every change:**

| Skill | When to Use |
|-------|-------------|
| `/commit` | **Every single commit** - conventional commits format |
| `/unit-tests` | Writing and running tests - Vitest best practices |

**Never commit directly without using `/commit` skill. Never write tests without consulting `/unit-tests` skill.**

## Step 1: Verify Issue is Triaged

```bash
gh issue view <number> --json title,labels
```

### Check Required Labels

**The issue MUST have `status/triaged` label.**

If the issue does NOT have `status/triaged`:

```
Issue #<number> is NOT triaged.

Labels found: [list of current labels]

This issue must be triaged before it can be worked on.
Run /triage <number> to triage this issue.
```

**STOP** - Do not proceed until issue has `status/triaged` label.

### Check for Blockers

If issue has `status/blocked` label:
```
Issue #<number> is blocked.

This issue cannot be worked on until the blocker is resolved.
```

### Check for RFC Requirement

If issue has `rfc-needed` label:
```
Issue #<number> requires RFC before implementation.

Run /create-rfc <number> to create the RFC proposal first.
```

**STOP** - Do not proceed until RFC is approved.

## Step 2: Create Branch

```bash
git checkout -b fix/<issue-number>-<short-description>
```

## Step 3: Implement the Fix - ATOMIC COMMITS REQUIRED

**IMPORTANT**: Make atomic commits at every step. Each commit should be:
- Self-contained (can be reverted independently)
- Logical (one change, one purpose)
- Testable (passes tests)

### Implementation Process

For each logical change:

1. **Make the change**
2. **Test it**
3. **Commit using `/commit` skill**:
   ```
   Use /commit skill with message:
   fix(<scope>): <description>

   [What changed and why]

   Co-Authored-By: martyy-code <nesalia.inc@gmail.com>
   ```

### Example of Atomic Commits for Issue Fix

If fixing a login bug that requires:
1. Add validation function
2. Update error handling
3. Add tests

```bash
# Commit 1: Add validation function
git add src/auth/validator.ts
/commit "fix(auth): add email validation function

- Added isValidEmail() helper
- Used in login flow before authentication

Co-Authored-By: martyy-code <nesalia.inc@gmail.com>"

# Commit 2: Update error handling
git add src/auth/login.ts
/commit "fix(auth): improve error messages for invalid login

- Added specific error types
- Included context for debugging

Co-Authored-By: martyy-code <nesalia.inc@gmail.com>"

# Commit 3: Add tests
git add tests/auth/
/commit "test(auth): add tests for validation and login errors

- Tests for isValidEmail()
- Tests for error handling edge cases

Co-Authored-By: martyy-code <nesalia.inc@gmail.com>"
```

## Step 4: Test

**Use `/unit-tests` skill for testing best practices.**

### Testing Philosophy

Follow these principles when writing tests for fixes:

1. **Tests as Documentation** - Tests must be readable and explain the behavior
2. **Implementation Agnostic** - Test inputs/outputs, not internal implementation
3. **Fast Feedback** - Unit tests must run in milliseconds

### The No-Mock Rule

**Business logic should never be mocked.** If you need 5+ mocks to test one function, refactor instead.

```typescript
// ❌ BAD: Mocking internal logic
vi.mock('./internal-helper', () => ({ calculateTax: vi.fn().mockReturnValue(20) }));

// ✅ GOOD: Test final behavior
it('should calculate final price including tax', () => {
  expect(calculateOrder(order).total).toBe(120);
});
```

### What to Mock (Boundaries Only)

| Mock | Use For |
|------|---------|
| `vi.useFakeTimers()` | Date/time (`Date.now()`, `setTimeout`) |
| MSW | Network/API calls |
| `vi.fn()` | Random/UUID (`Math.random()`) |

### Run Tests

```bash
# Run all tests
npx vitest

# Run specific file
npx vitest src/auth/login.test.ts

# Run with coverage
npx vitest run --coverage

# Watch mode during development
npx vitest watch
```

### Regression Tests

**Every fix MUST have a regression test.** If the bug had no test, add one:

```bash
# Add test for the bug that was fixed
git add tests/auth/login.test.ts
/commit "test(auth): add regression test for login bug

- Tests for invalid email format validation
- Tests for empty password error

Closes #<number>

Co-Authored-By: martyy-code <nesalia.inc@gmail.com>"
```

## Step 5: Push

```bash
git push -u origin HEAD
```

## Step 6: Link Issue to PR

After creating the PR, link it to the issue using `gh issue develop`:

```bash
# Link issue to the PR branch
gh issue develop <number> --base canary --name fix/<issue-number>-<short-description>
```

This creates a link between the issue and the development branch.

## Step 7: Add Labels

Add appropriate labels to the issue:

```bash
# Mark as in progress
gh issue edit <number> --add-label "status/in-progress" --add-assignee "@me"
```

## Step 8: Create PR

```bash
cat > /tmp/pr-body.md << 'EOF'
## Summary
Fixed issue #<number>

## Changes Made
- [What was changed]

## Testing
- [How it was tested]

Closes #<number>

Co-Authored-By: martyy-code <nesalia.inc@gmail.com>
EOF

gh pr create --title "[fix]: resolve issue #<number>" --body-file /tmp/pr-body.md --base canary
rm /tmp/pr-body.md
```

## Why Atomic Commits Matter

- **Revert capability**: If something breaks, you can revert a single commit without reverting the entire fix
- **Code review**: Reviewers can see the logical progression
- **Bisect-friendly**: `git bisect` works better with atomic commits
- **Clear history**: Each commit tells part of the story

## Example Workflow

```
/fix-issue 45

Reading issue #45...
Verifying triage status...

Labels: [bug, priority/p1, effort/s, area/auth, status/triaged]
✓ Issue is triaged.

Creating branch fix/45-token-refresh...

Implementing fix with atomic commits...

Commit 1: fix(auth): add validation helper
Commit 2: fix(auth): update error handling
Commit 3: test(auth): add validation tests

Testing...
✓ All tests pass.

Pushing...

Linking issue to PR branch...
Adding labels (status/in-progress)...
Creating PR to canary...

✓ PR created: https://github.com/owner/repo/pull/X
```

## Issue Not Triaged Example

```
/fix-issue 45

Reading issue #45...
Verifying triage status...

Labels: [bug, priority/p1]
✗ Issue is NOT triaged.

Missing labels:
- status/triaged

This issue must be triaged before it can be worked on.
Run /triage 45 to triage this issue.
```

## Quick Reference

```
/fix-issue <number>
→ Verify status/triaged label is present
→ If NOT triaged: Run /triage <number> first
→ If blocked or rfc-needed: Handle accordingly
→ Create branch
→ Implement with ATOMIC commits using /commit skill
→ Test after each commit
→ Push
→ Create PR
```

## Failure Modes

| Condition | Action |
|-----------|--------|
| `status/triaged` missing | Run `/triage <number>` |
| `status/blocked` present | Do not proceed until unblocked |
| `rfc-needed` present | Run `/create-rfc <number>` |
| `status/in-progress` present | Issue already being worked on |

## Related Skills

| Skill | When to Use |
|-------|-------------|
| `/triage` | **MUST run first** if issue is not triaged |
| `/commit` | **MUST use for every commit** |
| `/unit-tests` | **MUST use for writing tests** |
| `/create-rfc` | When issue has `rfc-needed` label |
| `/push` | Push changes |
| `/create-pr` | PR creation details |