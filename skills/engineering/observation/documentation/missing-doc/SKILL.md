---
name: missing-doc
description: Guide the creation of missing documentation following Marty conventions. Use this skill when the user asks to create docs, add documentation, write docs for new features, or document missing areas. Also use when the user says things like "this needs documentation", "document this", "create docs for this", or "we're missing docs on X".
disable-model-invocation: false
context: fork
agent: general-purpose
permissionMode: bypassPermissions
allowed-tools: Bash, Read, Glob, Grep, Write, Edit
---

# Create Missing Documentation Guide

Follow this guide to create documentation where it is missing following Marty conventions.

## When to Create Docs

- Feature has no documentation
- API endpoint not documented
- Process not documented
- Configuration not explained
- Runbook missing for operations
- Onboarding docs need updating

## Documentation Locations

| Type | Location |
|------|----------|
| API docs | `docs/api/` |
| Guides | `docs/guides/` |
| Runbooks | `docs/runbooks/` |
| Architecture | `docs/rfc/` |
| Learning | `docs/learnings/` |

## Creation Process

### Step 1: Identify Missing Documentation

```bash
# Check what exists
find docs -type f -name "*.md"

# Identify gaps
# Compare existing to what should exist

# Check for API coverage
grep -r "api" docs/ --include="*.md"
```

### Step 2: Research Content

- Understand the feature/process
- Identify target audience
- Gather requirements
- Collect examples

### Step 3: Create Documentation

```bash
# Create appropriate file
cat > docs/path/new-doc.md << 'EOF'
# Title

## Summary
Brief description of what this covers.

## Details
Detailed information...

## Examples
Code examples if applicable...

## Related
Links to related documentation.
EOF
```

## Documentation Structure

```markdown
---
title: Document Title
description: Brief description
last_updated: YYYY-MM-DD
---

# Title

## Overview
What is this about?

## Prerequisites
What is needed before?

## Guide
Step-by-step content...

## Examples
Working examples...

## Troubleshooting
Common issues and solutions...

## Related
Links to related docs.
```

## Checklist for New Docs

- [ ] Clear title
- [ ] Purpose stated
- [ ] Audience identified
- [ ] Prerequisites listed
- [ ] Steps are clear
- [ ] Examples work
- [ ] Linked to related docs

## Quick Reference

```
Check existing:       find docs -type f -name "*.md"
Create API doc:       cat > docs/api/endpoint.md << 'EOF' ...
Create guide:         cat > docs/guides/topic.md << 'EOF' ...
Add to index:         Update docs/README.md if exists
```

## Related Skills

| Skill | When to Use |
|-------|-------------|
| `/commit` | Commit new docs |
| `/create-issue` | Track doc creation work |
| `/push` | Push new documentation |