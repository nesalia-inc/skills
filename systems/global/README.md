# Global Systems

This directory contains documentation for organization-wide systems that apply across all projects.

---

## Project Context System

**Location:** `../understand-project/`

**Purpose:** Provide AI agents with instant project understanding when loading a conversation. The agent immediately knows what the project is, how it works, its current status, and relevant conventions.

**Key Concepts:**
- **Target State** — Definition files describe what the project SHOULD be, not current reality
- **Lazy Loading** — Load only relevant context files based on task
- **Entry Points** — Triggers that tell the agent when to read/write context files
- **ADRs** — Architecture Decision Records preserving the "why" behind choices

**Structure:**
```
.context/
├── map.md                 # Entry point (read first)
├── global/               # Organization + business context
│   ├── organization.md
│   └── business.md
└── project/
    ├── definition/       # Static target state
    │   ├── identity.md
    │   ├── architecture/
    │   ├── structure.md
    │   ├── stack.md
    │   └── decisions/    # ADRs
    ├── rules/           # How to work in this project
    │   ├── conventions/ # Naming, code style, git workflow
    │   ├── patterns/    # Common solutions
    │   ├── methodologies/ # Team processes
    │   └── constraints/  # Hard limitations
    └── status/           # Ephemeral current state
        ├── current.md
        ├── active-issues.md
        ├── recent.md
        └── progress.md
```

**Documentation:**
- `../understand-project/README.md` — System overview
- `../understand-project/ENTRY-POINTS.md` — When to read/write context files
- `../understand-project/SKILLS.md` — Skills inventory
- `../understand-project/ADR-PROCESS.md` — How to create and use ADRs

---

## Project Rules System

**Location:** `../project-rules/`

**Purpose:** Provide project-specific conventions, patterns, methodologies, and constraints once project context is understood.

**Integration:** Project Rules is integrated into the Project Context System under `.context/project/rules/`. See `../project-rules/README.md` for full details.

**Key Concepts:**
- **Conventions** — "How we do X" — agreed standards
- **Patterns** — "When facing Y, use Z" — reusable solutions
- **Methodologies** — "How we work as a team" — processes
- **Constraints** — "We must/can't do X" — limitations

**Documentation:**
- `../project-rules/README.md` — System overview
- `../project-rules/SKILLS.md` — Skills for managing rules

---

## Future Systems

Other organization-wide systems will be documented here as they are created.