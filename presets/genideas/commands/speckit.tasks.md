---
name: speckit.tasks
description: "Generate dependency-ordered implementation tasks with parallel tracks and strict checklist format. Usage: /speckit.tasks [spec-number or path]"
user-invocable: true
---

## User Input

```text
$ARGUMENTS
```

You **MUST** consider the user input before proceeding (if not empty).

## Outline

Generate an actionable, dependency-ordered tasks.md based on the spec and plan.

### Steps

1. **Locate spec**: If user provides a number (e.g., "005"), read `specs/005-*/spec.md`, `specs/005-*/plan.md`, and `specs/005-*/clarify.md`. If no number, use highest-numbered spec.

2. **Load design documents**:
   - **Required**: spec.md (requirements), plan.md (architecture, module layout)
   - **Optional**: clarify.md (edge cases), tradeoffs.md (decisions), checklist.md
   - Scan existing code to understand what already exists vs what's new

3. **Generate tasks.md** with strict format:

   ```markdown
   # {NNN}: {Title} — Implementation Tasks

   ## Dependency Graph
   ```
   {ASCII dependency graph}
   ```

   ## Parallel Tracks
   - **Track A** (description): T-001 → T-002 → T-005
   - **Track B** (description): T-003 → T-004
   - **Track C** (description): T-006 (independent)

   ---

   ## Tasks

   ### T-001: {Title}
   **Priority**: P0 | **Effort**: S/M/L | **Depends on**: none

   - [ ] Sub-task with specific file path
   - [ ] Sub-task with specific file path
   ...

   **Acceptance**: {concrete pass/fail criterion}

   ---

   ### T-002: {Title}
   **Priority**: P0 | **Effort**: M | **Depends on**: T-001
   ...

   ## Summary
   | Track | Tasks | Effort |
   |-------|-------|--------|
   | ... | ... | ... |
   | **Total** | **N tasks** | **M parallel tracks** |
   ```

4. **Task generation rules**:
   - Each task maps to a coherent code unit (one module, one feature)
   - Sub-tasks are checkboxes (`- [ ]`) — each specific enough for LLM implementation
   - Every sub-task references specific files with full paths
   - Every code task has a corresponding test expectation
   - Dependencies must be explicit (`Depends on: T-001, T-003`)
   - Priority: P0 = must-have, P1 = important, P2 = nice-to-have
   - Effort: S (< 1hr), M (1-3hr), L (3-8hr)
   - **Acceptance**: each task has concrete pass/fail

5. **Phase structure** (recommended):
   - **Foundation**: scaffold, types, constants (no deps)
   - **Core logic**: shared/reusable modules (depends on foundation)
   - **Feature A/B/C**: independent feature tracks (depends on core)
   - **Integration**: cross-feature tests, build config (depends on features)
   - **Verification**: final checks, documentation (depends on integration)

6. **Identify parallel tracks**: Group independent tasks that can be worked on simultaneously. Show clearly in the dependency graph.

7. **Report**: Total task count, parallel tracks count, effort breakdown, and suggest next step: `/speckit.analyze {NNN}` or `/speckit.implement {NNN}`.

### Task Quality Rules

- Every task must reference specific files with full paths
- Sub-tasks should be completable in one coding session
- Dependencies must be explicit (no implicit ordering)
- Each task must have a concrete acceptance criterion
- Consider project test patterns: `pytest` with fixtures, vitest for TS, etc.
- Tasks should be ordered so that each delivers a testable increment
