---
name: context-claude-integration
description: Integrate .context/ with Claude Code's native systems (CLAUDE.md, auto memory, rules).
---

# Context Claude Integration

Integrate the `.context/` system with Claude Code's native features (CLAUDE.md, auto memory, path-scoped rules).

## What is the Project Context System?

The **Project Context System** solves a fundamental problem: when an AI agent starts a conversation, it has no context about the project. It must guess the domain, rebuild structure mentally, and rediscover conventions.

The system defines context in `.context/` files that the agent reads at startup. But Claude Code has its own native systems for providing context (CLAUDE.md, rules, memory). This skill integrates `.context/` with those native systems so the context system works seamlessly.

### How It Fits In

```
Skills workflow:
  /project-init → calls /context-claude-integration
  /context-claude-integration → links .context/ to Claude Code native systems

Claude Code native systems:
  - CLAUDE.md          → Primary project instructions
  - .claude/rules/     → Modular path-scoped rules
  - Auto Memory        → Agent-generated cross-session notes

.context/ system:
  - map.md             → Entry point
  - project/definition → Static target state
  - project/status     → Ephemeral current state
```

## Why Integrate?

Claude Code already has:
- **CLAUDE.md** — Human-written instructions that persist
- **Rules** — Modular, path-scoped instruction files
- **Memory** — Auto-generated notes for cross-session continuity

`.context/` already has:
- Rich project structure (global/organization, project/definition, project/status)
- Entry Points system for read/write triggers
- ADR system for documenting decisions
- Staleness detection

Integration ensures:
1. Claude Code knows about `.context/` and reads it automatically
2. Entry Points work with native mechanisms
3. Context is preserved across sessions via memory

## Integration Points

### 1. CLAUDE.md (Primary Integration)

CLAUDE.md is the main entry point for Claude Code. It should reference `.context/`.

**Note:** `project-init` already creates CLAUDE.md content. This skill enhances it further.

Add to existing CLAUDE.md or create if missing:

```markdown
# Project Context

This project uses the **Project Context System** (`.context/`). This system provides
agents with instant project understanding by defining context once and reusing it.

## Context System Structure

```
.context/
├── map.md                 # Entry point (start here!)
├── global/               # Organization + business context
└── project/
    ├── definition/       # Static: target state (what project should be)
    │   ├── identity.md
    │   ├── architecture/
    │   ├── structure.md
    │   ├── conventions.md
    │   ├── stack.md
    │   └── decisions/    # ADRs - document why choices were made
    └── status/           # Ephemeral: current state
        ├── current.md
        ├── active-issues.md
        ├── recent.md
        └── progress.md
```

## Entry Points

| When... | Read | Write |
|---------|------|-------|
| Session starts | `.context/map.md` + `status/*` | — |
| Before writing code | `definition/conventions.md` | — |
| Before tech changes | `definition/decisions/*.md` | — |
| Task completes | — | `status/recent.md` |
| Before commit/PR | — | `status/progress.md` |
| Major decision made | — | Create ADR in `decisions/` |

## Project Context Protocol (PCP)

1. **Read `.context/map.md`** — Always start here to understand the project
2. **Check staleness** — Alert if status files are old (>14 days)
3. **Follow conventions** — Apply `definition/conventions.md` rules
4. **Check ADRs** — Before proposing tech changes, read related decisions
5. **Update status** — On task completion, offer to update `status/recent.md`

## Skills

Available skills:
- `/project-context` — Load and provide project context
- `/context-check` — Validate context alignment
- `/create-adr` — Document a technical decision
- `/review-adr` — Read ADRs before proposing changes
- `/context-update` — Update status files
```

### 2. Path-Scoped Rules (`.claude/rules/`)

Path-scoped rules load only when certain files are accessed. Create rules that reference context files:

#### `.claude/rules/context-read.md` (Always loaded)

```markdown
# Context System Overview

This project uses the Project Context System (`.context/`).

- Read `.context/map.md` at session start
- Use [[context/project/definition/conventions.md]] before writing code
- Check [[context/project/definition/decisions/]] before tech changes

**Entry Points:**
- Read `conventions.md` before coding
- Read `decisions/*.md` before changing tech
- Update `status/recent.md` on task completion
```

#### `.claude/rules/adr-check.md` (When tech changes proposed)

```markdown
---
paths:
  - "**/*.{ts,js,py,go,java}"
---

# ADR Check Rule

Before proposing to replace a library, framework, or architecture pattern:

1. Search `.context/project/definition/decisions/` for related ADRs
2. Read the ADR to understand why the current choice was made
3. Do NOT propose changes until you understand the original reasoning

If no ADR exists, suggest creating one.
```

#### `.claude/rules/conventions.md` (Always loaded, points to context)

```markdown
# Coding Conventions

Follow the project's coding conventions defined in:
[[.context/project/definition/conventions.md]]

Key points:
- Naming conventions
- Code style rules
- Git workflow
- Agent-specific instructions
```

### 3. Auto Memory Integration (Optional)

Auto memory synthesizes context for faster loading across sessions.

After context is enriched (`context-enrich` skill), the agent can write a summary to memory:

```
~/.claude/projects/<project>/memory/
├── MEMORY.md              # Summary of project context (from map.md)
└── contexts/
    ├── project-summary.md  # Project overview
    └── tech-stack.md        # Stack overview (from stack.md)
```

**Note:** This is optional and can be done by the agent after `context-enrich` completes.

## What to Create

| File | Purpose |
|------|---------|
| `CLAUDE.md` (enhance existing) | Primary project instructions referencing `.context/` |
| `.claude/rules/context-read.md` | General context awareness (always loaded) |
| `.claude/rules/adr-check.md` | ADR enforcement (loaded on code access) |
| `.claude/rules/conventions.md` | Points to conventions in `.context/` |

## Order of Operations

1. **Enhance CLAUDE.md** — Already created by `project-init`, add more detail here
2. **Create path-scoped rules** — `context-read.md`, `adr-check.md`, `conventions.md`
3. **Optional: Setup memory** — After `context-enrich` completes, agent can synthesize to memory

## Verification

After integration:

1. Run `/memory` — Verify CLAUDE.md loads correctly
2. Open a code file — Verify rules load correctly
3. Ask about tech choices — Verify ADRs are checked

## Skills Integration

| Skill | Role |
|-------|------|
| `project-init` | Calls this skill after creating CLAUDE.md |
| `context-enrich` | After enrichment, agent can synthesize to memory |
| `project-context` | Reads CLAUDE.md which references `.context/` |