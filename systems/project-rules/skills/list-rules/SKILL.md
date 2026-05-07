---
name: list-rules
description: List all rules, conventions, and constraints in the project. Use when user asks "what rules do we have?" or for onboarding.
---

# List Rules

List all project rules organized by category.

## When to Use

- User asks "what rules do we have?" or "show me all conventions"
- Onboarding a new team member
- Getting overview of project rules

## Process

### Step 1: Scan Rules Directory

```bash
Glob: ".context/project/rules/conventions/*.md"
Glob: ".context/project/rules/patterns/**/*.md"
Glob: ".context/project/rules/methodologies/**/*.md"
Glob: ".context/project/rules/constraints/*.md"
```

### Step 2: Read Each File

Extract frontmatter to get type, category, description.

### Step 3: Generate Summary

Organize by category with counts:

```markdown
## Project Rules Summary

### Conventions (8)
| Rule | Domain | File |
|------|--------|------|
| Error Handling | code | [[conventions/error-handling.md]] |
| API Design | api | [[conventions/api-design.md]] |
| Git Workflow | git | [[conventions/git-workflow.md]] |
| ... | ... | ... |

### Patterns (5)
| Pattern | Category | File |
|---------|----------|------|
| Repository | data | [[patterns/data/repository.md]] |
| Result Wrapper | api | [[patterns/api/result-wrapper.md]] |
| ... | ... | ... |

### Methodologies (3)
| Methodology | Category | File |
|-------------|----------|------|
| How We Work | workflow | [[methodologies/workflow/how-we-work.md]] |
| Decision Process | process | [[methodologies/process/decision-process.md]] |
| ... | ... | ... |

### Constraints (4)
| Constraint | Type | Enforced |
|------------|------|----------|
| No Classes for End Users | architecture | ⚠️ Yes |
| Must Use Result Type | tech | ⚠️ Yes |
| ... | ... | ... |

### Total: 20 rules documented
```

## Update README

Optionally update `.context/project/rules/README.md` with this summary.

## Onboarding Use Case

When onboarding a new developer, this skill provides the complete picture of how the project works.

Suggest reading in order:
1. [[methodologies/onboarding/]] — If exists
2. [[conventions/git-workflow.md]] — How to work with git
3. [[constraints/]] — Hard constraints
4. [[conventions/error-handling.md]] — How to handle errors
5. [[patterns/]] — Common solutions

## Confirmation

```
Listed [N] conventions, [N] patterns, [N] methodologies, [N] constraints.
Use /check-rules to see rules relevant to your current task.
```