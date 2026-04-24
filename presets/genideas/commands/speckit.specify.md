---
name: speckit.specify
description: "Create a new numbered spec from a feature description, or review/fill gaps in an existing spec. Usage: /speckit.specify <feature description or spec-number>"
user-invocable: true
---

## User Input

```text
$ARGUMENTS
```

You **MUST** use the user input to determine the mode:
- If input is a feature description (text): **create** a new spec
- If input is a number (e.g., "019"): **review** existing spec, fill gaps

## Outline

You are creating/reviewing a spec for the Dokkaebi Harness (aw) project. Specs live in `specs/` with sequential numbering.

### Steps

1. **Determine spec number and mode**:
   - **New spec**: List `specs/` directory, find highest NNN prefix, use NNN+1 (zero-padded to 3 digits).
   - **Existing spec**: Locate `specs/{NNN}-*/spec.md`, read it, identify gaps.

2. **Generate short name** (new spec only): Create a 2-4 word kebab-case name from the feature description (e.g., "repo-map", "cost-dashboard", "figma-plugin-mvp").

3. **Create spec directory** (new spec only): `specs/{NNN}-{short-name}/`

4. **Create branch**: If not already on `ms/{NNN}-*` branch:
   ```bash
   git checkout -b ms/{NNN}-{short-name}
   ```

5. **Write spec.md** following this project's established format:

   ```markdown
   # {NNN}: {Title}

   ## 1. Purpose
   <1-2 paragraphs explaining why this feature exists>
   <Dependencies on other specs if any>

   ## 2. Requirements

   ### FR-001: {Name}
   - Bullet points defining functional requirements
   - Each requirement must be testable and unambiguous

   ### FR-002: {Name}
   ...

   ## 3. Non-Functional Requirements

   ### NFR-001: {Name}
   ...

   ## 4. Out of Scope
   - Items explicitly excluded

   ## 5. Acceptance Criteria
   - [ ] Testable criterion 1
   - [ ] Testable criterion 2
   ...
   ```

6. **Quality validation**: Run a self-check against these criteria. If any FAIL, fix before proceeding:

   | Check | Rule |
   |-------|------|
   | No implementation details | No frameworks, APIs, tech stack in FRs — focus on WHAT, not HOW |
   | Testable FRs | Each FR can be verified with a test |
   | Unambiguous | No "should consider", "might", "if possible" without resolution |
   | Measurable ACs | Acceptance criteria have concrete pass/fail conditions |
   | Project alignment | Aligns with CLAUDE.md conventions |
   | Max 3 clarifications | At most 3 `[NEEDS CLARIFICATION]` markers for genuinely unclear aspects |

   For any `[NEEDS CLARIFICATION]` markers, present options in a **tradeoff table**:
   ```markdown
   ## Question [N]: [Topic]

   | Option | Description | Implications |
   |--------|-------------|-------------|
   | A | ... | ... |
   | B | ... | ... |
   | C | ... | ... |

   **Recommended**: Option X — reasoning
   ```
   Wait for user answers, then resolve markers in spec.

7. **Report**: Output the spec number, directory path, branch name, and suggest: `/speckit.clarify {NNN}` or `/speckit.plan {NNN}`.

### Review Mode (existing spec)

When reviewing an existing spec:
1. Read spec.md fully
2. Check all quality criteria from step 6
3. Identify gaps: missing FRs, vague requirements, missing ACs, scope issues
4. Fix gaps directly in spec.md (or present as clarification questions if decision needed)
5. Report what was fixed/improved

## Guidelines

- Focus on **WHAT** users/system needs and **WHY**
- Dokkaebi conventions: Python 3.11+, pytest, atomic writes, JSONL ledgers, tier routing
- Reference existing modules when extending (`dokkaebi/core/`, `dokkaebi/llm/`, etc.)
- Each FR should map to implementable code units
- Keep spec concise — detail goes in plan.md and clarify.md
- File naming: lowercase for all spec files (spec.md, plan.md, tasks.md, clarify.md, guide.md)
- When extending existing architecture, check plan.md AD decisions from prior specs
