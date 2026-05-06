# Project Context System

A system for providing Claude Code agents with instant project understanding when loading a conversation. The agent immediately knows what the project is, how it works, its current status, and relevant conventions.

## Overview

When an agent loads a conversation, it has no context about the project. It must guess the domain, rebuild the structure mentally, and rediscover conventions — wasting time and producing inconsistent results.

The Project Context System solves this by defining context once, in a standardized format, and providing the agent with a complete context snapshot at startup.

## Context Separation: Humans vs Agents

This system creates a clear separation between documentation meant for humans and context meant for agents:

| Audience | Location | Purpose |
|----------|----------|---------|
| Humans | `docs/` | Human-readable documentation (architecture docs, how-tos, etc.) |
| Agents | `.context/` | Structured context files read by agents at startup |

**All agent context lives under `.context/`** — humans generally do not read or edit these files directly.

## Directory Structure

```
.context/
├── map.md                      # ENTRY POINT - read first
├── global/                    # Organization/business level
│   ├── organization.md         # "Who are we?"
│   └── business.md            # "What business are we in?"
└── project/                   # Project-specific level
    ├── definition/            # STATIC - rarely changes
    │   ├── identity.md
    │   ├── architecture/
    │   │   ├── overview.md
    │   │   └── data-flow.md
    │   ├── structure.md
    │   ├── stack.md
    │   └── decisions/
    │       └── 0001-*.md       # Architecture Decision Records
    │
    ├── rules/                 # How to work in this project
    │   ├── README.md           # Entry point for rules
    │   ├── conventions/       # Naming, formatting, git, etc.
    │   ├── patterns/          # Common solutions
    │   ├── methodologies/     # Team processes
    │   └── constraints/       # Hard limitations
    │
    └── status/               # EPHEMERAL - changes frequently
        ├── current.md
        ├── active-issues.md
        ├── recent.md
        └── progress.md        # Current work session tracking
```

## Project Rules

After understanding the project (definition), the agent must also understand **how to work in this project** via the Project Rules system.

Project rules are stored in `.context/project/rules/` and include:
- **Conventions** — How we do things (naming, code style, git workflow)
- **Patterns** — Recurring solutions to problems
- **Methodologies** — Team processes and workflows
- **Constraints** — Hard limitations (tech, business, architecture)

See `../project-rules/README.md` for full system documentation.

## Entry Point: map.md

The agent reads `map.md` first. This file contains:

- A ultra-condensed summary (10 lines max) of the project
- A table of contents linking to all available context files
- **Load Triggers** — when to load each file based on the agent's task

**Lazy loading principle:** Do not load everything at once. Load only what's relevant to the current task.

### map.md Format

```markdown
---
created: 2024-01-15
updated: 2024-03-20
---

# Project Context Map

## Summary
E-commerce platform for Nesalia Inc. Built with Node.js/React. Target: 10k daily users.

## Files

| File | Purpose | Load Trigger |
|------|---------|-------------|
| [[global/organization.md]] | Nesalia Inc. identity | When asked about the organization |
| [[global/business.md]] | E-commerce business model | When asked about business context |
| [[project/definition/identity.md]] | Project vision and goals | On first interaction with project |
| [[project/definition/architecture/overview.md]] | Component overview | When understanding system design |
| [[project/definition/architecture/data-flow.md]] | Data flow | When debugging or modifying data flow |
| [[project/definition/structure.md]] | Folder organization | When navigating or adding new files |
| [[project/definition/stack.md]] | Technologies | Before installing/updating deps |
| [[project/definition/decisions/]] | Architecture decisions | Before refactoring or proposing tech changes |
| [[project/rules/README.md]] | Rules entry point | Always, after definitions |
| [[project/rules/conventions/]] | Project conventions | Before writing code |
| [[project/rules/patterns/]] | Common patterns | When solving recurring problems |
| [[project/rules/constraints/]] | Hard constraints | Before architectural decisions |
| [[project/status/current.md]] | Current sprint and phase | At start of each session |
| [[project/status/active-issues.md]] | Current blockers | When encountering blockers |
| [[project/status/recent.md]] | Latest changes | After git operations |
| [[project/status/progress.md]] | Current work session | At start and end of each session |
```

## Context Pointers

Documents link to each other using markdown links: `[[path/to/file.md]]`

This creates a navigable context network. Example in `architecture/overview.md`:

```markdown
The API layer uses REST. For caching strategy, see [[decisions/0001-redis-caching.md]].

Folder structure is documented in [[structure.md]].
```

## Context Levels

### Global Context

High-level context that applies across all projects within an organization.

| Document | Purpose | Question Answered |
|----------|---------|-------------------|
| `global/organization.md` | Organization identity | "Who are we as an organization?" |
| `global/business.md` | Business context | "What business are we in? What are our goals and strategy?" |

### Project Context — Phase 1: Definition

Static data that rarely changes. Once written, it provides long-term context.

**Location**: `.context/project/definition/`

| Document | Purpose | Question Answered |
|----------|---------|-------------------|
| `identity.md` | Project identity | "What is this project and what problem does it solve?" |
| `architecture/overview.md` | Components overview | "What are the main components?" |
| `architecture/data-flow.md` | Data flow | "How does data move through the system?" |
| `structure.md` | Folder organization | "Why is the project organized this way?" |
| `stack.md` | Technologies | "What is this built with?" |
| `decisions/*.md` | Architecture decisions | "Why was this choice made?" |

> **Note:** Conventions are now in `.context/project/rules/conventions/` (see Project Rules below).

> **Important:** Definition documents describe the **target state** — the vision, the goal, what the project *should* become. They do NOT describe the current reality, which may be messy or incomplete. This allows the agent to understand the project's intention and identify gaps between current state and desired state.

#### Decisions (ADR)

Architecture Decision Records capture the *why* behind choices:

- **Filename format:** `0001-descriptive-title.md` (sequential numbering)
- **Content:** Context, decision, consequences
- **Purpose:** Prevents the agent from proposing to change tech/stack/patterns without understanding the original reasoning

### Project Context — Phase 2: Status

Ephemeral data that changes frequently (daily or per-sprint).

**Location**: `.context/project/status/`

| Document | Purpose | Question Answered |
|----------|---------|-------------------|
| `current.md` | Current state | "What is the current sprint and phase?" |
| `active-issues.md` | Blockers | "What is blocking progress?" |
| `recent.md` | Latest changes | "What was recently merged or deployed?" |
| `progress.md` | Work session tracking | "What is being worked on right now?" |

> **Note:** Status documents should be auto-maintained where possible (e.g., `recent.md` via git hooks).

#### progress.md Purpose

Unlike `recent.md` (which tracks git history), `progress.md` tracks current work within a session:

```markdown
## Session Progress

### Current Task
- Task: Migrate user API to v2
- Status: Step 2/3 complete
- Next: Update integration tests

### Completed This Session
- [x] Created new route handlers
- [x] Updated validation schema
```

This enables:
- Resume after disconnect/session restart
- Cross-agent continuity (if multiple agents work on same project)
- Quick context injection for long-running tasks

## What the Agent Must Understand

### From Static Data

| Category | What the Agent Understands |
|-----------|----------------------------|
| **Purpose** | Why this project exists, what problem it solves, for whom |
| **Functioning** | Components, interactions, data flow, external dependencies |
| **Organization** | Folder structure rationale, navigation patterns, entry points |
| **Rules** | Naming conventions, coding style, commit/PR process, testing approach, agent personality |
| **Stack** | Languages, frameworks, libraries, infrastructure, build/deploy tools |
| **Decisions** | Why certain choices were made (to avoid rewriting things unnecessarily) |
| **Organization Context** | High-level identity and business domain |
| **Business Context** | Business model, target customers, strategic objectives |

### From Ephemeral Data

| Category | What the Agent Understands |
|-----------|----------------------------|
| **Current State** | Current sprint, project phase, active goals |
| **Blockers** | Issues blocking progress |
| **Recent Activity** | Latest merges, deployments, significant changes |
| **Session Progress** | What is being worked on right now, completed steps |

## Project Rules

After understanding the project definition, the agent must understand **how to work in this project** via the Project Rules system.

**Location**: `.context/project/rules/`

| Category | Purpose | When to Load |
|----------|---------|--------------|
| `rules/README.md` | Entry point for rules | Always after definition |
| `rules/conventions/` | How we do things | Before writing code |
| `rules/patterns/` | Recurring solutions | When solving problems |
| `rules/methodologies/` | Team processes | When doing team work |
| `rules/constraints/` | Hard limitations | Before architectural decisions |

**Key distinction from Definition:**
- **Definition** — What the project IS (target state)
- **Rules** — How to WORK in this project (conventions, patterns, constraints)

See `../project-rules/README.md` for full system documentation.

## Document Format

Every document uses YAML frontmatter for metadata:

```yaml
---
created: 2024-01-15
updated: 2024-03-20
---
```

**Metadata fields:**

| Field | Required | Description |
|-------|----------|-------------|
| `created` | Always | Creation date of the document |
| `updated` | Only if changed | Last modification date |

**Rule:** If a document has no `updated` field, assume it has never been modified since creation.

### Date Handling

> **Critical Rule:** The agent must always use the real system date (from the OS/environment), never the training data cutoff date. When calculating staleness, use the actual current date.

## Staleness Detection

A document becomes **stale** when its `updated` date is older than the configured threshold (default: 90 days for definition docs, 14 days for status docs).

### Staleness Behavior

When detecting stale documents, the agent must:

| Document Type | Behavior |
|---------------|----------|
| **Status (old)** | "I notice [[status/current.md]] is 20 days old. Should I analyze recent commits to sync the project status before proceeding?" |
| **Definition (old)** | "This definition document hasn't been reviewed in 6 months. Shall I verify it still reflects the target state?" |
| **Structure (detected drift)** | "The project structure has changed. I can compare `tree -I 'node_modules|dist|.git'` with [[structure.md]] and propose updates if needed." |

## Loading Flow

When an agent starts a conversation:

```
[Agent Load]
      │
      ▼
┌─────────────────────┐
│  Read map.md         │ → Entry point, get project summary + TOC + triggers
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│  Check Staleness    │ → Alert if status docs are old
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│  Load Relevant      │ → Based on user's request + load triggers
│  Context Files       │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│  Load Status        │ → current.md + progress.md
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│  Context Injection  │ → Add relevant context to agent's prompt
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│  Project Ready       │ → Agent has appropriate context
└─────────────────────┘
```

**Lazy Loading:** The agent does not load everything. Based on `map.md` and the user's request, it loads only the necessary files.

## Agent Behavior Rules

When working with a project that has a `.context/` directory:

1. **Read map.md first** — Always start by reading `.context/map.md` to understand the project structure
2. **Check for staleness** — Alert if status documents are older than threshold, offer to sync
3. **Follow conventions** — Apply the project's `conventions.md` rules, including agent-specific instructions
4. **Respect decisions** — Before proposing to change a tech/stack/pattern, read the related ADR in [[decisions/]]
5. **Update status** — At end of significant work, update [[status/recent.md]] and [[status/progress.md]]
6. **Compare structure** — When navigating, compare actual folder structure with [[structure.md]], propose updates if drift detected
7. **Context-First Workflow** — Before making architectural or stack changes, update the corresponding `.context/definition/` file. Refuse to code until target state is aligned with intention.

## System Prompt: Project Context Protocol (PCP)

To activate this system, insert this in the agent's system prompt or configuration:

```markdown
# Project Context Protocol (PCP)

You are operating in a project equipped with a Project Context System (.context/).

## Initialization Rules

1. ALWAYS start by reading `.context/map.md`. Do not guess the project structure.
2. Based on the user's request, identify which files in `.context/` are relevant using the Load Triggers in map.md.
3. Check the `updated` date in the frontmatter. If a status file is older than 14 days, warn the user and offer to sync it with current project state.
4. Before proposing architectural changes, you MUST read the relevant ADRs in `project/definition/decisions/`.
5. You must follow the conventions defined in `project/rules/conventions/` and respect constraints in `project/rules/constraints/`.

## Context-First Workflow

Any modification of architecture or stack MUST start by updating the corresponding file in `.context/definition/`. Do not code until the target context is aligned with the new intention.

## Output Rule

After completing a significant task, update:
- `project/status/recent.md` — Summarize what was done
- `project/status/progress.md` — Update current session progress
```

## Skills

This system uses the following skills:

| Skill | Purpose |
|-------|---------|
| `project-context` | Main skill for loading and providing project context to the agent |
| `project-init` | Scaffold a new `.context/` structure |

### Project Rules Skills

The Project Rules system provides additional skills:

| Skill | Purpose |
|-------|---------|
| `/check-rules` | Get rules applicable to current task |
| `/add-convention` | Document a new convention |
| `/add-constraint` | Document a constraint |
| `/add-pattern` | Document a pattern |
| `/list-rules` | List all project rules |
| `/review-rules` | Review rules for gaps/contradictions |

See `../project-rules/README.md` for details.

## Future Enhancements

- **Automatic status generation** — Git hooks for `recent.md`, script-based `structure.md` validation
- **Pre-commit validation** — CI check that alerts when context is stale
- **GitHub/Jira integration** — Sync status from external systems