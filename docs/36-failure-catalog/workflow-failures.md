# Workflow Failures

## Known failure patterns

Cycle in a dynamically generated workflow graph; parallel branch write conflict; stuck human-approval gate with no timeout; partial rollback leaving orphaned resources.

## Cross-reference

See `docs/45-code-perfection-failure-modes/06-workflow-engine.md` for the closest code-level prevention checklist covering this subsystem (that directory is organized by broader cross-cutting concern, not 1:1 by this file's subsystem name), and `docs/25-failure-modes/INDEX.md` for the full narrative failure-mode set.
