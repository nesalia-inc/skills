# RFC Process for Marty - Design Report

**Date**: 2026-04-28
**Status**: Draft
**Sources**: Rust RFC process, LLVM RFC process, Fuchsia RFC process

---

## Executive Summary

This document outlines a proposed RFC (Request for Comments) process for Marty, inspired by best practices from major open source projects (Rust, LLVM, Fuchsia). The goal is to create a lightweight but structured approach for making technical decisions, suitable for a solo developer with agent assistance.

---

## Why an RFC Process?

The current workflow for new features in Marty is:
1. Discussion (research, learning)
2. Documentation (`docs/learnings/`, `docs/plans/`)
3. **Gap**: Paralysis when transitioning to implementation

An RFC process provides:
- **Structured thinking**: Force clarification of motivation, alternatives, drawbacks before implementation
- **Decision record**: Historical document of why decisions were made
- **Scope definition**: Prevents feature creep by defining boundaries early
- **CTO/CEO approval gate**: Human (you) reviews and approves before implementation

---

## When to Use RFC

Not everything needs an RFC:

| Does NOT need RFC | Needs RFC |
|-------------------|-----------|
| Bug fixes | New features with architectural impact |
| Hotfixes (1-liners) | Changes that constrain future development |
| Documentation improvements | Changes to system architecture |
| Small, bounded changes | Policy or convention changes |
| Research/learning (no code) | Refactors that change behavior |

**Rule of thumb**: If you don't know exactly what to build or how it fits in, write an RFC first.

---

## RFC Lifecycle in Marty

```
┌─────────────────────────────────────────────────────┐
│  1. Problem Statement                                │
│     - What's the problem?                            │
│     - Why does it matter?                            │
│     - Who is affected?                               │
└─────────────────┬───────────────────────────────────┘
                  ↓
┌─────────────────────────────────────────────────────┐
│  2. RFC Draft                                        │
│     - Paired with Archi/Tech Lead agent              │
│     - Written in docs/rfc/RFC-XXXX-title.md          │
│     - Structured document (see template below)       │
└─────────────────┬───────────────────────────────────┘
                  ↓
┌─────────────────────────────────────────────────────┐
│  3. Review & Feedback                               │
│     - Self-review (you are CTO/CEO)                 │
│     - Agent may identify gaps                        │
│     - Revisions as needed                            │
└─────────────────┬───────────────────────────────────┘
                  ↓
┌─────────────────────────────────────────────────────┐
│  4. Approval                                        │
│     - You (CTO/CEO) approve or reject               │
│     - Status: Approved / Rejected / Postponed        │
└─────────────────┬───────────────────────────────────┘
                  ↓
┌─────────────────────────────────────────────────────┐
│  5. Implementation                                  │
│     - Assign to Builder agent                       │
│     - Reference RFC in commits/PRs                  │
└─────────────────────────────────────────────────────┘
```

---

## RFC vs Implementation

An RFC describes the **APPROACH** (what and why), NOT the implementation (how):

| | RFC | Implementation |
|--|-----|----------------|
| **When** | BEFORE approval | AFTER approval |
| **Contains** | Design, approach, decisions | Code, configs, tests |
| **Purpose** | Decide IF and WHAT to build | Build it |
| **Written by** | Archi agent + Human | Builder agent |

### What an RFC Should NOT Contain

- No YAML or workflow files
- No configuration files
- No code snippets (only examples for illustration)
- No step-by-step implementation instructions

### What an RFC Should Contain

- **What** we want to build (high-level)
- **Why** we want to build it
- **Why NOT** alternative approaches
- **What** similar projects have done (prior art)
- **What could go wrong** (risks)
- **What's still open** (open questions)

### Example: RFC vs Implementation

**RFC says**: "We will use a GitHub Action to parse conventional commits and generate CHANGELOG.md entries via a PR"

**Implementation (later)**: The Builder agent writes `.github/workflows/changelog.yml` with specific YAML

---

## RFC Template

Based on Rust RFC template, simplified for Marty:

```markdown
---
RFC: XXXX
Title: [Descriptive Title]
Status: Proposed
Author: [Human Name]
Date: YYYY-MM-DD
---

## Summary

One paragraph explanation of what this RFC proposes.

## Motivation

Why are we doing this? What problem does it solve?

Describe:
- The current state / pain point
- Who needs this and why
- What happens if we don't address it

## Proposed Solution

What exactly will be built or changed?

Be specific about:
- The approach (not the implementation)
- Key decisions made
- What the system does (not how it works internally)

## Alternatives Considered

What other approaches were considered? Why were they rejected?

For each alternative:
- Describe the approach
- Why not chosen (complexity, tradeoffs, etc.)

## Prior Art

How have other projects/systems solved this?

Include:
- Links to relevant documentation
- Lessons learned from other implementations
- What patterns exist in Marty already that relate

## Drawbacks / Risks

What could go wrong? What are the disadvantages?

Be honest about:
- Added complexity
- Breaking changes
- Migration needs
- Ongoing maintenance burden

## Open Questions

What is not yet decided? What needs more investigation?

List:
- Questions to resolve during implementation
- Potential changes if requirements shift
- Items deferred to future work

## Related RFCs

- Related: [RFC-XXX](link) - brief connection explanation
- Supersedes: [RFC-XXX](link) - if applicable

---

## Status History

| Date | Status | Notes |
|------|--------|-------|
| YYYY-MM-DD | Proposed | Initial draft |

---

## Status Definitions

| Status | Meaning |
|--------|---------|
| **Proposed** | Under discussion, not yet approved |
| **Approved** | Accepted, ready for implementation |
| **Rejected** | Not accepted, rationale documented |
| **Postponed** | Deferred to later, may be revived |
| **Superseded** | Replaced by another RFC |

---

## File Naming and Issue Linking

RFCs and GitHub Issues share the same number:
- GitHub Issue #42
- RFC-0042 in `docs/rfc/RFC-0042-title.md`

This creates a clear link between tracking (GitHub) and design (RFC doc).

```
docs/rfc/
├── RFC-0001-automated-changelog.md    # Linked to GitHub Issue #1
├── RFC-0002-first-feature.md         # Linked to GitHub Issue #2
└── ...
```

## RFC = Dynamic Document

Unlike traditional RFCs (written once, then static), Marty RFCs are **enriched iteratively**:

1. Draft created by agent from issue context
2. Human discusses ideas with agents
3. RFC document updated to reflect decisions
4. When approved, RFC is "complete"

This allows the RFC to capture the **reasoning process**, not just the final decision.

---

## Related Documents

This RFC process design is part of the larger Issue and RFC process. See `docs/reports/issue-rfc-process-design-2026-04-28.md` for the complete picture including:
- GitHub Issues and `/create-issue` skill
- How Issues and RFCs interact
- Complete workflow examples

---

## Integration with Agents

### Archi/Tech Lead Agent (RFC Authoring)

When you want to write an RFC:
```
@archi: "Let's write an RFC for adding GitHub OAuth"
→ Archi agent reads the codebase to understand context
→ Archi agent helps structure the RFC
→ Archi agent writes first draft in docs/rfc/
→ You review and approve
```

The Archi agent should:
- Read existing codebase context before drafting
- Reference related RFCs for consistency
- Suggest alternatives and identify gaps
- Write APPROACH in `docs/rfc/`, NOT implementation details

### Builder Agent (RFC Implementation)

When implementing an approved RFC:
```
@builder: "Implement RFC-0002"
→ Builder reads RFC-0002 (APPROACH)
→ Builder writes the actual code (YAML, configs, etc.)
→ Builder references RFC number in commits
```

The Builder agent:
- Reads the RFC to understand WHAT to build and WHY
- Makes implementation decisions (how)
- Writes the actual implementation files
- Does NOT re-discuss the approach (that's already approved)

### Reviewer Agent (RFC Review)

Before approval, RFC can be reviewed:
```
@reviewer: "Review RFC-0002 for quality"
→ Reviewer checks completeness of template
→ Reviewer identifies gaps in reasoning
→ Reviewer suggests improvements
```

---

## Comparison with Source Projects

| Aspect | Rust | LLVM | Fuchsia | Marty (Proposed) |
|--------|------|------|---------|------------------|
| Template | Detailed | Light | Detailed | Medium |
| Process steps | 6+ | 4 | 8+ | 5 |
| Review period | Yes | No | Yes (7 days) | Until approved |
| Facilitator | Sub-team | No | Yes | Agent assist |
| Stakeholders | Community | Discourse | Multiple | Self (solo) |
| RFC numbers | Yes | Yes | Yes | Yes |

**Key insight**: Marty can be lighter than all of these since you're the only decision-maker, but the structured thinking (alternatives, drawbacks, prior art) is still valuable.

---

## Next Steps

1. Create `docs/rfc/` directory structure
2. Create RFC template in `docs/rfc/RFC-0001-template.md`
3. Define Archi/Tech Lead agent with RFC authoring instructions
4. Test with a real feature idea

---

## Appendix: Rust RFC Template (Full)

The Rust RFC template is more detailed. For reference, key sections:

- **Summary**: One paragraph
- **Motivation**: Problem + specific use cases + why it helps
- **Guide-level explanation**: Teaching the feature to someone (accessible)
- **Reference-level explanation**: Technical details, corner cases, implementation hints
- **Drawbacks**: Why NOT do this?
- **Rationale and alternatives**: Why this design over others?
- **Prior art**: What did other languages/projects do?
- **Unresolved questions**: What remains to be decided?
- **Future possibilities**: Natural extensions (not a reason to accept)

---

## Appendix: Fuchsia RFC Process Steps

For reference, Fuchsia's 8 steps (overkill for Marty but educational):

1. Identify the problem (socialize early)
2. Problem statement (write it up)
3. Assign facilitator (FEC helps)
4. Stakeholder discovery (who cares?)
5. Socialization (discuss solutions)
6. Draft and iteration (write RFC, get feedback)
7. Last call (final 7-day review)
8. Submit (accepted/rejected)

Marty's simplified version removes:
- Multi-stakeholder review
- Last call period
- Facilitator role (replaced by agent assist)

---

*This document should be updated as the RFC process evolves for Marty.*
