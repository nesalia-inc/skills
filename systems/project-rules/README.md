# Project Rules System

**Purpose:** Provide the agent with project-specific rules, conventions, patterns, and methodologies once project context is understood.

## Problem

Even after the agent understands the project (via Project Context System), it doesn't automatically know:
- **Conventions** — How to name files, write commits, format code
- **Patterns** — Common solutions to recurring problems in this project
- **Methodologies** — How work is done (workflows, processes)
- **Constraints** — Project-specific limitations and requirements

The agent might guess incorrectly or apply generic conventions that don't match the project's standards.

## Solution

A system that delivers project-specific rules/conventions/patterns/methodologies to the agent, integrated with the Project Context System.

## Integration with Project Context System

```
Project Context System (.context/)
         │
         ├──→ definition/      # What the project is (target state)
         │
         └──→ rules/           # How to work in this project
              ├── README.md              # Entry point
              ├── conventions/
              │   ├── error-handling.md
              │   ├── api-design.md
              │   └── ...
              ├── patterns/
              │   ├── api/
              │   ├── component/
              │   └── ...
              ├── methodologies/
              │   ├── how-we-work.md
              │   └── ...
              └── constraints/
                  ├── no-classes-end-users.md
                  └── result-type-required.md
```

## Directory Structure

```
.context/project/rules/
├── README.md              # Entry point - always read
├── conventions/
│   ├── error-handling.md
│   ├── api-design.md
│   ├── git-workflow.md
│   └── naming.md
├── patterns/
│   ├── api/
│   │   ├── repository.md
│   │   └── result-wrapper.md
│   ├── component/
│   └── data/
├── methodologies/
│   ├── how-we-work.md
│   ├── decision-process.md
│   └── onboarding.md
└── constraints/
    ├── no-classes-end-users.md
    └── result-type-required.md
```

## What Each Category Contains

### Conventions

Project-specific conventions:
- **Naming** — File naming, variable naming, function naming
- **Code formatting** — Indentation, line length, style
- **Git conventions** — Commit format, branch naming, PR process
- **Documentation** — Doc format, comment style

**Example conventions:**
- "Never expose classes to end users" — API design rule
- "Use `Result` type from `@deessejs/fp` for error handling" — Error handling convention
- "All API responses must use the `ApiResponse<T>` wrapper" — Response format convention

### Patterns

Common solutions to recurring problems:
- **API patterns** — How to structure endpoints, handle errors
- **Component patterns** — How to build UI components
- **Data patterns** — How to handle data fetching, caching, state
- **Testing patterns** — How to test specific things

**Example patterns:**
- "When validation fails, return 400 with `ValidationError` type"
- "Use repository pattern for data access"

### Methodologies

How work is done:
- **How we work** — Daily/weekly workflows
- **Decision process** — How decisions are made
- **Onboarding** — How new team members get up to speed
- **Communication** — How the team communicates

### Constraints

Project-specific constraints:
- **Tech constraints** — Can't use X because of Y
- **Business constraints** — Must comply with Z regulation
- **Architecture constraints** — Must use A architecture pattern

**Example constraints:**
- "Never expose classes to end users" — Architecture constraint
- "Must use `@deessejs/fp` Result type for all error handling" — Tech constraint

## Key Concepts

| Concept | Description |
|---------|-------------|
| **Convention** | "How we do X" — agreed standard |
| **Pattern** | "When facing Y, use Z approach" — reusable solution |
| **Methodology** | "How we work as a team" — process |
| **Constraint** | "We must/can't do X" — limitation |

## Entry Point: rules/README.md

When the agent loads project context, it reads `.context/project/rules/README.md` to understand applicable rules.

This file contains:
- Table of contents for all rules
- Quick summary of key conventions
- Load triggers for each category

## Loading Flow

```
Agent loads project context
         │
         ▼
┌─────────────────────────────────────┐
│  1. Read .context/map.md            │
│     (project overview)              │
└──────────────────┬──────────────────┘
                   │
                   ▼
┌─────────────────────────────────────┐
│  2. Read .context/project/rules/    │
│     README.md (entry point)         │
└──────────────────┬──────────────────┘
                   │
                   ▼
┌─────────────────────────────────────┐
│  3. Use /check-rules for task       │
│     (get applicable rules by task)  │
│  OR                                 │
│  Load rules by category as needed   │
└─────────────────────────────────────┘
```

## Skills

| Skill | Purpose |
|-------|---------|
| `/add-convention` | Add a new convention |
| `/add-constraint` | Document a constraint |
| `/add-pattern` | Document a pattern |
| `/add-methodology` | Document a methodology |
| `/check-rules` | Get rules applicable to current task |
| `/list-rules` | List all rules (onboarding) |
| `/review-rules` | Identify gaps/contradictions |

## Templates

Templates available in `../templates/`:
- `convention-template.md` — For adding conventions
- `constraint-template.md` — For adding constraints
- `pattern-template.md` — For adding patterns
- `methodology-template.md` — For adding methodologies

## Integration with Other Systems

| System | Integration |
|--------|-------------|
| **Project Context** | Rules are loaded after definition |
| **Tech Knowledge** | Conventions reference tech standards |
| **Critical Review** | Review against rules when questioning decisions |
| **context-management** | Existing skill manages adding/updating context |