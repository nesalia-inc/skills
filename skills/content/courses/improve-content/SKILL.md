---
name: improve-content
description: Improve existing training content following Marty documentation rules. Use this skill when asked to improve, update, or enhance existing courses, tutorials, or educational material. Example: "/improve-content path/to/course.md"
disable-model-invocation: false
context: fork
agent: general-purpose
permissionMode: bypassPermissions
allowed-tools: Bash, Read, Write, Edit
---

# Improve Content Guide

Improves existing training content following Marty documentation rules.

## Marty Content Rules

**See `./rules.md` for the complete rules.** Summary:

1. **Authoritative Voice** - "We do X", not "Here's how you might..."
2. **Forward-Looking Only** - No "Previously..." or "Originally..."
3. **Complete Information** - Explicit, actionable, no gaps
4. **Practical Examples** - Real code, not placeholders
5. **Progressive Complexity** - Simple → Advanced
6. **Hands-On Exercises** - Practice reinforces learning

## What to Improve

### When Improving Content, Focus On:

1. **Voice** - Does it sound authoritative?
2. **Completeness** - Are steps actionable and complete?
3. **Examples** - Are they real and practical?
4. **Structure** - Is it progressive complexity?
5. **Freshness** - Is information current?

### Common Improvements:

- Rewrite "Here's how you might..." → "We do..."
- Remove "Previously..." statements
- Add missing steps or context
- Replace placeholder examples with real ones
- Reorganize for progressive complexity

## Process

### Step 1: Read Existing Content

```bash
cat path/to/content.md
```

### Step 2: Identify Issues

Check for:
- Non-authoritative voice
- Historical traces (Previously, Originally, Before)
- Gaps in steps
- Placeholder examples
- Outdated information

### Step 3: Rewrite Sections

**Rewrite completely, don't append.** Remove old content entirely and replace with improved version.

```
❌ BAD:
Add this at the end:
Note: Previously we used X, but now we use Y.

✅ GOOD:
We use Y for this.
```

### Step 4: Verify Quality

Read improved content and check:
- Does it sound authoritative?
- Could this be published as-is?
- No traces of previous versions?

## Quality Checklist

- [ ] Written in authoritative voice ("We do X")
- [ ] No historical traces (no "Previously...", "Originally...", "Before...")
- [ ] All steps are complete and actionable
- [ ] Real examples with real code
- [ ] Progressive complexity maintained
- [ ] Information is current

## Improve vs Create

| `/create-content` | `/improve-content` |
|-------------------|---------------------|
| New content from scratch | Update existing content |
| Start with outline | Rewrite to fix issues |
| Add missing sections | Remove/update bad sections |
| Follow all rules | Apply rules to fix violations |

## Related Skills

| Skill | When to Use |
|-------|-------------|
| `/create-content` | Create new content |
| `/update-doc` | Update documentation (not training) |
