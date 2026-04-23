---
description: "Verify implementation against spec requirements and acceptance criteria."
---

## User Input

```text
$ARGUMENTS
```

You MUST consider the user input before proceeding if it is not empty.

## Outline

1. Locate the target spec and read its artifacts.
   - Prefer `specs/{NNN}-*/` when a number is provided.
   - Read `spec.md`, `tasks.md`, and other supporting files when present.

2. Load implementation evidence.
   - Read the implementation files referenced by the spec or tasks.
   - Check that expected tests, commands, and outputs exist.

3. Verify acceptance criteria one by one.
   - For testable criteria, identify the matching tests and run them when feasible.
   - For code criteria, inspect the relevant source and point to concrete evidence.
   - For CLI criteria, verify the command or help output exists.

4. Generate a verification report.
   - Include criterion, pass or fail status, and concrete evidence.
   - Summarize tests run and the overall verdict.

5. Write the result to `verify-report.md` in the spec directory and report the verdict.

## Guidelines

- Verify implementation against the spec, not general code quality.
- Prefer real evidence over guesses.
- Fail the report if any required acceptance criterion is not met.
