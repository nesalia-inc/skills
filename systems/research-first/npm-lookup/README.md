# NPM Lookup

**Purpose:** Query npm registry to get the latest versions of packages on demand.

## Concept

When the agent needs to know the latest version of a package, it can use the NPM Lookup system to query npm's registry directly.

This is part of the **Research-First Protocol** — verify versions before recommending or using packages.

## Usage

```
Agent needs to know latest version of a package
         │
         ▼
┌─────────────────────────────────────┐
│  NPM Lookup                          │
│  Query: npm view <package> version  │
│  Returns: latest version + metadata  │
└─────────────────────────────────────┘
```

## How It Works

Use `npm view` command to query the npm registry:

```bash
npm view <package> version      # Latest version
npm view <package> versions     # All versions
npm view <package> dist-tags    # Distribution tags (latest, next, beta)
```

### Examples

```bash
# Get latest version
npm view zod version
# Output: 4.2.1

# Get all versions
npm view next versions
# Output: 14.0.0, 14.0.1, 14.1.0, 14.2.0, 15.0.0...

# Get dist-tags (latest, next, beta)
npm view zod dist-tags
# Output: { latest: '4.2.1', beta: '5.0.0-beta.1' }
```

## Integration with Latest Version Index

```
NPM Lookup
    │
    ├──→ Used by: Research-First Protocol (version verification)
    │
    └──→ Can update: ../tech-knowledge/latest-version-index/
         When new version detected, offer to update index
```

## On-Demand vs Auto-Update

| Mode | Trigger | Action |
|------|---------|--------|
| **On-demand** | Agent needs to know version | Query npm, return result |
| **Offer to update** | Newer version found than in index | Offer to update latest-version-index |

## Skill

### `npm-lookup` Skill

```yaml
---
name: npm-lookup
description: Look up the latest version of a package from npm registry
---

# NPM Lookup

When asked about a package's version, use the npm registry to get accurate information.

## Commands

```bash
# Latest stable version
npm view <package> version

# All available versions
npm view <package> versions

# Release date of latest version
npm view <package> time

# Peer dependencies
npm view <package> peerDependencies

# Package info (description, repository, etc.)
npm view <package>
```

## Output Format

When returning version info:

```markdown
**Package:** zod
**Latest:** 4.2.1
**Released:** 2024-01-15
**Dist-tags:** { latest: '4.2.1', beta: '5.0.0-beta.1' }
**Repository:** https://github.com/colinhacks/zod
```

## When to Use

- Before recommending a package version
- When user asks "what's the latest version of X?"
- When training docs mention a version, verify it's still current
- Before installing a package, verify version

## Integration with Other Systems

| System | How it uses NPM Lookup |
|--------|----------------------|
| **Research-First Protocol** | Verify versions before using packages |
| **Latest Version Index** | On-demand lookup to update index |
| **Tech Radar** | Verify versions when checking tech status |

## TODO

- [x] Create npm-lookup subsystem
- [ ] Define skill for npm lookup
- [ ] Define output format
- [ ] Create update-index integration