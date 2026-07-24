# Feature Flags

Every feature ships behind a flag until it has passed the full test suite in `12-testing/` and had at least one chaos test (`chaos-tests.md`) run against its failure paths. Flags are three-state: off / internal / general — never a direct off-to-general jump. Flag state changes are themselves audit-logged per `docs/10-security/audit.md`.
