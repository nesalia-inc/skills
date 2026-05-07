# Research-First Protocol

**Purpose:** Ensure the agent always seeks external information from the web or training docs rather than relying on training data knowledge alone.

## Problem

The agent often acts as if it knows everything from training. It:
- Doesn't verify package documentation before using
- Assumes its knowledge of how to use libraries is correct
- Doesn't search for latest tutorials or guides
- Says "I know how to do this" without checking

This leads to using outdated patterns or incorrect API usage.

## Proposed Solution

A protocol that forces the agent to research externally before acting:

| Rule | Description |
|------|-------------|
| **Training-First Rule** | Check training docs in `../tech-knowledge/training/` before searching web |
| **Web-First Rule** | Always search documentation/tutorial before explaining how to use a package |
| **Version Verification** | Query npm registry for actual package versions before use |
| **Knowledge Acknowledgment** | Explicitly state when relying on external research vs training |

## Research Flow

```
Agent needs to work with Technology X
          │
          ▼
┌─────────────────────────────────────┐
│  1. Check training docs              │ → ../tech-knowledge/training/x/
│     (deep knowledge available?)     │
└──────────────────┬──────────────────┘
                   │
          ┌────────┴────────┐
          │                 │
     Yes  │                 │  No
          ▼                 ▼
┌─────────────────┐  ┌─────────────────────────────────┐
│  Use training   │  │  2. Web Research                 │
│  docs + skills   │  │     (external search +          │
└─────────────────┘  │      synthesis)                   │
                     └──────────────────┬──────────────────┘
                                        │
                                        ▼
                              ┌─────────────────────────────────┐
                              │  3. NPM Lookup                  │
                              │     (verify package versions)    │
                              │     npm-lookup/                  │
                              └──────────────────┬──────────────────┘
                                                 │
                                                 ▼
                                       ┌─────────────────────┐
                                       │  Proceed with work   │
                                       │  (cite sources used)  │
                                       └─────────────────────┘
```

### Sub-Systems

| Sub-system | Purpose |
|------------|---------|
| **web-research/** | Search external sources when training is incomplete |
| **npm-lookup/** | Query npm registry for accurate package versions |

## NPM Lookup

When the agent needs to know a package version, it queries npm directly:

```bash
npm view <package> version      # Latest version
npm view <package> versions      # All versions
npm view <package> dist-tags     # Dist-tags (latest, next, beta)
```

### NPM Lookup Output

```markdown
**Package:** zod
**Latest:** 4.2.1
**Released:** 2024-01-15
**Repository:** https://github.com/colinhacks/zod
```

See `npm-lookup/README.md` for details.

## Web Research

When training docs don't have the answer, the agent searches external sources:

```
Training docs missing answer
         │
         ▼
web-research/
- Draft search query
- Execute web search
- Read top results
- Synthesize answer
- Cite sources
```

See `web-research/README.md` for details.

## Knowledge Sources Priority

1. **Training docs** (`../tech-knowledge/training/[tech]/`) — Deep expertise, curated for the org
2. **NPM Lookup** (`npm-lookup/`) — Verify actual package versions
3. **Web search** — Official docs, GitHub, tutorials (via web-research/)
4. **Tech radar** (`../tech-knowledge/tech-radar/`) — Current tech status and alternatives
5. **Training data** — Only as fallback, acknowledge uncertainty

## Acknowledgment Format

When using external sources, explicitly state:

```
"I've verified the latest Zod v4 API by checking [official docs].
The current recommended approach is: ..."
```

When uncertain:

```
"I'm not certain about the latest version of X.
Let me check [source] before proceeding."
```

When using web research:

```markdown
Based on my research:

**Answer:** [Direct answer to question]

**Sources:**
- [Official Docs](url)
- [GitHub Issue](url)
- [Tutorial](url)

**Summary:** [2-3 sentence synthesis]
```

## Integration with Tech Knowledge System

```
Research-First Protocol
         │
         ▼
┌─────────────────────────────────────┐
│  Uses: Tech Knowledge System        │
│  - training/          → deep docs    │
│  - tech-radar/       → tech status   │
│  - latest-version-index/ → versions │
│  - web-research/    → external search│
│  - npm-lookup/     → npm registry   │
└─────────────────────────────────────┘
```

## TODO

- [x] Define research workflow steps
- [x] Define when web search is required vs optional
- [x] Create web-research sub-system
- [x] Create npm-lookup sub-system
- [x] Define knowledge limitation acknowledgment format
- [ ] Create research skill
- [ ] Create npm-lookup skill
- [ ] Define citation format
- [ ] Define integration with training docs (optional update)