## Problem

Using inline object literals for Result types creates maintenance and performance issues that compound as the codebase grows.

## Why It Matters

- **Maintenance:** Every change to Result structure requires modifying every call site
- **Performance:** Object literals create new function closures per instance; classes use shared prototypes
- **Debugging:** No structured way to attach error context or metadata
- **Consistency:** Error handling patterns vary across modules

## Current vs Expected DX
```typescript
// Current DX - inline object literals
function fetchData(): { success: boolean; data?: any; error?: string } {
  if (something) return { success: true, data: result };
  return { success: false, error: "failed" }; // raw string
}
```

```typescript
// Expected DX - class-based Result
function fetchData(): Result<Data> {
  if (something) return Result.ok(result);
  return Result.fail(new Error("failed"));
}
```

## Current Situation

Currently, Result types are inline objects scattered across the codebase:

```typescript
// Result factory in builder.ts
function ok<T>(value: T): { success: true; data: T } {
  return { success: true, data: value };
}
```

Issues:
- Each `ok()` call creates new object and new closures
- No shared state or methods possible
- Adding features requires touching every call site

## What Would Change

Refactor to class-based Results with prototype methods:

```typescript
// Result classes - internal implementation
class Ok<T> {
  constructor(public readonly value: T) {}
  isOk(): this is Ok<T> { return true; }
  isErr(): this is Err { return false; }
}

class Err {
  constructor(public readonly error: Error, public readonly context?: Record<string, any>) {}
  isOk(): this is Ok<never> { return false; }
  isErr(): this is Err { return true; }
}

// Public API - unchanged
export function ok<T>(value: T): Ok<T> { return new Ok(value); }
export function err(error: Error, context?: Record<string, any>): Err { return new Err(error, context); }
```

**API Stability:** The public API (`ok()`, `err()`) remains unchanged. Only internal implementation changes.

## Tasks

- [ ] Create `Ok` and `Err` classes in `builder.ts`
- [ ] Move shared logic to common base if applicable
- [ ] Verify `Object.freeze` behavior is maintained (or document if deprecated)
- [ ] Ensure type inference unchanged for consumers
- [ ] Update all internal usages to use new classes
- [ ] Verify no `instanceof` checks are broken (internal only)

## Notes

- Memory efficiency improves significantly with large arrays of Results
- Prototype-based methods are shared across all instances
- No breaking changes for external consumers of `ok()` and `err()`