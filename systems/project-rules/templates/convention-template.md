# Conventions Templates

## convention-template.md

```markdown
---
created: 2024-01-15
type: convention
domain: [api|code|git|docs|naming|architecture]
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

## Usage

Copy this template when adding a new convention to:
- `.context/project/rules/conventions/[name].md`

## Common Convention Types

| Domain | File Name Example |
|--------|-------------------|
| API Design | `api-design.md` |
| Error Handling | `error-handling.md` |
| Git Workflow | `git-workflow.md` |
| Code Style | `code-style.md` |
| Naming | `naming.md` |
| Documentation | `documentation.md` |
| Testing | `testing.md` |
| TypeScript | `typescript.md` |

## Example: Error Handling Convention

```markdown
---
created: 2024-01-15
type: convention
domain: code
---

# Convention: Error Handling with Result Type

## Rule

All functions that can fail MUST return `Result<T, E>` from `@deessejs/fp`.

## Details

Never throw exceptions for expected failures. Use the Result type for:
- Network failures
- Validation errors
- Not found errors
- Permission denied

## Examples

### ✅ Correct
```typescript
function parseUser(input: unknown): Result<User, ValidationError> {
  // return Ok(user) or Err(validationError)
}

async function fetchUser(id: string): ResultAsync<User, ApiError> {
  // return OkAsync(user) or ErrAsync(apiError)
}
```

### ❌ Incorrect
```typescript
// Throwing for expected failures
function parseUser(input: unknown): User {
  throw new Error('Invalid input'); // BAD
}
```

## Enforcement

- ESLint rule: `no-throw-in-sync`
- Code review requirement
- Tests must cover error paths