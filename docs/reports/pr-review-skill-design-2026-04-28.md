# PR Review Skill Design Report

**Date**: 2026-04-28
**Status**: Draft

---

## Executive Summary

The `/pr-review` skill reviews GitHub Pull Requests automatically. It checks code quality, adherence to standards, test coverage, and provides actionable feedback. This is the Reviewer Agent in action.

---

## Purpose

When a PR is created or updated, `/pr-review` provides:
- **Automated code review** without human waiting
- **Standards compliance check** (formatting, naming, patterns)
- **Test coverage assessment**
- **Security vulnerability scan** (basic)
- **Feedback quality** (constructive, actionable)

---

## When to Trigger

| Trigger | Description |
|---------|-------------|
| Manual | Human says `/pr-review` or `/pr-review #42` |
| On PR creation | Automatically when PR is opened |
| On PR update | Automatically when new commits are pushed |
| Scheduled | Daily check on open PRs |

---

## Review Process

```
/pr-review #42
    ↓
┌─────────────────────────────────────────────┐
│  1. Fetch PR Details                        │
│     - Title, description, files changed      │
│     - PR author                              │
│     - Linked issue/RFC                       │
│     - Branch comparison                       │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  2. Read Changed Files                       │
│     - All modified files                      │
│     - All new files                          │
│     - Deleted files                          │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  3. Standards Check                           │
│     - Code formatting                        │
│     - Naming conventions                     │
│     - File structure                         │
│     - Import ordering                        │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  4. Test Coverage Check                      │
│     - Are tests added/changed?               │
│     - Test quality                           │
│     - Edge cases covered?                    │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  5. Security Scan                            │
│     - Hardcoded secrets?                     │
│     - SQL injection risks?                   │
│     - XSS vulnerabilities?                   │
│     - Unsafe dependencies?                   │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  6. RFC Compliance Check                     │
│     - Does implementation match RFC?          │
│     - Are all RFC items addressed?           │
│     - Scope creep detected?                  │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  7. Write Review                             │
│     - Post comments on PR                    │
│     - Summarize findings                     │
│     - Approve / Request Changes             │
└─────────────────────────────────────────────┘
```

---

## Review Checklist

### Code Quality
- [ ] Code follows project style guidelines
- [ ] Naming conventions are consistent
- [ ] No commented-out code
- [ ] No TODO/FIXME without linked issue
- [ ] Functions are reasonably sized (< 50 lines)
- [ ] No deeply nested logic (> 3 levels)

### Testing
- [ ] Tests added for new functionality
- [ ] Tests modified for changed functionality
- [ ] Edge cases are covered
- [ ] No tests skipped or commented
- [ ] Test files follow naming convention

### Security
- [ ] No secrets in code (API keys, tokens, passwords)
- [ ] User input is sanitized
- [ ] SQL queries use parameterized statements
- [ ] Output is properly escaped
- [ ] Dependencies are up-to-date

### Documentation
- [ ] New public APIs are documented
- [ ] Complex logic has comments
- [ ] README updated if needed
- [ ] CHANGELOG updated for user-facing changes

### RFC Compliance
- [ ] Implementation matches approved RFC
- [ ] No scope creep beyond RFC
- [ ] All acceptance criteria addressed

---

## Review Output

### Comment Types

| Type | When | Example |
|------|------|--------|
| **Approve** | All checks pass | "LGTM, ready to merge" |
| **Request Changes** | Issues found | "Please fix the security issue..." |
| **Comment** | Suggestions only | "Consider using X here..." |
| ** Nitpick** | Minor, optional | "Nit: could rename to Y..." |

### PR Comment Example

```markdown
## PR Review: #42 - Add GitHub OAuth

### Summary
✅ **Approved** - Ready to merge

### Changes
- 12 files changed
- 847 lines added, 23 lines deleted
- Linked to RFC-0042

### Code Quality
✅ No issues found

### Test Coverage
⚠️ **Missing tests** for `src/auth/github.ts`
- New file has no tests
- Please add tests for `getGitHubRepos()` function

### Security
✅ No secrets detected
✅ Input sanitization in place

### RFC Compliance
✅ Matches RFC-0042 specification

### Suggestions (Optional)
- Consider extracting `getAccessToken` to a utility file for reusability

### Files needing attention
- `src/auth/github.ts` - Add tests (blocking)
```

---

## Review Labels

| Label | Applied When |
|-------|--------------|
| `review/approved` | PR passes all checks |
| `review/changes-requested` | Issues found, needs fixes |
| `review/commented` | Feedback provided, no blocking |
| `review/security-issue` | Security vulnerability found |
| `review/tests-needed` | Test coverage insufficient |
| `review/rfc-compliant` | Matches approved RFC |

---

## Severity Levels for Issues Found

| Severity | Blocking | Example |
|----------|----------|---------|
| **Critical** | Yes | Security vulnerability, secrets exposed |
| **Major** | Yes | Breaking changes, missing tests |
| **Minor** | No | Style issues, suggestions |
| **Nitpick** | No | Very minor, cosmetic |

---

## Integration with Other Skills

| Skill | Integration |
|-------|-------------|
| `/implement` | Calls `/pr-review` after creating PR |
| `/pr-update` | Triggers `/pr-review` after update |
| `/create-issue` | PR linked to issue |
| `/create-rfc` | RFC number in PR description |

---

## Bot Account

PR reviews should be posted from a dedicated bot account (e.g., `@marty-reviewer`) to:
- Separate from Human reviews
- Allow automated and manual review flow
- Maintain clear audit trail

---

## Open Questions

1. **Should reviews be blocking?** (i.e., PR can't merge without approval)

2. **Merge vs Rebase** — Should the bot suggest one over the other?

3. **Review frequency** — How often to re-review on new commits? (Immediately? Batch?)

4. **Human override** — Can Human approve despite bot requesting changes?

5. **Review history** — Should bot's previous reviews be considered for context?

6. **Multi-PR reviews** — How to handle PRs that depend on other PRs?

---

## Related Documents

- `docs/reports/pr-creation-skill-design-2026-04-28.md` — Creating PRs
- `docs/reports/pr-update-skill-design-2026-04-28.md` — Updating PRs
- `docs/reports/issue-rfc-process-design-2026-04-28.md` — General workflow

---

*This document is a design for a future skill. The skill itself has not been created yet.*
