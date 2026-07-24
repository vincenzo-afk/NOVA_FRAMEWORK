# Implementation Order

## Purpose

Translates `ROADMAP.md`'s phase structure and
`docs/02-architecture/dependency-map.md`'s service dependency graph into a
concrete build sequence at the code level.

## Scope

Build-time sequencing. Product-level phase definitions are `ROADMAP.md`;
service-level dependency reasoning is `docs/02-architecture/dependency-map.md`.

## Phase 1 build order

1. Communication Bus and message envelope
   (`docs/02-architecture/communication-model.md`) — the foundation every
   other service depends on.
2. Runtime Manager (`docs/03-runtime/runtime-manager.md`) — needed to
   supervise everything built after it.
3. Memory storage engines and the four memory tiers
   (`docs/04-memory/memory-storage.md`, `memory-architecture.md`).
4. Knowledge Graph and the fixed ontology
   (`docs/04-memory/knowledge-graph.md`, `ontology.md`).
5. Observer framework and the Filesystem, Applications, and Windows
   observers first (`docs/07-observers/`) — the sources most directly
   needed for the Phase 1 use cases in `docs/01-product/use-cases.md`.
6. Indexing pipeline and Retrieval Fusion Engine
   (`docs/04-memory/indexing.md`, `retrieval-engine.md`).
7. Context Builder and Search (`docs/05-ai/context-builder.md`,
   `docs/04-memory/search.md`) — completing the read-only Q&A capability
   that defines Phase 1's deliverable.

## Phase 2 build order

1. Task Manager and Scheduler (`docs/03-runtime/task-manager.md`,
   `scheduler.md`).
2. Tool Registry and the `tool-interface.md` contract.
3. Native Runtime and CLI tiers (`docs/06-tools/native-runtime.md`,
   `cli.md`) — the safest, first-implemented execution tiers.
4. Permission Manager and Resource Manager
   (`docs/03-runtime/permission-manager.md`, `resource-manager.md`).
5. Executor and Verifier (`docs/03-runtime/executor.md`, `verifier.md`).
6. Undo/rollback mechanism for reversible-write actions, built alongside
   the Executor rather than retrofitted afterward.

## Phase 3 and beyond

Follow the same pattern: Planner and Model Router
(`docs/03-runtime/planner.md`, `docs/05-ai/model-router.md`) for Phase 3's
multi-step orchestration; Accessibility, Vision, and Automation tiers
(`docs/06-tools/accessibility.md`, `docs/06-tools/vision.md`, `docs/06-tools/automation.md`) only in
Phase 4, after the undo/verification/permission scaffolding from Phases 2
and 3 is proven.

## Why this specific ordering within each phase

Within every phase, storage and framework-level components (memory,
observer framework, tool registry) are built before the components that
depend on them (indexing before retrieval, tool registry before
executor) — this mirrors `docs/02-architecture/dependency-map.md`
directly and prevents building a consumer against a dependency that does
not yet exist in a stable form.

## Related documents

- `ROADMAP.md` — the phase definitions this order implements
- `docs/02-architecture/dependency-map.md` — the dependency graph this
  sequencing follows
- `milestones.md` — concrete milestones marking progress through this
  order
