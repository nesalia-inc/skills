---
name: create-adr
description: Create an Architecture Decision Record when major technical decisions are made.
---

# Create ADR

Create an ADR (Architecture Decision Record) to document significant technical choices and their rationale.

## What is the Project Context System?

The **Project Context System** solves a fundamental problem: when an AI agent starts a conversation, it has no context about the project. It must guess the domain, rebuild structure mentally, and rediscover conventions.

ADRs are part of this system. They live in `.context/project/definition/decisions/` and document WHY choices were made. This prevents the agent from proposing to replace technologies/patterns without understanding the original reasoning.

### How ADRs Fit In

```
.context/
├── project/
│   ├── definition/
│   │   └── decisions/
│   │       ├── 0001-use-postgresql.md
│   │       └── 0002-event-driven-arch.md
│   └── status/

project-context (core skill)
    │
    ├── Reads ADRs before proposing tech changes
    └── Enforces: "Check ADRs before questioning choices"

create-adr → Creates new ADRs
review-adr → Reads and summarizes ADRs before changes
```

## When to Create an ADR

Create an ADR when making a significant technical decision:

| Decision Type | Example |
|---------------|---------|
| **Technology choice** | "We chose PostgreSQL over MongoDB" |
| **Architecture pattern** | "We use event-driven architecture" |
| **Framework selection** | "We use React instead of Vue" |
| **Design pattern** | "We follow clean architecture" |
| **Infrastructure** | "We deploy to Kubernetes" |
| **Cross-cutting concern** | "We use JWT for authentication" |
| **Code organization** | "We group by feature, not type" |
| **Process decision** | "We use trunk-based development" |

### ADR Threshold

An ADR is needed when TWO OR MORE of these are true:
1. The decision affects multiple files/services
2. There were meaningful alternatives considered
3. The choice would be hard to reverse
4. New team members would need to understand the reasoning

**Rule:** If you find yourself saying "we chose X because...", document it.

## Process

### Step 1: Detect the Decision

Is this significant enough? Check the threshold above.

### Step 2: Check Existing ADRs

Before creating, search for related ADRs:
```bash
grep -l "keyword" .context/project/definition/decisions/*.md
```

Maybe one already exists.

### Step 3: Find Next Number

List existing ADRs:
```bash
ls -1 .context/project/definition/decisions/*.md | sort
```

If last is `0003-something.md`, next is `0004`.

### Step 4: Create the ADR

Use the template below. Be thorough on Context (why) and Consequences (impact).

### Step 5: Link from Context

After creating, update relevant files:
- `[[map.md]]` — Add to files table
- `[[project/definition/stack.md]]` — Link from relevant tech
- `[[project/definition/architecture/*.md]]` — Link from relevant component

## Naming Convention

```
0001-short-descriptive-title.md
```

Rules:
- Sequential 4-digit number, zero-padded
- Lowercase kebab-case
- Max 60 characters total (filename)
- No spaces, special chars (except `-`)

## ADR Template

```markdown
---
created: 2024-01-15
---

# ADR [ID]: [Title]

## Context

[Problem being solved or why this decision was needed.
What was the situation? What constraints existed?
What forced this choice?]

## Decision

[What was chosen and why.
Be specific about the solution adopted.
Include the final choice clearly.]

## Alternatives Considered

[What other options were evaluated?
For each: what it was, and why rejected]

## Consequences

### Positive
[Benefits of this choice]

### Negative
[Trade-offs, risks, introduced complexity]

### Neutral
[Things to consider, but not strictly good or bad]
```

## Example: Complete ADR

```markdown
---
created: 2024-01-15
---

# ADR 0015: Use Redis for Session Caching

## Context

The application needs to handle 10k concurrent users. Session data
is accessed on every request, and database queries for session
retrieval are creating a bottleneck. The team has 3 weeks to ship.

Constraints:
- Small team (2 backend devs)
- Need horizontal scaling for launch
- Existing Redis infrastructure available

## Decision

We will use Redis as a distributed cache for session data.

Implementation:
- Redis for session storage (external)
- 1-hour TTL with sliding expiration
- Fallback to database if Redis unavailable

## Alternatives Considered

**In-memory cache (Node.js Map)**
Rejected because: doesn't scale across instances, sessions lost on restart.

**Memcached**
Rejected because: Redis offers better persistence and more data structures.

## Consequences

### Positive
- Sub-millisecond session retrieval
- Horizontal scaling support
- Session survives app restarts
- Existing Redis infra reduces cost

### Negative
- Network latency for cache operations (0.5-1ms)
- Additional infrastructure to maintain
- Cache invalidation complexity

### Neutral
- Redis expertise needed (team has it)
- Monitoring required for cache hit rate
```

## Linking to Other Context

Use `[[path/to/file.md]]` pointers:

```markdown
This decision affects [[architecture/overview.md]].

See also: [[stack.md]] (Redis is documented there).
```

## Important Rules

1. **Document the WHY** — Not just what was chosen, but why alternatives were rejected
2. **Be specific** — "We chose PostgreSQL" is vague. "We chose PostgreSQL for primary DB due to ACID compliance and JSON support" is good
3. **Consequences matter** — Include at least one negative. Every choice has trade-offs
4. **Link related context** — Update map.md, stack.md, architecture.md after creating

## Skills Integration

| Skill | Role |
|-------|------|
| `project-context` | Triggers ADR creation before tech changes |
| `review-adr` | Reads ADRs before proposing changes |
| `context-update` | Updates related files after ADR creation |