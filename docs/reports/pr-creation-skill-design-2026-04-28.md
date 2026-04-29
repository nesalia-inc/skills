# PR Creation Skill Design Report

**Date**: 2026-04-28
**Status**: Draft

---

## Executive Summary

The `/create-pr` skill creates GitHub Pull Requests. It captures the right information, links to issues and RFCs, and ensures the PR is ready for review.

---

## Purpose

When implementation is complete, `/create-pr`:
- Creates a properly formatted PR
- Links to related issue(s) and RFC(s)
- Provides clear description of changes
- Adds appropriate labels
- Ensures CI checks are configured

---

## When to Trigger

| Trigger | Description |
|---------|-------------|
| After `/implement` | When RFC is implemented, PR is auto-created |
| Manual | Human says `/create-pr` or `/pr` |
| After branch push | Agent detects branch is ready |

---

## PR Creation Process

```
/create-pr
    ↓
┌─────────────────────────────────────────────┐
│  1. Detect Context                          │
│     - What branch are we on?                 │
│     - What issue/RFC is this for?           │
│     - What files changed?                    │
│     - Is CI configured for these files?      │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  2. Generate PR Title                         │
│     - Use conventional commit format         │
│     - Include issue number if applicable      │
│     Example: "feat(auth): add GitHub OAuth" │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  3. Write PR Description                     │
│     - Summary of changes                     │
│     - Why these changes                      │
│     - Breaking changes (if any)              │
│     - Migration notes (if any)               │
│     - Related issues and RFCs                │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  4. Set Labels                               │
│     - type/* from issue                     │
│     - size/* from triage                    │
│     - Linked to RFC if applicable            │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  5. Assign Reviewers                         │
│     - Self-review (Human) for final approval │
│     - Or none if automated                   │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  6. Create PR                                │
│     - POST to GitHub                         │
│     - Notify Human if needed                 │
│     - Trigger /pr-review automatically       │
└─────────────────────────────────────────────┘
```

---

## PR Title Format

Following Conventional Commits:

```
<type>(<scope>): <description>

feat(auth): add GitHub OAuth with full repo access
fix(docs): correct typo in installation guide
refactor(agents): extract memory handling to module
docs(process): add RFC process documentation
chore(ci): add changelog generation workflow
```

### Type Mapping

| Type | Use When |
|------|----------|
| `feat` | New feature |
| `fix` | Bug fix |
| `docs` | Documentation only |
| `style` | Formatting, no code change |
| `refactor` | Code refactoring, no behavior change |
| `perf` | Performance improvement |
| `test` | Tests only |
| `chore` | Maintenance, tooling |

---

## PR Description Template

```markdown
## Summary

<!-- Brief description of what this PR does -->

Implement GitHub OAuth authentication with full repository access scope.

## Motivation

<!-- Why are these changes needed? -->

- Closes #42
- Implements RFC-0042
- User requested ability to access private GitHub repos

## Changes

<!-- Detailed description of changes -->

- Added GitHub OAuth provider configuration
- Added scope configuration for repo access
- Added `getAccessToken()` helper for API calls
- Updated tests for new authentication flow

## Breaking Changes

<!-- Any breaking changes? -->

- None

## Migration

<!-- How to migrate if applicable -->

- No migration needed for existing users

## Testing

<!-- How was this tested? -->

- Unit tests added for `getGitHubRepos()`
- Manual testing with GitHub OAuth flow
- Tested with both public and private repos

## Related Issues & RFCs

- Closes #42
- RFC-0042

## Checklist

- [ ] Code follows project conventions
- [ ] Tests pass locally
- [ ] Documentation updated
- [ ] CHANGELOG updated (if user-facing)
```

---

## PR Labels

| Label | Applied When |
|-------|--------------|
| `type/bug` | Bug fix PR |
| `type/feature` | Feature PR |
| `type/refactor` | Refactoring PR |
| `type/chore` | Maintenance PR |
| `type/docs` | Documentation PR |
| `size/*` | From triage |
| `rfc/approved` | Linked RFC is approved |
| `status/awaiting-review` | Initial state |

---

## CI Configuration

The skill should verify:

1. **Required checks configured** — At minimum: tests, lint
2. **Checks will run on this PR** — Not skipped by path filters
3. **Status checks required for merge** — Branch protection rules

If CI is not configured, the skill should:
- Warn that CI is missing
- Offer to add basic CI configuration
- Or proceed with PR if Human approves

---

## Branch Naming

Following convention:

| Type | Format | Example |
|------|--------|---------|
| Feature | `feature/issue-42-github-oauth` | |
| Bug fix | `bugfix/issue-47-login-crash` | |
| RFC | `rfc-42-oauth-implementation` | |
| Chore | `chore/update-deps` | |
| Refactor | `refactor/auth-module` | |

---

## Draft PRs

Support for draft PRs when work is in progress:

```markdown
/crate-pr --draft
```

Draft PRs:
- Are not reviewed
- Don't trigger CI required checks
- Can be converted to real PR when ready

---

## WIP PRs

For work-in-progress that shouldn't be merged yet:

```
/create-pr --wip
```

Adds `[WIP]` prefix to title and marks as draft.

---

## Skill Usage

### `/create-pr`

Creates PR with auto-detected context.

### `/create-pr --draft`

Creates draft PR.

### `/create-pr --wip`

Creates WIP PR.

### `/create-pr #42`

Creates PR for specific issue #42.

### `/create-pr RFC-0042`

Creates PR implementing RFC-0042.

---

## Integration with Other Skills

| Skill | Integration |
|-------|-------------|
| `/implement` | Calls `/create-pr` after implementation |
| `/pr-review` | Triggered automatically after PR creation |
| `/pr-update` | Linked to this PR |
| `/create-issue` | PR linked to issue |
| `/create-rfc` | PR implements specific RFC |

---

## Open Questions

1. **Auto-assign reviewer?** Or leave for Human to assign?

2. **Should PR be created immediately** when branch is pushed, or only when `/implement` completes?

3. **Squash vs merge commits** — Default to squash? Allow choosing?

4. **PR template** — Should this be in `.github/pull_request_template.md` instead of generated by skill?

5. **Multi-PR for large RFCs** — How to handle when an RFC requires multiple PRs?

---

## Related Documents

- `docs/reports/pr-review-skill-design-2026-04-28.md` — Reviewing PRs
- `docs/reports/pr-update-skill-design-2026-04-28.md` — Updating PRs
- `docs/reports/issue-rfc-process-design-2026-04-28.md` — General workflow

---

*This document is a design for a future skill. The skill itself has not been created yet.*
