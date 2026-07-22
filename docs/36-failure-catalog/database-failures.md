# Database Failures

## Known failure patterns

Migration applied without a rollback path; schema version mismatch between reader and writer during a rolling update; write-ahead log not flushed before reporting write success.

## Cross-reference

See the corresponding subsystem file in `45-code-perfection-failure-modes/` for the code-level prevention checklist, and `25-failure-modes/INDEX.md` for the full narrative failure-mode set.
