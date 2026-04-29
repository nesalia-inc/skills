# Deps Update Skill Design Report

**Date**: 2026-04-28
**Status**: Draft

---

## Executive Summary

The `/deps-update` skill manages dependency updates. It checks for outdated packages, security vulnerabilities, and proposes or applies updates with minimal disruption.

---

## Purpose

Dependency management is critical for:
- **Security** — Patches for vulnerabilities
- **Compatibility** — Stay current with latest versions
- **Maintenance** — Reduce technical debt
- **Features** — New package versions enable new features

`/deps-update` automates this without breaking things.

---

## When to Trigger

| Trigger | Description |
|---------|-------------|
| Manual | Human says `/deps-update` |
| Scheduled | Weekly dependency health check |
| Security alert | GitHub notifies of vulnerability |
| On PR merge | Light check for new dependencies |

---

## Update Process

```
/deps-update
    ↓
┌─────────────────────────────────────────────┐
│  1. Scan Dependencies                        │
│     - Read package.json, package-lock.json  │
│     - npm outdated / pip freeze / etc.      │
│     - Check for security advisories         │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  2. Categorize Updates                       │
│     → Security patches (critical)            │
│     → Major version updates (risky)         │
│     → Minor version updates (safe)         │
│     → Patch updates (very safe)             │
│     → Dev dependencies                      │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  3. Risk Assessment                          │
│     - Breaking changes in changelog?        │
│     - Dependents affected?                  │
│     - Test coverage for this package?        │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  4. Apply or Propose                         │
│     → Safe: Auto-apply                      │
│     → Risky: Create PR for review           │
│     → Critical: Alert immediately           │
└─────────────────────────────────────────────┘
```

---

## Update Categories

### By Risk Level

| Category | Risk | Auto-Apply | Example |
|----------|------|-----------|---------|
| **Security** | Critical | Yes (if safe) | `lodash@4.17.21` patch |
| **Patch** | Low | Yes | `express@4.18.0 → 4.18.1` |
| **Minor** | Medium | PR | `react@18.0 → 18.1` |
| **Major** | High | PR + Approval | `react@17 → 18` |
| **Dev** | Low | PR | `eslint@8 → 9` |

### By Type

| Type | Examples | Priority |
|------|----------|----------|
| Runtime deps | react, express, next | High |
| Dev deps | eslint, jest, typescript | Medium |
| Peer deps | Less critical | Low |
| Optional deps | Platform-specific | Low |

---

## Security Vulnerabilities

### Check Sources

1. **GitHub Advisories** — `npm audit` + GitHub API
2. **NPM Advisory Database** — `npm audit`
3. **Snyk** — If configured
4. **Dependabot alerts** — Already in GitHub

### Vulnerability Report

```markdown
## Security Report

### Critical (Act Now)
| Package | Current | Fixed | Vulnerability |
|---------|---------|-------|---------------|
| json5 | 1.0.0 | 1.0.2 | CVE-2024-XXXXX |
| | | | Prototype Pollution |

### High
| Package | Current | Fixed | Vulnerability |
|---------|---------|-------|---------------|
| word-wrap | 1.2.3 | 1.2.6 | CVE-XXXX |

### Medium
| Package | Current | Fixed | Vulnerability |
|---------|---------|-------|---------------|
| semver | 7.0.0 | 7.5.2 | Regular Expression Denial of Service |

### Recommended Actions
1. Update `json5` immediately (critical)
2. Update `semver` this week (medium)
```

---

## Update Strategies

### Strategy 1: All at Once (Batch)

```bash
# Update all safe dependencies
npm update
```

**Pros**: Fast, consolidated
**Cons**: Large PR, harder to debug issues

### Strategy 2: One per PR (Individual)

```bash
# Create separate PR for each major update
npm update react --save
npm update next --save
```

**Pros**: Clear review, easy rollback
**Cons**: Many PRs, longer to land all updates

### Strategy 3: By Risk Tier

```bash
# Security patches: immediate
# Patch/minor: weekly batch
# Major: monthly, with extra review
```

**Pros**: Balanced approach
**Cons**: More complex to manage

---

## Breaking Changes Detection

### How to Detect

```
Major version update detected: react@17 → 18
    ↓
Check:
1. Read package.json dependents
2. Check react ecosystem packages compatibility
3. Read breaking changes in CHANGELOG
4. Search for migration guides
    ↓
If breaking: Flag as risky, require human approval
If safe: Proceed with update
```

### Breaking Changes Report

```markdown
## Update: react@17 → 18

### Breaking Changes
⚠️ **3 breaking changes detected**

1. **New JSX Transform** — No longer need to import React
   - Files affected: 12
   - Migration: Auto-fix available

2. **Removed legacy APIs** — StrictMode changes
   - Files affected: 3
   - Migration: Manual changes needed

3. **Concurrent Features** — New patterns
   - Files affected: 8
   - Migration: Review required

### Recommended Approach
1. Update react and react-dom
2. Run codemod for JSX transform
3. Manual review for Concurrent features
```

---

## Automated Updates

### Safe to Auto-Apply

```javascript
// Package is:
// - Patch or minor version
// - No breaking changes in changelog
// - Not a core dependency
// - Tests pass after update
```

### Requires PR

```javascript
// Package is:
// - Major version
// - Breaking changes found
// - Core dependency
// - Affects many files
```

### Never Auto-Update

```javascript
// Package is:
// - Security vulnerability (manual review)
// - System-level package (node, python system)
// - Breaking change without migration path
```

---

## Update PR Template

```markdown
## Dependency Update: [package]@[version]

### Summary
Update [package] from @[old] to @[new]

### Why Update
- Security: CVE-XXXXX patched
- Compatibility: New version supports X
- Maintenance: Reduce technical debt

### Breaking Changes
- [YES/NO]
- If yes: [details]

### Files Changed
- `package.json`
- `package-lock.json`

### Test Results
- [ ] Unit tests: PASS
- [ ] Integration tests: PASS
- [ ] E2E tests: PASS

### Risk Assessment
| Factor | Level |
|--------|-------|
| Breaking changes | LOW/MEDIUM/HIGH |
| Affected files | N |
| Test coverage | % |

### Migration Steps (if needed)
1. [Step 1]
2. [Step 2]

### Related
- Security advisory: [link]
- Changelog: [link]
```

---

## Skill Usage

### `/deps-update`

Check and update all dependencies.

### `/deps-update --security`

Focus on security patches only.

### `/deps-update react`

Update specific package.

### `/deps-update --dry-run`

Preview changes without applying.

### `/deps-update --major`

Include major version updates (risky).

### `/deps-update --audit`

Run full security audit.

---

## Tools Used

| Tool | Purpose |
|------|---------|
| `npm outdated` | Check for updates |
| `npm audit` | Security vulnerabilities |
| `npm update` | Apply updates |
| `npm-check` | Interactive update UI |
| `renovate` | Automated PRs (optional) |

---

## Integration with Other Skills

| Skill | Integration |
|-------|-------------|
| `/pr-creation` | Creates PR for risky updates |
| `/pr-review` | Reviews breaking changes |
| `/ci-fix` | Fixes any CI failures from update |
| `/security-audit` | (Future) Deeper security scanning |

---

## Open Questions

1. **Auto-update vs PR** — Which risk level auto-applies? (Recommendation: patch/minor safe, major always PR)

2. **Update frequency** — Weekly batch? Daily security checks? Monthly major?

3. **Lockfiles** — Always update lockfile? Sometimes rollback needed?

4. **Dependabot vs skill** — Use Dependabot for automated PRs? Or this skill replaces it?

5. **Multi-package repos** — How to handle monorepos with multiple package.json?

---

## Related Documents

- `docs/reports/pr-creation-skill-design-2026-04-28.md` — Update PR creation
- `docs/reports/ci-fix-monitor-skill-design-2026-04-28.md` — CI validation

---

*This document is a design for a future skill. The skill itself has not been created yet.*
