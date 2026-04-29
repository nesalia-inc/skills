# Metrics Skill Design Report

**Date**: 2026-04-28
**Status**: Draft

---

## Executive Summary

The `/metrics` skill tracks project health metrics over time: code coverage, complexity, test quality, tech debt, and other measurements. It provides dashboards and alerts when metrics degrade.

---

## Purpose

Understanding project health requires measurement:
- **Code coverage** — How much code is tested
- **Complexity** — How complex is the code
- **Tech debt** — What needs refactoring
- **Quality trends** — Is quality improving or degrading
- **Productivity** — Velocity over time

`/metrics` collects, tracks, and reports these metrics.

---

## When to Trigger

| Trigger | Description |
|---------|-------------|
| Manual | Human says `/metrics` or `/metrics report` |
| On PR merge | Collect post-merge metrics |
| Scheduled | Weekly metrics digest |
| On demand | `/metrics --since last-week` |

---

## Metrics Collected

### 1. Code Coverage

```markdown
## Coverage Report

### Overall
| Metric | Value | Trend |
|--------|-------|-------|
| Lines | 78% | ↑ +2% |
| Functions | 82% | → |
| Branches | 71% | ↑ +1% |
| Statements | 79% | ↑ +3% |

### By Module
| Module | Coverage | Trend |
|--------|----------|-------|
| src/auth | 85% | → |
| src/api | 72% | ↑ +5% |
| src/core | 91% | → |
| src/utils | 65% | ↓ -3% |

### Coverage Gaps (Files < 50%)
- src/utils/legacy.ts: 23%
- src/utils/crypto.ts: 41%
- src/core/deprecated.ts: 12%
```

### 2. Complexity

```markdown
## Complexity Report

### By File (Top 10 Most Complex)
| File | Cyclomatic | Cognitive | Lines |
|------|------------|-----------|-------|
| src/auth/oauth.ts | 24 | 18 | 245 |
| src/core/parser.ts | 21 | 22 | 189 |
| src/api/handler.ts | 18 | 15 | 312 |

### Complexity Distribution
| Level | Files | Lines |
|-------|-------|-------|
| Low (1-10) | 45 | 2,340 |
| Medium (11-20) | 23 | 1,890 |
| High (21+) | 8 | 620 |

### Cognitive Complexity Alerts
⚠️ 3 files exceed cognitive complexity threshold (15):
- src/auth/oauth.ts: 18
- src/core/parser.ts: 22
- src/api/handler.ts: 15
```

### 3. Tech Debt

```markdown
## Tech Debt Report

### Quick Wins ( < 1 hour)
| Issue | File | Estimate |
|-------|------|----------|
| TODO comment | src/utils/format.ts:42 | 15m |
| Deprecated function | src/core/old.ts:15 | 30m |
| Magic numbers | src/api/handler.ts:78 | 20m |

### Medium Effort (1-4 hours)
| Issue | File | Estimate |
|-------|------|----------|
| Refactor parser | src/core/parser.ts | 3h |
| Type any | src/auth/oauth.ts:45 | 2h |

### Large Refactors (4+ hours)
| Issue | File | Estimate |
|-------|------|----------|
| Legacy auth module | src/auth/legacy.ts | 8h |
| Old config system | src/core/config/ | 12h |

### Total Tech Debt
- Quick wins: 65m
- Medium: 5h
- Large: 20h
- **Total: ~25h**
```

### 4. Test Quality

```markdown
## Test Quality Report

### Test Suite Health
| Metric | Value |
|--------|-------|
| Total tests | 342 |
| Passing | 338 |
| Failing | 4 |
| Skipped | 0 |
| Flaky | 3 (0.8%) |

### Test Duration
| Suite | Duration | Threshold |
|-------|----------|-----------|
| Unit tests | 12s | 30s |
| Integration | 45s | 60s |
| E2E | 3m12s | 5m |
| **Total** | **4m09s** | **10m** |

### Flaky Tests
⚠️ 3 flaky tests detected:
- auth.test.ts: "should handle timeout" (20% failure)
- payment.test.ts: "process refund" (15% failure)
- sync.test.ts: "concurrent updates" (10% failure)
```

### 5. Productivity Metrics

```markdown
## Productivity Report

### This Week
| Metric | Value |
|--------|-------|
| PRs merged | 12 |
| Issues closed | 8 |
| Lines added | 1,245 |
| Lines removed | 432 |
| Net change | +813 |

### Velocity Trend (Last 4 weeks)
| Week | PRs | Issues | Lines |
|------|-----|---------|-------|
| W12 | 8 | 5 | +450 |
| W13 | 11 | 7 | +890 |
| W14 | 9 | 6 | +620 |
| W15 | 12 | 8 | +813 |

### Average Cycle Time
| Stage | Avg Time |
|-------|----------|
| Issue → PR | 3.2 days |
| PR → Merge | 1.4 days |
| **Total** | **4.6 days** |
```

---

## Metrics Collection

### Tools Used

| Metric | Tool |
|--------|------|
| Coverage | Jest, Istanbul, pytest-cov |
| Complexity | SonarQube, ESLint complexity rules, radon |
| Tech Debt | SonarQube, eslint rules |
| Tests | Jest, pytest, playwright |
| Productivity | GitHub API, git stats |

### Collection Process

```
/metrics --collect
    ↓
┌─────────────────────────────────────────────┐
│  1. Run Coverage                           │
│     - jest --coverage                       │
│     - Parse coverage.json                   │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  2. Analyze Complexity                       │
│     - eslint --complexity                   │
│     - radon cc -a                         │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  3. Check Tech Debt                         │
│     - Run eslint --rules                   │
│     - Parse TODOs, FIXMEs                  │
│     - Check deprecation warnings           │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  4. Gather Productivity                     │
│     - GitHub API: PRs, issues             │
│     - Git stats: lines added/removed     │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  5. Store in Metrics Store                 │
│     - JSON files in .metrics/             │
│     - Historical tracking                  │
│     - Generate report                      │
└─────────────────────────────────────────────┘
```

---

## Metrics Storage

### Structure

```
.metrics/
├── coverage/
│   ├── 2026-04-21.json
│   ├── 2026-04-28.json
│   └── current.json
├── complexity/
│   ├── 2026-04-21.json
│   ├── 2026-04-28.json
│   └── current.json
├── tech-debt/
│   └── current.json
└── productivity/
    ├── weekly-2026-W12.json
    ├── weekly-2026-W13.json
    ├── weekly-2026-W14.json
    └── weekly-2026-W15.json
```

### Historical Tracking

Metrics are stored with timestamps to track trends over time.

---

## Alerting

### Thresholds

```yaml
alerts:
  coverage:
    warning: 70%  # Yellow
    critical: 50%  # Red

  complexity:
    warning: 15  # cognitive
    critical: 25  # cognitive

  tech_debt:
    warning: 10h  # accumulated hours
    critical: 20h

  flaky_tests:
    warning: 2%  # flaky rate
    critical: 5%
```

### Alert Actions

```markdown
## ⚠️ Metrics Alert

### Coverage Drop Detected
Repo: owner/repo
Before: 78%
After: 71%
Change: -7%

PR: #1234 "Add OAuth feature"
Author: @user

### Action Required
Coverage dropped below 75% threshold.
Coverage must improve before merge.
```

---

## Dashboard

### Weekly Digest

```markdown
## Metrics Digest - Week 15 (2026-04-21 to 2026-04-28)

### Overall Health: ⚠️ 73%

### Key Changes
- Coverage: 78% → 76% (-2%)
- Complexity: 12.3 → 11.8 (improved)
- Tech debt: 25h → 23h (reduced 2h)
- Flaky tests: 1% → 2% (⚠️ increased)

### PRs Needing Attention
| PR | Coverage | Action |
|----|----------|--------|
| #1245 | 61% | Must improve to 75% |
| #1247 | 68% | Must improve to 70% |

### Top Complexity Offenders
1. src/auth/oauth.ts: 22 (cognitive)
2. src/core/parser.ts: 21 (cognitive)

### Recommended Actions
1. Fix flaky tests in auth.test.ts
2. Improve coverage in #1245
3. Refactor oauth.ts complexity
```

---

## Skill Usage

### `/metrics`

Show current metrics dashboard.

### `/metrics report`

Full detailed metrics report.

### `/metrics --collect`

Collect fresh metrics (runs tests, coverage, etc).

### `/metrics --since last-week`

Compare with last week.

### `/metrics --since last-month`

Monthly trend report.

### `/metrics --alerts`

Show only items triggering alerts.

### `/metrics --coverage`

Focus on coverage only.

### `/metrics --complexity`

Focus on complexity only.

---

## Integration with Other Skills

| Skill | Integration |
|-------|-------------|
| `/pr-review` | Reports metrics on PR (coverage, complexity) |
| `/ci-fix` | Alerts when metrics degrade CI |
| `/deps-update` | Tracks dependency health |
| `/stale` | Metrics influence stale decisions |

---

## Open Questions

1. **Tooling** — What metrics tools are available? SonarQube self-hosted? Or lightweight alternatives?

2. **Thresholds** — What thresholds make sense for Marty? (Coverage: 70%? 80%?)

3. **Trend analysis** — How many weeks of history to keep? (Recommendation: 12 weeks / quarter)

4. **Cost vs benefit** — Heavy metrics tools (SonarQube) vs lightweight (ESLint + custom scripts)

5. **CI integration** — Collect metrics on every PR? Or only on demand?

---

## Related Documents

- `docs/reports/ci-fix-monitor-skill-design-2026-04-28.md` — CI metrics
- `docs/reports/pr-review-skill-design-2026-04-28.md` — PR metrics

---

*This document is a design for a future skill. The skill itself has not been created yet.*
