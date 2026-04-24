---
name: speckit.plan
description: "Create technical implementation plan with architecture decisions, research, and design contracts. Usage: /speckit.plan [spec-number or path]"
user-invocable: true
---

## User Input

```text
$ARGUMENTS
```

You **MUST** consider the user input before proceeding (if not empty).

## Outline

Create a plan.md with architecture decisions, module layout, data flow, and integration points. Two-phase approach: research unknowns first, then design.

### Steps

1. **Locate spec**: If user provides a number (e.g., "005"), read `specs/005-*/spec.md` and `specs/005-*/clarify.md` (if exists). If no number, use highest-numbered spec.

2. **Load context**:
   - Read spec.md, clarify.md, tradeoffs.md, checklist.md from the spec directory
   - Read `governance/CONSTITUTION.md` for policy constraints
   - Read `CLAUDE.md` for coding conventions
   - Scan existing codebase structure to understand integration points

3. **Phase 0 — Research**: For each technical unknown in the spec:
   - Examine existing code patterns in the project
   - Identify reusable infrastructure (atomic writes, JSONL, LLMRouter, etc.)
   - Research external dependencies/APIs if needed
   - Determine which existing modules need modification vs new modules
   - **Output**: Resolve all "NEEDS CLARIFICATION" items. Document findings internally.

4. **Phase 1 — Design**: Write plan.md:

   ```markdown
   # {NNN}: {Title} — Implementation Plan

   ## Architecture Overview
   ```
   {directory tree with NEW/MODIFY annotations}
   ```

   ## Key Architecture Decisions

   ### AD-001: {Decision Title}
   - **What**: description
   - **Why**: reasoning
   - **Alternative considered**: what was rejected and why

   ### AD-002: {Decision Title}
   ...

   ## Data Flow
   ### {Path Name}
   ```
   {ASCII diagram showing data flow}
   ```

   ## Integration with Existing Infrastructure
   | Component | How this feature uses it |
   |-----------|------------------------|
   ...

   ## Dependencies
   ### External
   - {package} — {purpose}
   ### Internal
   - {module} — {what it provides}

   ## Prompt Architecture (if LLM prompts involved)
   | Prompt | Input | Output | Tier |
   |--------|-------|--------|------|
   ...

   ## New CLI Commands (if any)
   | Command | Description |
   |---------|-------------|
   ...

   ## Risk Mitigation
   | Risk | Impact | Mitigation |
   |------|--------|-----------|
   ...

   ## Test Strategy
   1. Unit tests — ...
   2. Integration tests — ...
   3. Fixture-based tests — ...
   ```

5. **Validate**:
   - No constitution violations
   - All NEEDS CLARIFICATION resolved
   - Design uses existing infrastructure where possible (don't reinvent)
   - Module layout follows project conventions
   - Risk mitigation covers key failure modes

6. **Report**: Output plan.md path, list of new/modified modules, key decisions count, and suggest next step: `/speckit.tasks {NNN}`.

### Design Principles

- **Prefer extending over creating**: Use existing patterns (JSONL, atomic writes, LLMRouter)
- **Minimal dependencies**: Avoid new external deps unless essential
- **Separate concerns**: New features in their own modules, integrated via hooks
- **Backward compatibility**: Existing tests must still pass
- **Tier routing**: Place new LLM tasks in appropriate tier (T0/T1/T2/T4)
- **Testability**: Design for unit testability — separate pure logic from I/O
