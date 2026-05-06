# Training System

**Purpose:** Provide comprehensive training documents that make agents experts in specific technologies, plus the subagent infrastructure to deliver that expertise.

## Problem

The agent:
- Doesn't know how to use packages correctly (wrong API usage)
- Doesn't know latest versions or best practices
- Lacks deep knowledge in specific domains
- Can't become an expert on demand

## Solution

A multi-layer training system:

```
Training System
├── Training Docs    # Deep knowledge about a technology (local documentation)
├── Skills           # How-to use the technology (procedures)
└── Expert Subagent  # AI agent specialized in this technology
```

---

## Training Documents Structure

Each technology has a complete training folder:

```
training/
└── [technology]/
    ├── README.md              # Entry point - overview and learning path
    ├── fundamentals/
    │   ├── README.md          # Fundamentals overview
    │   ├── core-concepts.md   # Core concepts explained
    │   ├── key-terminology.md # Glossary of terms
    │   └── mental-models.md   # How to think about this tech
    ├── deep-dives/
    │   ├── README.md          # Deep dives overview
    │   ├── topic-1.md         # Deep dive into specific topic
    │   ├── topic-2.md
    │   └── ...
    ├── patterns/
    │   ├── README.md          # Patterns overview
    │   ├── pattern-1.md        # Common pattern with examples
    │   ├── pattern-2.md
    │   └── anti-patterns.md    # Patterns to avoid
    ├── best-practices/
    │   ├── README.md          # Best practices overview
    │   ├── do-and-donts.md     # Clear do/don't list
    │   ├── recommended-approaches.md
    │   └── common-mistakes.md  # Mistakes and how to avoid
    ├── troubleshooting/
    │   ├── README.md          # Troubleshooting overview
    │   ├── error-codes.md      # Common errors and solutions
    │   ├── debugging.md       # How to debug issues
    │   └── faq.md             # Frequently asked questions
    ├── resources/
    │   ├── README.md          # Resources overview
    │   ├── official-docs.md    # Links to official docs
    │   ├── tutorials.md        # Good tutorials
    │   ├── videos.md           # Video resources
    │   └── community.md        # Community resources
    ├── v4-migration/          # (optional) Version-specific migration
    │   ├── README.md
    │   ├── changes.md
    │   └── guide.md
    └── learning-path.md       # How to go from beginner to expert
```

### File Naming Convention

- All files in English (or consistent language)
- Lowercase with hyphens: `core-concepts.md`
- Folders lowercase: `fundamentals/`
- README.md in every folder explaining its contents

---

## Expert Subagent

Each technology training folder includes a subagent definition that becomes an "expert" in that technology.

### Subagent Purpose

The expert subagent is used by the main agent to:
- **Ask questions** — "What's the best way to handle X in Zod?"
- **Implement elements** — "Create a schema for user validation"
- **Review code** — "Review this schema for issues"
- **Explain concepts** — "Explain how Zod inference works"
- **Troubleshoot** — "Help me debug this validation error"

### Subagent Definition

Created at: `[technology]/subagent/SKILL.md`

```yaml
---
name: zod-expert
description: Expert in Zod validation library, including v4 features
context: fork
agent: general-purpose
memory: project
allowed-tools:
  - Read
  - Write
  - Bash
  - Grep
  - Glob
---

You are an expert in [Technology Name].

Your expertise comes from reading and mastering the training documents in this directory:
- fundamentals/ — Core concepts and terminology
- deep-dives/ — Advanced topics
- patterns/ — Common and recommended patterns
- best-practices/ — How to use correctly
- troubleshooting/ — Common issues and solutions

When asked to help:
1. First read relevant training docs to ensure your knowledge is current
2. Draw from the curated best practices and patterns
3. Provide examples from the training materials
4. Cite which document/resource informed your answer

You can be consulted for:
- How to implement specific features
- Best practices for specific use cases
- Code review against best practices
- Troubleshooting complex issues
- Explaining complex concepts
```

### How the Main Agent Uses the Expert Subagent

```
Main Agent: "I need to validate a nested object with conditional fields"
          │
          ▼
┌─────────────────────────────────────────────────────┐
│  Main Agent invokes zod-expert subagent            │
│  Asks: "What's the best approach for nested +      │
│         conditional validation?"                    │
└────────────────────────┬────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────┐
│  zod-expert subagent                                │
│  1. Reads training/zod/patterns/validation.md       │
│  2. Reads training/zod/deep-dives/conditionals.md   │
│  3. Provides expert answer with examples            │
└────────────────────────┬────────────────────────────┘
                         │
                         ▼
              Main Agent receives expert guidance
                         │
                         ▼
              Main Agent implements using that guidance
```

### Subagent Files

```
training/
└── [technology]/
    ├── README.md
    ├── fundamentals/
    ├── deep-dives/
    ├── patterns/
    ├── best-practices/
    ├── troubleshooting/
    ├── resources/
    │
    └── subagent/
        └── SKILL.md          # Expert subagent definition
```

---

## Example: Zod Training

```
training/zod/
├── README.md              # "Master Zod validation library"
├── fundamentals/
│   ├── README.md
│   ├── core-concepts.md   # Schemas, types, inference
│   ├── key-terminology.md # Zod terms
│   └── mental-models.md   # Think in schemas
├── deep-dives/
│   ├── README.md
│   ├── inference.md        # Type inference deep dive
│   ├── transforms.md      # Input/output transforms
│   ├── coercion.md        # Type coercion
│   └── refinement.md      # Custom validation logic
├── patterns/
│   ├── README.md
│   ├── object-validation.md
│   ├── array-validation.md
│   ├── nested-schemas.md
│   ├── discriminated-unions.md
│   └── optional-vs-default.md
├── best-practices/
│   ├── README.md
│   ├── do-and-donts.md
│   ├── schema-organization.md
│   └── error-handling.md
├── troubleshooting/
│   ├── README.md
│   ├── error-codes.md
│   ├── type-issues.md
│   └── common-mistakes.md
├── v4-migration/
│   ├── README.md
│   ├── whats-new.md
│   └── breaking-changes.md
├── resources/
│   ├── README.md
│   ├── official-docs.md
│   └── tutorials.md
└── subagent/
    └── SKILL.md
```

---

## Skills from Training

Training documents also feed into skills that the main agent can use:

```
training/zod/
├── ...
└── skills/
    ├── create-schema/       # How to create schemas
    │   └── SKILL.md
    ├── validate-data/       # How to validate data
    │   └── SKILL.md
    └── migrate-v3-v4/       # Migration guide skill
        └── SKILL.md
```

But skills are optional — the primary delivery mechanism is the expert subagent.

---

## How to Create a New Technology Training

1. Create folder: `training/[technology]/`
2. Create all subfolders with README.md files
3. Write comprehensive training documents
4. Create `subagent/SKILL.md` with expert subagent definition
5. Optionally create skills in `skills/` subfolder

---

## Integration with Other Systems

| System | Integration |
|--------|-------------|
| **Tech Radar** | Training reflects technologies marked as "Adopt" |
| **Tech Recommendations** | Best practices align with recommendations |
| **Research-First Protocol** | Check training docs before web search |
| **Project Context** | Agent uses training to understand project tech stack |

## TODO

- [ ] Define training document format per type
- [ ] Create example training for one technology (Zod)
- [ ] Create expert subagent for that technology
- [ ] Define skill creation from training
- [ ] Create script to scaffold new technology training