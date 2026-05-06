---
name: add-convention
description: Add a new convention to the project rules. Use when user says "add a rule about X", "document this practice", "we always do X".
---

# Add Convention

Add a new convention to `.context/project/rules/conventions/`.

## When to Use

- User says "add a rule about X" or "we follow this practice"
- User explains how something is done in the project
- Agent identifies a convention that should be documented

## Process

### Step 1: Identify Convention Type

Determine the domain:
- `api` — API design, response formats
- `code` — Code style, error handling
- `git` — Commit messages, branch naming
- `docs` — Documentation format
- `naming` — File/variable naming conventions
- `architecture` — System design rules

### Step 2: Check for Duplicates

Search for existing similar conventions:

```bash
Grep pattern="[keyword]" path=".context/project/rules/conventions"
```

### Step 3: Create Convention File

Use `../../templates/convention-template.md` format:

```markdown
---
created: $DATE
type: convention
domain: [domain]
---

# Convention: [Short Title]

## Rule

[One sentence: the actual rule]

## Details

[Extended explanation of why this rule exists]

## Examples

### ✅ Correct
```[language]
[correct code or practice]
```

### ❌ Incorrect
```[language]
[incorrect code or practice]
```

## Enforcement

[How this rule is enforced - linting, code review, etc.]
```

### Step 4: Update README

Add to `.context/project/rules/README.md` table of contents.

## Convention Domains

| Domain | File Location |
|--------|--------------|
| api | `conventions/api-design.md` |
| code | `conventions/error-handling.md`, `conventions/typescript.md` |
| git | `conventions/git-workflow.md` |
| naming | `conventions/naming.md` |
| architecture | `conventions/architecture.md` |

## Confirmation

After adding:

```
✓ Added convention: [title]
  → Location: .context/project/rules/conventions/[file].md
  → Domain: [domain]
```