---
name: project-context
description: Load and provide project context to the agent. Automatically reads .context/map.md and enforces the Project Context System entry points.
---

# Project Context

This skill provides the agent with instant project understanding by managing the `.context/` directory.

## What is the Project Context System?

When an AI agent starts a conversation, it has no context about the project. It must guess the domain, rebuild structure mentally, and rediscover conventions — wasting time and producing inconsistent results.

The **Project Context System** solves this by defining context once in `.context/` files. The agent reads them at startup to understand the project immediately.

### How It Works

```
User starts conversation
       │
       ▼
┌─────────────────────────────────────┐
│  1. Read .context/map.md             │ → Entry point, project summary
└──────────────────┬──────────────────┘
                   │
                   ▼
┌─────────────────────────────────────┐
│  2. Check staleness                  │ → Alert if status files are old
└──────────────────┬──────────────────┘
                   │
                   ▼
┌─────────────────────────────────────┐
│  3. Load relevant files             │ → Based on task (lazy loading)
│     per Load Triggers in map.md      │
└──────────────────┬──────────────────┘
                   │
                   ▼
┌─────────────────────────────────────┐
│  4. Context Handshake               │ → "Context Loaded: Project X..."
└──────────────────┬──────────────────┘
                   │
                   ▼
         Agent has full context
```

### Key Concepts

| Concept | Description |
|---------|-------------|
| **Target State** | Definition files describe what the project SHOULD be, not the current messy reality |
| **Lazy Loading** | Don't load everything — load only what's relevant to the current task |
| **Entry Points** | Triggers that tell the agent when to read/write context files |
| **Context Pointers** | `[[path/to/file.md]]` links — use these, not relative paths |
| **ADRs** | Architecture Decision Records in `decisions/` — read before questioning choices |
| **Staleness** | Status files older than threshold (14 days) need update |

## Directory Structure

```
.context/
├── map.md                 # ENTRY POINT - read first
├── global/               # Organization + business context
│   ├── organization.md
│   └── business.md
└── project/
    ├── definition/       # STATIC - target state
    │   ├── identity.md
    │   ├── architecture/
    │   │   ├── overview.md
    │   │   └── data-flow.md
    │   ├── structure.md
    │   ├── conventions.md
    │   ├── stack.md
    │   └── decisions/    # ADRs - document why choices were made
    └── status/           # EPHEMERAL - changes frequently
        ├── current.md
        ├── active-issues.md
        ├── recent.md
        └── progress.md
```

## Loading Flow

### Step 1: Read map.md

Always start here. It contains:
- Project summary (1-2 lines)
- Table of all context files with Load Triggers
- Operational Protocol for the agent

### Step 2: Check Staleness

Check `updated` frontmatter on status files:
- Status docs: stale after 14 days
- Definition docs: stale after 90 days

If stale, offer to sync:
> "I notice `[[status/current.md]]` is 20 days old. Should I analyze recent commits to update it?"

### Step 3: Lazy Load Based on Task

Use the Load Triggers from `map.md`. Examples:

| Task | Files to Load |
|------|--------------|
| Understand project | `map.md` + `identity.md` |
| Write code | `conventions.md` + `structure.md` |
| Install deps | `stack.md` |
| Propose tech change | Relevant ADR(s) in `decisions/` |
| Debug | `architecture/data-flow.md` + relevant ADR |
| Start session | `map.md` + `status/current.md` + `status/progress.md` |

### Step 4: Context Handshake

After loading context, signal to the user:

```
Context Loaded: [Project Name] ([Sprint/Phase]).
- [X] active ADRs
- [Y] blockers in [[project/status/active-issues.md]]
- Status: [up-to-date/stale]

Ready to proceed.
```

Example:
```
Context Loaded: Nesalia E-commerce (Sprint 4).
- 3 active ADRs
- 1 blocker in [[project/status/active-issues.md]]
- Status: up-to-date

Ready to proceed.
```

## Entry Points

These triggers tell you when to READ or WRITE to context files.

### Read Triggers

| When... | Read | Why |
|---------|------|-----|
| Conversation starts | `.context/map.md` | Get overview + navigation |
| Before writing code | `[[project/definition/conventions.md]]` | Apply project rules |
| Before installing deps | `[[project/definition/stack.md]]` | Know target stack |
| Before tech changes | `[[project/definition/decisions/*.md]]` | Understand past decisions |
| When navigating | `[[project/definition/structure.md]]` | Respect folder logic |
| At session start | `[[project/status/current.md]]` | Know current sprint |
| At session start | `[[project/status/progress.md]]` | Know ongoing work |
| When blocked | `[[project/status/active-issues.md]]` | Check existing blockers |
| After git ops | `[[project/status/recent.md]]` | Track changes |

### Write Triggers

| When... | Write | What |
|---------|-------|------|
| Task completes | `[[project/status/recent.md]]` | Summary of what was done |
| Before commit/PR | `[[project/status/progress.md]]` | Session progress |
| Major decision made | `[[project/definition/decisions/000X-title.md]]` | New ADR |
| Blocker encountered | `[[project/status/active-issues.md]]` | Update blockers |
| Architecture changes | `[[project/definition/*.md]]` | Update target state first |

## Lazy Loading Rules

1. **Don't load everything** — Only load files relevant to the current task
2. **Use Load Triggers** — `map.md` tells you when to load each file
3. **Load progressively** — Start with `map.md`, add files as needed
4. **Context Pointers** — Use `[[path]]` format, not relative paths

### Context Pointer Format

Always use `[[path/to/file.md]]` format for links within `.context/`:

```
Good:  See [[project/definition/conventions.md]]
Bad:   See ../project/definition/conventions.md
```

## Rules the Agent Must Follow

### Rule 1: Read map.md First

> Never guess project structure. Always read `.context/map.md` first.

### Rule 2: Check Staleness on Status Files

> If `updated` is older than 14 days, alert the user and offer to sync.

### Rule 3: Follow Entry Points

> Use the Read/Write triggers to know when to access context files.

### Rule 4: Context-First on Architecture Changes

> Before changing architecture/stack, update the corresponding `definition/*.md` first. Code follows context, not the other way around.

### Rule 5: Check ADRs Before Questioning Choices

> Before proposing to replace a library/pattern/approach, read the related ADR in `[[decisions/]]`. Only propose changes after understanding the original reasoning.

### Rule 6: Use Real System Dates

> Always use the actual system date (from OS/environment) for frontmatter and staleness calculations. Never use training data cutoff date.

### Rule 7: Offer Context Updates

> On task completion and before commit/PR, OFFER to update context files. Don't update automatically — let the user confirm.

## Important Notes

- **Target state, not current state** — Definition files describe the desired future, not the current messy reality. This helps identify gaps.
- **ADRs preserve reasoning** — When you find an ADR, summarize it for the user. If none exists when questioning a choice, suggest creating one.
- **Staleness alerts are mandatory** — Don't skip staleness checks. They prevent the agent from working with outdated information.
- **Handshake confirms activation** — The Context Handshake tells the user the system is active and warns of any staleness issues.

## Skills Integration

The `project-context` skill works with:
- `/context-check` — Validate context alignment (run on session start or demand)
- `/context-update` — Update status files (called on task completion)
- `/create-adr` — Document major decisions
- `/review-adr` — Read ADRs before proposing changes