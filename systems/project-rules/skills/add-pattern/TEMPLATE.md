# Patterns Templates

## pattern-template.md

```markdown
---
created: 2024-01-15
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

[Specific situations where this pattern is appropriate]

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

## Usage

Copy this template when documenting a new pattern in:
- `.context/project/rules/patterns/[category]/[name].md`

## Common Pattern Categories

| Category | File Location |
|----------|--------------|
| API Patterns | `patterns/api/` |
| Component Patterns | `patterns/component/` |
| Data Patterns | `patterns/data/` |
| Testing Patterns | `patterns/testing/` |
| Architecture Patterns | `patterns/architecture/` |

## Example: Repository Pattern

```markdown
---
created: 2024-01-15
type: pattern
category: data
---

# Pattern: Repository Pattern

## Context

Need to access data from multiple sources (DB, API, cache) with a unified interface.

## Solution

Create a repository class that abstracts data access:

```typescript
interface UserRepository {
  findById(id: string): Promise<Result<User, NotFoundError>>;
  findAll(filter: UserFilter): Promise<Result<User[], DbError>>;
  create(data: CreateUserInput): Promise<Result<User, ValidationError>>;
  update(id: string, data: UpdateUserInput): Promise<Result<User, ValidationError>>;
  delete(id: string): Promise<Result<void, NotFoundError>>;
}
```

## When to Use

### ✅ Good to use when:
- Multiple data sources exist or might exist
- Business logic needs to be isolated from data access
- Testing requires mocking data layer

### ❌ Don't use when:
- Simple CRUD that won't have multiple sources
- Performance-critical code (adds indirection)

## Related Patterns

- Unit of Work (for transaction management)
- Factory (for creating repositories)

## Related Conventions

- [[error-handling-result-type]]
- [[api-design-guidelines]]