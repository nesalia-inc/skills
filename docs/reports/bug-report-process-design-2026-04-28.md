# Bug Report Process Design Report

**Date**: 2026-04-28
**Status**: Draft

---

## Executive Summary

Bug reports are a special case of issues. While most issues are proactive (features, refactors), bugs are reactive (something is broken). This document designs a process for handling bug reports that integrates with the existing Issue/RFC workflow.

---

## Bug Report vs Regular Issue

| Aspect | Bug Report | Regular Issue |
|--------|------------|---------------|
| **Trigger** | Reactive (something broke) | Proactive (planned work) |
| **Discovery** | User report, monitoring, testing | Planned |
| **Urgency** | Often high | Usually low-medium |
| **RFC needed** | Rarely (unless architectural) | Often |
| **Process** | Fast-track to fix | Full workflow |

---

## Bug Report Skill

### `/report-bug` or `/bug`

**Trigger**: `/bug [description]` or `/report-bug`

**What it does**:
1. Creates a GitHub Issue with `bug` label
2. Prompts for or extracts:
   - Steps to reproduce
   - Expected behavior
   - Actual behavior
   - Environment/context
3. Assesses severity automatically from description
4. Assigns priority based on severity

---

## Bug Report Template

When `/bug` is triggered, the resulting GitHub Issue should follow this structure:

```markdown
---
title: [Concise description of the bug]
type: bug
severity: [critical/high/medium/low]
status: open
created: YYYY-MM-DD
reporter: [human/agent name]
---

## Bug Description
[Clear description of what goes wrong]

## Steps to Reproduce
1. [First step]
2. [Second step]
3. [Third step]

## Expected Behavior
[What should happen]

## Actual Behavior
[What actually happens]

## Environment
- OS: [e.g., Windows 11]
- Context: [e.g., during auth flow, when running tests]

## Screenshots / Logs
[Any relevant screenshots or error messages]

## Severity Assessment
| Severity | Criteria | Priority |
|---------|----------|----------|
| Critical | Data loss, security breach, complete breakage | p0 |
| High | Major feature broken, no workaround | p1 |
| Medium | Feature partially works, workaround exists | p2 |
| Low | Minor issue, cosmetic | p3 |

---

## Bug Severity Levels

| Severity | Label | Response Time | Example |
|----------|-------|----------------|---------|
| **Critical** | `severity/critical` | Immediate | Data corruption, auth bypass |
| **High** | `severity/high` | Same day | Feature completely broken |
| **Medium** | `severity/medium` | This week | Feature partially works |
| **Low** | `severity/low` | Backlog | Typo, minor UI glitch |

---

## Bug Triage Process

```
Bug Report Created
    ↓
┌─────────────────────────────────────────────┐
│  1. Severity Assessment                     │
│     Agent or Human assesses severity        │
│     → Labels: severity/* + priority/*      │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  2. Workaround Check                        │
│     Is there a known workaround?            │
│     → Add to issue description              │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  3. Root Cause Assessment                   │
│     How complex is the fix?                 │
│     → Small fix → Direct to Builder        │
│     → Complex → Consider RFC (rare)        │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  4. Assignment                              │
│     → Assign to appropriate agent           │
│     → Set status = in-progress             │
└─────────────────────────────────────────────┘
```

---

## Bug vs RFC Decision

Most bugs should NOT have RFCs. Use this decision tree:

```
Is this a new feature being built incorrectly?
    YES → Create RFC
    NO ↓

Is this a complex architectural issue?
    YES → Create RFC
    NO ↓

Is this a straightforward bug fix?
    YES → NO RFC needed
    NO ↓

Uncertain?
    → Ask Human (CTO/CEO)
```

**When a bug DOES need an RFC:**
- Bug reveals a fundamental architectural flaw
- Fix requires significant design decision
- Fix will constrain future development

---

## Integration with Existing Skills

| Skill | Role in Bug Flow |
|-------|------------------|
| `/bug` or `/report-bug` | Creates bug issue with severity |
| `/triage` | Assesses severity, assigns |
| `/create-issue` | Can create bug issues too |
| `/implement` | Implements the fix |

---

## Bug Metrics (Future)

Once this process is established, we could track:

| Metric | Description |
|--------|-------------|
| **MTBF** | Mean Time Between Failures |
| **MTTR** | Mean Time To Resolution |
| **Bug count by severity** | How many critical vs low bugs |
| **Bug count by component** | Which parts have most bugs |
| **Bug reopen rate** | How often bugs come back |

---

## Example Bug Workflows

### Example 1: Critical Bug

```
Human: "/bug le login crash quand je me connecte avec GitHub"
    ↓
Agent creates Issue #47
- Title: "Login crash with GitHub OAuth"
- Labels: bug, severity/critical, priority/p0
- Status: open
    ↓
Critical = immediate attention
    ↓
Agent assigned for hotfix
    ↓
Fix implemented, PR merged
Issue #47 closed
```

### Example 2: Medium Bug with Workaround

```
Human: "/bug la page de profil ne charge plus depuis hier"
    ↓
Agent creates Issue #48
- Title: "Profile page not loading"
- Labels: bug, severity/medium, priority/p2
- Status: open
    ↓
Agent identifies workaround: "Clear cache and re-login"
Workaround added to issue
    ↓
Assigned to Builder agent
    ↓
Fix implemented in next sprint
Issue #48 closed
```

---

## Open Questions

1. **Who assesses severity?** Human or agent? (Recommendation: Agent first, Human confirms for p0/p3)

2. **Should bugs auto-create a branch?** `bugfix/issue-47-login-crash` ? (Recommendation: Yes)

3. **Should there be a bug template in GitHub itself?** `.github/ISSUE_TEMPLATE/bug_report.md` in addition to `/bug` skill?

4. **Hotfix process** — Critical bugs that need immediate fix:
   - Skip PR review?
   - Merge directly to main?
   - Notify after?

---

## Related Documents

- `docs/reports/issue-rfc-process-design-2026-04-28.md` — General issue/RFC process
- `docs/reports/init-labels-skill-design-2026-04-28.md` — Label system (includes `severity/*` labels)

---

*This document should be updated as the bug report process evolves.*
