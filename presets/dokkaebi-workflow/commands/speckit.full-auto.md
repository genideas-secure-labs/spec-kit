---
description: "End-to-end autonomous pipeline: prepare, review, implement, review, postmortem, then wait for merge confirmation."
---

## User Input

```text
$ARGUMENTS
```

You MUST consider the user input before proceeding if it is not empty.

## Goal

Run a full spec-to-PR pipeline with only the final merge decision left to the user.

## Pipeline

### Phase 1: Prepare

- Run `/speckit.prepare {ARGS}`.
- During clarify, provide recommended answers with explicit tradeoff tables for each question.
- Pause for the user at the clarify decision point before continuing.
- If the repository has established prior patterns, follow them unless the user asks otherwise.

### Phase 2: Pre-Implementation Review

- Run `/speckit.auto-review {ARGS}`.
- Fix all actionable findings before implementation starts.
- Ensure spec artifacts are clean before moving on.

### Phase 3: Implement

- Run `/speckit.implement {ARGS}`.
- Execute tasks in dependency order.
- Run relevant tests as work progresses.
- Commit progress at meaningful checkpoints.

### Phase 4: Post-Implementation Review

- Run `/speckit.auto-review {ARGS}` again.
- Fix all actionable findings before declaring the feature ready.

### Phase 5: Postmortem

- Write `POSTMORTEM.md` in the spec folder, for example `specs/{feature}/POSTMORTEM.md`.
- Include delivered artifacts, test results, review results, key design decisions, and follow-up work.

### Phase 6: Merge Confirmation

- Push changes and prepare a PR against the repo's normal integration branch.
- Present a concise phase-by-phase summary with PR context.
- Wait for explicit user confirmation before merging.

## Behavior Rules

- Each phase must complete successfully before moving to the next phase.
- If any phase fails, stop and report where it failed and what remains.
- Commit after major milestones rather than after every tiny step.
- Follow existing project conventions from the constitution, specs, and repo guidance.
