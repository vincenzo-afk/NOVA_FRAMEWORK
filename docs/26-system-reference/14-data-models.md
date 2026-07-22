# Data Models Reference

## Purpose

A single cross-cutting catalog of every entity in NOVA, in one place, so
no entity's shape has to be reconstructed by reading five different
component docs. Each entity's *authoritative* definition still lives in
its owning component's document (linked below) — this file is an index
and a consistency check, not a second source of truth. Where this file
and an owning document disagree, the owning document wins and this file
has drifted; see `11-documentation-lint-ci.md`.

## Scope

Every persisted or long-lived entity that crosses a component boundary
(i.e., that at least one other component reads or references). Purely
internal, component-private data structures are documented in that
component's own file and are not listed here.

## How to read each entry

For every entity: **fields**, **ownership**, **validation**,
**constraints**, **defaults**, **lifecycle**, **serialization**,
**persistence**, **relationships** — per the master documentation
outline, Section 5. Full field-by-field detail lives in the owning doc;
this table gives the summary and the cross-reference.

## Entity catalog

### Task

- **Owner:** Task Manager (`docs/03-runtime/task-manager.md`)
- **Fields:** `task_id` (UUID, immutable), `state`, `plan_ref`,
  `parent_task_id` (nullable), `created_at`, `updated_at`,
  `origin` (user / autonomous / scheduled).
- **Validation:** `state` must be a valid transition per
  `docs/26-system-reference/04-state-transition-tables.md`.
- **Constraints:** exactly one current state at any moment
  (`system-invariants.md`); `task_id` is never reused.
- **Defaults:** `state = Pending` on creation.
- **Lifecycle:** Created → Planned → Executing → (Paused ↔ Executing) →
  Completed / Failed / Cancelled. See `03-runtime/task-manager.md`.
- **Serialization:** JSON, versioned envelope (see Event System,
  `07-event-catalog.md`).
- **Persistence:** durable store keyed by `task_id`; see
  `docs/13-devops/storage-layout.md`.
- **Relationships:** references zero or one `parent_task_id`; produces
  one or more Events; may reference Memory entries it created.

### Memory Entry / Node

- **Owner:** Memory subsystem (`docs/04-memory/memory-architecture.md`,
  `docs/04-memory/knowledge-graph.md`)
- **Fields:** `node_id` (UUID, immutable), `type`, `content`,
  `confidence`, `source_task_id`, `created_at`, `version`.
- **Validation:** `type` must be one of the types enumerated in
  `docs/04-memory/memory-types.md`; graph must remain acyclic
  (`system-invariants.md`).
- **Constraints:** never references a deleted node directly — deletion
  produces a tombstone (`docs/04-memory/memory-garbage-collection.md`).
- **Defaults:** `confidence` starts at the source's stated confidence;
  see `docs/04-memory/memory-confidence.md`.
- **Lifecycle:** Created → Indexed → (Reinforced / Superseded) →
  Archived / Deleted. See `docs/04-memory/memory-lifecycle.md`.
- **Serialization:** graph-native record plus embedding vector; see
  `docs/04-memory/embeddings.md`.
- **Persistence:** knowledge graph store + vector index; see
  `docs/04-memory/memory-storage.md`.
- **Relationships:** edges to other nodes (ontology in
  `docs/04-memory/ontology.md`); many-to-one with source Task.

### Plugin

- **Owner:** Extensibility subsystem
  (`docs/16-extensibility/plugin-lifecycle.md`)
- **Fields:** `plugin_id` (immutable), `version`, `manifest`,
  `granted_permissions`, `state`, `installed_at`.
- **Validation:** manifest must declare every capability it uses; see
  `docs/16-extensibility/plugin-permissions.md`.
- **Constraints:** never has direct storage or internal-API access
  (`constraints.md`).
- **Defaults:** `granted_permissions = []` until explicitly approved.
- **Lifecycle:** Discovered → Installed → Loaded → Running →
  (Suspended) → Unloaded → Removed. See
  `docs/16-extensibility/plugin-lifecycle.md`.
- **Serialization:** manifest as JSON/TOML; see
  `docs/16-extensibility/plugin-versioning.md`.
- **Persistence:** local plugin registry; see
  `docs/13-devops/storage-layout.md`.
- **Relationships:** many-to-many with granted capabilities; one-to-many
  with emitted Events.

### Tool / Capability

- **Owner:** Tool system (`docs/06-tools/tool-registry.md`,
  `docs/05-ai/capability-registry.md`)
- **Fields:** `tool_id`, `schema_version`, `input_schema`,
  `output_schema`, `owning_component`.
- **Validation:** every input validated against `input_schema` before
  execution; see `docs/06-tools/tool-interface.md`.
- **Constraints:** schema changes are additive-only within a major
  version; see `docs/06-tools/tool-schema-versioning.md`.
- **Lifecycle:** Registered → Available → (Deprecated) → Removed.
- **Serialization:** JSON Schema.
- **Persistence:** tool registry; see `docs/06-tools/tool-registry.md`.
- **Relationships:** referenced by Task plans; many-to-one with owning
  component or Plugin.

### Workspace

- **Owner:** Runtime / identity layer
  (`docs/28-multi-device-protocol/10-identity-and-workspace.md`)
- **Fields:** `workspace_id` (immutable), `owner_id`, `devices[]`,
  `created_at`.
- **Constraints:** exactly one owner at all times
  (`system-invariants.md`).
- **Lifecycle:** Created → Active → (Migrating) → Archived.
- **Persistence:** durable store, replicated across paired devices; see
  `docs/28-multi-device-protocol/01-cross-device-sync.md`.
- **Relationships:** one-to-many with Devices, Tasks, and Memory nodes.

### Provider / Model Route

- **Owner:** Model routing (`docs/05-ai/model-router.md`,
  `docs/18-providers/provider-interface.md`)
- **Fields:** `provider_id`, `capabilities[]`, `credentials_ref`,
  `status`.
- **Constraints:** `credentials_ref` never stores the raw secret inline;
  see `docs/10-security/secrets.md`.
- **Lifecycle:** Registered → Available → Degraded → Unavailable → 
  Removed. See `docs/18-providers/provider-interface.md`.
- **Relationships:** many-to-many with Capabilities; referenced by the
  Model Routing Matrix (`docs/05-ai/model-routing-matrix.md`).

### Event

- **Owner:** Event bus (`docs/02-architecture/event-bus-specification.md`)
- **Fields:** `message_id` (globally unique, immutable), `type`,
  `payload`, `published_at`, `priority`.
- **Constraints:** duplicate `message_id` always treated as a
  redelivery, never a new event (`system-invariants.md`).
- **Lifecycle:** Published → Delivered → Acknowledged / Retried /
  Dead-lettered. See `docs/26-system-reference/07-event-catalog.md`.
- **Relationships:** every mutation of every other entity in this
  catalog produces at least one Event (`system-invariants.md`).

## Consistency rule

Any new entity that crosses a component boundary must be added here in
the same change that introduces it, per the documentation-lint check in
`11-documentation-lint-ci.md`. An entity added to an owning doc but
omitted here is treated as a documentation defect, not a minor omission,
because this catalog is what an AI implementer or reviewer checks first
when a task touches more than one subsystem.
