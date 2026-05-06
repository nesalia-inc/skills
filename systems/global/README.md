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
    │   ├── conventions.md
    │   ├── stack.md
    │   └── decisions/    # ADRs
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

## Future Systems

Other organization-wide systems will be documented here as they are created.