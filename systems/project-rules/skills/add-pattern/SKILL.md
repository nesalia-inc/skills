---
name: add-pattern
description: Document a recurring solution to a problem. Use when user describes "how we solve X in this project" or "we always use this approach for Y".
---

# Add Pattern

Document a pattern in `.context/project/rules/patterns/`.

## When to Use

- User describes a common solution to a recurring problem
- User says "when we do X, we follow this approach"
- Agent identifies a repeated solution pattern

## Pattern Categories

| Category | Description |
|----------|-------------|
| `api` | API design patterns, endpoint structures |
| `component` | UI component patterns |
| `data` | Data access, caching, state patterns |
| `testing` | Test organization, mocking patterns |
| `architecture` | System-level patterns |

## Process

### Step 1: Identify Pattern Category

Determine where to place the pattern.

### Step 2: Check for Duplicates

```bash
Grep pattern="[pattern name]" path=".context/project/rules/patterns"
```

### Step 3: Create Pattern File

Use this template: `./TEMPLATE.md`

```markdown
---
created: $DATE
type: pattern
category: [api|component|data|testing|architecture]
---

# Pattern: [Pattern Name]

## Context

[When does this pattern apply? What problem does it solve?]

## Solution

[How to implement this pattern]

## Structure

```[language]
[code structure showing the pattern]
```

## When to Use

### ✅ Good to use when:
- [Condition 1]
- [Condition 2]

### ❌ Don't use when:
- [Condition 1]
- [Condition 2]

## Related Patterns

- [[related-pattern-1]]
- [[related-pattern-2]]

## Related Conventions

- [[convention-name]]
```

### Step 4: Update README

Add to `.context/project/rules/README.md` patterns section.

## Pattern Examples

| Pattern | Category | Purpose |
|---------|----------|---------|
| Repository Pattern | data | Abstract data access behind interface |
| Result Wrapper | api | Consistent error response format |
| Component Composition | component | Build complex UIs from simple parts |

## Confirmation

After adding:

```
✓ Added pattern: [title]
  → Location: .context/project/rules/patterns/[category]/[file].md
  → Category: [category]
```