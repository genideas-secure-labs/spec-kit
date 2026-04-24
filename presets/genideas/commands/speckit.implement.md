---
name: speckit.implement
description: "Execute implementation by processing all tasks from tasks.md. Usage: /speckit.implement [spec-number or path]"
user-invocable: true
---

## User Input

```text
$ARGUMENTS
```

You **MUST** consider the user input before proceeding (if not empty).

## Outline

Execute the implementation plan by processing tasks defined in tasks.md, phase by phase.

### Steps

1. **Locate spec**: If user provides a number (e.g., "005"), read all files in `specs/005-*/`. If no number, use highest-numbered spec.

2. **Load context**:
   - **Required**: tasks.md (task list), plan.md (architecture), spec.md (requirements)
   - **Optional**: clarify.md (edge cases and constraints)
   - Read `CLAUDE.md` for coding conventions

3. **Parse tasks**: Extract from tasks.md:
   - Task groups with sub-tasks (checkboxes)
   - File paths to create/modify
   - Test files to create
   - Dependency order
   - Parallel tracks

4. **Execute phase by phase**:
   - Follow dependency order from tasks.md
   - For each task:
     a. Read existing code if modifying (never blind-edit)
     b. Implement the change following Dokkaebi conventions
     c. Write tests alongside implementation
     d. Mark sub-task as `[x]` in tasks.md when done
   - After each task group: run `pytest` on new/modified test files
   - Report progress after each completed task

5. **Dokkaebi coding conventions** (from CLAUDE.md):
   - `from __future__ import annotations` at top of every module
   - Type hints on all public APIs
   - `@dataclass` or Pydantic `BaseModel` for structured data
   - `StrEnum` for state machine states
   - Atomic writes for all file I/O (temp → fsync → os.replace)
   - JSONL ledgers: append-only, one JSON object per line
   - 120 char line limit, PEP 8

6. **Error handling**:
   - If a task fails: halt, report error with context, suggest fix
   - For parallel tasks: continue with successful ones, report failures
   - If tests fail: fix the implementation before moving on

7. **Completion validation**:
   - All tasks marked `[x]` in tasks.md
   - All new tests pass
   - Existing 913+ tests still pass (`pytest tests/`)
   - New modules have `__init__.py` with `__all__` exports

8. **Report**: Summary of completed work, test results, any remaining issues.

### Resume Support

- If tasks.md has some tasks already marked `[x]`, **skip completed tasks** and resume from the first uncompleted one
- This allows interrupted implementations to continue without re-doing completed work
- When resuming: verify completed tasks' tests still pass before continuing

### Parallel Execution

- When multiple tasks have no mutual dependencies, launch them as parallel agents
- Use the Agent tool with `run_in_background=true` for independent tasks
- Wait for all parallel agents before proceeding to dependent tasks
- This dramatically speeds up implementation of specs with parallel tracks

### Implementation Quality Rules

- Read before write — always understand existing code before modifying
- One task at a time — complete and test before moving on
- Tests alongside code — never leave untested code
- Backward compatibility — existing functionality must not break
- Minimal changes — implement exactly what the task specifies, no extras
- Prefer parallel agent execution for independent tasks
