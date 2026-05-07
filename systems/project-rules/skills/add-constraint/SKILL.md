---
name: add-constraint
description: Document a constraint (tech, business, or architecture limitation). Use when user says "we must", "we can't", "due to X we have to Y".
---

# Add Constraint

Document a constraint in `.context/project/rules/constraints/`.

## When to Use

- User says "we must do X" or "we can't do Y"
- User mentions compliance requirements, tech limitations
- Agent identifies an architectural or technical constraint

## Process

### Step 1: Classify Constraint Type

| Type | Description |
|------|-------------|
| `tech` | Can't use certain technologies (e.g., must use Result type) |
| `business` | Compliance, regulatory, contractual requirements |
| `architecture` | Must use specific patterns (e.g., no classes for end users) |

### Step 2: Check for Duplicates

```bash
Grep pattern="[keyword]" path=".context/project/rules/constraints"
```

### Step 3: Create Constraint File

Use this template: `./TEMPLATE.md`

```markdown
---
created: $DATE
type: constraint
category: [tech|business|architecture]
enforced: [yes|no|partial]
---

# Constraint: [Short Title]

## Statement

[One sentence: the actual constraint]

## Reason

[Why this constraint exists]

## Impact

[How this constraint affects development]

## Mitigation

[How to work within this constraint]

## Exceptions

[When this constraint might not apply, if any]
```

### Step 4: Update README

Add to `.context/project/rules/README.md` constraints section.

## Constraint Examples

| Statement | Category |
|-----------|----------|
| "Never expose classes to end users" | architecture |
| "Must use `Result` type from `@deessejs/fp`" | tech |
| "User data must be deleted after 24 months" | business |

## Confirmation

After adding:

```
✓ Added constraint: [title]
  → Location: .context/project/rules/constraints/[file].md
  → Category: [tech|business|architecture]
  → Enforced: [yes|no|partial]
```