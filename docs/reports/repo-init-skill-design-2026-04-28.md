# Repo Init Skill Design Report

**Date**: 2026-04-28
**Status**: Draft

---

## Executive Summary

The `/repo-init` skill initializes new GitHub repositories with Marty's standard setup: labels, CI/CD, issue templates, branch protection, and conventions. It ensures consistency across all repos Marty works on.

---

## Purpose

When Marty starts on a new repository, it needs:
- **Standard labels** — Same as other repos
- **CI/CD configuration** — Testing, linting
- **Issue templates** — Bug reports, feature requests
- **Branch protection** — main/master protected
- **Contributing guidelines** — For collaborators
- **Standard files** — README, LICENSE, .gitignore

`/repo-init` sets this up in one command.

---

## When to Trigger

| Trigger | Description |
|---------|-------------|
| Manual | Human says `/repo-init` or `/repo-init owner/repo` |
| On repo creation | Agent detects new repo access |
| Before first PR | Setup before work begins |

---

## Init Process

```
/repo-init
    ↓
┌─────────────────────────────────────────────┐
│  1. Detect Target                            │
│     - Current repo (default)                │
│     - Or specified: owner/repo              │
│     - Verify write access                  │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  2. Show Current State                      │
│     - Existing labels                       │
│     - Existing CI                          │
│     - Existing templates                   │
│     - What needs to be added               │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  3. Apply Standard Configuration            │
│     → Labels (from /init-labels)            │
│     → CI workflows                         │
│     → Issue templates                      │
│     → Standard files                       │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  4. Configure Branch Protection              │
│     - main branch protected                 │
│     - Require PR reviews                   │
│     - Require status checks                 │
│     - No force push                        │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  5. Initial Commit (Optional)                │
│     - Create initial commit if repo empty    │
│     - Or skip if already has code           │
└─────────────────────────────────────────────┘
```

---

## What Gets Configured

### 1. Labels (via `/init-labels`)

All standard labels are created:
- `bug`, `feature`, `refactor`, `chore`, `docs`, `test`, `research`
- `priority/p0` through `priority/p3`
- `status/triaged`, `status/in-progress`, `status/blocked`, etc.
- `rfc-needed`, `rfc/draft`, `rfc/approved`
- `size/xs` through `size/xl`

### 2. CI/CD Workflows

```yaml
.github/workflows/
├── ci.yml          # Main: test, lint, typecheck
├── stale.yml       # Auto-close stale issues
└── labeler.yml     # Auto-label based on paths
```

### 3. Issue Templates

```
.github/ISSUE_TEMPLATE/
├── bug_report.md
├── feature_request.md
├── question.md
└── custom.md (optional)
```

### 4. PR Template

```markdown
.github/
└── pull_request_template.md
```

### 5. Standard Files

| File | Purpose |
|------|---------|
| `README.md` | Project overview |
| `LICENSE` | License (MIT default) |
| `.gitignore` | Git ignore patterns |
| `CONTRIBUTING.md` | How to contribute |
| `SECURITY.md` | Security policy |

### 6. Branch Protection

```json
{
  "required_status_checks": {
    "strict": true,
    "contexts": ["ci"]
  },
  "enforce_admins": true,
  "required_reviewing_approvals": 1,
  "disallow_force_pushes": true
}
```

---

## Configuration Levels

### Minimal (Fast)

```
/repo-init --minimal
```

- Standard labels only
- Basic CI (test only)
- No templates
- No branch protection

### Standard (Recommended)

```
/repo-init
```

- All labels
- Full CI (test, lint, typecheck)
- Issue templates
- PR template
- Branch protection
- Standard files

### Full (Complete)

```
/repo-init --full
```

- Everything in standard
- Security policy
- Code of conduct
- Issue forms (instead of templates)
- Dependabot configuration
- Wiki enabled

---

## GitHub Settings Applied

### General Settings

| Setting | Value |
|---------|-------|
| Allow merge commits | Yes |
| Allow squash merging | Yes |
| Allow rebase merging | No |
| Auto-delete head branches | Yes |
| Wiki | Disabled (unless --full) |
| Projects | Disabled (unless requested) |

### Repository Topics

Suggest based on repo contents:
- `javascript`, `typescript`, `python`, etc.
- `cli`, `web`, `api`, `library`
- `marty-managed`

---

## Init Output

```markdown
## Repo Init Complete: owner/repo

### Labels Created: 25
✅ bug, feature, refactor, chore, docs, test, research
✅ priority/p0, p1, p2, p3
✅ status/triaged, in-progress, blocked, in-review, done
✅ rfc-needed, rfc/draft, rfc/approved, rfc/rejected
✅ size/xs, s, m, l, xl

### CI/CD Configured: 2
✅ .github/workflows/ci.yml (test, lint, typecheck)
✅ .github/workflows/stale.yml (auto-close stale)

### Templates: 4
✅ Bug report template
✅ Feature request template
✅ Question template
✅ PR template

### Branch Protection
✅ main branch protected
✅ Require 1 approval
✅ Require status checks
✅ Block force push

### Files Created: 3
✅ .gitignore
✅ CONTRIBUTING.md
✅ SECURITY.md

### Files Skipped (already exist): 2
⚠️ README.md (keep existing)
⚠️ LICENSE (keep existing)
```

---

## What NOT to Overwrite

The skill should NEVER overwrite existing files:

| File | Behavior |
|------|----------|
| `README.md` | Keep existing (unless empty) |
| `LICENSE` | Keep existing |
| `package.json` | Keep existing |
| Existing CI workflows | Keep existing (add new ones) |
| Existing labels | Skip if already exists |

---

## Validation

### Pre-Init Check

```markdown
## Pre-Init Validation

✅ Repository exists: owner/repo
✅ Write access: confirmed
✅ Not archived: repo is active

⚠️ Has existing labels: 5 (will merge with standard)
⚠️ Has existing CI: 1 workflow (will not overwrite)

Ready to init? YES
```

---

## Skill Usage

### `/repo-init`

Initialize current repository.

### `/repo-init owner/repo`

Initialize specified repository.

### `/repo-init --minimal`

Minimal setup (labels + basic CI).

### `/repo-init --full`

Complete setup with all features.

### `/repo-init --dry-run`

Preview what will be configured.

### `/repo-init --force`

Overwrite existing configurations (DANGEROUS).

---

## Tools Used

| Tool | Purpose |
|------|---------|
| GitHub API | Create labels, settings, files |
| `gh` CLI | Repo operations |
| Git | File operations |

---

## Integration with Other Skills

| Skill | Integration |
|-------|-------------|
| `/init-labels` | Uses label definitions |
| `/create-issue` | Uses templates |
| `/pr-creation` | Uses PR template |
| `/ci-fix` | Uses CI workflows |

---

## Open Questions

1. **Repo scope** — Own repos only? Or collaborators too? (Permissions matter)

2. **Templates customization** — Allow custom templates per repo? Or always standard?

3. **Existing CI handling** — Merge with existing CI? Or skip if exists?

4. **Init on first access** — Auto-init when accessing new repo? Or manual?

5. **Rollback** — How to undo init if something goes wrong?

6. **Monorepo support** — Different setup for monorepos?

---

## Related Documents

- `docs/reports/init-labels-skill-design-2026-04-28.md` — Label definitions
- `docs/reports/ci-fix-monitor-skill-design-2026-04-28.md` — CI configuration

---

*This document is a design for a future skill. The skill itself has not been created yet.*
