---
name: improve-doc
description: Guide the improvement of existing documentation following Marty conventions. Use this skill when the user asks to improve docs, enhance documentation, make docs clearer, or update existing docs. Also use when the user says things like "improve the documentation", "this docs needs better examples", or "update the existing docs".
disable-model-invocation: false
context: fork
agent: general-purpose
permissionMode: bypassPermissions
allowed-tools: Bash, Read, Glob, Grep, Write, Edit
---

# Improve Documentation Guide

Follow this guide to improve existing documentation following Marty conventions.

## When to Improve Docs

- Documentation exists but is unclear
- Examples are outdated or missing
- Structure needs organization
- Missing context for readers
- Code examples don't work

## Improvement Process

### Step 1: Find Existing Documentation

```bash
# Find docs related to topic
find docs -name "*.md" | xargs grep -l "keyword"

# Check structure
find docs -type f -name "*.md" | head -20
```

### Step 2: Review Current Doc

Read the existing documentation and identify:
- What is missing
- What is unclear
- What is outdated
- What examples would help

### Step 3: Make Improvements

```bash
# Read current doc
cat docs/path/to/doc.md

# Edit with improvements
# Focus on: clarity, examples, structure
```

## Doc Improvement Checklist

- [ ] Clear title that describes what it covers
- [ ] Brief summary at the start
- [ ] Prerequisites if any
- [ ] Step-by-step instructions
- [ ] Working code examples
- [ ] Expected output shown
- [ ] Common pitfalls mentioned
- [ ] Links to related docs

## Improvement Examples

### Before (Unclear)
```markdown
# API
Use the API to get data.
```

### After (Clear)
```markdown
# API Endpoints

## GET /api/users

Retrieves a list of all users.

**Prerequisites**: Requires authentication token.

**Example Request**:
```bash
curl -H "Authorization: Bearer $TOKEN" https://api.example.com/users
```

**Example Response**:
```json
{
  "users": [
    {"id": 1, "name": "John"}
  ]
}
```
```

## Quick Reference

```
Find docs:            find docs -name "*.md" | xargs grep -l "keyword"
Read current:          cat docs/path/to/doc.md
Improve clarity:      Add summary, examples, structure
Verify format:        Markdown lint if available
```

## Related Skills

| Skill | When to Use |
|-------|-------------|
| `/commit` | Commit documentation changes |
| `/create-issue` | Create issue for major doc work |
| `/push` | Push doc improvements |