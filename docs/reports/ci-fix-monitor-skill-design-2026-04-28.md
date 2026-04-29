# CI Fix & Monitor Skill Design Report

**Date**: 2026-04-28
**Status**: Draft

---

## Executive Summary

The `/ci-fix` skill fixes failing CI/CD pipelines and monitors their health. It diagnoses issues, applies fixes, and alerts when CI is unhealthy.

---

## Purpose

CI/CD is critical for code quality. `/ci-fix` ensures pipelines stay healthy:
- **Diagnoses** failing CI runs
- **Fixes** common issues automatically
- **Monitors** CI health over time
- **Alerts** when intervention is needed
- **Optimizes** slow or flaky pipelines

---

## Two Modes

### Mode 1: Fix (`/ci-fix`)

When CI fails, `/ci-fix`:
1. Fetches failure details
2. Diagnoses the root cause
3. Attempts automatic fix
4. If can't fix, describes what needs manual intervention

### Mode 2: Monitor (`/ci-monitor`)

Periodic health check:
1. Checks all open PRs CI status
2. Identifies flaky tests
3. Reports CI health metrics
4. Alerts on systemic issues

---

## CI Failure Diagnosis

```
CI failed on PR #42
    ↓
┌─────────────────────────────────────────────┐
│  1. Fetch Failure Details                    │
│     - Which step failed?                    │
│     - Error message                         │
│     - Logs (last 100 lines)                │
│     - Which files were changed?             │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  2. Categorize Failure                       │
│     → Test failure                          │
│     → Lint failure                         │
│     → Build failure                         │
│     → Deploy failure                        │
│     → Timeout                               │
│     → Permission error                      │
│     → Unknown                               │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  3. Diagnose Root Cause                      │
│     Match error to known patterns            │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  4. Attempt Fix                             │
│     → Auto-fixable → Apply fix             │
│     → Not auto-fixable → Report issue      │
└─────────────────────────────────────────────┘
```

---

## Common Failures & Fixes

### Test Failures

| Cause | Diagnosis | Fix |
|-------|-----------|-----|
| New code has no tests | Coverage drop | Add tests |
| Existing tests broken | Regressed | Fix test or revert code |
| Flaky test | Non-deterministic | Mark as flaky, retry |
| Missing dependency | Import error | Add to package.json |
| Wrong assertion | Logic error | Fix assertion |

**Auto-fix approach:**
```
If "test failed" AND "no test added in this PR":
    → Add skeleton test for new code
    → Run test again
    → If still fails, report issue
```

### Lint Failures

| Cause | Diagnosis | Fix |
|-------|-----------|-----|
| Formatting | Prettier/ESLint violations | Auto-format |
| Import order | Wrong import grouping | Reorder imports |
| Unused vars | Variable not used | Remove or use |
| Type errors | TS strict mode violations | Add types |

**Auto-fix approach:**
```
If "lint failed":
    → Run lint --fix
    → Commit fix
    → Re-run CI
```

### Build Failures

| Cause | Diagnosis | Fix |
|-------|-----------|-----|
| Type error | TypeScript compilation | Add proper types |
| Missing export | Export not found | Add export |
| Circular dependency | Dependency cycle | Refactor imports |
| Out of memory | Build too large | Increase memory limit |

### Dependency Issues

| Cause | Diagnosis | Fix |
|-------|-----------|-----|
| Missing package | Import error for new pkg | npm install |
| Version conflict | Peer dependency error | Update versions |
| Private registry | npm auth failure | Fix credentials |

---

## CI Monitoring

### `/ci-monitor` Checks

```markdown
## CI Health Report

### Open PRs Status
| PR | CI Status | Duration | Issue |
|----|-----------|----------|-------|
| #42 | ✅ Passing | 3m | - |
| #43 | ❌ Failing | 5m | Test timeout |
| #44 | 🔄 Running | 8m | - |

### Flaky Tests Detected
| Test | Flakiness | Last Failure |
|------|-----------|---------------|
| auth.test.ts | 20% | 2h ago |
| payment.test.ts | 15% | 1d ago |

### Slow Tests
| Test | Duration | Threshold |
|------|----------|-----------|
| integration.test.ts | 45s | 30s |
| e2e.test.ts | 120s | 60s |

### CI Health Score
| Metric | Value | Status |
|--------|-------|--------|
| Success rate | 94% | ✅ Good |
| Avg duration | 4m | ✅ Good |
| Flaky rate | 5% | ⚠️ Needs attention |

### Recommendations
1. Fix `auth.test.ts` - 20% flakiness
2. Speed up `integration.test.ts` - 45s > 30s threshold
```

---

## Alerting

### When to Alert

| Event | Alert Level | Action |
|-------|-------------|--------|
| CI completely down | Critical | Immediate notification |
| >50% of PRs failing | High | Urgent fix needed |
| New flaky test detected | Medium | Investigate soon |
| Slow test (>threshold) | Low | Optimize when convenient |
| CI health declining | Medium | Monitor closely |

### Alert Channels

| Channel | Use Case |
|---------|----------|
| GitHub PR comment | CI failed, needs author attention |
| Notification to Human | Critical issues, systemic problems |
| GitHub Action log | Detailed debug info |

---

## Fix Execution

### Safe vs Risky Fixes

| Fix Type | Risk | Auto-apply? |
|----------|------|-------------|
| Format code | Safe | Yes |
| Add missing import | Safe | Yes |
| Remove unused variable | Safe | Yes |
| Update dependency | Medium | Ask first |
| Change CI config | Risky | Never |
| Refactor code | Risky | Never |

### Fix Flow

```
Auto-fixable?
    ↓ YES
Apply fix → Push to branch → Re-run CI
    ↓ NO
Report: "Cannot auto-fix. [Explanation]"
    ↓
Human intervention needed
```

### Rollback

If fix causes more issues:
```
If CI fails after auto-fix:
    → Revert changes
    → Notify: "Auto-fix caused new failure, reverted"
    → Report issue to Human
```

---

## Skill Usage

### `/ci-fix`

Fix failing CI on current PR.

### `/ci-fix #42`

Fix failing CI on PR #42.

### `/ci-fix --watch`

Watch CI and auto-fix as failures occur.

### `/ci-monitor`

Run CI health check.

### `/ci-monitor --report`

Generate detailed CI health report.

### `/ci-monitor --flaky`

Focus on flaky tests only.

---

## CI Configuration

### Expected CI Setup

```yaml
# GitHub Actions expected workflows
.github/workflows/
├── ci.yml          # Main CI (tests, lint, typecheck)
├── deploy.yml      # Deploy on merge
└── stale.yml       # Close stale PRs
```

### Health Thresholds

| Metric | Healthy | Warning | Critical |
|--------|---------|---------|----------|
| Success rate | >95% | 85-95% | <85% |
| Avg duration | <5min | 5-10min | >10min |
| Flaky rate | <2% | 2-5% | >5% |

---

## Integration with Other Skills

| Skill | Integration |
|-------|-------------|
| `/pr-update` | Triggered when CI status changes |
| `/implement` | Calls `/ci-fix` if CI fails after implementation |
| `/pr-review` | Reports CI status in review |

---

## Open Questions

1. **Auto-fix scope** — What fixes are safe to auto-apply? (See safe vs risky table above)

2. **Notification preferences** — Should all fixes notify Human, or only failures?

3. **CI tool support** — GitHub Actions only? What about CircleCI, Travis?

4. **Fix attribution** — When auto-fix is applied, who gets credit (Human or bot)?

5. **Test coverage for fixes** — Should auto-fixed tests require new tests?

6. **Rebase during fix** — If branch is outdated, auto-rebase before applying fix?

---

## Related Documents

- `docs/reports/pr-review-skill-design-2026-04-28.md` — PR Review (checks CI)
- `docs/reports/pr-update-skill-design-2026-04-28.md` — PR Update (monitors CI)
- `docs/reports/issue-rfc-process-design-2026-04-28.md` — General workflow

---

*This document is a design for a future skill. The skill itself has not been created yet.*
