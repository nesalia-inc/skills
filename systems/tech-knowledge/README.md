# Tech Knowledge System

**Purpose:** Ensure the agent knows about the latest technologies, versions, and alternatives — not relying on training data which may be outdated.

## Problem

The agent's training data has a cutoff date. It doesn't know:
- Latest versions of packages (e.g., Zod v4, NextJS 16)
- New technologies that emerged after training (e.g., E2B for sandboxing instead of Docker)
- Current best practices that have evolved

This leads to recommending outdated solutions or ignoring better alternatives.

## Proposed Solution

A system that provides the agent with up-to-date technology knowledge:

| Component | Purpose |
|-----------|---------|
| **Tech Radar** | Document current technologies, versions, and alternatives |
| **Latest Versions Index** | Track latest versions of key packages |
| **Tech Recommendations** | Document preferred technologies and why |

## TODO

- [ ] Define tech radar structure
- [ ] Define latest versions index format
- [ ] Define how the agent accesses this knowledge
- [ ] Create skills to manage and use this system