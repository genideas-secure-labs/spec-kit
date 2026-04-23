---
description: "Full spec preparation pipeline: specify, clarify, checklist, plan, tasks, analyze, then hand off for implementation."
---

## User Input

```text
$ARGUMENTS
```

You MUST use the user input to determine the mode.

- If the input is a feature description, start from `speckit.specify`.
- If the input is a spec number, resume from the existing spec and fill any gaps.

## Goal

Prepare a spec so implementation can start with clear requirements, clarified decisions, a plan, tasks, and a clean consistency pass.

## Pipeline

### Step 1: Specify

- For a new request, create or update the target spec directory and `spec.md`.
- For an existing spec, review `spec.md` and fill important gaps before continuing.

### Step 2: Clarify

- Run a structured ambiguity scan.
- Ask targeted clarification questions one at a time.
- Every question should include explicit tradeoffs and a recommended option.
- Pause for the user's answers before proceeding.

### Step 3: Checklist

- Validate requirement quality rather than implementation details.
- Check completeness, clarity, consistency, measurability, coverage, and traceability.
- If the checklist fails, fix the spec artifacts and re-check.

### Step 4: Plan

- Produce the technical plan with architecture, risks, and implementation approach.

### Step 5: Tasks

- Produce dependency-ordered tasks with clear execution order and parallelizable work where appropriate.

### Step 6: Analyze

- Run cross-artifact consistency checks.
- Fix critical and high-severity issues before stopping.
- Re-run analysis until the artifacts are clean or a hard blocker remains.

## Execution Rules

- Resume from existing artifacts when possible instead of recreating work.
- Human interaction is concentrated in clarify; the rest should proceed autonomously when safe.
- At the end, report what was created and suggest the next implementation step.
