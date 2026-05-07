# Skills Inventory

This document lists the skills for the Project Rules system.

## Core Skills (On-Demand)

### `add-convention`

Add a new convention to `.context/project/rules/conventions/`.

**Triggers:** "add a rule about X", "document this practice", "we follow this convention"
**Template:** `skills/add-convention/TEMPLATE.md`

---

### `add-constraint`

Document a constraint (tech, business, or architecture limitation).

**Triggers:** "we must", "we can't", "due to X we have to Y"
**Template:** `skills/add-constraint/TEMPLATE.md`

---

### `add-pattern`

Document a recurring solution to a problem.

**Triggers:** "how we solve X in this project", "we always use this approach for Y"
**Template:** `skills/add-pattern/TEMPLATE.md`

---

### `add-methodology`

Document how work is done in the project.

**Triggers:** "how we do X in this team", "describe the process"
**Template:** `skills/add-methodology/TEMPLATE.md`

---

## Utility Skills

### `check-rules`

Check what rules, conventions, and constraints apply to the current work.

**Triggers:** Before starting a task, "what rules apply here?", "what's the convention for X?"

**Output:** List of relevant conventions, patterns, constraints with file links

---

### `list-rules`

List all rules in the project with a summary.

**Triggers:** "what rules do we have?", "show me all conventions", onboarding

**Output:** Organized summary by category (conventions, patterns, methodologies, constraints)

---

### `review-rules`

Review rules to identify gaps, contradictions, or outdated content.

**Triggers:** Periodic review, "review our rules", "audit our conventions"

**Checks:** Contradictions, alignment with project definition, gaps, stale content

---

## Skills Summary

| Skill | Type | Trigger |
|-------|------|---------|
| `add-convention` | On-demand | Add new convention |
| `add-constraint` | On-demand | Add limitation |
| `add-pattern` | On-demand | Add pattern |
| `add-methodology` | On-demand | Add process |
| `check-rules` | Utility | Before task, on question |
| `list-rules` | Utility | Onboarding, overview |
| `review-rules` | Utility | Periodic review |

---

## Location

All skills are in `skills/` directory:
```
skills/
├── add-convention/
│   ├── SKILL.md
│   └── TEMPLATE.md
├── add-constraint/
│   ├── SKILL.md
│   └── TEMPLATE.md
├── add-pattern/
│   ├── SKILL.md
│   └── TEMPLATE.md
├── add-methodology/
│   ├── SKILL.md
│   └── TEMPLATE.md
├── check-rules/SKILL.md
├── list-rules/SKILL.md
└── review-rules/SKILL.md
```