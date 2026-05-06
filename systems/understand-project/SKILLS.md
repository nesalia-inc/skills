# Skills Inventory

This document lists the skills required to implement the Project Context System. Each skill has a specific role and triggers. Content is not yet written.

---

## Core Skills

### `project-context`

**Role:** Main skill for loading and providing project context to the agent.

**Triggers:**
- Conversation starts (auto-load on project detection)
- User requests project understanding
- Agent needs to understand project structure/conventions

**Files involved:**
- `.context/map.md` (entry point)
- All definition and status files

**Responsibilities:**
- Load `map.md` and provide context summary
- Perform Context Handshake
- Guide lazy loading based on task
- Enforce Entry Points rules

**Type:** Core skill, always available in context

---

### `project-init`

**Role:** Scaffold a new `.context/` structure for a project.

**Triggers:**
- User asks to "init context" or "setup project context"
- New project without `.context/` detected

**Files created:**
```
.context/
├── map.md
├── global/
│   ├── organization.md
│   └── business.md
└── project/
    ├── definition/
    │   ├── identity.md
    │   ├── architecture/
    │   │   ├── overview.md
    │   │   └── data-flow.md
    │   ├── structure.md
    │   ├── conventions.md
    │   ├── stack.md
    │   └── decisions/
    └── status/
        ├── current.md
        ├── active-issues.md
        ├── recent.md
        └── progress.md
```

**Responsibilities:**
- Create directory structure
- Generate template files with proper frontmatter
- Help populate initial content (identity, architecture, etc.)

**Type:** On-demand skill, invoked when setting up

---

## Context Management Skills

### `context-update`

**Role:** Update context files when triggered by Entry Points.

**Triggers:**
- Task completion (update `recent.md`)
- Pre-commit/PR (update `progress.md`)
- Blocker identified (update `active-issues.md`)
- Structure drift detected (update `structure.md`)

**Files involved:**
- `[[project/status/recent.md]]`
- `[[project/status/progress.md]]`
- `[[project/status/active-issues.md]]`
- `[[project/definition/structure.md]]`

**Responsibilities:**
- Detect what needs updating based on recent work
- Generate appropriate updates
- Maintain `updated` frontmatter timestamp

**Type:** Utility skill, called during workflow

---

### `context-check`

**Role:** Validate context alignment with reality (stale detection, structure drift).

**Triggers:**
- Conversation starts (stale check)
- User asks to "check context" or "validate context"
- Pre-commit hook

**Files involved:**
- All `.context/` files (check `updated` dates)
- Actual project structure (compare with `structure.md`)
- Git history (check against `recent.md`)

**Responsibilities:**
- Detect stale documents
- Compare actual structure with `structure.md`
- Offer to sync/update outdated files
- Report blockers status

**Type:** Utility skill, invoked on demand or at session start

---

## ADR Skills

### `create-adr`

**Role:** Create an Architecture Decision Record when major technical decisions are made.

**Triggers:**
- Agent makes significant technical choice
- User asks to "document this decision" or "create ADR"
- Before proposing tech/stack changes (to document the current decision)

**Files created:**
- `[[project/definition/decisions/000X-title.md]]`

**Responsibilities:**
- Guide agent through ADR template
- Determine next ADR number
- Ensure context is captured (why this choice, not alternatives)
- Create properly formatted file with frontmatter

**Type:** On-demand skill, invoked during decision-making

---

### `review-adr`

**Role:** Read and summarize relevant ADRs before proposing changes.

**Triggers:**
- Before proposing tech/stack changes
- When agent questions existing architecture/stack
- User asks "why was this chosen?" or "what's the reasoning?"

**Files involved:**
- `[[project/definition/decisions/*.md]]`

**Responsibilities:**
- Find relevant ADRs based on the proposed change
- Summarize the decision rationale
- Explain why the current approach was chosen
- Block refactoring proposals until ADRs are understood

**Type:** Utility skill, called before tech changes

---

## Setup Skills

### `project-init`

**Role:** Scaffold a new `.context/` structure for a project.

**Triggers:**
- User asks to "init context" or "setup project context"
- New project without `.context/` detected

**Files created:**
```
.context/
├── map.md
├── global/
│   ├── organization.md
│   └── business.md
└── project/
    ├── definition/
    │   ├── identity.md
    │   ├── architecture/
    │   │   ├── overview.md
    │   │   └── data-flow.md
    │   ├── structure.md
    │   ├── conventions.md
    │   ├── stack.md
    │   └── decisions/
    └── status/
        ├── current.md
        ├── active-issues.md
        ├── recent.md
        └── progress.md
```

**Responsibilities:**
- Create directory structure
- Generate template files with proper frontmatter
- Create/update `CLAUDE.md` to document how the Project Context System works
- Trigger `context-enrich` to populate initial content

**CLAUDE.md Content:**
The `project-init` skill must create a `CLAUDE.md` that explains:
- The existence and purpose of `.context/` directory
- How to use the context system (map.md, entry points, etc.)
- The Project Context Protocol (PCP) for the agent
- How to invoke relevant skills (project-context, create-adr, etc.)

**Type:** On-demand skill, invoked when setting up

---

### `context-enrich`

**Role:** Populate static context documents (definition files) with real content by asking the user questions via `AskUserQuestion`.

**Triggers:**
- After `project-init` creates the structure
- User asks to "enrich context" or "populate context"
- User asks to "describe the project" or "setup project context"

**Files populated:**
- `[[global/organization.md]]`
- `[[global/business.md]]`
- `[[project/definition/identity.md]]`
- `[[project/definition/architecture/overview.md]]`
- `[[project/definition/architecture/data-flow.md]]`
- `[[project/definition/structure.md]]`
- `[[project/definition/conventions.md]]`
- `[[project/definition/stack.md]]`
- `[[map.md]]`

**Responsibilities:**
- Ask comprehensive questions to gather project information
- Use `AskUserQuestion` tool to interact with the user
- Populate each document with real content based on answers
- Update frontmatter `created` dates
- Ensure `map.md` is updated with correct file references and load triggers

**Methodology:**
- Ask questions in logical order (organization → business → project → technical details)
- Use multi-select questions when multiple options apply
- Use single-select for clear choices
- Leave room for free-text where needed
- Iterate until all definition files are populated

**Type:** Setup skill, invoked after `project-init` or on user request

---

### `context-claude-integration`

**Role:** Integrate `.context/` with Claude Code's native systems (CLAUDE.md, auto memory, rules).

**Triggers:**
- During `project-init` setup
- When user asks to "link context to Claude"
- Before first use of context system

**Responsibilities:**
- Create/update `CLAUDE.md` to reference `.context/`
- Setup path-scoped rules in `.claude/rules/` that reference context files
- Guide auto memory usage (synthesize context into MEMORY.md)
- Ensure context loads before project rules

**Type:** Setup skill, used during initial configuration

---

## Skills Summary Table

| Skill | Role | Type | Trigger |
|-------|------|------|---------|
| `project-context` | Main context loader and provider | Core | Always available |
| `project-init` | Scaffold `.context/` structure | Setup | On-demand |
| `context-enrich` | Populate definition files via questions | Setup | After init or on request |
| `context-update` | Update status files | Utility | On task completion, pre-commit |
| `context-check` | Validate and detect drift | Utility | On-demand, session start |
| `create-adr` | Create decision records | Utility | On major decisions |
| `review-adr` | Read and explain ADRs | Utility | Before tech changes |
| `context-claude-integration` | Link to Claude Code systems | Setup | During initial setup |

---

## Skills Hierarchy

```
project-context (core)
    ├── Reads map.md
    ├── Enforces Entry Points
    └── Uses:
         ├── context-check (for stale detection)
         ├── context-update (for status writes)
         └── review-adr (before proposing changes)

project-init
    └── Triggers:
         └── context-enrich (to populate initial content)
              └── context-claude-integration (to setup CLAUDE.md)

create-adr
    └── Used by context-update (when major decision made)
```

---

## Implementation Priority

1. **Phase 1 (Core):**
   - `project-context` — the main skill
   - `project-init` — to create structure

2. **Phase 2 (Setup):**
   - `context-enrich` — populate definition files via questions
   - `context-claude-integration` — link to Claude Code systems

3. **Phase 3 (Management):**
   - `context-check` — stale detection
   - `context-update` — status updates

4. **Phase 4 (ADR):**
   - `create-adr` — decision documentation
   - `review-adr` — ADR lookup