# Network Failures

## Known failure patterns

Partial request timeout distinguishing 'no response' from 'response lost after success'; DNS failure not distinguished from auth failure; retry storm on provider recovery without backoff jitter.

## Cross-reference

See the corresponding subsystem file in `45-code-perfection-failure-modes/` for the code-level prevention checklist, and `25-failure-modes/INDEX.md` for the full narrative failure-mode set.
