# Naming Conventions

## Purpose

Establishes the canonical naming rules for identifiers, fields, and
states across every schema in this repository, making explicit a
convention that has been followed consistently but implicitly — this
document is what future contributions are checked against, rather than
relying on contributors inferring the pattern from examples.

## Scope

Naming conventions for identifiers, JSON field names, states, and topic
names. Does not cover code-level naming (function/class names), which
belongs to `docs/14-development/coding-standards.md`.

## Identifier naming

Every entity identifier uses `snake_case` with an explicit `_id` suffix
naming the entity type: `task_id`, `plugin_id`, `capability_id`,
`tool_id`, `message_id`, `correlation_id`, `event_id`. Never `taskId`, `TaskID`, `tid`, or a bare `id` field without a type-identifying
prefix — an unqualified `id` field is ambiguous the moment a record is
handled alongside records of other entity types, which happens often
given how frequently entities cross-reference each other
(`docs/04-memory/ontology.md`).

## JSON field naming

All JSON schemas throughout this repository (tool contracts, API
payloads, configuration) use `snake_case` for field names, consistently
— this repository does not mix `snake_case` and `camelCase` across
different schemas, since a contributor moving between, for example,
`docs/06-tools/tool-interface.md` and `docs/08-api/schemas.md` should
never need to remember a different casing convention per document.

## State naming

State machine states use `PascalCase` (e.g., `Created`, `WaitingUser`,
`Retrying` in `docs/03-runtime/task-manager.md`; `Enabled`, `Disabled` in `docs/16-extensibility/plugin-lifecycle.md`) when referenced in
diagrams and prose, and the corresponding `snake_case` form
(`waiting_user`, `retrying`) when serialized in an API payload
(`docs/08-api/schemas.md`) — the same state, two casing conventions by
context, documented here specifically so this distinction is deliberate,
not an inconsistency.

## Topic and event naming

Communication Bus topics (`docs/02-architecture/communication-model.md`)
use dot-separated `lowercase` segments: `observer.filesystem.file_created`,
`task.completed`, `system.heartbeat.<service_name>` — a hierarchical,
greppable naming scheme where the first segment identifies the owning
category (`observer`, `task`, `memory`, `system`) and subsequent segments
narrow specificity.

## Version strings

Every version field (`docs/08-api/versioning.md`,
`docs/06-tools/tool-schema-versioning.md`,
`docs/16-extensibility/plugin-versioning.md`,
`docs/05-ai/prompt-versioning.md`) uses semantic versioning
(`major.minor.patch`) — no repository-specific alternative versioning
scheme is used anywhere, so version comparison logic can be shared
across every versioned entity type rather than reimplemented per schema.

## ID generation strategy

Every `_id` field (per the identifier naming convention above) is
generated as a UUID, specifically **UUID v7** rather than v4, wherever
the underlying platform/library supports it. UUID v7 embeds a timestamp
component, which means IDs are naturally sortable by creation order —
useful directly for Timeline Memory (`docs/04-memory/timeline.md`) and
for efficient indexing (`docs/04-memory/indexing.md`), without requiring
a separate creation-timestamp lookup purely to order records by recency.
Where a platform's UUID library does not yet support v7, UUID v4 is an
acceptable fallback, but v7 is preferred wherever available. IDs are
never generated as sequential integers, since a sequential ID scheme
would leak information about record volume/ordering across the external
API (`docs/08-api/`) and would complicate the eventual multi-device
synchronization work (`ROADMAP.md` Phase 5), where independently
generated IDs from different machines must never collide.

## Enforcement

New schemas are checked against this convention as part of
`docs/14-development/module-checklist.md`. An existing schema found to
violate this convention during an otherwise-motivated revision should be
corrected as part of that revision; this document does not mandate a
standalone sweep to fix any pre-existing inconsistency, consistent with
the same reasoning in `docs/14-development/module-contract-standard.md`
regarding retroactive rewrites.

## Related documents

- `docs/14-development/coding-standards.md` — code-level naming,
  distinct from the schema-level conventions here
- `docs/14-development/module-contract-standard.md` — the analogous
  "apply going forward, not retroactively" enforcement approach
- `docs/references/schema-index.md` — the master index where these
  conventions are most visibly applied across all schemas at once
