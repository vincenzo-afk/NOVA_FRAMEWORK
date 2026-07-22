# Runtime Failures

## Known failure patterns

State Manager fails to reach ready state at boot; Service Lifecycle deadlock between two services awaiting each other; Executor left in a stuck 'in-progress' state after crash with no recovery sweep. See `03-runtime/failure-recovery.md` for recovery contracts.

## Cross-reference

See the corresponding subsystem file in `45-code-perfection-failure-modes/` for the code-level prevention checklist, and `25-failure-modes/INDEX.md` for the full narrative failure-mode set.
