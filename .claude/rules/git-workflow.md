---
name: git-workflow
description: Git conventions
paths:
  - "**/*.md"
---

# Git Workflow Conventions

## Commit Messages

- Follow conventional commits format
- Use marty code agent for commit messages
- Always include Co-Authored-By: martyy-code <noreply@anthropic.com> in commit messages

## Commit Message Format

```
<type>(<scope>): <description>

[optional body]

Co-Authored-By: martyy-code <noreply@anthropic.com>
```

### Type Examples

- `feat` - New feature
- `fix` - Bug fix
- `docs` - Documentation
- `refactor` - Code refactoring
- `test` - Tests
- `chore` - Maintenance tasks

## Workflow

1. Create a new branch for changes
2. Make commits following conventional commits with marty code agent
3. Include Co-Authored-By: martyy-code in all commits
4. Submit pull request for review
5. Merge after approval

## Important Notes

- All commits must have Co-Authored-By: martyy-code <noreply@anthropic.com>
- Use marty code agent when crafting commit messages
- Keep commits atomic and focused