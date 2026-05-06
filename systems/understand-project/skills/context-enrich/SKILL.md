---
name: context-enrich
description: Populate static context documents with real content by asking the user questions via AskUserQuestion.
---

# Context Enrich

Populate the static definition files in `.context/` with real content by asking comprehensive questions to the user.

## What is the Project Context System?

The **Project Context System** solves a fundamental problem: when an AI agent starts a conversation, it has no context about the project. It must guess the domain, rebuild the structure mentally, and rediscover conventions — wasting time and producing inconsistent results.

The system works by defining context once in `.context/` files, then the agent reads them at startup to understand the project immediately.

### How It Works

```
Agent starts conversation
       │
       ▼
Read .context/map.md (entry point - project summary + navigation)
       │
       ▼
Load relevant context files based on task (lazy loading)
       │
       ▼
Context Handshake: "Context Loaded: Project X (Sprint 4). 2 ADRs. 1 blocker. Ready."
       │
       ▼
Agent works with full project understanding
```

### Key Concepts

| Concept | Description |
|---------|-------------|
| **Target State** | Definition files describe what the project SHOULD be, not the current messy reality |
| **Lazy Loading** | Don't load everything — load only what's relevant to the current task |
| **Entry Points** | Triggers that tell the agent when to read/write context files |
| **Context Pointers** | `[[path/to/file.md]]` links that create a navigable context network |
| **ADRs** | Architecture Decision Records — document why choices were made |

### Directory Structure

```
.context/
├── map.md                 # Entry point - always read first
├── global/               # Organization + business context (same across projects)
│   ├── organization.md   # "Who are we?"
│   └── business.md       # "What business are we in?"
└── project/              # Project-specific context
    ├── definition/       # STATIC - target state (what project should become)
    │   ├── identity.md   # Project vision
    │   ├── architecture/ # Components + data flow
    │   ├── structure.md  # Folder organization
    │   ├── conventions.md # Coding rules + agent instructions
    │   ├── stack.md      # Technologies
    │   └── decisions/    # Architecture Decision Records
    └── status/           # EPHEMERAL - changes frequently
        ├── current.md    # Current sprint/phase
        ├── active-issues.md # Current blockers
        ├── recent.md     # Latest changes
        └── progress.md   # Current work session
```

## Purpose of This Skill

After `project-init` creates the empty `.context/` structure, `context-enrich` fills it with real content by asking the user comprehensive questions.

Without enrichment, the context files are just empty templates. This skill makes the system actually useful.

## Files to Populate

Populate in this order (logical flow from high-level to specific):

### Phase 1: Global Context (same for all projects)

1. **[[global/organization.md]]** — Organization identity
   - Name, description, size, mission

2. **[[global/business.md]]** — Business context
   - Domain, business model, target customers, strategic objectives

### Phase 2: Project Definition (static, target state)

3. **[[project/definition/identity.md]]** — Project identity
   - Name, purpose, target users, success metrics, target state vision

4. **[[project/definition/architecture/overview.md]]** — Components
   - Main components, their responsibilities, how they interact

5. **[[project/definition/architecture/data-flow.md]]** — Data flow
   - Entry points, processing, storage, output

6. **[[project/definition/structure.md]]** — Project structure
   - Root organization, key directories, naming conventions, entry points

7. **[[project/definition/conventions.md]]** — Coding standards
   - Naming, code style, testing approach, git workflow, agent instructions

8. **[[project/definition/stack.md]]** — Technology stack
   - Languages, frameworks, libraries, infrastructure, environments

### Phase 3: Status (ephemeral, will change)

9. **[[project/status/current.md]]** — Current status
   - Sprint/phase, goals, progress

10. **[[project/status/active-issues.md]]** — Blockers
    - Current blockers, resolved this week

### Phase 4: Map Update

11. **[[map.md]]** — Update summary and load triggers with actual content

## Question Strategy

### For Organization (global/organization.md)

```markdown
Questions to ask:
1. "What is the organization name?"
2. "How would you describe the organization in one sentence?"
3. "How big is the team?" (single-select: Small/Medium/Large)
4. "What is the organization's mission statement?"
```

### For Business (global/business.md)

```markdown
Questions to ask:
1. "What industry/business domain is this organization in?"
2. "How does the business make money?" (membership, product sales, services, etc.)
3. "Who are the target customers/users?"
4. "What are the top 3-5 strategic objectives?" (multi-select or free text)
```

### For Project Identity (project/definition/identity.md)

```markdown
Questions to ask:
1. "What is the project name?"
2. "What problem does this project solve? What need does it address?"
3. "Who are the target users of this project?"
4. "How would you measure success for this project?"
5. "What is the vision for the project? What should it become?"
```

### For Architecture (project/definition/architecture/*.md)

```markdown
Questions to ask:
1. "What are the main components of this system?" (multi-select or free text)
2. "How do these components communicate?" (REST, events, direct calls, etc.)
3. "What are the external dependencies?" (APIs, services, third-parties)
4. "How does data enter the system?"
5. "How is data processed and stored?"
6. "How does data exit or get exposed?"
```

### For Structure (project/definition/structure.md)

```markdown
Questions to ask:
1. "Walk me through the root-level folders. What is their purpose?"
2. "Is there a specific logic to how files are organized?"
3. "What naming conventions are used?"
4. "Where is the main entry point of the application?"
```

### For Conventions (project/definition/conventions.md)

```markdown
Questions to ask:
1. "What naming conventions are used?" (e.g., camelCase, kebab-case, etc.)
2. "Are there specific code style rules?" (indentation, line length, etc.)
3. "How are tests organized and written?"
4. "What is the git workflow?" (conventional commits, branch naming, etc.)
5. "Any specific instructions for how an AI agent should behave?"
```

### For Stack (project/definition/stack.md)

```markdown
Questions to ask:
1. "What programming languages are used?"
2. "What are the main frameworks and their purpose?"
3. "What key libraries are used and why were they chosen?"
4. "Where is the application deployed?" (which cloud/infra)
5. "What are the different environments?" (dev, staging, prod)
```

## Method

1. **Start with organization** — It's the highest level and doesn't depend on project details
2. **Use AskUserQuestion tool** — For all user interactions
3. **Choose question type wisely:**
   - Multi-select when multiple options apply
   - Single-select for clear choices
   - Free-text for nuanced or unique information
4. **Apply Pareto principle** — Get the essential 20%, don't drill on details
5. **Accept general answers** — "It's a React app" is enough, don't ask for every library
6. **Update files as you go** — Write content to files after gathering info
7. **Update frontmatter** — Set `created` date when file is first populated
8. **Update map.md last** — With the actual summary and correct load triggers

## Signals to Move to Next File

When to stop asking and move on:
- Core purpose is identified
- User gives general/vague answer (accept it, don't drill)
- You've hit the "must-have" questions for that file
- Further questions would be "nice-to-have" that can be added later

## Pareto Principle: When to Stop

More questions ≠ better context. Get the essential 20% that provides 80% of value.

### Rule: Stop when you have the essential answer

For each file, there are:
- **Must-have** questions (core identity, non-negotiable)
- **Nice-to-have** questions (details that can be filled later)
- **Skip entirely** questions (overly specific, rarely needed)

### By File: Essential Only

| File | Must-have (stop here) | Nice-to-have (skip for now) |
|------|----------------------|----------------------------|
| **organization.md** | Name + one-line description | Size, mission statement |
| **business.md** | Domain + business model | Strategic objectives, target customers details |
| **identity.md** | Project name + core purpose | Success metrics, target state vision |
| **architecture/overview.md** | Main components (3-5 max) | Detailed responsibilities, all interactions |
| **architecture/data-flow.md** | Entry point + final output | Every intermediate step |
| **structure.md** | Root folders + entry point | Every subdirectory explanation |
| **conventions.md** | Naming + git workflow | Code style details, testing approach |
| **stack.md** | Language + main framework | Library list, environment details |

### Signal to Stop Asking

Stop and move to next file when:
- You have enough to describe the file's core purpose
- Further questions would provide diminishing returns
- The user gives a vague/general answer (accept it, don't drill deeper)

### Example

```
User: "It's a React app with Node backend"
→ Accept. Don't ask:
  - Which React version? Why not Vue?
  - Which Node version? Express or Fastify?
  - Why not Next.js/NestJS?
→ Move to next question/file.
```

## Important Notes

- **Target state, not current state** — When filling definition files, describe what the project should be, not necessarily what it currently is. This allows identifying gaps.
- **Use real dates** — Always use system date (from OS), never training data cutoff
- **Context Pointers** — Use `[[path/to/file.md]]` format for links between context files
- **Be thorough** — The quality of this enrichment determines how well the agent understands the project

## Output

At the end:
- All definition files populated with real content
- `map.md` has correct summary and load triggers
- `status/current.md` and `status/active-issues.md` have initial content
- Agent can understand the project immediately from `.context/` files