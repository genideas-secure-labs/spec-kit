---
description: "Generate guide.md for a completed spec."
---

## User Input

```text
$ARGUMENTS
```

You MUST consider the user input before proceeding if it is not empty.

## Outline

Generate `guide.md` showing how to use the implemented feature, with CLI examples and expected outputs.

1. Locate the target spec.
   - If the user provides a number, read files from `specs/{NNN}-*/`.
   - If no number is provided, use the highest-numbered or currently active spec.

2. Load context.
   - Read `spec.md`, `plan.md`, `tasks.md`, and `clarify.md` from the spec directory when available.

3. Write `guide.md`.
   - Include a quick start section with the key commands.
   - Explain each user-visible capability in simple terms.
   - Add realistic command examples and realistic output examples.
   - Include files created or modified, plus migration or compatibility notes if relevant.

4. Run quality checks.
   - Ensure the important commands from the plan are documented.
   - Keep examples consistent with the specification and implementation.

5. Report the output path.
