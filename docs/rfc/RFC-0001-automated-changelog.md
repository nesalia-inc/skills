---
RFC: 0001
Title: Automated Changelog Generation from Git Commits
Status: Proposed
Author: Human (CTO/CEO)
Date: 2026-04-28
---

## Summary

This RFC proposes adding an automated changelog generation system that parses conventional commits to produce a `CHANGELOG.md` file. The system runs as a GitHub Action on merge to main and creates a PR with the changelog update for human review.

## Motivation

Currently, changelogs for Marty are maintained manually, which is:
- **Error-prone**: Easy to forget to update CHANGELOG.md
- **Inconsistent**: Different formats depending on what was remembered
- **Time-consuming**: Manual editing after every merge
- **Incomplete**: Hard to track all changes across many branches

With multiple agents contributing to Marty, maintaining a consistent changelog manually becomes increasingly difficult. We need a system that extracts changelog-worthy information automatically from commit messages.

## Proposed Solution

### Core Approach

1. **Conventional Commits**: All contributors (humans and agents) use the [Conventional Commits](https://www.conventionalcommits.org/) format for commit messages:
   - `feat(skills): add new feature`
   - `fix(docs): correct typo`
   - `refactor(agents): extract memory module`

2. **Automated Parsing**: A GitHub Action parses commits since the last release tag and groups them by type (feat, fix, docs, etc.)

3. **CHANGELOG.md Generation**: The action generates entries in [Keep a Changelog](https://keepachangelog.com/) format

4. **PR-Based Review**: Instead of auto-merging, the action creates a PR so the changelog can be reviewed before merge

### What Gets Included

- `feat` → Features section
- `fix` → Bug Fixes section
- `docs` → Documentation section
- `refactor` → Code Refactoring section
- `perf` → Performance Improvements section

### What Gets Excluded

- `chore`, `style`, `test` → Maintenance/internal, not user-facing

### Scope Support

Commits may optionally include a scope to categorize changes:
- `feat(skills): ...`
- `feat(docs): ...`
- `feat(ci): ...`

### Key Decisions

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Commit format | Conventional Commits | Widely adopted, agent-friendly |
| Changelog format | Keep a Changelog | Standard, expected by users |
| Review flow | PR (not auto-merge) | Human should review before merge |
| Scope | Optional | Flexibility vs consistency tradeoff |

## Alternatives Considered

### Alternative 1: Manual Changelog Maintenance

Keep updating CHANGELOG.md manually.

**Rejected because**:
- Error-prone with multiple contributors
- Inconsistent formatting
- Easy to forget

### Alternative 2: Semantic Release

Use `semantic-release` to auto-publish GitHub Releases.

**Rejected because**:
- GitHub releases only, no CHANGELOG.md
- Requires strict conventional commit enforcement
- Overkill for solo developer

### Alternative 3: Release Please by Google

Use Google's `release-please` tool.

**Rejected because**:
- More complex setup
- Designed for mono-repos
- Separate release PR workflow adds overhead

### Alternative 4: changesets

Use `changesets` for multi-package changelog management.

**Rejected because**:
- Requires `changesets` CLI in every commit
- Extra step for agents to manage
- More overhead than needed

## Prior Art

- [Keep a Changelog](https://keepachangelog.com/) — Standard format that defines what good changelogs look like
- [Conventional Commits](https://www.conventionalcommits.org/) — Commit message specification
- [Release Drafter](https://github.com/release-drafter/release-drafter) — GitHub Action for release notes (outputs GitHub Releases, not CHANGELOG.md)
- [Conventional Changelog](https://github.com/conventional-changelog/conventional-changelog) — Tools for parsing conventional commits

### Lessons from Prior Art

- **Conventional Commits** are well-adopted and agents can easily format commits correctly
- **Keep a Changelog** format is what users expect
- **Release Drafter** works well but targets Releases, not CHANGELOG.md — we need a similar approach but with markdown output

## Drawbacks / Risks

1. **Enforcement gap**: If agents don't use conventional commits, changelog will be incomplete
   - **Mitigation**: Builder agent instructions should mandate conventional commits

2. **Scope inconsistency**: Agents might use different scopes or no scopes
   - **Mitigation**: Define a list of approved scopes; review PR before merge

3. **Breaking changes not highlighted**: Conventional commits require `!` or `BREAKING CHANGE:` footer to mark breaking changes
   - **Mitigation**: Document the requirement; verify in review

4. **Existing commits don't conform**: Historical commits won't appear in changelog
   - **Mitigation**: Accept partial changelog initially; improve over time

## Open Questions

1. **Should the changelog PR auto-merge or require manual approval?**
   - **Recommendation**: Manual approval — you review before merge

2. **What should the version scheme be?**
   - **Recommendation**: Semantic versioning (1.0.0, 1.1.0, 2.0.0)

3. **Should breaking changes require a different process?**
   - **Recommendation**: Document and handle case-by-case for now

4. **How to handle dependent changes (e.g., "new skill that depends on new agent feature")?**
   - **Recommendation**: Manual grouping in review for now

## Related RFCs

- None yet

## Status History

| Date | Status | Notes |
|------|--------|-------|
| 2026-04-28 | Proposed | Initial draft |
