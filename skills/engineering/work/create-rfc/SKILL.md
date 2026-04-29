---
name: create-rfc
description: Create an RFC proposal from an issue that needs planning. Use when user says "create rfc", "propose rfc", "this needs planning", or when /fix-issue refuses an issue because it needs planning.
disable-model-invocation: false
context: fork
agent: general-purpose
permissionMode: bypassPermissions
allowed-tools: Bash, Read, Glob, Grep, Write, Edit
---

# Create RFC from Issue Guide

Creates an RFC proposal based on an issue that requires planning. The RFC is created on a branch, then a PR is created to discuss it.

## When to Use

- `/fix-issue` refused an issue because it needs planning
- User says "create rfc for issue X"
- Issue is too complex for direct implementation

## RFC Creation Criteria

An issue needs RFC when:
- Requires architecture decision
- Affects multiple systems or layers
- Changes core patterns or paradigms
- Needs detailed implementation plan
- Is a large refactoring
- Has unclear scope that needs breakdown

## Step 1: Analyze the Issue

```bash
gh issue view <number>
```

Understand:
- What problem is this solving?
- What are the constraints?
- What alternatives exist?

## Step 2: Create Branch

```bash
git checkout -b rfc/issue-<number>-<short-title>
```

## Step 3: Create RFC Document

```bash
cat > docs/rfc/RFC-XXX-issue-<number>.md << 'EOF'
# RFC-XXX: [Issue Title]

## Status
Proposed

## Motivation
[Why is this change needed? What problem does it solve?]

## Detailed Specification
[What exactly will be implemented? Be specific about:
- New APIs, interfaces, behaviors
- Data models
- Workflows
- Error handling]

## Implementation Plan
[High-level phases or steps]

## Alternatives Considered
[What else was considered and why this approach was chosen]

## Open Questions
[Things to resolve before implementation]

## Risks
[Potential problems and mitigations]

## Related Issues
#<number>
EOF
```

## Step 4: Link Issue to PR

Link the issue to the development branch:

```bash
gh issue develop <number> --base canary --name rfc/issue-<number>-<short-title>
```

## Step 5: Add Labels

Add appropriate labels to the issue:

```bash
# Mark as needs-review and add rfc label
gh issue edit <number> --add-label "rfc/draft" --add-label "status/in-review" --add-assignee "@me"
```

## Step 6: Commit RFC

```bash
git add docs/rfc/RFC-XXX-issue-<number>.md
git commit -m "rfc: propose RFC-XXX for issue #<number>

Created RFC proposal based on issue #<number>.
Requires planning before implementation.

Co-Authored-By: martyy-code <nesalia.inc@gmail.com>"
```

## Step 7: Push and Create PR

```bash
git push -u origin HEAD

cat > /tmp/pr-body.md << 'EOF'
## Summary
RFC proposal for issue #<number>

This RFC proposes a solution for the planning-required issue.

## Motivation
[Brief from issue]

## Status
Proposed - ready for discussion

Co-Authored-By: martyy-code <nesalia.inc@gmail.com>
EOF

gh pr create --title "[RFC]: Issue #<number> - [Brief Title]" --body-file /tmp/pr-body.md --base canary
rm /tmp/pr-body.md
```

## Step 8: Comment on Original Issue

```bash
gh issue comment <number> --body "This issue requires planning and a solution has been proposed.

RFC created: docs/rfc/RFC-XXX-issue-<number>.md
PR for discussion: [PR URL]

The RFC is ready for review and discussion before implementation can begin."
```

## Example Workflow

```
/create-rfc 45

Reading issue #45...
Analyzing...

Issue #45: Refactor data layer to use classes instead of objects.

Creating branch rfc/45-data-layer-refactor...
Creating RFC document...
Committing...
Pushing...
Creating PR to canary...

RFC created: docs/rfc/RFC-0001-issue-45.md
PR: https://github.com/owner/repo/pull/X

Commented on issue #45 to link RFC.
```

## Quick Reference

```
/create-rfc <issue-number>
→ Analyze issue
→ Create branch rfc/issue-<number>-<title>
→ Create RFC in docs/rfc/
→ Commit and push
→ Create PR to canary
→ Comment on original issue
```

## Related Skills

| Skill | When to Use |
|-------|-------------|
| `/fix-issue` | When issue is ready for direct implementation |
| `/commit` | Commit RFC |
| `/create-pr` | PR creation details |