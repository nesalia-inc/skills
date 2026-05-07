# Methodologies Templates

## methodology-template.md

```markdown
---
created: 2024-01-15
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

### Example 2: [Brief title]
[Description]

## Related Methodologies

- [[related-methodology-1]]
- [[related-methodology-2]]
```

## Usage

Copy this template when documenting a new methodology in:
- `.context/project/rules/methodologies/[category]/[name].md`

## Common Methodology Categories

| Category | Description |
|----------|-------------|
| Workflow | How work gets done (sprints, code review, etc.) |
| Process | Decision-making, planning, retrospectives |
| Onboarding | Getting new team members up to speed |
| Communication | How the team communicates |

## Example: How We Work

```markdown
---
created: 2024-01-15
type: methodology
category: workflow
---

# Methodology: How We Work

## Overview

Our team works in 2-week sprints with weekly reviews and daily standups.

## Principles

1. **Async by default** — Slack for questions, GitHub for discussions
2. **PRs for everything** — All changes go through PR review
3. **Test before merge** — CI must pass before merge
4. **Document decisions** — Use ADRs for architectural decisions

## Process

### Sprint Planning (Day 1)
1. Review previous sprint
2. Groom backlog
3. Assign tickets

### Daily Standup (Every day, 9:30 AM)
1. What did I do yesterday?
2. What will I do today?
3. Any blockers?

### Sprint Review (Day 10)
1. Demo completed work
2. Get stakeholder feedback
3. Plan next sprint

### Retrospective (Day 10, after review)
1. What went well?
2. What could be better?
3. Action items for next sprint

## Related Methodologies

- [[decision-process]]
- [[onboarding]]
```

## Example: Decision Process

```markdown
---
created: 2024-01-15
type: methodology
category: process
---

# Methodology: Decision Process

## Overview

Major decisions are made collaboratively with documentation.

## Principles

1. **Scope determines process** — Small decisions: PR discussion. Medium: team meeting. Large: RFC.
2. **Document the why** — ADRs for architectural decisions
3. **Revisit if wrong** — It's okay to change decisions with new info

## Process

### Small Decision (< 1 day)
1. Discuss in PR
2. Make decision
3. Update relevant docs

### Medium Decision (1-3 days)
1. Team discussion
2. Decision documented in PR/issue
3. Implement

### Large Decision (Architectural)
1. Create RFC document
2. Team review (1 week)
3. Decision made
4. ADR created
5. Implement

## Related Methodologies

- [[how-we-work]]
```