# ADR Creation Process

Architecture Decision Records (ADRs) document significant technical choices and their rationale. This document describes the process for creating, managing, and using ADRs within the Project Context System.

---

## When to Create an ADR

An ADR should be created when:

| Situation | Example |
|-----------|---------|
| **Technology choice** | "We chose PostgreSQL over MongoDB for this service" |
| **Architecture pattern** | "We use event-driven architecture for async processing" |
| **Framework/library selection** | "We use React Query instead of Redux for server state" |
| **Design pattern** | "We follow clean architecture with dependency injection" |
| **Infrastructure decision** | "We deploy to Kubernetes rather than AWS Lambda" |
| **Cross-cutting concern** | "We use JWT for authentication across services" |
| **Code organization** | "We group code by feature, not by type" |
| **Process/Workflow** | "We use trunk-based development with short-lived branches" |

### ADR Threshold

Not every decision needs an ADR. Create one when:

1. The decision affects multiple files/services
2. The choice is non-obvious (there were alternatives)
3. The decision would be hard to reverse
4. New team members would need to understand the reasoning

**Rule:** If you need to say "we chose X because..." more than once, document it in an ADR.

---

## ADR Creation Workflow

```
[Decision Made]
      │
      ▼
┌─────────────────────┐
│  Identify Need       │ → Is this significant enough for an ADR?
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│  Find Next Number   │ → Check existing ADRs in [[decisions/]]
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│  Create ADR File     │ → Use template from [[project/rules/conventions/]]
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│  Link from Context   │ → Update [[map.md]] and relevant docs
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│  Use in Future       │ → Reference when questioning the choice
└─────────────────────┘
```

---

## Naming Convention

### Filename Format

```
0001-short-descriptive-title.md
```

Rules:
- Sequential numbering (4 digits, zero-padded)
- Lowercase kebab-case
- Max 60 characters
- No spaces or special characters

### Examples

| Good | Bad |
|------|-----|
| `0001-use-postgresql-for-primary-db.md` | `ADR-1-Postgres.md` |
| `0002-adopt-event-driven-architecture.md` | `0002_Events.docx` |
| `0015-migrate-to-graphql.md` | `graphql decision.md` |

### ADR ID in Content

```markdown
# ADR 0015: Migrate to GraphQL
```

The ID in the title should match the filename number.

---

## ADR Template

Located in `[[project/rules/conventions/]]` (or use skill `/add-convention`):

```markdown
---
created: 2024-01-15
---

# ADR [ID]: [Title]

## Context

[Problem being solved or why this decision was needed.
What was the situation? What constraints existed?]

## Decision

[What was chosen and why.
Be specific about the solution adopted.]

## Alternatives Considered

[What other options were evaluated?
Why were they rejected?]

## Consequences

### Positive
[Benefits of this choice]

### Negative
[Trade-offs or risks introduced]

### Neutral
[Things that will need to be considered but aren't strictly good or bad]

---

## Metadata (Optional)

- **Status:** Accepted | Deprecated | Superseded
- **Superseded by:** [ADR ID if applicable]
- **Decided by:** [Team/Person]
- **Date:** [YYYY-MM-DD]
```

---

## Workflow Steps

### Step 1: Detect the Decision

The agent (or human) makes a significant technical choice:

```
Agent: "I'll use Redis for session caching because it's faster than in-memory."
```

### Step 2: Check Existing ADRs

Before creating, check if a related ADR already exists:

```
Agent: Should I create an ADR for Redis caching?
  → Check [[decisions/*.md]] for related decisions
  → Check [[stack.md]] for caching strategy
```

### Step 3: Determine ADR Number

List existing ADRs to find the next number:

```bash
# In .context/project/definition/decisions/
ls -1 *.md | sort
# Output:
# 0001-use-postgresql-for-primary-db.md
# 0002-adopt-event-driven-architecture.md
# ...
# Next number: 0015
```

### Step 4: Create the ADR

Create `0015-use-redis-for-session-caching.md`:

```markdown
---
created: 2024-01-15
---

# ADR 0015: Use Redis for Session Caching

## Context

The application needs to handle 10k concurrent users. Session data
is accessed on every request, and database queries for session
retrieval are creating a bottleneck.

## Decision

We will use Redis as a distributed cache for session data.

## Alternatives Considered

- **In-memory cache (Node.js Map):** Rejected because it doesn't
  scale across multiple instances and causes session loss during restarts.
- **Memcached:** Rejected because Redis offers better persistence
  and more advanced data structures.

## Consequences

### Positive
- Sub-millisecond session retrieval
- Horizontal scaling support
- Session persistence across restarts

### Negative
- Additional infrastructure (Redis server)
- Network latency for cache operations
- Cache invalidation complexity
```

### Step 5: Link from Relevant Context

Update other context files to reference the new ADR:

In `[[stack.md]]`:
```markdown
## Caching
- Redis for session caching [[decisions/0015-use-redis-for-session-caching.md]]
```

In `[[architecture/data-flow.md]]`:
```markdown
## Session Flow
1. User authenticates → session stored in Redis [[decisions/0015-use-redis-for-session-caching.md]]
```

### Step 6: Update map.md

Add the new ADR to the files table:

```markdown
| [[project/definition/decisions/0015-use-redis-for-session-caching.md]] | Redis caching decision | Before adding caching layer |
```

---

## ADR States

ADRs can have the following states:

| Status | Meaning | When to Use |
|--------|---------|-------------|
| **Accepted** | Current, active decision | Default state when created |
| **Deprecated** | No longer followed | When practice has changed but ADR kept for history |
| **Superseded** | Replaced by another ADR | When a new ADR replaces this decision |

### Changing ADR State

To deprecate or supersede an ADR:

```markdown
## Metadata

- **Status:** Deprecated
- **Superseded by:** 0023-use-cluster-redis.md
- **Date:** 2024-06-01
```

---

## Using ADRs

### Before Proposing Changes

When the user or agent wants to change something:

```
User: "Shouldn't we use MongoDB instead of PostgreSQL? It's more flexible."

Agent:
1. Check [[decisions/0001-use-postgresql-for-primary-db.md]]
2. Read ADR content
3. Respond: "ADR 0001 explains why PostgreSQL was chosen: [summary].
   Changing would require creating a new ADR to supersede it.
   Do you want to proceed with that?"
```

### Finding Related ADRs

To find which ADR covers a topic:

```markdown
# Search for keywords in .context/project/definition/decisions/

# Example: Find ADR about caching
grep -l "cache" .context/project/definition/decisions/*.md
# Output: 0015-use-redis-for-session-caching.md
```

### ADR Review Process

Before any refactoring that touches an ADR-covered topic:

1. Read the relevant ADR(s)
2. Understand the original context and constraints
3. If proposing change, create a new ADR that supersedes the old one
4. Never delete old ADRs — preserve history

---

## ADR Creation Entry Points

Per the Entry Points system:

| Trigger | Action |
|---------|--------|
| **Major technical decision** | Create ADR to document rationale |
| **Before proposing tech/stack changes** | Read related ADRs first |
| **On task completion** | If significant decision made, create ADR |

---

## Skills Involved

| Skill | Role |
|-------|------|
| `create-adr` | Create new ADR files |
| `review-adr` | Read and summarize ADRs before changes |
| `project-context` | Enforce ADR check before tech proposals |
| `context-update` | Update related context files when ADR created |

---

## Integration with Entry Points

From the Entry Points document, Rule 5 states:

> "If you want to replace a library, pattern, or approach, first read the related ADR in [[decisions/]]. Only propose changes after understanding the original reasoning."

This means the agent must:

1. **Detect** the proposal (user or agent wants to change something)
2. **Search** for relevant ADR(s)
3. **Read and understand** the ADR
4. **Either:** Accept the ADR reasoning and not propose changes, **or:** Propose a new ADR that supersedes the old one

---

## Example Full Flow

```
User: "Let's use GraphQL instead of REST for the user API."

Agent Response:
────────────────────────────────────────────────────────

[Trigger detected: Tech change proposal]

1. Searching for existing REST-related ADRs...
   Found: [[decisions/0008-use-rest-for-simplicity.md]]

2. Reading ADR:
   "Context: Small team, need fast delivery, REST chosen for
   simplicity and lower learning curve."

3. Context Handshake:
   ┌─────────────────────────────────────────────────────┐
   │ ADR 0008 detected: REST for simplicity              │
   │ Status: Accepted                                    │
   │ Key reasoning: Team size, delivery speed            │
   └─────────────────────────────────────────────────────┘

4. Response:
   "ADR 0008 explains why REST was chosen for this project:
   small team, need for speed, lower complexity.

   GraphQL would introduce:
   - Higher setup complexity (schema, resolvers)
   - Different error handling patterns
   - Additional tooling/monitoring needs

   If you want to proceed with GraphQL, I can create ADR 0023
   that supersedes 0008, documenting the new decision.

   Should I proceed?"

────────────────────────────────────────────────────────
```

---

## Summary

| Aspect | Rule |
|--------|------|
| When to create | Significant tech choice affecting multiple components |
| Naming | `000X-descriptive-title.md` (sequential, kebab-case) |
| Template | Context, Decision, Alternatives, Consequences |
| Linking | Update `map.md`, `stack.md`, `architecture.md` |
| States | Accepted → Deprecated/Superseded |
| Before changes | Always read related ADR first |