---
name: project-init
description: Scaffold a new .context/ structure for a project. Creates directories, template files, CLAUDE.md, and default content for template files.
---

# Project Init

Scaffold a new `.context/` directory structure for a project. This skill initializes the Project Context System, which provides agents with instant project understanding.

## Purpose

The Project Context System solves the problem of agents having no context when starting a conversation. Instead of guessing project structure, conventions, and status, the agent reads `.context/` files to understand the project immediately.

## What It Does

1. Create the `.context/` directory structure
2. Generate template files with proper YAML frontmatter and DEFAULT CONTENT
3. Create a `CLAUDE.md` with the Project Context Protocol
4. Trigger `context-enrich` to populate initial content

## Directory Structure Created

```
.context/
├── map.md                      # Entry point with project summary + load triggers
├── global/
│   ├── organization.md         # Organization identity
│   └── business.md            # Business context
└── project/
    ├── definition/            # STATIC - target state (what project SHOULD be)
    │   ├── identity.md        # Project vision and goals
    │   ├── architecture/
    │   │   ├── overview.md    # Main components
    │   │   └── data-flow.md   # Data flow
    │   ├── structure.md       # Folder organization rationale
    │   ├── conventions.md     # Coding rules + agent instructions
    │   ├── stack.md          # Technologies
    │   └── decisions/        # ADRs (Architecture Decision Records)
    └── status/               # EPHEMERAL - changes frequently
        ├── current.md       # Current sprint and phase
        ├── active-issues.md  # Current blockers
        ├── recent.md        # Latest changes
        └── progress.md      # Current work session tracking
```

## Template Files with Default Content

### `.context/map.md` (REQUIRED - has default content)

```markdown
---
created: $DATE
updated: $DATE
---

# Project Context Map

## Summary
[One-line project description. Example: E-commerce platform for Nesalia Inc. Built with Node.js/React. Target: 10k daily users.]

## Context System Overview

This project uses the Project Context System (`.context/`). All agent context lives here.

- **Global context:** Organization and business level information
- **Project context:** Definition (static target state) + Status (ephemeral)
- **Entry point:** Always read this file first

## Files

| File | Purpose | Load Trigger |
|------|---------|-------------|
| [[global/organization.md]] | Organization identity | When asked about the organization |
| [[global/business.md]] | Business context | When asked about business goals |
| [[project/definition/identity.md]] | Project vision | On first interaction |
| [[project/definition/architecture/overview.md]] | Components | When understanding system design |
| [[project/definition/architecture/data-flow.md]] | Data flow | When debugging/modifying data flow |
| [[project/definition/structure.md]] | Folder organization | When navigating or adding files |
| [[project/definition/conventions.md]] | Coding rules + agent instructions | Before writing code |
| [[project/definition/stack.md]] | Technologies | Before installing/updating deps |
| [[project/definition/decisions/]] | Architecture decisions | Before refactoring or proposing tech changes |
| [[project/status/current.md]] | Current sprint/phase | At start of each session |
| [[project/status/active-issues.md]] | Blockers | When encountering blockers |
| [[project/status/recent.md]] | Latest changes | After git operations |
| [[project/status/progress.md]] | Work session tracking | At start and end of session |

## Operational Protocol

1. **On Start**: Read this file + `status/current.md` + `status/progress.md`. Perform Context Handshake.
2. **On Code**: Read `definition/conventions.md` before writing.
3. **On Tech Change**: Check `definition/decisions/` before proposing.
4. **On Task Completion**: Update `status/recent.md`.
5. **On Commit/PR**: Update `status/progress.md` if work in progress.
6. **On Architecture/Stack Change**: Update target `definition/*.md` first, then code.
```

### `.context/global/organization.md` (template)

```markdown
---
created: $DATE
---

# Organization Identity

## Name
[Organization name]

## Description
[Short description of who we are]

## Size
[Small/Medium/Large team]

## Mission
[Organization's core mission statement]
```

### `.context/global/business.md` (template)

```markdown
---
created: $DATE
---

# Business Context

## Domain
[Industry/business domain]

## Business Model
[How the business makes money]

## Target Customers
[Who are the customers/users]

## Strategic Objectives
[Top 3-5 strategic goals]
```

### `.context/project/definition/identity.md` (template)

```markdown
---
created: $DATE
---

# Project Identity

## Name
[Project name]

## Purpose
[What problem does this project solve?]

## Target Users
[Who uses this project?]

## Success Metrics
[How is success measured?]

## Target State
[What is the desired end state? Describe the vision.]
```

### `.context/project/definition/architecture/overview.md` (template)

```markdown
---
created: $DATE
---

# Architecture Overview

## Components
[List main components and their responsibilities]

## Interactions
[How components communicate]

## External Dependencies
[APIs, services, third-parties]
```

### `.context/project/definition/architecture/data-flow.md` (template)

```markdown
---
created: $DATE
---

# Data Flow

## Entry Points
[How data enters the system]

## Processing
[How data is processed]

## Storage
[Where data is stored]

## Output
[How data exits or is exposed]
```

### `.context/project/definition/structure.md` (template)

```markdown
---
created: $DATE
---

# Project Structure

## Root Organization
[Explain the root-level folders and their purpose]

## Key Directories
[For each key directory, explain why it exists]

## Naming Conventions
[How files and folders are named]

## Entry Points
[Where does the application start?]
```

### `.context/project/definition/conventions.md` (template)

```markdown
---
created: $DATE
---

# Coding Conventions

## Naming
[Naming conventions for files, variables, functions, etc.]

## Code Style
[Indentation, line length, formatting rules]

## Testing Approach
[How tests are organized and written]

## Git Workflow
[Commit conventions, branch strategy]

## Agent Instructions
[Specific instructions for how the agent should behave on this project]
```

### `.context/project/definition/stack.md` (template)

```markdown
---
created: $DATE
---

# Technology Stack

## Languages
[Languages used]

## Frameworks
[Main frameworks and their purpose]

## Libraries
[Key libraries and why they're chosen]

## Infrastructure
[Where and how the app is deployed]

## Environments
[Development, staging, production]
```

### `.context/project/definition/decisions/` (empty directory, first ADR will be 0001)

### `.context/project/status/current.md` (template)

```markdown
---
created: $DATE
---

# Current Status

## Sprint/Phase
[Current sprint name or project phase]

## Goals
[What's being worked on this period]

## Progress
[Overall project progress summary]
```

### `.context/project/status/active-issues.md` (template)

```markdown
---
created: $DATE
---

# Active Blockers

## Current Blockers
[List of current issues blocking progress]

## Resolved This Week
[Blockers that were resolved]
```

### `.context/project/status/recent.md` (template)

```markdown
---
created: $DATE
---

# Recent Changes

## Changes
[Summary of recent changes, organized by date]
```

### `.context/project/status/progress.md` (template)

```markdown
---
created: $DATE
---

# Session Progress

## Current Task
- Task: [What is being worked on]
- Status: [In progress/Complete/Blocked]
- Next: [Next step]

## Completed This Session
[List of completed items]
```

## CLAUDE.md Content

Create a `CLAUDE.md` at the project root with this content:

```markdown
# Project Context

This project uses the **Project Context System** (`.context/`). This system provides agents with instant project understanding by defining context once and reusing it across conversations.

## Quick Start

1. **Read `.context/map.md`** — Always start here to understand the project
2. **Follow Entry Points** — Read context files only when needed (lazy loading)
3. **Update context on task completion** — Keep status files current
4. **Check ADRs before tech changes** — Understand past decisions first

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
    │   └── decisions/    # Architecture Decision Records
    └── status/           # Ephemeral: current state
        ├── current.md
        ├── active-issues.md
        ├── recent.md
        └── progress.md
```

## Entry Points (When to Read/Write)

| When... | Read | Write |
|---------|------|-------|
| Conversation starts | `.context/map.md` + `status/*` | — |
| Before writing code | `definition/conventions.md` | — |
| Before tech changes | `definition/decisions/*.md` | — |
| Task completes | — | `status/recent.md` |
| Before commit/PR | — | `status/progress.md` |
| Major decision made | — | Create ADR in `decisions/` |

## Important Rules

1. **Read map.md first** — Never guess project structure
2. **Target state, not current state** — Definition files describe what the project SHOULD be
3. **Context-First Workflow** — Update context before coding when architecture/stack changes
4. **Check ADRs before questioning choices** — Read decisions before proposing tech changes
5. **Use real dates** — Always use system date, never training data cutoff

## Skills

Available skills for this system:
- `/project-context` — Load and provide project context
- `/context-check` — Validate context alignment
- `/create-adr` — Document a technical decision
- `/review-adr` — Read ADRs before proposing changes
- `/context-update` — Update status files
```

## After Initialization

After creating all files, invoke the `context-enrich` skill to populate the definition files with real content by asking the user comprehensive questions.