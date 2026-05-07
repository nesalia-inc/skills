---
name: check-rules
description: Check what rules, conventions, and constraints apply to the current work. Use before starting a task or when user asks "what rules apply here?".
---

# Check Rules

Check which rules apply to the current task or work context.

## When to Use

- Before starting a new implementation task
- User asks "what's the convention for X?" or "what rules apply here?"
- Agent needs to know applicable constraints before proceeding

## Process

### Step 1: Identify the Task Context

What type of work is being done?
- Writing API endpoint → Check api conventions + error handling
- Building UI component → Check component patterns
- Writing tests → Check testing patterns
- Making architectural decision → Check constraints + patterns

### Step 2: Scan Relevant Rules

```bash
Glob: ".context/project/rules/conventions/*.md"
Glob: ".context/project/rules/patterns/**/*.md"
Glob: ".context/project/rules/constraints/*.md"
```

### Step 3: Return Applicable Rules

Group by category and highlight warnings:

```markdown
## Applicable Rules for [Task]

### Conventions
- [[conventions/error-handling.md]] — Use Result type for errors
- [[conventions/api-design.md]] — RESTful endpoint structure

### Patterns
- [[patterns/repository.md]] — Use repository pattern for data access

### Constraints
⚠️ [[constraints/no-classes-end-users.md]] — Never return class instances to API

### Action Required
Before implementing, read [[conventions/error-handling.md]] to ensure proper Result type usage.
```

## Rule Lookup by Task

| Task | Check These Rules |
|------|------------------|
| API endpoint | conventions/api-design, conventions/error-handling, constraints/* |
| UI Component | patterns/component, conventions/naming |
| Data access | patterns/repository, patterns/data-fetching |
| Testing | patterns/testing, conventions/code-style |
| Architecture | constraints/architecture, patterns/architecture |

## Important Constraints to Always Check

- [[constraints/no-classes-end-users.md]] — Architecture
- [[constraints/result-type-required.md]] — Tech (error handling)

## If No Matching Rules Found

```
No specific rules found for [task type].

Consider adding rules if this is a recurring situation.
Use /add-convention, /add-pattern, or /add-constraint.
```

## Output Format

Always include:
1. List of relevant conventions (with file links)
2. List of relevant patterns (with file links)
3. Any constraints that affect the work (⚠️ prefix)
4. Recommended action before starting