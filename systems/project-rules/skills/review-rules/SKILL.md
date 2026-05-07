---
name: review-rules
description: Review rules to identify gaps, contradictions, or outdated content. Use periodically or when user asks to "review our rules".
---

# Review Rules

Review all project rules to identify issues.

## When to Use

- Periodic review (monthly/quarterly)
- User asks to "review our rules" or "audit our conventions"
- After major project changes

## Review Checklist

### 1. Check for Contradictions

```bash
Read all convention files and look for conflicts
```

Examples:
- One rule says "use async/await" another says "use callbacks"
- One pattern says "use repository" another says "use direct queries"

### 2. Verify Alignment with Project Definition

Compare rules against `.context/project/definition/`:
- Do conventions match the target architecture?
- Are patterns aligned with stated principles?

### 3. Identify Gaps

| Area | Questions |
|------|-----------|
| Conventions | Is there a convention for X? (file naming, error handling, etc.) |
| Patterns | Do we have patterns for common problems? |
| Methodologies | Are processes documented? |
| Constraints | Are all hard constraints documented? |

### 4. Check for Stale Content

Check `updated` frontmatter:
- Conventions not updated in 6+ months
- Patterns that reference outdated tech

## Output Format

```markdown
## Rules Review

### Contradictions Found ⚠️
- [[conventions/a.md]] and [[conventions/b.md]] conflict on [topic]

### Gaps Identified
- No convention for [topic] — consider adding
- No pattern for [common problem]

### Stale Content
- [[conventions/old.md]] — not updated in 8 months

### Recommendations
1. Resolve contradiction between A and B
2. Add convention for [missing area]
3. Update [stale file]
```

## Rule for Critical Review

When reviewing rules, apply the "devil's advocate" test:
- Does this rule make sense?
- Is it enforced?
- Is it still relevant?
- What happens if we break it?

## If No Issues Found

```
✓ Rules review complete
No contradictions found.
All rules appear current and aligned.
Consider adding rules for: [identified gaps]
```

## Update README

After review, offer to update `.context/project/rules/README.md` with any changes.