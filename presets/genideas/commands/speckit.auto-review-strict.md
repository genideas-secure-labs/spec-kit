---
name: speckit.auto-review-strict
description: "Strict variant of speckit.auto-review — reports and auto-fixes findings down to LOW severity. Usage: /speckit.auto-review-strict [spec-number or path]"
user-invocable: true
---

## User Input

```text
$ARGUMENTS
```

You **MUST** consider the user input before proceeding (if not empty).

## Goal

Same as `/speckit.auto-review` but with a **lower severity floor**: CRITICAL + HIGH + MEDIUM + LOW findings are all reported and auto-fixed.

Use this mode when:
- Preparing for production merge (Phase 6 of `/speckit.full-auto`)
- User explicitly wants exhaustive review
- Pre-release polish pass

**Non-strict mode** (`/speckit.auto-review`) filters to CRITICAL + HIGH only — use that when speed matters.

## Prerequisites

Same as `/speckit.auto-review`:
- `codex` CLI installed
- Git repo with commits on current branch
- Spec folder `specs/{NNN}-*/` exists

## Steps

Follow all steps in `/speckit.auto-review` **SKILL.md**, with these overrides:

### Override 1 — Codex prompt severity floor

In the prompt template (step 3), replace:

> Report CRITICAL and HIGH findings only. Skip MEDIUM and LOW.

with:

> Report **ALL** findings including CRITICAL, HIGH, MEDIUM, and LOW severity. Be thorough — this is a strict review. Even minor issues (naming inconsistencies, missing comments on non-obvious logic, slightly-off error messages, unused imports, minor performance inefficiencies) should be reported.
>
> For LOW severity findings, include only those that are actionable and objective. Do NOT include purely stylistic preferences or subjective "could be better" suggestions without a concrete improvement.

### Override 2 — Fix filter

In step 5 (Parse Findings), do NOT filter out MEDIUM and LOW. Keep all findings.

### Override 3 — Fix priority

When applying fixes (step 6), process in severity order:
1. All CRITICAL first (may require immediate user escalation)
2. All HIGH
3. All MEDIUM
4. All LOW

This minimizes rework (a CRITICAL fix may subsume several LOW findings).

### Override 4 — Iteration cap

Max **10 iterations** (vs 5 in non-strict) since LOW findings may cascade.

### Override 5 — Report

Write or append to `specs/{NNN}-*/auto-review-strict.md` (distinct from `auto-review.md`):

```markdown
# Auto-Review (Strict) Report — {date}

**Mode**: {pre-implement | post-implement}
**Severity threshold**: LOW+ (all)
**Rounds**: {N}
**By severity**:
- CRITICAL: {count} found, {count} fixed
- HIGH: {count} found, {count} fixed
- MEDIUM: {count} found, {count} fixed
- LOW: {count} found, {count} fixed

## Findings By Round

### Round 1
- F1 [MEDIUM] src/foo.rs:42 — <issue> → Fixed
- F2 [LOW] tasks.md — <issue> → Fixed
- F3 [HIGH] spec.md:88 — <issue> → Fixed

### Round 2
...

## Status
✅ Clean at round {N}

## Disputed / Deferred
- F7 [LOW] — deferred because <reason>
```

## Behavior Rules (additional to auto-review)

- **LOW findings with disputable merit**: if a LOW finding is subjective (style preference), apply the Codex recommendation anyway UNLESS it contradicts:
  - Project conventions documented in CLAUDE.md
  - Decisions recorded in plan.md / clarify.md / tradeoffs.md
  - Rust clippy defaults
  
  In case of contradiction, record as "Disputed" with the contradiction source.

- **Don't gold-plate**: even in strict mode, do not invent new features. Fixes must address Codex's actual finding text, not expand beyond it.

- **Commit grouping**: group fixes by severity in commit messages:
  ```
  fix(spec-{NNN}): auto-review-strict round {R} — {N} findings
  
  CRITICAL: 0
  HIGH: 2 (F1 spec path inconsistency, F3 missing error handling)
  MEDIUM: 3 (F2, F4, F5)
  LOW: 5 (F6, F7, F8, F9, F10)
  
  Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>
  ```

## Exit Criteria

- 0 findings across all severities: ✅ strict-clean
- Max iterations with remaining findings: ⚠ partial — continue with acknowledged debt
- Codex unavailable: ❌ abort

## Invocation from `/speckit.full-auto`

When called from `/speckit.full-auto`:
- Phase 2 (pre-implement): strict review of spec artifacts before coding
- Phase 4 (post-implement): strict review of code before merge

Operate autonomously. User interaction only on architectural conflicts or disputes.
