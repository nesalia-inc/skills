# Tech Recommandations

**Purpose:** Document technology choices and the reasoning behind them — why we recommend X over Y.

## Concept

For each technology decision, document:
- **What** — Technology recommended
- **Why** — Reasoning for this choice
- **Why not alternatives** — Why other options were rejected
- **When to use** — Appropriate use cases
- **When not to use** — Situations to avoid

## Contrast with Tech Radar

| Aspect | Tech Radar | Tech Recommendations |
|--------|------------|----------------------|
| Content | All tech status | Recommendations for specific use cases |
| Focus | What exists | What to choose and why |
| Audience | Broader awareness | Decision guidance |

## Structure

```
tech-recommandations/
├── README.md          # This file
├── backend.md        # Backend recommendations
├── frontend.md       # Frontend recommendations
├── devops.md         # Infrastructure recommendations
└── templates/
    └── recommendation.md  # Template for new entries
```

## Usage

The agent consults this when:
- Deciding between multiple technologies
- Recommending a specific approach
- Justifying a choice to users

## TODO

- [ ] Define recommendation entry format
- [ ] Create template
- [ ] Add initial recommendations