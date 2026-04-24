---
name: speckit.verify
description: "Verify implementation against spec requirements. Usage: /speckit.verify [spec-number]"
user-invocable: true
---

## User Input

```text
$ARGUMENTS
```

You **MUST** consider the user input before proceeding (if not empty).

## Outline

Verify that the current implementation satisfies the spec's requirements and acceptance criteria.

### Steps

1. **Locate spec**: Read all files in `specs/{NNN}-*/`.
2. **Load implementation**: Read the files listed in tasks.md, check they exist.
3. **Verify each acceptance criterion**:
   - For testable criteria: check if corresponding test exists and passes
   - For code criteria: grep/read source to verify implementation
   - For CLI criteria: verify command exists in help output
4. **Run tests**: Execute `pytest` on spec-related test files.
5. **Generate verification report**:

   ```markdown
   # {NNN}: Verification Report

   ## Acceptance Criteria
   | # | Criterion | Status | Evidence |
   |---|-----------|--------|----------|
   | 1 | ... | PASS | test_xxx.py::test_yyy passes |
   | 2 | ... | FAIL | file not found: ... |

   ## Test Results
   - Total: N
   - Passed: N
   - Failed: N

   ## Coverage
   - FRs verified: X/Y
   - Acceptance criteria met: X/Y

   ## Verdict: PASS / FAIL
   ```

6. **Write** `specs/{NNN}-{name}/verify-report.md`.
7. **Report**: Verdict + failing criteria.

### Guidelines
- This verifies IMPLEMENTATION against SPEC, not code quality
- Run actual tests, don't just check file existence
- Be specific about evidence (which test, which file, which line)
- FAIL verdict if any acceptance criterion is not met
