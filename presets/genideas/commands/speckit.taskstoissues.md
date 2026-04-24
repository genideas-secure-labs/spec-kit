---
name: speckit.taskstoissues
description: "Convert tasks.md into GitHub issues with labels and dependencies. Usage: /speckit.taskstoissues [spec-number]"
user-invocable: true
---

## User Input

```text
$ARGUMENTS
```

You **MUST** consider the user input before proceeding (if not empty).

## Outline

Parse tasks.md and create GitHub issues for each task, preserving dependencies and execution order.

### Steps

1. **Locate spec**: Read `specs/{NNN}-*/tasks.md`.
2. **Parse tasks**: Extract task title, files, tests, sub-tasks, dependencies.
3. **Determine repo**: Check `git remote -v` for GitHub repo URL.
4. **Create issues** using `gh issue create`:
   - Title: `[{NNN}] Task {N}: {Title}`
   - Body: Files, tests, sub-tasks as checkboxes, dependency links
   - Labels: `spec-{NNN}`, `task`
   - Milestone: `ms/{NNN}-{short-name}` if exists
5. **Link dependencies**: Add "Depends on #X" references in issue body.
6. **Create tracking issue**: Summary issue linking all task issues with execution order diagram.
7. **Report**: List of created issues with URLs.

### Guidelines
- Use `gh issue create` (requires gh CLI authenticated)
- Don't create duplicate issues (check existing by title prefix)
- Add sub-tasks as checkboxes in issue body
- Include dependency graph in tracking issue
