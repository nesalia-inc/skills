# Tech Radar

**Purpose:** Document current technologies, their versions, and alternatives for the agent to consult.

## Concept

The Tech Radar is a curated list of technologies used or considered in the organization, with:
- **Name** — Technology name
- **Version** — Current version (or "latest")
- **Status** — Adopt / Trial / Assess / Hold
- **Alternatives** — Other options considered
- **Notes** — Why this choice was made

## Structure

```
tech-radar/
├── README.md          # This file
├── backend.md         # Backend technologies
├── frontend.md        # Frontend technologies
├── devops.md          # DevOps/infrastructure
├── ai-ml.md           # AI/ML tools
└── general.md         # Cross-cutting tools
```

## Status Definitions

| Status | Meaning |
|--------|---------|
| **Adopt** | Recommended for use, know this well |
| **Trial** | Worth trying on new projects |
| **Assess** | Worth exploring, learn about |
| **Hold** | Avoid or phase out |

## Usage

The agent reads the relevant radar section before recommending technologies.

## TODO

- [ ] Define radar entry format
- [ ] Create example entries
- [ ] Define update workflow