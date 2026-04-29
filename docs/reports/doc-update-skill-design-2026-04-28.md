# Doc Update Skill Design Report

**Date**: 2026-04-28
**Status**: Draft

---

## Executive Summary

The `/doc-update` skill keeps documentation current. It detects when docs are outdated, updates them automatically or flags what needs manual attention, and ensures docs sync with code changes.

---

## Purpose

Documentation rots when it's not kept in sync. `/doc-update`:
- **Detects** when code changes affect docs
- **Updates** outdated documentation
- **Creates** missing documentation
- **Flags** what can't be auto-updated
- **Tracks** documentation coverage

---

## When to Trigger

| Trigger | Description |
|---------|-------------|
| After PR merge | When merged code changes docs |
| Manual | Human says `/doc-update` |
| On file change | When code in `docs/` changes |
| Scheduled | Weekly doc health check |

---

## Update Process

```
/doc-update
    ↓
┌─────────────────────────────────────────────┐
│  1. Scan for Doc Changes Needed               │
│     - Read recent commits since last doc update│
│     - Compare code vs docs                    │
│     - Find:                                 │
│       → API changes (new params, removed)     │
│       → Config changes                        │
│       → New files/features                   │
│       → Breaking changes                     │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  2. Categorize Updates                       │
│     → Auto-updatable (safe)                 │
│     → Needs human review                    │
│     → Missing docs (create needed)          │
│     → Outdated but no change in code         │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  3. Apply Auto-Updates                       │
│     → Update API examples                   │
│     → Fix typos                             │
│     → Update links                          │
│     → Add missing sections                   │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  4. Report What Needs Human Help              │
│     → Complex changes needed                 │
│     → Architectural docs outdated           │
│     → New feature needs doc                 │
└─────────────────────────────────────────────┘
```

---

## Doc Types

### Auto-Updatable (Safe)

| Doc Type | Update Type |
|----------|-------------|
| API parameter lists | Sync with code signature |
| Code examples | Re-run and update output |
| Links | Fix broken URLs |
| Version numbers | Update version references |
| Typos/grammar | Auto-fix |
| Formatting | Auto-format |

### Needs Human Review

| Doc Type | Why Human Needed |
|----------|------------------|
| Conceptual docs | Understanding the "why" |
| Architecture docs | Design decisions |
| Tutorial docs | User experience |
| Migration guides | Breaking changes need context |
| Release notes | Summarize impact |

---

## Detection Patterns

### Pattern 1: API Changed

```
Code change detected:
  src/api/auth.ts - added 'scopes' parameter to getAccessToken()

Doc needs update:
  docs/api/auth.md - Parameter list is outdated

Action:
  → Update parameter list automatically
  → Flag if description needs updating
```

### Pattern 2: New File Created

```
New file detected:
  src/auth/github-oauth.ts - new file

Doc needs update:
  docs/learnings/better-auth/ - No mention of GitHub OAuth

Action:
  → Create skeleton docs for new feature
  → Flag for human to fill in details
```

### Pattern 3: Config Changed

```
Config change detected:
  auth.config.ts - session timeout changed from 1h to 24h

Doc needs update:
  docs/configuration.md - Session section outdated

Action:
  → Update timeout value
  → Flag if explanation needs change
```

### Pattern 4: Deprecation

```
Code change detected:
  src/auth/legacy.ts - marked as @deprecated

Doc needs update:
  docs/auth/legacy.md - Needs deprecation notice

Action:
  → Add deprecation banner
  → Add "use X instead" reference
  → Flag for migration timeline
```

---

## Update Actions

### Action 1: Auto-Update

Apply safe changes directly:

```markdown
## Before
```typescript
const token = await auth.getAccessToken(providerId);
```

## After (auto-updated)
```typescript
const token = await auth.getAccessToken(providerId, { scopes: ['repo'] });
```
```

### Action 2: Create Skeleton

Create placeholder for new docs:

```markdown
## docs/learnings/better-auth/github-oauth.md (NEW)

---
created: 2026-04-28
needs: Human review
---

# GitHub OAuth

## What is it?

<!-- TODO: Human to fill in -->

## Why use it?

<!-- TODO: Human to fill in -->

## Setup

<!-- TODO: Human to fill in -->
```

### Action 3: Flag for Review

Mark what needs human attention:

```markdown
## Flag: docs/auth/architecture.md

⚠️ **Needs Human Review**

- Section "Session Management" is outdated (see commit abc123)
- New session storage option added in #1234
- Please update migration guide

Assigned to: @human
```

---

## Documentation Structure

Expected structure:

```
docs/
├── learnings/           # Package/library docs (generated)
│   └── better-auth/
│       ├── README.md
│       └── core-concepts/
├── internal/            # Internal docs
│   ├── architecture.md
│   └── processes.md
├── guides/              # How-to guides
│   └── getting-started.md
├── reference/           # API reference
│   └── api/
└── reports/             # Design docs, RFCs
```

---

## Coverage Tracking

### Documentation Health Metrics

| Metric | Description |
|--------|-------------|
| Coverage % | How many features have docs |
| Freshness | Days since last update |
| Completeness | Are all sections filled? |
| Accuracy | Are examples runnable? |

### Coverage Report

```markdown
## Doc Coverage Report

### Package: better-auth
| Feature | Status | Last Updated | Completeness |
|---------|--------|--------------|-------------|
| Installation | ✅ Documented | 2d ago | 100% |
| OAuth | ✅ Documented | 1w ago | 90% |
| Session | ⚠️ Outdated | 3w ago | 70% |
| 2FA | ❌ Missing | - | 0% |

### Overall Coverage: 78%

### Action Items
1. Update Session docs (3w old)
2. Create 2FA docs
3. Run all code examples to verify accuracy
```

---

## Skill Usage

### `/doc-update`

Update docs for current changes.

### `/doc-update --all`

Full documentation sweep.

### `/doc-update --check`

Check what needs updating without changing anything.

### `/doc-update --report`

Generate coverage report.

### `/doc-update docs/learnings/better-auth/`

Update specific documentation section.

### `/doc-update --create`

Create missing documentation for new code.

---

## Doc Update PR

When `/doc-update` makes changes, it should:

1. Create a PR with doc updates
2. Link to related code PRs
3. Tag affected maintainers
4. Follow doc PR conventions

```markdown
## Doc Update PR

### Changes
- Updated API examples in `docs/api/auth.md`
- Fixed broken links in `docs/learnings/`
- Created skeleton for new GitHub OAuth docs

### Related PRs
- #1234 (code changes)
- #1235 (config changes)

### Coverage Impact
- +5% coverage (added GitHub OAuth skeleton)
```

---

## Integration with Other Skills

| Skill | Integration |
|-------|-------------|
| `/implement` | Calls `/doc-update` after implementation |
| `/pr-review` | Checks if docs updated for PR |
| `/pr-creation` | Includes doc update PR if needed |
| `/learn` | Creates initial documentation |
| `/create-rfc` | RFCs become documentation |

---

## Doc-First vs Code-First

### Two Approaches

| Approach | Description |
|----------|--------------|
| **Doc-First** | Write docs, then implement code |
| **Code-First** | Write code, then update docs |

### Recommendation for Marty

**Code-First with Doc Sync**:

1. Implement code
2. `/doc-update` auto-updates what it can
3. Human fills in conceptual docs
4. RFC captures "why" decisions

---

## Open Questions

1. **Doc source of truth** — Where should docs live? (Next to code? In `docs/`? Both?)

2. **Code examples in docs** — Should they be auto-tested? (Run on every doc update?)

3. **Versioned docs** — How to handle docs for different versions?

4. **Generated vs Written** — When to generate (API refs) vs write (guides)?

5. **Doc PR size** — Small doc fixes per PR? Or batch doc updates?

6. **External docs** — How to update docs hosted elsewhere (e.g., GitHub Wiki, external sites)?

---

## Related Documents

- `docs/reports/issue-rfc-process-design-2026-04-28.md` — General workflow
- `docs/learnings/` — Example of package documentation
- `docs/reports/init-labels-skill-design-2026-04-28.md` — Labels (includes `type/docs`)

---

*This document is a design for a future skill. The skill itself has not been created yet.*
