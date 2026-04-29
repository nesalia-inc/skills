# Sync Skill Design Report

**Date**: 2026-04-28
**Status**: Draft

---

## Executive Summary

The `/sync` skill synchronizes configurations across multiple repositories. It ensures labels, CI templates, workflows, and conventions stay consistent without manual copy-paste.

---

## Purpose

Marty works on multiple repositories. Keeping them in sync is painful:
- **Labels** — Update labels once, apply everywhere
- **CI Templates** — Share workflow templates
- **Configs** — .github/config files
- **Scripts** — Standard bash/node scripts
- **Documentation** — Contributing guidelines

`/sync` pushes changes to one repo and pulls to others.

---

## When to Trigger

| Trigger | Description |
|---------|-------------|
| Manual | Human says `/sync` or `/sync labels` |
| On config change | When a template is updated |
| Scheduled | Weekly sync verification |
| On new repo | When repo is added to sync list |

---

## Sync Process

```
/sync
    ↓
┌─────────────────────────────────────────────┐
│  1. Determine Sync Type                      │
│     → labels                                 │
│     → ci-templates                          │
│     → configs                               │
│     → all                                   │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  2. Load Source of Truth                     │
│     Read current configs from Marty's repo   │
│     (.claude/sync/templates/)               │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  3. Compare Target Repos                     │
│     For each repo in sync list:             │
│     - Fetch current state                   │
│     - Compare with source                   │
│     - Flag differences                       │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  4. Apply Changes                            │
│     → Additions: Create new                 │
│     → Modifications: Update                │
│     → Deletions: Skip (unless --force)      │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  5. Report Results                           │
│     What changed where                    │
│     What conflicts exist                   │
│     What needs manual resolution           │
└─────────────────────────────────────────────┘
```

---

## Sync Types

### 1. Labels Sync

```yaml
# Source: Marty/.claude/sync/labels/
# Target: All repos in sync list

Syncs:
- Label names
- Label colors
- Label descriptions
- Label aliases (optional)
```

**Behavior:**
- Creates missing labels
- Updates existing labels (color, description)
- NEVER deletes labels (unless `--purge`)

### 2. CI Templates Sync

```yaml
# Source: Marty/.github/workflows/
# Target: All repos in sync list

Syncs:
- ci.yml
- stale.yml
- labeler.yml (optional)
- pr-reviews.yml (optional)
```

**Behavior:**
- Updates existing workflows
- Creates missing workflows
- NEVER overwrites custom workflows (unless `--force`)

### 3. Config Files Sync

```yaml
# Source: Marty/.github/
# Target: All repos in sync list

Syncs:
- ISSUE_TEMPLATE/
- pull_request_template.md
- CONTRIBUTING.md
- SECURITY.md
- .gitignore (if standard)
```

### 4. Full Sync

```bash
/sync --all
```

Syncs labels + CI + configs at once.

---

## Sync Configuration

### Sync List

```yaml
# Marty/.claude/sync/repos.yml
repos:
  - owner/repo-one
  - owner/repo-two
  - other-org/repo-three

exclusions:
  owner/repo-three:
    - ci.yml  # Don't sync ci.yml to this repo
    - labels:  # Don't sync labels to this repo
      - experimental
```

### Template Variables

```yaml
# Supports variables in templates
# Replaced during sync

{{OWNER}}  → Repository owner
{{REPO}}    → Repository name
{{YEAR}}    → Current year
{{DATE}}    → Current date
```

---

## Conflict Resolution

### Types of Conflicts

| Conflict | Detection | Resolution |
|----------|-----------|------------|
| Label exists, different color | Name matches, color differs | Skip (keep target) |
| File exists, different content | Hash differs | Skip (keep target) |
| Label missing | Only in source | Create |
| New file | Only in source | Create |

### Resolution Strategies

```markdown
## Conflict: labels in repo-one

| Label | Source | Target | Resolution |
|-------|--------|--------|------------|
| bug | red | blue | ⚠️ SKIPPED (keep target) |

Resolution strategy: SKIP (target takes precedence)
Use --force to override
```

### Force Override

```bash
/sync --force
```

Force sync all changes, overwriting target differences.

---

## Sync Preview

### Dry Run Output

```markdown
## Sync Preview: labels

### repo-one
| Action | Item |
|--------|------|
| CREATE | priority/p0 (red) |
| UPDATE | bug (d73a4a → dc2626) |
| SKIP | feature (exists, identical) |
| SKIP | docs (exists, identical) |

### repo-two
| Action | Item |
|--------|------|
| CREATE | priority/p0 (red) |
| CREATE | priority/p1 (orange) |
| CREATE | priority/p2 (yellow) |
| SKIP | bug (exists, identical) |

### repo-three
⚠️ EXCLUDED: ci.yml (in exclusions list)

### Summary
| Action | Count |
|--------|-------|
| CREATE | 5 |
| UPDATE | 1 |
| SKIP | 12 |
| EXCLUDED | 1 |

Ready to sync? YES (--dry-run)
```

---

## Selective Sync

### Sync to Specific Repos

```bash
/sync labels --to repo-one,repo-two
```

### Sync Specific Items

```bash
# Sync only specific labels
/sync labels --match "priority/*"

# Sync only specific CI workflows
/sync ci --match "ci.yml,stale.yml"
```

### Exclude Patterns

```bash
# Exclude by pattern
/sync labels --exclude "experimental*,wip-*"

# Exclude specific repos
/sync all --exclude-repo "owner/archived-repo"
```

---

## Rollback

### If Sync Goes Wrong

```bash
/sync --rollback
```

**Rollback behavior:**
1. Fetch previous state from git reflog
2. Restore overwritten labels/files
3. Report what was restored

**Rollback is only possible if:**
- Changes tracked in git
- Sync was recent (within 24h)
- Repo wasn't force-pushed in between

---

## Sync Source of Truth

### Structure

```yaml
# Marty/.claude/sync/
├── templates/
│   ├── labels/
│   │   ├── bug.yml
│   │   ├── feature.yml
│   │   ├── priority/
│   │   │   ├── p0.yml
│   │   │   ├── p1.yml
│   │   │   └── ...
│   │   └── ...
│   ├── ci/
│   │   ├── ci.yml
│   │   ├── stale.yml
│   │   └── labeler.yml
│   └── configs/
│       ├── ISSUE_TEMPLATE/
│       │   ├── bug_report.md
│       │   └── feature_request.md
│       ├── pull_request_template.md
│       ├── CONTRIBUTING.md
│       └── SECURITY.md
└── repos.yml
```

### Updating Source

```bash
# Update a label definition
vim .claude/sync/templates/labels/bug.yml
    ↓
/sync labels
    ↓
All repos get updated
```

---

## Skill Usage

### `/sync`

Sync all configured items to all repos.

### `/sync labels`

Sync labels only.

### `/sync ci`

Sync CI templates only.

### `/sync configs`

Sync config files only.

### `/sync --dry-run`

Preview without applying.

### `/sync --to repo1,repo2`

Sync only to specific repos.

### `/sync --force`

Force overwrite existing differences.

### `/sync --rollback`

Rollback last sync.

---

## Reporting

### Sync Report

```markdown
## Sync Report: labels - 2026-04-28

### Synced Repos: 5

| Repo | Created | Updated | Skipped | Errors |
|------|---------|---------|---------|--------|
| owner/repo-one | 3 | 1 | 8 | 0 |
| owner/repo-two | 3 | 0 | 9 | 0 |
| org/repo-three | 2 | 1 | 7 | 1 ⚠️ |
| org/repo-four | 3 | 0 | 8 | 0 |
| private/repo-five | 3 | 0 | 8 | 0 |

### Errors
⚠️ org/repo-three: Permission denied (write access required)

### Summary
- Total created: 14
- Total updated: 2
- Total skipped: 40
- Errors: 1

### Next Steps
Fix permissions for org/repo-three
```

---

## Integration with Other Skills

| Skill | Integration |
|-------|-------------|
| `/repo-init` | Adds new repos to sync list |
| `/init-labels` | Defines label templates |
| `/create-issue` | Tracks sync as an issue |

---

## Open Questions

1. **Bidirectional sync** — Source → Target only? Or also detect changes in target and pull back?

2. **Sync frequency** — Auto-sync on source change? Or manual trigger only?

3. **Permission handling** — How to handle repos where Marty has limited permissions?

4. **Customization** — Allow per-repo overrides? Or all repos get identical configs?

5. **Version conflicts** — If target was manually edited, which wins?

6. **Monitoring** — Track sync history? Audit trail of changes?

---

## Related Documents

- `docs/reports/init-labels-skill-design-2026-04-28.md` — Label definitions
- `docs/reports/repo-init-skill-design-2026-04-28.md` — Repo setup

---

*This document is a design for a future skill. The skill itself has not been created yet.*
