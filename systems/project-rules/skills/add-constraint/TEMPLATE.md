# Constraints Templates

## constraint-template.md

```markdown
---
created: 2024-01-15
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

## Usage

Copy this template when documenting a constraint in:
- `.context/project/rules/constraints/[category]/[name].md`

## Common Constraint Categories

| Category | Description |
|----------|-------------|
| Tech Constraints | Can't use certain technologies or patterns |
| Business Constraints | Regulatory, compliance, contractual requirements |
| Architecture Constraints | Must use specific architectural approaches |

## Example: No Classes for End Users

```markdown
---
created: 2024-01-15
type: constraint
category: architecture
enforced: yes
---

# Constraint: Never Expose Classes to End Users

## Statement

API endpoints MUST NOT return class instances. Always return plain objects or DTOs.

## Reason

- End users may be in different languages/frameworks
- Class serialization is complex and error-prone
- Decouples API from internal implementation

## Impact

- All API responses use plain objects
- Repository methods return DTOs, not entities
- Mapping layer exists between internal and external models

## Mitigation

- Use DTO pattern for all API boundaries
- Create mapper functions for entity ↔ DTO conversion
- Document in [[api-design-guidelines]]

## Exceptions

None — this is a hard architectural constraint.
```

## Example: Must Use Result Type

```markdown
---
created: 2024-01-15
type: constraint
category: tech
enforced: yes
---

# Constraint: Must Use Result Type for Error Handling

## Statement

All functions that can fail MUST return `Result<T, E>` from `@deessejs/fp`.

## Reason

- Consistent error handling across codebase
- Type-safe error propagation
- Forces explicit error handling at call sites

## Impact

- No `throw` for expected failures
- No `try/catch` for business logic
- All error paths are explicit in return type

## Mitigation

- Use `@deessejs/fp` library
- ESLint rule prevents `throw`
- Code review checks for Result usage

## Exceptions

- Third-party libraries that throw (wrap in try/catch at boundary)
- Internal utilities may throw (but wrap before exposing)
```

## Example: Data Retention Policy

```markdown
---
created: 2024-01-15
type: constraint
category: business
enforced: yes
---

# Constraint: User Data Retention Policy

## Statement

User data must be deleted after 24 months of inactivity per GDPR compliance.

## Reason

- GDPR compliance requirement
- Data minimization principle

## Impact

- Need to track last activity date
- Automated cleanup job required
- User must be notified before deletion

## Mitigation

- Add `lastActivityAt` to user entity
- Create cron job to identify inactive users
- Send notification email 30 days before deletion
- Delete after notification period

## Exceptions

- Users with active subscriptions (never delete)
- Users who have requested data export (pause deletion)
```