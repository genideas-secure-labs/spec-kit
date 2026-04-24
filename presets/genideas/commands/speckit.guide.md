---
name: speckit.guide
description: "Generate execution guide (guide.md) for a completed spec. Usage: /speckit.guide [spec-number or path]"
user-invocable: true
---

## User Input

```text
$ARGUMENTS
```

You **MUST** consider the user input before proceeding (if not empty).

## Outline

Generate a guide.md showing how to use the implemented feature, with CLI examples and expected outputs.

### Steps

1. **Locate spec**: If user provides a number, read all files in `specs/{NNN}-*/`. If no number, use highest-numbered spec.

2. **Load context**: Read spec.md, plan.md, tasks.md, clarify.md from the spec directory.

3. **Write guide.md** following the project format (see `specs/004-goal-oriented-reconciler/guide.md` as reference):

   ```markdown
   # {NNN}: {Title} — Execution Guide

   ## Quick Start
   ```bash
   # Key commands to use this feature
   dokkaebi <command> [options]
   ```

   ## Feature 1: {Name}

   ### What It Does
   <1-2 sentences>

   ### How It Improves [X]
   - **Before**: <old behavior>
   - **After**: <new behavior>

   ### CLI Usage
   ```bash
   dokkaebi <command> [flags]
   ```

   ### Output Example
   ```
   <realistic example output>
   ```

   ## Files Created / Modified
   | File | Feature | Action |
   |------|---------|--------|
   ...

   ## Migration Notes
   <backward compatibility info>
   ```

4. **Quality checks**:
   - All CLI commands from plan.md are documented
   - Output examples are realistic and consistent with the spec
   - Migration notes address backward compatibility

5. **Report**: Output guide.md path.
