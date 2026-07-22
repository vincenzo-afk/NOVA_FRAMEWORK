# Duplicate Events — Edge Case

## Scenario

Idempotency keys on all side-effecting operations; observer-level dedup on identical consecutive events within a short window.

## Requirement

Every edge case in this directory must have an explicit test in `12-testing/` — an edge case with no test is an edge case that will regress silently.
