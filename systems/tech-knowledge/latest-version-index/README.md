# Latest Version Index

**Purpose:** Track versions and maturity of packages we care about. Unlike npm-lookup (which queries on-demand), this index is a curated reference that gets updated when versions change.

## Concept

This index tracks:
- **Package name** — The npm package name
- **Current version** — Version we're tracking
- **Released** — When it was released (date + relative age)
- **Stability** — stable / beta / experimental / deprecated
- **Production ready** — Yes/No with justification
- **Analysis** — What the release says, community feedback, maturity assessment
- **Changelog** — Link to release notes
- **Updated** — When we last updated this record

## Why This Matters

The agent might think "React 19 is too new" or "Next 15 is beta" because of training data. This index tells the truth:

```
Training data might say: "React 19 is experimental"
Reality: React 19 released 9 months ago, stable, production-ready
```

## Format

```yaml
packages:
  - name: react
    currentVersion: 19.0.0
    released: 2024-11-15
    age: "9 months"
    stability: stable
    productionReady: true
    analysis: |
      React 19 has been out for 9 months. Server Components
      are now production-ready. Next.js 15 fully supports it.
      Breaking changes from v18 are minimal.
    url: https://react.dev/blog

  - name: next
    currentVersion: 15.2.0
    released: 2025-01-20
    age: "3 months"
    stability: stable
    productionReady: true
    analysis: |
      Next 15 released with React 19 support. App Router
      is now the default. Some edge cases remain but stable.
    url: https://nextjs.org/blog/category/releases

  - name: zod
    currentVersion: 4.2.1
    released: 2024-10-01
    age: "7 months"
    stability: stable
    productionReady: true
    analysis: |
      Zod v4 is stable and widely adopted. Type inference
      improvements are significant. Migration from v3 is smooth.
    url: https://github.com/colinhacks/zod/releases
```

## Why Not Just Use NPM Lookup?

NPM Lookup gives you the version right now. This index gives you:
- **Maturity context** — Age, stability, production status
- **Analysis** — What the releases actually say
- **Curated** — Only packages we care about are listed
- **Stability assessment** — "stable" vs "beta" vs "experimental"
- **Audit trail** — Know what we were targeting and when

## Structure

```
latest-version-index/
├── README.md          # This file
├── packages.json       # Curated list of tracked packages
└── scripts/            # Optional: update-check script
```

## Usage Flow

```
Agent needs to know about Package X
         │
         ▼
┌─────────────────────────────────────┐
│  1. Check latest-version-index      │
│     (do we have a record?)         │
└──────────────────┬──────────────────┘
                   │
          ┌────────┴────────┐
          │                 │
     Yes  │                 │  No
          ▼                 ▼
┌─────────────────┐  ┌─────────────────────────────────┐
│  Return full    │  │  2. Use npm-lookup              │
│  record with    │  │     (query npm for latest)      │
│  analysis       │  └──────────────────┬──────────────────┘
└─────────────────┘                    │
                                        ▼
                              ┌─────────────────────────────────┐
                              │  Research release blog          │
                              │  Assess stability/maturity      │
                              │  Create analysis entry           │
                              └─────────────────────────────────┘
```

## Analysis Creation

When creating an analysis entry, the agent should:
1. Read the release blog/changelog
2. Check community feedback (GitHub issues, Reddit)
3. Assess production readiness
4. Write a 2-3 sentence analysis

### Analysis Template

```markdown
analysis: |
  [Package] [version] [released X months ago].
  [Key change from previous version].
  [Production status and recommendation].
```

## Update Strategy

| When | Action |
|------|--------|
| Agent uses npm-lookup | Compare with index, offer to update if different |
| New version released | Research new release, update analysis |
| Package not in index | Add on first use with full analysis |
| Periodic review | Verify all records still accurate |

## Key Distinction

| System | Purpose | Output |
|--------|---------|--------|
| **npm-lookup** | Get actual version from npm | "4.2.1" |
| **latest-version-index** | Get maturity context | Version + age + stability + analysis |

## Integration

```
latest-version-index/
         │
         ├──→ Used by: Research-First (version verification)
         │
         └──→ Updated by: Agent after npm-lookup + release blog analysis
```

## TODO

- [x] Define package tracking format with analysis
- [ ] Create initial package list with full analysis
- [ ] Define update mechanism
- [ ] Create packages.json structure
- [ ] Define analysis template for agents