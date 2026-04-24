---
name: speckit.constitution
description: "Create or update project governing principles (CONSTITUTION.md). Usage: /speckit.constitution"
user-invocable: true
---

## User Input

```text
$ARGUMENTS
```

You **MUST** consider the user input before proceeding (if not empty).

## Outline

Create or update `governance/CONSTITUTION.md` with project principles, policies, and development guidelines.

### Steps

1. **Check existing**: Read `governance/CONSTITUTION.md` if it exists.
2. **Load context**: Read `CLAUDE.md`, `AGENTS.md`, recent specs for project patterns.
3. **Generate or update** following sections:
   - Project identity and mission
   - Core principles (immutable)
   - Development policies (coding style, testing, security)
   - Aspect policies (AOP rules, waiver policy)
   - Change management (proposal flow, canonical vs derived)
   - Agent guidelines (what agents can/cannot do)
4. **Preserve existing**: If updating, keep existing principles, add new ones. Never silently remove.
5. **Report**: Output path and summary of changes.

### Guidelines
- Focus on principles that CONSTRAIN behavior, not describe it
- Each principle should be actionable and verifiable
- Reference CLAUDE.md conventions but don't duplicate them
- Keep it concise — principles, not prose
