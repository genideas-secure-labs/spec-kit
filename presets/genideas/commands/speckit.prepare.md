---
name: speckit.prepare
description: "Full spec preparation pipeline: specify → clarify (tradeoffs) → checklist → plan → tasks → analyze (auto-fix) → commit → GitHub. Usage: /speckit.prepare <feature description or spec-number>"
user-invocable: true
---

## User Input

```text
$ARGUMENTS
```

You **MUST** use the user input to determine the mode:
- If input is a feature description (text): start from **specify** (create new spec)
- If input is a number (e.g., "018"): start from **existing spec** (review + fill gaps)

## Outline

Batch pipeline that prepares a spec for implementation by running all pre-implement steps in sequence. Stops at each human decision point (clarify Q&A), then continues automatically.

### Pipeline

```
Step 1: SPECIFY
  - If new: create specs/{NNN}-{name}/spec.md + create branch ms/{NNN}-{name}
  - If existing: review spec.md, fill gaps if needed
  - Quality validation: no impl details, testable FRs, measurable ACs
  - Output: spec.md ready

Step 2: CLARIFY
  - Structured ambiguity scan (10 categories)
  - Present questions ONE at a time with MANDATORY tradeoff tables
  - EVERY question must have a comparison table — no exceptions
  - Wait for user answers (this is the human-in-the-loop step)
  - Output: clarify.md + tradeoffs.md

Step 3: CHECKLIST
  - "Unit tests for English" — validate requirement QUALITY, not implementation
  - Test: completeness, clarity, consistency, measurability, coverage, traceability
  - If FAIL: fix spec/clarify, re-check (max 3 iterations)
  - Output: checklist.md (all PASS)

Step 4: PLAN
  - Phase 0: Research unknowns, examine existing code
  - Phase 1: Architecture decisions, module layout, data flow, risk mitigation
  - Output: plan.md

Step 5: TASKS
  - Dependency-ordered task breakdown with strict format
  - T-{NNN} IDs, Priority (P0/P1/P2), Effort (S/M/L), Depends on
  - Parallel tracks identification, dependency graph
  - Output: tasks.md

Step 6: ANALYZE
  - Cross-artifact consistency check (6 detection passes)
  - Severity: CRITICAL / HIGH / MEDIUM / LOW
  - If CRITICAL or HIGH findings: FIX ALL automatically
  - Re-analyze until clean (max 3 iterations)
  - Output: clean analysis (0 CRITICAL, 0 HIGH)

Step 7: COMMIT
  - Git add all spec artifacts
  - Commit: "Add spec {NNN}: {title} (prepared for implement)"
  - Push branch

Step 8: GITHUB
  - Create tracking issue: "[{NNN}] {Title} — spec prepared"
  - Post preparation summary (artifacts, stats, key decisions)
  - Label: spec-{NNN}, prepared
  - Output: issue URL + review link
```

### Execution Rules

1. **Steps 1, 3-8 are automatic** — no user input needed
2. **Step 2 (clarify) is interactive** — user must answer questions with tradeoff tables
3. **Step 6 loops** — analyze → fix → re-analyze until clean (max 3 iterations)
4. **Branch**: if not already on `ms/{NNN}-*` branch, create it at Step 1
5. **Resume**: if some artifacts already exist, skip completed steps
6. **Output**: at the end, report what was created and suggest `/speckit.implement {NNN}`

### Resume Detection

Check which artifacts exist in `specs/{NNN}-{name}/`:
- `spec.md` exists → skip Step 1
- `clarify.md` exists → skip Step 2
- `checklist.md` exists → skip Step 3
- `plan.md` exists → skip Step 4
- `tasks.md` exists → skip Step 5
- All exist + analyze clean → skip Step 6

### Step 8: GITHUB Detail

1. **Check for existing tracking issue**: `gh issue list --label "spec-{NNN}" --state open`
2. **If no tracking issue exists**, create one:
   ```
   gh issue create \
     --title "[{NNN}] {Title} — spec prepared" \
     --label "spec-{NNN},prepared" \
     --body "<preparation summary with artifacts table, key decisions, branch link>"
   ```
3. **If tracking issue exists**, add a comment with the preparation summary instead.
4. **Output to user**:
   - Issue URL (clickable)
   - Direct review link: `https://github.com/{owner}/{repo}/compare/main...ms/{NNN}-{name}`

### Final Report

```
✅ Spec {NNN}: {Title} — prepared for implementation

  spec.md      ✅ (X FRs, Y NFRs)
  clarify.md   ✅ (Z questions resolved)
  tradeoffs.md ✅ (Z decisions documented)
  checklist.md ✅ (all requirements pass)
  plan.md      ✅ (N modules, M decisions)
  tasks.md     ✅ (K tasks, J parallel tracks)
  analyze      ✅ (0 issues)

  Branch: ms/{NNN}-{name}
  Issue:  {issue URL}
  Review: {compare URL}
  Next:   /speckit.implement {NNN}
```

### Guidelines

- This is the "one command to prepare everything" workflow
- Human interaction is ONLY at clarify step (tradeoff decisions)
- **EVERY clarify question MUST have a tradeoff comparison table** — this is the core principle
- Everything else is autonomous
- If any step fails irrecoverably, stop and report what succeeded
- Use existing speckit skills internally (specify, clarify, checklist, plan, tasks, analyze)
