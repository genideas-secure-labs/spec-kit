---
name: speckit.analyze
description: "Non-destructive cross-artifact consistency analysis with severity-based findings and remediation. Usage: /speckit.analyze [spec-number or path]"
user-invocable: true
---

## User Input

```text
$ARGUMENTS
```

You **MUST** consider the user input before proceeding (if not empty).

## Goal

Identify inconsistencies, gaps, and quality issues across spec.md, plan.md, tasks.md, clarify.md, and tradeoffs.md. Output a structured analysis report with severity-based findings.

## Operating Constraints

- **STRICTLY READ-ONLY** when run standalone: Do NOT modify any files. Output analysis report only.
- **FIX MODE** when run within `/speckit.prepare`: Apply all fixes automatically (CRITICAL and HIGH first), then re-analyze until clean (max 3 iterations).
- **Constitution Authority**: `governance/CONSTITUTION.md` is non-negotiable. Constitution conflicts are automatically CRITICAL.

## Steps

### 1. Load Artifacts

Read from `specs/{NNN}-*/`:
- **Required**: spec.md, plan.md, tasks.md
- **Optional**: clarify.md, tradeoffs.md, checklist.md
- **Context**: `governance/CONSTITUTION.md`, `CLAUDE.md`

If any required file is missing, abort and suggest which command to run.

### 2. Build Semantic Models

- **Requirements inventory**: FR-001, FR-002, ..., NFR-001, ...
- **AC inventory**: Acceptance criteria from spec.md
- **Task coverage mapping**: Which tasks cover which FRs/NFRs
- **Decision inventory**: From clarify.md/tradeoffs.md — check reflected in spec
- **Module/file reference consistency**: plan layout vs task file paths

### 3. Detection Passes (max 50 findings)

#### A. Duplication Detection
- Near-duplicate requirements across FRs
- Overlapping task descriptions

#### B. Ambiguity Detection
- Vague adjectives ("fast", "scalable", "robust") without measurable criteria
- Unresolved placeholders (TODO, [NEEDS CLARIFICATION], ???)

#### C. Underspecification
- Requirements with verbs but missing measurable outcome
- Tasks referencing files/components not defined in plan
- FRs without acceptance criteria

#### D. Constitution Alignment
- Any requirement or plan element conflicting with MUST principles
- Missing mandated sections or quality gates

#### E. Coverage Gaps
- FRs with zero associated tasks
- Tasks with no mapped requirement
- ACs with no corresponding task verification
- Clarify decisions not reflected in spec.md

#### F. Inconsistency
- Terminology drift (same concept named differently across files)
- Data entities in plan but absent in spec (or vice versa)
- Task ordering contradictions
- Tradeoff decisions in tradeoffs.md not matching spec.md content

### 4. Severity Assignment

| Severity | Criteria |
|----------|---------|
| **CRITICAL** | Constitution MUST violation, missing core artifact, blocking FR with zero coverage |
| **HIGH** | Conflicting requirements, ambiguous security/performance, untestable AC, clarify decision not in spec |
| **MEDIUM** | Terminology drift, missing NFR coverage, underspecified edge case |
| **LOW** | Style/wording improvements, minor redundancy |

### 5. Output Report

```markdown
## Specification Analysis Report — {NNN}: {Title}

### Findings

| ID | Category | Severity | Location | Summary | Recommendation |
|----|----------|----------|----------|---------|----------------|
| D1 | Duplication | MEDIUM | spec.md:FR-003 | Similar to FR-001 | Merge |
| C1 | Coverage | HIGH | spec.md:FR-005 | No task covers FR-005 | Add task |
| F1 | Inconsistency | HIGH | clarify→spec | Q4 decision not in FR-003 | Update FR-003 |

### Coverage Summary

| Requirement | Has Task? | Task IDs | Notes |
|-------------|-----------|----------|-------|

### Metrics
- Total FRs: N, NFRs: M
- Total Tasks: K
- Coverage: X% (FRs with >=1 task)
- CRITICAL: N, HIGH: N, MEDIUM: N, LOW: N

### Next Actions
- If CRITICAL/HIGH issues: resolve before `/speckit.implement`
- If clean (0 CRITICAL, 0 HIGH): proceed to `/speckit.implement`
```

### 6. Remediation

- **Standalone mode**: Ask "Would you like me to fix these issues?" (do NOT apply automatically)
- **Prepare mode**: Apply all fixes automatically, re-analyze, repeat until clean (max 3 iterations)

## Project-Specific Checks

- All new modules have corresponding test files in tasks
- LLM-related tasks specify tier routing (T0/T1/T2/T4)
- File I/O tasks use atomic write pattern
- JSONL ledger tasks are append-only
- Security-related changes have AOP aspect coverage
- CLI commands are registered in main.py
- TypeScript projects: build config, type definitions present
