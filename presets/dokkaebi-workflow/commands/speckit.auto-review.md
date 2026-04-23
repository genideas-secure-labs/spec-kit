---
description: "Run Dokkaebi review-loop as a post-implement speckit step, using an isolated worktree when the repo is dirty."
---

## User Input

```text
$ARGUMENTS
```

You MUST consider the user input before proceeding if it is not empty.

## Outline

1. Resolve the current feature context.
   - If the user provided a spec number, prefer the matching `specs/{NNN}-*/` directory.
   - Otherwise, use the current feature branch and repo-local spec resolution.

2. Load the review context when available.
   - Read `spec.md` and `tasks.md` from the active feature directory.
   - Read `quickstart.md` if it exists.
   - Check `git status --short` before running anything.

3. Run the safe auto-review wrapper.
   - Default command: `tools/speckit_auto_review.sh`
   - If the user provided flags, pass them through to `dokkaebi review-loop`.
   - Do not stash or revert user changes.

4. Report the review result.
   - Summarize rounds, findings, fixed count, and remaining count from the command output.
   - If a temporary worktree was kept, report its path and branch so the user can inspect or cherry-pick the result.

## Defaults

- No arguments means "run the repo's default `dokkaebi review-loop` safely".
- For a smoke run in this repo, `--reviewer openai --fixer codex --max-rounds 1` is a good explicit override.

## Safety Rules

- Prefer the wrapper over calling `dokkaebi review-loop` directly when the current worktree is dirty.
- Never delete or revert the user's existing changes.
- If the isolated run fails, keep the worktree and surface the path instead of cleaning it up.
