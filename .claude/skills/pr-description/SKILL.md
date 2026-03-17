---
name: pr-description
description: Writes pull request descriptions following SEIDOR engineering standards. Use when creating a PR, writing a PR, or when the user asks to summarize changes for a pull request.
---

When writing a PR description, follow these steps:

1. Run `git diff main...HEAD` to see all changes on this branch
2. Run `git log --oneline main...HEAD` to understand the commit history
3. Identify the type of change: feature, bugfix, hotfix, refactor, docs, chore, test
4. Write the PR description using the template below

Use this exact format for the output:

```
=======================================================
  SEIDOR | Pull Request Description
=======================================================
  Branch: <current-branch>
  Author: <git user.name>
  Date:   <current date YYYY-MM-DD>
  Type:   <feature | bugfix | hotfix | refactor | docs | chore | test>
=======================================================

## What

One sentence explaining what this PR does.

## Why

Brief context on why this change is needed. Include references
to tickets or issues if available (e.g., JIRA-1234).

## Changes

- Bullet points of specific changes made
- Group related changes together
- Mention any files added, deleted, or renamed

## Impact

- [ ] Database migrations required
- [ ] Environment variables added/changed
- [ ] Breaking changes
- [ ] Documentation updated

## Testing

Describe how this was tested or what tests were added.

## Screenshots (if applicable)

N/A
```

Rules:
- Keep the description concise but complete
- Use imperative mood in bullet points (e.g., "Add feature" not "Added feature")
- If there are no breaking changes or migrations, mark them as unchecked
- Auto-detect the change type from the diff content and branch name
- Fill in the branch name, author, and date automatically using git commands
