# Context Generation for AI Coding Agents


## Purpose

What context an AI agent needs loaded before it can safely generate code
for a given ticket, so it isn't reasoning from partial or stale
understanding of the architecture.

## Minimum context set per task type

- **Any task**: `00-overview/design-principles.md`, `00-overview/system-invariants.md`,
  `00-overview/non-goals.md` (to avoid building explicitly-excluded scope).
- **Any task touching memory**: the full `04-memory/` set for the
  specific tier involved, plus `memory-conflict-resolution.md` if writes
  can race.
- **Any task touching a tool or plugin**: `06-tools/`, `10-security/permissions.md`,
  `16-extensibility/plugin-sandboxing.md`.
- **Any task touching the Planner/Executor**: `03-runtime/planner-executor-contract.md`
  in full — this is the single most load-bearing contract in the system
  and partial reads of it produce subtly wrong step-execution code.
- **Any task touching cross-device behavior**: `28-multi-device-protocol/`
  in full, plus `26-system-reference/09-version-compatibility-matrix.md`.
- **Any task the agent is uncertain about**: `25-failure-modes/INDEX.md`
  and `45-code-perfection-failure-modes/INDEX.md` filtered to the
  relevant subsystem, read in full, not skimmed.

## Rule: context sufficiency check

Before generating code, the agent should be able to answer, from the
loaded context alone and without assumption:
1. What is the exact input/output contract of every function I'm calling?
2. What are the documented failure modes for this subsystem, and does my
   code handle each one explicitly?
3. Who else consumes the interface I'm about to change?
4. What is the acceptance criteria this code must satisfy?

If any answer requires guessing, load more context before writing code —
do not generate code against an assumed interface and mark it TODO.
