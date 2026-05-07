---
name: add-methodology
description: Document how work is done in the project. Use when user explains "how we do X in this team" or describes a process.
---

# Add Methodology

Document a methodology in `.context/project/rules/methodologies/`.

## When to Use

- User explains a team process or workflow
- User describes "how things work around here"
- New process is introduced

## Methodology Categories

| Category | Description |
|----------|-------------|
| `workflow` | Sprint planning, standups, code review |
| `process` | Decision-making, planning, retrospectives |
| `onboarding` | Getting new team members up to speed |
| `communication` | How the team communicates |

## Process

### Step 1: Identify Category

Determine if it's workflow, process, onboarding, or communication.

### Step 2: Check for Duplicates

```bash
Grep pattern="[methodology name]" path=".context/project/rules/methodologies"
```

### Step 3: Create Methodology File

Use this template: `./TEMPLATE.md`

```markdown
---
created: $DATE
type: methodology
category: [workflow|process|onboarding|communication]
---

# Methodology: [Title]

## Overview

[One paragraph: what this methodology is about]

## Principles

1. [Principle 1]
2. [Principle 2]
3. [Principle 3]

## Process

### Step 1: [Name]
[Description of the step]

### Step 2: [Name]
[Description of the step]

### Step 3: [Name]
[Description of the step]

## When to Apply

[In what situations this methodology applies]

## Examples

### Example 1: [Brief title]
[Description]

## Related Methodologies

- [[related-methodology-1]]
```

### Step 4: Update README

Add to `.context/project/rules/README.md` methodologies section.

## Methodology Examples

| Methodology | Category | Purpose |
|-------------|----------|---------|
| How We Work | workflow | Daily/weekly team workflows |
| Decision Process | process | How major decisions are made |
| Onboarding Guide | onboarding | Getting new developers up to speed |

## Confirmation

After adding:

```
✓ Added methodology: [title]
  → Location: .context/project/rules/methodologies/[category]/[file].md
  → Category: [category]
```