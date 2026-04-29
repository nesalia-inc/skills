# Marty Skills

Internal skills used by the development teams at **Nesalia Inc.** and **DeesseJS**.

## Overview

These skills define the workflows, conventions, and best practices for Marty's agent teams. They are used by Claude Code agents to perform various development tasks following consistent patterns across all projects.

## Skills Structure

```
skills/
├── engineering/
│   ├── basics/        # Fundamental skills (commit, push, sync)
│   ├── devops/        # DevOps workflows (git, issues, PRs)
│   └── ci-cd/         # CI/CD and deployment
└── learning/          # Learning and documentation skills
```

## Available Skills

### Development Basics
- `/commit` - Commit process with conventional commits
- `/push` - Push and PR creation workflow
- `/sync` - Branch synchronization

### DevOps
- `/create-issue` - Create GitHub issues
- `/edit-issue` - Edit GitHub issues
- `/create-pr` - Create pull requests
- `/update-pr` - Update pull requests
- `/close-pr` - Close pull requests
- `/merge-pr` - Merge pull requests

### CI/CD
- `/setup-canary` - Canary branch workflow

## Branch Hierarchy

```
main <- canary <- feature branches
```

- `main` - Production-ready (no direct push)
- `canary` - Integration testing (no direct push)
- `feature/*` - Development branches

## Conventions

All commits follow Conventional Commits format with Co-Authored-By trailer:

```
<type>(<scope>): <description>

Co-Authored-By: martyy-code <nesalia.inc@gmail.com>
```

## License

MIT License - See [LICENSE](LICENSE)