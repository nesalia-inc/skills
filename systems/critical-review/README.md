# Critical Review System

**Purpose:** Ensure the agent questions instructions, identifies gaps, and acts as a devil's advocate rather than blindly following.

## Problem

The agent:
- Never contradicts the user or says "you're wrong"
- Follows instructions blindly even when they lead to problems
- Doesn't stop to flag fundamental issues
- Always agrees, never challenges assumptions

This leads to implementing bad decisions without warning.

## Proposed Solution

A system that makes the agent pause and review critically:

| Component | Purpose |
|-----------|---------|
| **Assumption Challenge** | Question premises before accepting a task |
| **Gap Identification** | Flag missing information or fundamental issues |
| **Devil's Advocate Mode** | Push back when something seems wrong |
| **Sanity Check** | Health check before executing significant decisions |

## TODO

- [ ] Define when critical review is triggered
- [ ] Define the review criteria (what makes a "good" decision)
- [ ] Create skill for critical analysis
- [ ] Define how to communicate concerns without being blocking