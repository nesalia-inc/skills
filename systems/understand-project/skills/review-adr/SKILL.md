---
name: review-adr
description: Read and summarize relevant ADRs before proposing changes. Enforces checking decisions before questioning choices.
---

# Review ADR

Read and summarize relevant Architecture Decision Records before proposing technical changes.

## What is the Project Context System?

The **Project Context System** solves a fundamental problem: when an AI agent starts a conversation, it has no context about the project. It must guess the domain, rebuild structure mentally, and rediscover conventions.

ADRs (Architecture Decision Records) are part of this system. They live in `.context/project/definition/decisions/` and document WHY choices were made. Before the agent proposes to change or replace something, it must read the related ADR to understand the original reasoning.

### How ADRs Fit In

```
.context/
├── project/
│   └── definition/
│       └── decisions/
│           ├── 0001-use-postgresql.md
│           └── 0002-event-driven-arch.md

project-context (core skill)
    │
    └── Rule: "Check ADRs before questioning choices"

review-adr → Reads ADRs when changes are proposed
create-adr → Creates ADRs when new decisions are made
```

## The Core Rule

> **"If you want to replace a library, pattern, or approach, first read the related ADR in `[[project/definition/decisions/]]`. Only propose changes after understanding the original reasoning."**

This is enforced by the Entry Points system. Breaking this rule means the agent may propose to replace something that was carefully considered for good reasons.

## When to Use

| Situation | Action |
|-----------|--------|
| Before proposing tech/stack changes | Read related ADR |
| When user/agent questions existing architecture | Search and read ADR |
| When asked "why was this chosen?" | Summarize related ADR |
| When user proposes to replace something | Check ADR first |

## Process

### Step 1: Detect the Proposal

What is being changed or questioned?

```
User: "Shouldn't we use MongoDB instead of PostgreSQL?"
Agent: (detects: database technology change proposal)
```

### Step 2: Search for Related ADRs

Search `.context/project/definition/decisions/` for relevant content:

```bash
# By keyword
grep -l "postgresql\|database" .context/project/definition/decisions/*.md

# By pattern
grep -l "storage\|data" .context/project/definition/decisions/*.md
```

### Step 3: Read and Understand

Read the full ADR content. Focus on:
- **Context:** What problem was being solved?
- **Decision:** What was chosen and why?
- **Alternatives:** What was rejected and why?

### Step 4: Summarize for User

Present the ADR's reasoning clearly:

```
ADR 0001 explains why PostgreSQL was chosen:
- Context: Small team, needed fast delivery, relational data with ACID needs
- Decision: PostgreSQL for primary database
- Alternatives: MongoDB (rejected: less ACID support, different query patterns)

Summary: PostgreSQL was chosen because the team needed ACID compliance
and the data model is inherently relational. Changing would require
creating a new ADR to supersede this one.
```

### Step 5: Block or Proceed

Two outcomes:

**Block:** If user wants to change but hasn't considered the ADR reasoning:
```
"This ADR documents why PostgreSQL was chosen for these specific reasons.
Changing would require creating a new ADR that supersedes 0001.
Do you want to proceed with that process?"
```

**Proceed:** If user understands and wants new ADR:
```
"I'll create ADR XXXX to document the new decision and supersede 0001."
```

## Example: Full Flow

```
User: "Let's use GraphQL instead of REST for the user API."

Agent:
1. (detects tech change proposal)
2. (searches for REST/GraphQL related ADRs)
3. (finds ADR 0008: "Use REST for API simplicity")
4. (reads: Context - small team, fast delivery; Decision - REST chosen; Alternatives - GraphQL rejected due to complexity)

Response:
"ADR 0008 explains why REST was chosen for this project:

Context: Small team, need fast delivery. REST was chosen for simplicity
and lower learning curve compared to GraphQL.

Alternatives considered:
- GraphQL: Rejected because of schema setup complexity, different error
  handling patterns, and additional monitoring needs.

If you want to move to GraphQL, I can create a new ADR that supersedes
0008, documenting the new decision and reasoning.

Should I proceed?"
```

## No ADR Found

If no ADR exists when questioning a choice:

```
"I couldn't find an ADR explaining why [X] is used. This might mean:
1. The decision predates the ADR system, or
2. It was considered minor at the time.

Should I create an ADR documenting the current state and reasoning?
This would help future agents understand the context."
```

**Rule:** When no ADR exists and something is being questioned, document it.

## ADR States

| State | Meaning |
|-------|---------|
| **Accepted** | Current, active decision (default) |
| **Deprecated** | No longer followed, kept for history |
| **Superseded** | Replaced by a newer ADR |

When creating a new ADR that supersedes an old one:
1. New ADR has `Supersedes: [ADR ID]` in header
2. Old ADR's status changes to "Superseded"
3. Update `[[map.md]]` to reflect the change

## Important Rules

1. **Always check ADRs before proposing changes** — Never skip this
2. **Summarize the reasoning** — Don't just say "ADR exists", explain WHY
3. **Offer the supersession path** — If user wants to proceed, offer to create new ADR
4. **Document when no ADR found** — Create one to preserve the reasoning

## Skills Integration

| Skill | Role |
|-------|------|
| `project-context` | Enforces ADR check via Entry Points |
| `create-adr` | Creates new ADRs (including superseding ones) |
| `context-update` | Updates map.md when ADR status changes |