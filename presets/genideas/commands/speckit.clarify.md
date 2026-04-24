---
name: speckit.clarify
description: "Identify and resolve ambiguities in the current spec via targeted Q&A with mandatory tradeoff tables. Usage: /speckit.clarify [spec-number or path]"
user-invocable: true
---

## User Input

```text
$ARGUMENTS
```

You **MUST** consider the user input before proceeding (if not empty).

## Outline

Goal: Detect and reduce ambiguity in the active feature spec by asking up to 5 highly targeted clarification questions and encoding answers into clarify.md + tradeoffs.md.

### Steps

1. **Locate spec**: If user provides a number (e.g., "005"), read `specs/005-*/spec.md`. If no number, find the highest-numbered spec directory. If a path is given, use that directly.

2. **Load context**: Read the spec.md file. Also read `governance/CONSTITUTION.md` and any existing clarify.md if they exist.

3. **Structured ambiguity scan**: Analyze the spec across these 10 categories, marking each as Clear / Partial / Missing:

   | Category | What to check |
   |----------|--------------|
   | Scope & Behavior | Core goals, out-of-scope, user roles/personas |
   | Data Model | Entities, relationships, lifecycle, state transitions |
   | Interaction & UX | Critical user journeys, error/empty/loading states |
   | Non-Functional | Performance targets, scalability, reliability, observability |
   | Security & Privacy | AuthN/Z, data protection, threat model |
   | Integration | External dependencies, failure modes, protocols |
   | Edge Cases | Negative scenarios, rate limiting, conflict resolution |
   | Constraints & Tradeoffs | Technical constraints, explicit tradeoffs |
   | Terminology | Consistent naming, no ambiguous adjectives |
   | Dokkaebi-specific | LLM tier routing, AOP integration, ledger impact, tool calling |

   For each Partial/Missing category, generate a candidate question unless:
   - The information won't materially change implementation
   - It's better deferred to the planning phase

4. **Prioritize questions** (max 5): Rank by `Impact * Uncertainty` heuristic:
   - Only include questions whose answers materially impact architecture, data modeling, task decomposition, test design, UX, or compliance
   - Balance across categories (don't ask 3 questions in the same area)
   - Favor clarifications that reduce downstream rework risk

5. **Sequential Q&A loop**: Present **ONE question at a time**:

   For EVERY question, you **MUST** show a tradeoff table. This is non-negotiable.

   ```markdown
   ### Question N of M: {Topic}

   {Context — why this matters}

   | Option | Description | 장점 | 단점 |
   |--------|-------------|------|------|
   | **A** | ... | ... | ... |
   | **B** | ... | ... | ... |
   | **C** | ... | ... | ... |

   **추천**: Option X — {reasoning}
   ```

   After user answers:
   - If "yes" or "recommended": use the recommended option
   - If option letter (e.g., "A"): use that option
   - If ambiguous: ask for quick disambiguation (doesn't count as new question)
   - Record answer and move to next question

   Stop when: all critical ambiguities resolved, user says "done", or 5 questions asked.

6. **Write clarify.md**: Create/update `specs/{NNN}-{name}/clarify.md`:
   ```markdown
   # {NNN}: {Title} — Clarify

   ## Q1: {Question}
   **문제**: {Context}
   **결정**: **{Selected option}** — {description}
   **근거**: {Why this was chosen}
   ---
   ...
   ```

7. **Write tradeoffs.md**: Create/update `specs/{NNN}-{name}/tradeoffs.md`:
   ```markdown
   # {NNN}: {Title} — Tradeoff Decisions

   ## TD-001: {Question topic}
   | Option | 선택 |
   |--------|------|
   | **A: {desc}** | **SELECTED** |
   | B: {desc} | rejected — {reason} |
   | C: {desc} | rejected — {reason} |
   ---
   ...
   ```

8. **Report**: Number of questions asked, sections clarified, coverage summary table (category → status), and suggest next step: `/speckit.checklist {NNN}` or `/speckit.plan {NNN}`.

### Behavior Rules

- **EVERY question MUST have a tradeoff table** — no exceptions, even for simple yes/no decisions
- If no meaningful ambiguities found, report "No critical ambiguities detected" and suggest proceeding
- If spec.md is missing, instruct user to run `/speckit.specify` first
- Never exceed 5 questions
- Never reveal future queued questions in advance
- Favor clarifications that reduce downstream rework
- Consider Dokkaebi's existing patterns (atomic writes, JSONL, tier routing) when evaluating
- Respect user early termination signals ("done", "stop", "proceed")
