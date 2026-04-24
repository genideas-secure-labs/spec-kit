---
name: speckit.auto-review
description: "Run focused Codex CLI review on spec artifacts or implementation. Reports CRITICAL + HIGH severity findings by default and auto-fixes them. Usage: /speckit.auto-review [spec-number or path]"
user-invocable: true
---

## User Input

```text
$ARGUMENTS
```

You **MUST** consider the user input before proceeding (if not empty).

## Goal

Request a **second-opinion review from Codex CLI** on either:
- **Pre-implement mode**: spec artifacts (spec.md, plan.md, tasks.md, clarify.md) — before writing code
- **Post-implement mode**: the implementation diff + source files — after code is written

Codex reviews independently (no context from the current Claude conversation), catching issues Claude may have missed. Findings are categorized by severity; CRITICAL and HIGH are auto-fixed.

**Severity threshold**: CRITICAL + HIGH by default. For LOW+ use `/speckit.auto-review-strict`.

## Prerequisites

- `codex` CLI installed (check `which codex`)
- Git repo with commits on current branch
- Spec folder exists: `specs/{NNN}-*/`

If `codex` is missing, abort with clear message: "codex CLI not found. Install via `brew install codex` or equivalent."

## Steps

### 1. Locate Spec

- If input is a number (e.g., "037"), resolve `specs/037-*/` via Glob.
- If input is a path, use directly.
- If input empty, use the highest-numbered spec folder.
- Read `spec.md` title + branch info.

### 2. Detect Review Mode

Check git state + spec folder contents:

- **Pre-implement mode** (default if no implementation files):
  - Only spec artifacts committed (spec.md, plan.md, tasks.md, clarify.md, etc.)
  - No source code changes in the tasks.md target directories yet
  - Review target: **spec artifacts**
- **Post-implement mode**:
  - Source files referenced in tasks.md exist and are modified
  - Review target: **implementation diff + new/modified files**

Announce the detected mode in output.

### 3. Build Review Prompt

Construct a detailed prompt for `codex review`:

**Pre-implement prompt template**:
```
You are reviewing the spec artifacts for spec {NNN} ({TITLE}) before implementation begins.

Artifacts to review (in specs/{NNN}-*/):
- spec.md — requirements, acceptance criteria
- plan.md — architecture, design decisions
- tasks.md — implementation task breakdown
- clarify.md — resolved ambiguities
- tradeoffs.md — comparison tables (if present)

Review dimensions:
1. **Implementability**: Are tasks concrete enough to implement? Any hidden prereqs?
2. **Acceptance coverage**: Does every Acceptance Criterion map to at least one task?
3. **Contract consistency**: Do the spec, plan, and tasks agree on types, field names, file paths, URLs, versions?
4. **Platform gotchas**: For any cross-platform code, are OS-specific constraints surfaced (cfg gates, dependencies, path conventions)?
5. **Missing edge cases**: Error paths, concurrency, idempotency, config corruption, missing files, network failures.
6. **Out-of-scope leakage**: Do any tasks pull in work marked Out of Scope?

Report findings as:
- ID: F1, F2, ...
- Severity: CRITICAL | HIGH | MEDIUM | LOW
- Location: file:line or section
- Issue: one-sentence problem description
- Fix: concrete recommendation

Report CRITICAL and HIGH findings only. Skip MEDIUM and LOW.

Do NOT modify any files — analysis only.
```

**Post-implement prompt template**:
```
You are reviewing the implementation of spec {NNN} ({TITLE}).

Context:
- Spec artifacts in specs/{NNN}-*/
- Implementation committed on branch {BRANCH}
- Base branch: main

Review dimensions:
1. **Spec compliance**: Does the code implement the Acceptance Criteria?
2. **Correctness**: Bugs, race conditions, wrong error handling, missing validation at boundaries.
3. **Safety**: Panics in production paths (unwrap/expect without justification), unchecked arithmetic on user input, path traversal, injection.
4. **Resource handling**: File handles, sockets, locks freed on error paths.
5. **Cross-platform**: `#[cfg(...)]` gates correct? Dependencies available on target OS?
6. **Test coverage**: Do new tests cover new code paths? Are edge cases exercised?
7. **API contract stability**: External interfaces (JSON wire, config file schema, FFI) match spec precisely?

Report CRITICAL and HIGH findings only. Skip MEDIUM and LOW.

Format:
- ID, Severity, Location (file:line), Issue, Fix
```

### 4. Invoke Codex

Run `codex review` against the base branch:

```bash
codex review --base main "<prompt from step 3>"
```

If the current branch matches `main`/`master`, compare against `HEAD~1` instead.

Capture output. If Codex hangs or errors, retry once; if still failing, report and stop.

### 5. Parse Findings

Extract structured findings from Codex output. Expected format per finding:

```
F{N}: [SEVERITY] {file:line or section}
  Issue: ...
  Fix: ...
```

If Codex returns free-form text without clear findings, parse heuristically (look for "CRITICAL", "HIGH", "bug", "fix", "should", etc.).

Filter: keep only CRITICAL + HIGH. Drop MEDIUM + LOW.

### 6. Apply Fixes

For each finding:
- Read the referenced file
- Apply the recommended fix via Edit
- If fix requires judgment (ambiguous recommendation), defer to a `# TODO (review-F{N}): <fix>` comment and flag in the final report

Commit fixes:
```
git add {modified files}
git commit -m "fix(spec-{NNN}): auto-review pass {ROUND} — address {N} findings

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>"
```

### 7. Re-Review (Loop)

Repeat steps 4-6 until:
- Codex reports 0 CRITICAL + 0 HIGH findings, OR
- Max 5 iterations reached (report remaining findings and stop)

### 8. Report

Write `specs/{NNN}-*/auto-review.md` (append if exists):

```markdown
# Auto-Review Report — {date}

**Mode**: {pre-implement | post-implement}
**Severity threshold**: CRITICAL + HIGH
**Rounds**: {N}
**Total findings**: {count}
**Fixes applied**: {count}

## Round 1
- F1 [HIGH] ... → Fixed
- F2 [CRITICAL] ... → Fixed

## Round 2
- (clean)

## Status
✅ Clean at round {N}
```

Output to user: round count, fix count, final status.

## Behavior Rules

- **Never skip findings**: apply every CRITICAL and HIGH fix, even if small
- **Preserve Codex's recommendation wording** in commit messages for traceability
- **If a finding contradicts an intentional design decision** (documented in plan.md or clarify.md), add a comment in the affected file referencing the decision and skip the fix — but record this in auto-review.md as "Disputed"
- **Do not modify spec.md Acceptance Criteria** based on Codex findings — escalate to user instead
- **Stop on CRITICAL findings that require architectural changes** — report and wait for user

## Exit Criteria

- 0 CRITICAL + 0 HIGH remaining: ✅ pass
- Max iterations with remaining findings: ⚠ partial — user decides
- Codex unavailable: ❌ abort

## Invocation from `/speckit.full-auto`

When called from full-auto, this skill operates autonomously — no user prompts unless a finding is disputed or requires architectural decision.
