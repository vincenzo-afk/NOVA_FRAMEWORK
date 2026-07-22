# Filesystem Failures

## Known failure patterns

Sandbox path traversal not blocked; disk-full mid-write leaving a truncated file mistaken for valid; permission denied on an observer's watched path not surfaced to the user.

## Cross-reference

See the corresponding subsystem file in `45-code-perfection-failure-modes/` for the code-level prevention checklist, and `25-failure-modes/INDEX.md` for the full narrative failure-mode set.
