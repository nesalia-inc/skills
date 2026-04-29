---
name: learn
description: Learn a specific concept, technology, or URL provided by the user
disable-model-invocation: true
context: fork
agent: general-purpose
permissionMode: bypassPermissions
allowed-tools: Bash(fresh *), Read, Glob, Grep, WebFetch, AskUserQuestion, Write
---

# Learn

Learn `$ARGUMENTS` and document it in `docs/learnings/`.

## Purpose

When the user provides a specific concept, technology, link, or topic, research it thoroughly and create documentation for future reference. The learning MUST be saved to the appropriate technology folder.

## Process

1. **Analyze input**: Determine if it's a URL, a concept name, or a topic
2. **Identify technology**: Extract the technology/package from input or ask user
3. **Fetch if URL**: If a URL was provided, use `fresh fetch` to get content
4. **Search**: Use `fresh search` to find related documentation and resources
5. **Synthesize**: Combine findings into a structured learning document
6. **Save**: Write to `docs/learnings/<technology>/<topic>/README.md`
7. **ALWAYS create the document**: Do not fail to create files - this is mandatory

## Path Structure

Learnings are organized by technology:
```
docs/learnings/<technology>/
├── <topic-1>/
│   ├── README.md
│   └── sources.md
├── <topic-2>/
│   ├── README.md
│   └── sources.md
└── README.md    # Overview of all learnings for this technology
```

## Examples

- `/learn https://better-auth.com/docs/plugins/device-authorization`
  → `docs/learnings/better-auth/device-authorization/README.md`

- `/learn react-server-components`
  → `docs/learnings/react/server-components/README.md`

- `/learn device auth flow` → asks user to specify which technology
  → `docs/learnings/<tech>/device-auth/README.md`

## Input Handling

- **URL with domain**: Extract technology from domain (e.g., `better-auth.com` → `better-auth`)
- **URL with path**: Extract topic from path (e.g., `/docs/plugins/device-authorization` → `device-authorization`)
- **Concept name**: Ask user which technology this belongs to
- **Ambiguous input**: Ask the user to clarify if needed

## Documentation Structure

The goal of each learning document is to understand how to USE the concept. It should be practical and actionable.

### README.md Content

```markdown
# <Topic Name>

## What is it?
Brief description of what this is and what problem it solves.

## Why use it?
When and why you would choose this approach. Use cases and benefits.

## How to use it (Step by Step)
Step-by-step guide on using the concept, with code examples.

## When to use it?
Scenarios where this concept is the right choice, and when NOT to use it.

## Code Examples
Complete, runnable code examples demonstrating the concept.
Include both basic usage and advanced patterns.

## Configuration Options
All available options and what they do.

## Common Patterns
Typical use cases and how to implement them.

## Gotchas and Troubleshooting
Common mistakes, pitfalls, and how to avoid them.

## Related Concepts
Links to related learnings in the same technology.

## Sources
All source URLs used for learning this concept.
```

### sources.md Content

```markdown
# Sources for <Topic>

## Primary Source
- [Source Title](URL)

## Related Resources
- [Resource Title](URL)
```

## Guidelines

- **ALWAYS create the document** - Do not fail due to read-only mode
- Always use `fresh search` and `fresh fetch` - never use WebFetch directly
- **Practical focus**: The goal is to understand HOW to use the concept, not just theoretical knowledge
- **Code examples are mandatory**: Include complete, runnable code snippets
- **Actionable**: A developer reading this should be able to implement the concept
- Focus on when and why to use, not just how
- Note sources for future reference
- If the input is unclear, ask the user for clarification
- Organize by technology to keep learnings structured