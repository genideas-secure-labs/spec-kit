---
name: speckit.checklist
description: "Generate quality checklist — 'unit tests for English' that validate requirement quality, not implementation. Usage: /speckit.checklist [spec-number]"
user-invocable: true
---

## User Input

```text
$ARGUMENTS
```

You **MUST** consider the user input before proceeding (if not empty).

## Core Concept: "Unit Tests for English"

Checklists are **unit tests for requirements writing** — they validate the quality, clarity, and completeness of requirements, NOT implementation correctness.

**CORRECT** (testing requirement quality):
- "Are error handling requirements defined for all failure modes?" [Completeness]
- "Is 'fast loading' quantified with specific timing thresholds?" [Clarity]
- "Are requirements consistent between FR-001 and FR-003?" [Consistency]

**WRONG** (testing implementation):
- "Verify the button clicks correctly"
- "Test error handling works"
- "Confirm the API returns 200"

## Steps

1. **Locate spec**: If user provides a number, read `specs/{NNN}-*/spec.md`. If no number, use highest.
2. **Load artifacts**: Read spec.md, clarify.md, tradeoffs.md, plan.md if they exist.
3. **Generate checklist** across these quality dimensions:

### Quality Dimensions

**Requirement Completeness** — Are all necessary requirements documented?
- Are all functional scenarios covered?
- Are non-functional requirements present for all critical qualities?
- Are error/empty/loading states addressed?

**Requirement Clarity** — Are requirements specific and unambiguous?
- No vague adjectives ("robust", "intuitive", "fast") without quantification
- No "should", "might", "could" without explicit resolution
- Measurable acceptance criteria for every FR

**Requirement Consistency** — Do requirements align without conflicts?
- No contradictions between FRs
- Terminology used consistently across all sections
- Tradeoff decisions in clarify.md reflected in spec

**Acceptance Criteria Quality** — Are success criteria measurable?
- Each AC has concrete pass/fail conditions
- ACs are technology-agnostic where possible
- ACs cover both happy path and edge cases

**Scenario Coverage** — Are all flows/cases addressed?
- Primary user journeys documented
- Error/exception flows defined
- Edge cases identified (in spec or clarify.md)

**Dependencies & Assumptions** — Are they documented?
- External dependencies listed with failure modes
- Assumptions documented and validated
- Dokkaebi-specific: tier routing, AOP, ledger impact considered

**Traceability** — Can each requirement be traced?
- Every FR has at least one AC
- Every AC maps to testable criteria
- Out of scope section clearly bounds the work

### Checklist Item Format

```markdown
- [ ] CHK-{NNN} {Quality question} [{Dimension}, {reference}]
```

Examples:
- `- [ ] CHK-001 Are visual hierarchy requirements defined with measurable criteria? [Clarity, FR-002]`
- `- [ ] CHK-005 Are error handling requirements defined for all API failure modes? [Completeness, Gap]`
- `- [ ] CHK-012 Is 'fast' quantified with specific timing thresholds? [Clarity, NFR-001]`

4. **Evaluate**: Check each item against the spec. Mark as PASS or FAIL.
5. **Handle failures**: If items FAIL:
   - List failing items with specific issues (quote relevant spec sections)
   - Suggest fixes
   - If running within `/speckit.prepare`: apply fixes automatically, re-check (max 3 iterations)
6. **Write** `specs/{NNN}-{name}/checklist.md` with results.
7. **Report**: Pass rate, key issues, and suggest next step.

### Output Format

```markdown
# {NNN}: {Title} — Requirements Checklist

## Functional Requirements
| ID | Requirement | Testable | AC Linked | Status |
|----|------------|----------|-----------|--------|
| FR-001 | ... | Yes | AC-1, AC-3 | PASS |

## Non-Functional Requirements
| ID | Requirement | Verification Method | Status |
|----|------------|---------------------|--------|

## Quality Gates
- [x] Every FR has at least one linked AC
- [x] Every AC is testable
- [ ] No ambiguous language remaining

## Result: {X}/{Y} PASS
```

## Guidelines

- This validates requirement QUALITY, not implementation correctness
- Focus on catching ambiguity before implementation starts
- Flag "hidden assumptions" that become bugs later
- Items MUST ask about the requirements, not about system behavior
- Minimum 80% of items should have traceability references ([Spec FR-xxx], [Gap], etc.)
