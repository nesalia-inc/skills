---
name: create-content
description: Create new training content following Marty documentation rules. Use this skill when asked to create a course, tutorial, training material, or any educational content. Example: "/create-content introduction-to-auth"
disable-model-invocation: false
context: fork
agent: general-purpose
permissionMode: bypassPermissions
allowed-tools: Bash, Read, Write, Edit
---

# Create Content Guide

Creates training content (courses, tutorials, guides) following Marty documentation rules.

## Marty Content Rules

**See `./rules.md` for the complete rules.** Summary:

1. **Authoritative Voice** - "We do X", not "Here's how you might..."
2. **Forward-Looking Only** - No "Previously..." or "Originally..."
3. **Complete Information** - Explicit, actionable, no gaps
4. **Practical Examples** - Real code, not placeholders
5. **Progressive Complexity** - Simple → Advanced
6. **Hands-On Exercises** - Practice reinforces learning

## Content Structure

### For Courses

```markdown
# Course Title

## Overview
What this course covers and what you'll learn.

## Prerequisites
What should the learner know before starting.

## Lessons

### Lesson 1: [Title]
#### Theory
Concept explanation.

#### Practice
Step-by-step exercises.

#### Quiz
Quick knowledge check.

### Lesson 2: [Title]
...

## Summary
Key takeaways.
```

### For Tutorials

```markdown
# Tutorial: [Title]

## Goal
What the learner will accomplish.

## Steps
1. [Step 1]
2. [Step 2]
3. [Step 3]

## Result
What the learner should have at the end.

## Next Steps
Where to go from here.
```

## Process

### Step 1: Define the Audience

Who is this for?
- Beginner, intermediate, advanced?
- What do they already know?
- What will they be able to do after?

### Step 2: Define the Scope

What is included and NOT included?
- What will be covered?
- What is out of scope for this content?

### Step 3: Create Outline

```markdown
# [Title]

## Overview
## [Section 1]
## [Section 2]
## [Section 3]
## Summary
```

### Step 4: Fill in Content

Write with authoritative voice. Use complete sentences. Include examples.

### Step 5: Add Exercises

Hands-on exercises reinforce learning:
- Code along examples
- Challenge problems
- Real-world scenarios

## Quality Checklist

- [ ] Written in authoritative voice ("We do X")
- [ ] No historical traces ("Previously...", "Originally...")
- [ ] All steps are complete and actionable
- [ ] Real examples with real code
- [ ] Exercises included for hands-on learning
- [ ] Progressive complexity (simple → advanced)
- [ ] Clear audience defined
- [ ] Scope clearly defined

## Related Skills

| Skill | When to Use |
|-------|-------------|
| `/improve-content` | Improve existing content |
| `/update-doc` | Update documentation (not training) |
