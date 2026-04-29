# Release Skill Design Report

**Date**: 2026-04-28
**Status**: Draft

---

## Executive Summary

The `/release` skill manages software releases. It creates tags, GitHub releases, generates changelog entries, and ensures release notes are accurate and comprehensive.

---

## Purpose

Releasing software requires:
- **Version bumping** (semantic versioning)
- **Tag creation** and management
- **GitHub Release** creation
- **Release notes** generation from changelog
- **Distribution** (npm, Docker, etc.)

`/release` automates this process following semver and conventional commits.

---

## When to Trigger

| Trigger | Description |
|---------|-------------|
| Manual | Human says `/release` or `/release 1.2.0` |
| On milestone | When milestone is complete |
| On tag push | When a version tag is pushed |

---

## Release Process

```
/release
    ↓
┌─────────────────────────────────────────────┐
│  1. Determine Current Version                 │
│     - Read latest tag                        │
│     - Determine: patch / minor / major       │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  2. Analyze Changes Since Last Release       │
│     - Read commits since last tag           │
│     - Categorize: patch / minor / major    │
│     - Generate changelog draft              │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  3. Validate Release                       │
│     - All CI passing?                      │
│     - All issues in milestone closed?       │
│     - CHANGELOG updated?                  │
│     - Breaking changes documented?          │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  4. Create Release                          │
│     - Bump version in package.json          │
│     - Create git tag                        │
│     - Push tag to remote                    │
│     - Create GitHub Release                 │
│     - Publish to npm (if applicable)        │
└─────────────────────────────────────────────┘
```

---

## Version Bumping

### Semantic Versioning (semver)

| Change | Version | Example |
|--------|---------|---------|
| Patch | Bug fixes | 1.0.0 → 1.0.1 |
| Minor | New features (backward compatible) | 1.0.0 → 1.1.0 |
| Major | Breaking changes | 1.0.0 → 2.0.0 |

### Automatic Detection

```
Commits since last tag:
- "fix(auth): correct login redirect" → patch
- "feat(skills): add how-to skill" → minor
- "refactor(api): change response format" → major (breaking)

Suggested: minor (1.0.0 → 1.1.0)
Human confirms or overrides
```

### Manual Override

```
/release 2.0.0 --force
Force major release regardless of commits
```

---

## Changelog Generation

### From Conventional Commits

Using the automated changelog system (RFC-0001):

```markdown
## [1.1.0] - 2026-04-28

### Features
- **skills**: Add deep-research skill for comprehensive topic research
- **agents**: Implement Archi/Tech Lead agent with RFC authoring support

### Bug Fixes
- **docs**: Fix broken links in CLAUDE.md

### Documentation
- **process**: Add RFC process documentation
```

### Release Notes Format

```markdown
## Release v1.1.0

### What's New

#### Features
- **Skills System**: New `how-to` skill for quick answers from existing documentation
- **RFC Process**: Complete RFC lifecycle with approval workflow

#### Improvements
- Faster agent spawning
- Better context preservation

### Breaking Changes
None.

### Migration
No migration needed.

### Bug Fixes
- Fixed login redirect issue
- Corrected documentation links

### Contributors
@human

### Full Changelog
[Compare changes](https://github.com/.../compare/v1.0.0...v1.1.0)
```

---

## Release Validation

### Pre-Release Checklist

| Check | Required | Action if Fail |
|-------|----------|---------------|
| CI passing | Yes | Block release |
| Tests passing | Yes | Block release |
| Milestone complete | No | Warn |
| CHANGELOG updated | Yes | Block release |
| No uncommitted changes | Yes | Block release |
| Version not already exists | Yes | Block release |

### Validation Output

```markdown
## Release Validation: v1.1.0

✅ CI: All checks passing
✅ Tests: 48/48 passing
✅ CHANGELOG: Updated with 3 entries
✅ Git: Clean (no uncommitted changes)
✅ Version: v1.1.0 not yet tagged

⚠️ Milestone "v1.1.0": 2 issues still open
   - #45: Docs update pending
   - #46: Changelog verification

Release ready? YES (with warnings)
```

---

## GitHub Release Creation

### Auto-Creation Flow

```
Version bumped → Tag pushed → GitHub webhook → /release triggered
    ↓
Create GitHub Release:
- Title: "v1.1.0"
- Tag: v1.1.0
- Target: main
- Body: Release notes from changelog
    ↓
Notify: "@human v1.1.0 released"
```

### Release Assets

```
Upload to GitHub Release:
- ✅ CHANGELOG.md
- ✅ package.json (for npm)
- ❌ No build artifacts (none configured)
```

---

## NPM Publishing

### If Package is Public

```bash
npm publish --access public
```

### If Package is Private

```bash
npm publish --access restricted
```

### Dry Run

```bash
/release --dry-run
Preview release without publishing
```

---

## Skill Usage

### `/release`

Determine version automatically and release.

### `/release 1.2.0`

Force specific version.

### `/release --major`

Force major version bump.

### `/release --dry-run`

Preview release without making changes.

### `/release --publish`

Release and publish to npm.

### `/release --rollback`

Rollback to previous version.

---

## Rollback Process

```
/release --rollback
    ↓
1. Revert package.json version
2. Delete git tag
3. Delete GitHub Release
4. If published to npm: unpublish
5. Notify human
```

---

## Integration with Other Skills

| Skill | Integration |
|-------|-------------|
| `/implement` | Marks issues as resolved |
| `/create-pr` | Release notes reference merged PRs |
| `/ci-fix` | Ensures CI passes before release |
| `/doc-update` | Updates CHANGELOG.md |
| `/changelog` | (RFC-0001) Generates changelog |

---

## Open Questions

1. **Who approves releases?** Human confirms? Or auto-release on milestone?

2. **NPM or just GitHub releases?** Support both? Or GitHub only for now?

3. **Pre-release vs正式** — Support alpha/beta/rc tags?

4. **Rollback policy** — How many versions to keep? How to handle published npm?

5. **Scheduled releases** — Auto-release on schedule? Or manual only?

---

## Related Documents

- `docs/rfc/RFC-0001-automated-changelog.md` — Changelog system
- `docs/reports/ci-fix-monitor-skill-design-2026-04-28.md` — CI validation

---

*This document is a design for a future skill. The skill itself has not been created yet.*
