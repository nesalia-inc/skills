# Skills Inventory

This document lists the skills required for the Latest Version Index system. Content is not yet written.

---

## Core Skills

### `add-package`

**Role:** Add a new package to the latest-version-index.

**Triggers:**
- User asks to "add package X to index" or "track X"
- Agent discovers a package not in index but needs to track it

**Files involved:**
- `packages.json` — The curated package list

**Responsibilities:**
- Query npm for current version using npm-lookup
- Research release blog for maturity assessment
- Create full package entry with analysis
- Update `packages.json`
- Set `updated` date

**Type:** On-demand skill, invoked when adding new package to track

---

### `update-package`

**Role:** Update an existing package entry when a new version is released.

**Triggers:**
- Agent uses npm-lookup and finds newer version than in index
- User asks to "update package X"
- Periodic review (all packages)

**Files involved:**
- `packages.json` — The curated package list

**Responsibilities:**
- Query npm for latest version
- Research release blog for changes
- Assess stability/maturity changes
- Update package entry
- Preserve analysis style consistency

**Type:** Utility skill, invoked on new version discovery or on demand

---

### `check-package`

**Role:** Check a package's status in the index and compare with npm.

**Triggers:**
- User asks "what's the status of X?" or "is X up to date?"
- Before recommending a package

**Files involved:**
- `packages.json` — The curated package list
- npm registry (via npm-lookup)

**Responsibilities:**
- Return full package record from index
- Compare with npm latest
- Report if index is stale
- Offer to update if newer version exists

**Output format:**
```markdown
**Package:** zod
**Index Version:** 4.2.0
**NPM Latest:** 4.2.1 (newer!)
**Age:** 7 months
**Stability:** stable
**Production Ready:** true
**Analysis:** "..."

⚠️ Index is 1 version behind. Update recommended.
```

**Type:** Utility skill, invoked on demand

---

## Skills Summary Table

| Skill | Role | Type | Trigger |
|-------|------|------|---------|
| `add-package` | Add new package to index | On-demand | User request or first use |
| `update-package` | Update existing package entry | Utility | New version found or user request |
| `check-package` | Check package status | Utility | User question or before recommendation |

---

## Skills Hierarchy

```
latest-version-index
    │
    ├── add-package (add new package to tracking)
    ├── update-package (update existing package)
    └── check-package (lookup package status)
              │
              └── Uses: npm-lookup (from research-first system)
```

---

## Integration with Other Systems

| System | How it uses latest-version-index |
|--------|----------------------------------|
| **Research-First Protocol** | Check version before using package |
| **Tech Radar** | Verify versions when checking tech status |
| **Project Context** | Agent uses to understand project stack versions |

---

## Implementation Priority

1. **check-package** — Simplest, most used (verify before recommendation)
2. **add-package** — When we need to track new package
3. **update-package** — When versions change