---
name: context-update
description: Update context files when triggered by Entry Points (task completion, pre-commit, blocker identified).
---

# Context Update

Update context files when triggered by the Entry Points system.

## What is the Project Context System?

The **Project Context System** solves a fundamental problem: when an AI agent starts a conversation, it has no context about the project. It must guess the domain, rebuild structure mentally, and rediscover conventions.

Context files live in `.context/` and are read by the agent at startup. Status files (ephemeral) change frequently and must be kept current.

This skill handles updates to those status files.

### How It Fits In

```
project-context (core skill)
    │
    └── Uses: context-update (writes status files)

Skills workflow:
  /project-context → loads context, detects trigger
  /context-check → reports stale/drift issues
  /context-update → applies fixes (called by project-context)

Entry Points that trigger updates:
  - Task completion → recent.md
  - Pre-commit → progress.md
  - Blocker → active-issues.md
  - Structure drift → structure.md
```

## When to Offer Updates

Never update automatically. OFFER to update and wait for confirmation.

| Trigger | File to Update | Example Offer |
|---------|---------------|---------------|
| Task/feature completes | `[[project/status/recent.md]]` | "Task complete. Update recent.md with a summary?" |
| Before commit/PR | `[[project/status/progress.md]]` | "Before committing, should I update progress.md?" |
| Blocker identified | `[[project/status/active-issues.md]]` | "Found a blocker. Want me to document it?" |
| Architecture/stack changes | Corresponding `[[project/definition/*.md]]` | "This architecture change needs context update first." |
| Structure drift detected | `[[project/definition/structure.md]]` | "Structure has drifted. Should I update structure.md?" |

## Update Format Rules

### Frontmatter

Always include both dates:

```markdown
---
created: 2024-01-15
updated: 2024-05-06
---
```

- `created` — Set once at file creation
- `updated` — Set every time file is modified
- Use real system date (from OS), never training data cutoff

### Content Structure

Use consistent headers. Date sections for recent.md:

```markdown
## Recent Changes

### 2024-05-06
- Summary of changes

### 2024-05-04
- Previous changes
```

For progress.md, use checkbox format:

```markdown
## Completed This Session
- [x] Task 1
- [x] Task 2
```

## How to Update Each File

### recent.md (Task Completion)

After completing a significant task, add entry:

```markdown
---
created: 2024-01-15
updated: 2024-05-06
---

## Recent Changes

### 2024-05-06
- Implemented user authentication with JWT
- Added session management via Redis
- Created [[decisions/0015-use-redis-for-session-caching.md]]

### Previous entries...
```

Content should be:
- Concise summary of what was done
- Links to related ADRs if created
- References to relevant PRs if merged

### progress.md (Pre-Commit)

Before commit/PR, document current work state:

```markdown
---
created: 2024-01-15
updated: 2024-05-06
---

## Session Progress

### Current Task
- Task: [What is being worked on]
- Status: [In progress / Complete / Blocked]
- Next: [Next step after this commit]

### Completed This Session
- [x] JWT authentication implementation
- [x] Redis session caching
- [x] ADR-0015 documentation

### Previous Session (if any)
- Task: Previous task name
- Status: Complete
- Link: PR #123
```

### active-issues.md (Blocker Identified)

When encountering or resolving a blocker:

```markdown
---
created: 2024-01-15
updated: 2024-05-06
---

## Active Blockers

### 2024-05-06
- **Auth module:** Waiting for API keys from third-party provider
- **Database migration:** Blocked by backup completion

## Resolved This Week
- **CI pipeline:** Was failing due to missing env var, now fixed
```

### structure.md (Structure Drift)

When structure has changed:

```markdown
---
created: 2024-01-15
updated: 2024-05-06
---

# Project Structure

## Root Organization
- `src/` — Application source code
- `tests/` — Test files
- `docs/` — Documentation
- `scripts/` — Build/automation scripts

## Key Directories
...

## Changes from Previous
- Added `scripts/` directory for build automation
- Moved `utils/` to `src/common/` (refactoring)
```

## Important Rules

1. **Never update automatically** — Always OFFER, never force
2. **Use real dates** — System date from OS, not training cutoff
3. **Include updated frontmatter** — Every update sets `updated`
4. **Link related context** — Use `[[path/to/file.md]]` pointers
5. **Be concise** — Summary, not detailed log

## What This Skill Does vs What Others Do

| This Skill Does | context-check Does |
|-----------------|-------------------|
| Updates status files | Detects what needs updating |
| Modifies recent.md, progress.md, etc. | Reports stale/drift |
| Fixes issues | Finds issues |

This skill modifies files. `context-check` only validates and reports.