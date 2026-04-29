---
name: invalid-doc
description: Guide the fix of invalid or incorrect documentation following Marty conventions. Use this skill when the user asks to fix wrong docs, correct documentation errors, fix broken docs, or update incorrect docs. Also use when the user says things like "this docs is wrong", "fix the documentation", "this is outdated", or "the docs are incorrect".
disable-model-invocation: false
context: fork
agent: general-purpose
permissionMode: bypassPermissions
allowed-tools: Bash, Read, Glob, Grep, Write, Edit
---

# Fix Invalid Documentation Guide

Follow this guide to fix incorrect or invalid documentation following Marty conventions.

## When to Fix Docs

- Documentation contradicts actual behavior
- Instructions don't work as described
- Configuration values are wrong
- Deprecated content not marked
- Wrong paths or filenames
- Incorrect code examples

## Fix Process

### Step 1: Identify the Problem

```bash
# Verify actual behavior
# Compare doc to reality

# Check if file paths exist
ls actual/path.md

# Verify configuration values
cat config.json | jq .
```

### Step 2: Research Correct Information

- Test the actual behavior
- Check source code for correct values
- Verify paths match actual structure
- Consult relevant team members if needed

### Step 3: Fix the Documentation

```bash
# Correct the documentation with accurate information
# Include note explaining the fix if relevant
```

## Common Fixes

### Wrong Path

```markdown
# Before (Wrong)
See the configuration guide at `/docs/config.md`

# After (Correct)
See the configuration guide at `docs/guides/config.md`
```

### Outdated Command

```markdown
# Before (Outdated)
Run `npm run server` to start the server.

# After (Correct)
Run `npm run dev` to start the development server.
```

### Incorrect Configuration

```markdown
# Before (Wrong)
Set `timeout: 30` to configure the timeout.

# After (Correct)
Set `timeout: 30000` (in milliseconds) to configure the timeout.
```

## Documentation Health Check

- [ ] Commands work as documented
- [ ] Paths are correct
- [ ] Configuration values are accurate
- [ ] Version numbers are current
- [ ] Deprecated warnings are present

## Quick Reference

```
Verify path:           ls docs/path.md
Test command:          npm run command
Check config:          cat config.json | jq .
Fix with accurate info: Edit with correct information
```

## Related Skills

| Skill | When to Use |
|-------|-------------|
| `/commit` | Commit doc fixes |
| `/create-issue` | Document systemic doc issues |
| `/push` | Push fixes |