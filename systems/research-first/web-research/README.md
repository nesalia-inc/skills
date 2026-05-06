# Web Research System

**Purpose:** When training docs lack information, the agent can perform web research to find answers from external sources (official docs, GitHub, tutorials, community).

## Problem

Training docs can't cover everything. When the agent encounters:
- A topic not in training
- A question without answer in training
- Latest information not yet documented

It needs a way to research externally and integrate findings.

## Solution

A web research protocol that:
1. Takes a question
2. Searches external sources
3. Synthesizes and cites findings
4. Optionally updates training docs

## Structure

```
web-research/
├── README.md           # This file
└── skills/
    └── web-search/     # Skill to perform web research
        └── SKILL.md
```

## Research Flow

```
Question from training or user
         │
         ▼
┌─────────────────────────────────────┐
│  1. Draft search query              │
│     "How to handle X in Zod v4?"    │
└──────────────────┬──────────────────┘
                   │
                   ▼
┌─────────────────────────────────────┐
│  2. Execute web search              │
│     (WebSearch tool)                 │
└──────────────────┬──────────────────┘
                   │
                   ▼
┌─────────────────────────────────────┐
│  3. Read top results                │
│     (WebFetch relevant pages)       │
└──────────────────┬──────────────────┘
                   │
                   ▼
┌─────────────────────────────────────┐
│  4. Synthesize answer               │
│     Cite sources used               │
└──────────────────┬──────────────────┘
                   │
          ┌────────┴────────┐
          │                 │
    Update training    Just answer
          │                 │
          ▼                 ▼
    Add to training    Return answer
    docs (optional)    with citations
```

## Search Query Strategy

| Question Type | Search Strategy |
|---------------|-----------------|
| API usage | `"zod" "v4" "official docs" [specific API]` |
| Latest version | `[package name] "latest version" "2024"` |
| Best practice | `[package] "best practice" "tutorial"` |
| Error solution | `[package] "[error code]" "stackoverflow"` |
| Migration | `[package] "migration" "v3 to v4"` |

## Output Format

When answering from web research:

```markdown
Based on my research:

**Answer:** [Direct answer to question]

**Sources:**
- [Official Docs](url) — Primary source, most reliable
- [GitHub Issue](url) — For edge cases or bugs
- [Tutorial](url) — For examples

**Summary:** [2-3 sentence synthesis]

**Confidence:** High/Medium/Low
- High: Multiple sources agree
- Medium: Some sources conflict or limited info
- Low: Rare case, minimal documentation
```

## When to Use

| Situation | Action |
|-----------|--------|
| Training doesn't have the answer | Research externally |
| New version released | Search for changelog |
| Unclear error | Search error message |
| Unknown pattern | Search best practices |

## Integration

```
Research-First Protocol
         │
         ▼
┌─────────────────────────────────────┐
│  Training docs missing answer?      │
│         │                           │
│         ▼                           │
│  Web Research                       │
│  (external search + synthesis)      │
└─────────────────────────────────────┘
```

## Skills

### `web-search`

Skill that guides the agent through effective web research:

```yaml
---
name: web-search
description: Perform web research to find answers from external sources
---

# Web Research Protocol

1. **Analyze question** — What specific info is needed?
2. **Draft query** — Effective search query formulation
3. **Execute search** — Use WebSearch tool
4. **Read results** — Fetch relevant pages
5. **Synthesize** — Combine findings
6. **Cite** — Reference sources used
```

## TODO

- [ ] Define web research skill
- [ ] Define citation format
- [ ] Create update-training integration
- [ ] Define confidence levels