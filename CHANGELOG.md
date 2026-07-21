# Changelog

All notable changes to this documentation repository are recorded here.
Format follows [Keep a Changelog](https://keepachangelog.com/); this project
has not yet reached a versioned software release, so entries below track
documentation milestones, not code releases.

## [Unreleased]

No further open-ended gap-analysis passes are queued beyond the v5
evolution below. See `ROADMAP.md`'s note on further gap-analysis for why:
the higher-leverage next step is implementation, which will surface real
gaps rather than speculative ones. The targeted audit directly below was
an exception because it checked specific, named claims against the
existing text rather than searching for new categories of gap, and found
a small number of genuine ones plus one real cross-document
inconsistency; it is not a precedent for reopening general review.

## [1.0.1] — Targeted audit: knowledge-model, autonomy, and distributed-runtime gaps

Requested as a check against nine specific claimed gaps (self-improving
intelligence, unified knowledge model, trust/approval policy, goal-driven
operation, shared world model, observability, SDK/ecosystem, distributed
runtime, platform framing). Six of the nine were already fully specified
under different names; this pass fixed one real inconsistency and added
documents only for the three that were genuinely missing.

### Fixed
- `docs/05-ai/hallucination-prevention.md` — added an explicit mapping
  table between its own four-tier AI-specific risk scale
  (Low/Medium/High/Critical) and `docs/10-security/permissions.md`'s
  three-tier general scale (Read-only/Reversible-write/Destructive).
  These had been referenced as if interchangeable in
  `explainability.md` and `confidence-propagation.md` with no document
  ever stating how they related.
- `docs/05-ai/explainability.md`, `docs/05-ai/confidence-propagation.md`
  — disambiguated "risk tier" references to point at the correct scale
  following the fix above

### Added
- `docs/04-memory/ontology.md` — v2 node types (Person, Goal, Device) and
  edge types (`involves`, `pursues`, `advances`, `blocks`, `resides_on`),
  additive per this document's own versioning rule, closing the gap
  where people, goals, and paired devices were handled extensively
  elsewhere but had no Knowledge Graph representation
- `docs/23-autonomy/goal-tracking.md` — persistent multi-week goal
  representation and proactive blocker/deadline/opportunity surfacing,
  distinct from Task (single execution) and the daily Background Life
  Assistant briefing
- `docs/23-autonomy/strategy-evaluation.md` — comparison, promotion, and
  retirement of competing strategies for a recurring goal, distinct from
  episodic replay's single-best-prior-plan reuse
- `docs/20-devices/distributed-task-scheduling.md` — peer selection and
  load-aware task assignment among multiple Full Peer devices, the
  multi-peer case `multi-device-architecture.md` had left as an
  unaddressed default
- `docs/05-ai/explainability.md` — `why_not_alternatives` field, so the
  explanation schema can answer "why was this provider/capability
  rejected," not only "why was this one chosen"
- `docs/16-extensibility/plugin-architecture.md` — Developer Validation
  Harness section, the missing third-party plugin-testing counterpart to
  the internal `docs/12-testing/` suite

### Confirmed already covered (no change)
- Self-improving intelligence (routing/tone/timing adaptation) —
  `adaptive-personalization.md`; capability acquisition —
  `self-growing-capability.md`
- Trust/approval policy — the existing risk-tier + confidence-propagation
  design already implements "high confidence → proceed, low confidence →
  stop"; it was inconsistently labeled (see Fixed above), not
  functionally incomplete
- Architecture observability — `explainability.md` + `audit.md` already
  answer "why this," "why not that" (after the addition above), "which
  plugin," and "what was retried"
- SDK & ecosystem — `docs/08-api/sdk.md`, `docs/08-api/versioning.md`,
  and the six `docs/16-extensibility/plugin-*.md` documents already
  specify versioning, compatibility, and lifecycle in more depth than
  the request assumed
- "NOVA as platform" framing — already `README.md`'s and `vision.md`'s
  opening statement, unchanged

## [1.0.0] — v5 architecture evolution: provider-agnostic, multi-device, multi-channel

Ratified by `docs/15-decisions/adr-0008-v5-architecture-evolution.md`,
which formally repeals or narrows the v1 non-goals blocking this
expansion (see `docs/00-overview/non-goals.md` v2 for the full
repealed/narrowed/standing breakdown).

### Added
- `docs/15-decisions/adr-0008-v5-architecture-evolution.md` — the ADR
  authorizing this entire release
- `docs/18-providers/` (new) — `provider-interface.md`,
  `capability-management.md`, `provider-routing.md`,
  `hardware-detection.md`, `local-model-management.md`,
  `cloud-provider-management.md`, `credential-management.md`,
  `mcp-server-management.md`
- `docs/19-setup/` (new) — `setup-wizard.md`, `configuration-system.md`
- `docs/20-devices/` (new) — `multi-device-architecture.md`,
  `cross-device-memory.md`, `android-companion.md`,
  `screen-streaming.md`, `remote-control.md`, `ai-phone.md`
- `docs/21-channels/` (new) — `messaging-platforms.md`,
  `email-assistant.md`, `calendar-assistant.md`, `phone-calls.md`
- `docs/22-voice/` (new) — `voice-assistant.md`,
  `local-speech-models.md`
- `docs/23-autonomy/` (new) — `autonomous-plugin-discovery.md`,
  `automatic-software-installation.md`, `self-growing-capability.md`,
  `personal-analytics.md`, `adaptive-personalization.md`,
  `background-life-assistant.md`
- `docs/24-collaboration/` (new) — `multi-agent-collaboration.md`,
  `browser-agent.md`
- `docs/06-tools/vision-everywhere.md` (new) — unifies desktop, phone,
  camera, and browser vision under one capability domain

### Changed
- `docs/00-overview/non-goals.md` — revised to v2; repeals
  single-platform, single-device, and no-multi-agent exclusions; narrows
  "not AI-first" and "not adaptive"; restates every still-standing
  exclusion against each new capability domain
- `docs/01-product/project-scope.md` — revised to v2 reflecting the same
  expansion
- `docs/00-overview/goals.md` — added Phase 5 goals (concrete, testable
  targets replacing the prior "deferred" placeholder)
- `docs/00-overview/vision.md` — identity statement extended from
  single-PC to multi-device/multi-channel; core loop unchanged
- `docs/00-overview/architecture-summary.md` — diagram and service table
  extended with the Provider Layer, Setup Wizard, Device Mesh, Channel
  Adapters, Voice Pipeline, and Multi-Agent Coordinator
- `ROADMAP.md` — Phase 5 changed from "explicitly deferred" to
  "documentation complete"; added Phase 6 (full-peer mobile runtime,
  background-assistant maturity)
- `README.md` — updated identity statement, structure table, status, and
  reading order for v5

## [0.6.0] — Gap-analysis pass 3: implementation-ambiguity closure

### Added
- `docs/00-overview/normative-precedence.md` — which document wins when
  two appear to conflict (invariants → ADRs → scope → architecture →
  component specs → wire schemas → diagrams/references).
- `docs/00-overview/end-to-end-walkthrough.md` — one complete request
  traced through every major component in sequence.
- `docs/00-overview/time-semantics.md` — UTC vs. monotonic clock usage,
  timezone handling, and why cross-machine clock skew is out of scope
  for v1.
- `docs/03-runtime/planner-executor-contract.md` — the strict wire
  schema for Planner→Executor→Verifier handoffs.
- `docs/03-runtime/job-scheduler.md` — recurring/cron/delayed background
  jobs, distinct from user-triggered task dispatch.
- `docs/02-architecture/event-bus-specification.md` — a consolidation
  index over already-specified event bus properties, plus new TTL
  handling.
- `docs/02-architecture/thread-concurrency-model.md` — per-component
  threading model (single-threaded, actor, worker-pool, event-driven,
  async).
- `docs/02-architecture/ipc-mechanisms.md` — which transport is used
  where, and explicit rationale for not using gRPC, Redis, or
  SQLite-as-queue.
- `docs/13-devops/storage-layout.md` — the literal on-disk directory
  structure.
- `docs/14-development/configuration-schema.md` — key-by-key settings
  reference, populated only with values already established elsewhere
  (no fabricated defaults).
- `docs/05-ai/model-context-assembly.md` — the exact, ordered structure
  of every LLM call's input.
- `docs/05-ai/model-routing-matrix.md` — routing rules as a table,
  structured around required capability rather than hardcoded vendor
  names (staying consistent with the provider-agnostic architecture).
- `docs/10-security/permission-escalation.md` — temporary, single-use
  elevated permission, distinct from standing grants.
- `docs/10-security/supply-chain-security.md` — SBOM, dependency
  verification, hash validation, trusted-publisher status for plugins.
- `docs/06-tools/error-codes.md` — a stable, Stripe-style error code
  reference.
- `docs/17-workflow/workflow-engine.md` — the BPMN-lite workflow model
  (branching, parallel execution, human-approval gates, rollback),
  queued across three prior passes.
- `docs/diagrams/navigation-map.md` — the UI sitemap.

### Changed
- `docs/02-architecture/lifecycle.md` — expanded the boot sequence with
  config/secrets/logging/telemetry initialization and explicit startup-
  timeout, partial-startup, and retry handling.
- `docs/03-runtime/service-lifecycle.md` — added the formal
  Created→Initializing→Healthy→Degraded→Restarting→Stopping→Stopped→
  Failed state machine.
- `docs/00-overview/ownership-boundaries.md` — added a resource
  ownership table (cache, world model, browser session, logs, telemetry,
  etc.), distinct from the existing responsibility-ownership table.
- `docs/07-observers/observer-framework.md` — added scheduling detail
  (event-based vs. polling per source, CPU limits) and a per-observer
  state machine.
- `docs/03-runtime/state-manager.md` — added a concrete worked example
  of cross-observer conflict resolution (filesystem/browser/clipboard
  disagreeing about the same file).
- `docs/06-tools/tool-interface.md` — expanded the capability schema
  (latency, cost class, deterministic flag, dependencies, input/output
  JSON Schema).
- `docs/05-ai/tool-selection.md` — formalized ranking as an explicit,
  deterministic, ordered criteria list.
- `docs/03-runtime/failure-recovery.md` — added the concrete tool retry
  matrix (failure category → default recovery action).
- `docs/05-ai/model-router.md` — added the AI failure recovery
  escalation chain (retry → alternate provider → smaller context →
  deterministic fallback → human confirmation).
- `docs/05-ai/context-builder.md` — made the compression algorithm
  concrete (eviction → chunk summarization → importance trimming →
  sliding window → hard floor on request/format).
- `docs/04-memory/knowledge-graph.md` — added edge-weight and
  inactive-node update rules.
- `docs/04-memory/memory-confidence.md` — added a qualitative confidence
  change model (increase/decrease/decay/expiration/override) —
  deliberately without invented numeric coefficients, which would be
  implementation-tuning parameters, not architecture.
- `docs/16-extensibility/plugin-lifecycle.md` — added Verify/Sandbox/Load
  as explicit, security-ordered sub-steps.
- `docs/14-development/release-checklist.md` — added the
  Dev→Nightly→Beta→Stable→LTS release channel model.
- `docs/13-devops/runbook.md` — added a concrete backup restore drill
  procedure with checksum validation.
- `docs/11-performance/benchmarks.md` — added AI-quality evaluation
  metrics (hallucination rate, tool selection accuracy, false positive
  rate) and concrete stress-testing scenarios.
- `docs/14-development/naming-conventions.md` — added UUID v7 as the ID
  generation strategy and the rationale for avoiding sequential IDs.

### Noted, not duplicated
- Memory GC, memory versioning, plugin API versioning, rollback/
  compensation, API versioning, golden tests, cross-references, glossary
  links, naming conventions, feature flags, the capability maturity
  matrix, and the performance-budget framework already existed prior to
  this pass.

### Deliberately scoped down (and why)
- **Full UI component library** — belongs to implementation-time design
  work informed by the design system, not invented speculatively in an
  architecture repository with no concrete screens to validate it
  against.
- **Reference pseudocode for all "top 20" flows** — the end-to-end
  walkthrough plus the Planner-Executor contract cover the highest-
  leverage version of this; pseudo-coding every flow blurs from
  architecture into implementation.
- **Retroactive "Implementation Notes" sections on all 200+ existing
  files** — the same non-retroactive-sweep policy already established
  for `docs/14-development/module-contract-standard.md` and
  `naming-conventions.md` applies here too.

## [0.5.0] — Gap-analysis pass 2: invariants, ownership, and consistency

### Added
- `docs/00-overview/system-invariants.md` — cross-cutting properties
  that must always hold (immutable IDs, unique event IDs, exactly-one
  current task state, acyclic Knowledge Graph edges, replayable
  checkpoints, and others), the foundation testing is checked against.
- `docs/00-overview/ownership-boundaries.md` — explicit single-owner
  assignment per responsibility, preventing overlap as the system grows.
- `docs/00-overview/assumptions.md` — explicit operating assumptions
  (LLMs fail, tools time out, networks are unreliable, users
  contradict themselves, APIs change), distinct from requirements.
- `docs/02-architecture/dependency-rules.md` — enforced layer dependency
  direction and forbidden interactions (UI never calls Planner directly,
  Tools never call Planner, Plugins never bypass permissions).
- `docs/14-development/configuration.md` — configuration scope
  precedence (CLI → Workspace → Project → User → Global → Default).
- `docs/04-memory/memory-garbage-collection.md` — storage reclamation
  and Knowledge Graph defragmentation.
- `docs/04-memory/memory-lineage.md` — derived-from/summarized-from/
  merged-from/split-from provenance tracking.
- `docs/05-ai/confidence-propagation.md` — weakest-link combination of
  memory, retrieval, and reasoning confidence.
- `docs/05-ai/explainability.md` — the standard why-this-plan/capability/
  model explanation schema.
- `docs/14-development/naming-conventions.md` — canonical ID, field,
  state, and topic naming rules.
- `docs/diagrams/entity-relationship.md` — one logical ER diagram
  spanning Users, Projects, Tasks, Capabilities, Plugins, and Memory.
- `docs/references/schema-index.md` — a pure cross-reference index to
  every canonical schema location (no duplicated schema content).
- `docs/13-devops/runbook.md`, `incident-response.md` — routine
  operational procedures and incident containment/review process.
- `docs/14-development/release-checklist.md`,
  `documentation-style-guide.md`, `anti-patterns.md` — release gating,
  writing/metadata conventions, and consolidated plugin/agent/memory
  anti-patterns.
- `docs/12-testing/chaos-tests.md` — a fifth testing layer: deliberate
  fault injection (process kill, message corruption, lock starvation,
  mid-flow permission denial, plugin crash, storage corruption).

### Changed
- `docs/00-overview/non-goals.md` — added explicit product-identity
  exclusions (not a database or Git replacement, no autonomous financial
  decisions, no unsandboxed code execution, no factual-correctness
  guarantee, no human-approval bypass).
- `docs/02-architecture/lifecycle.md` — **fixed a real inconsistency**:
  the startup sequence never included plugin discovery or capability
  registration after the extensibility system was added in the prior
  pass. Corrected.
- `docs/03-runtime/failure-recovery.md` — added a failure taxonomy
  (Transient/Permanent/User/External/Security/Validation/Internal).
- `docs/02-architecture/communication-model.md` — made the at-least-once
  (not exactly-once, not at-most-once) delivery guarantee explicit;
  added dead-letter queue handling.
- `docs/02-architecture/event-driven-architecture.md` — added priority
  inversion prevention (dedicated consumer path for high-priority
  topics).
- `docs/03-runtime/world-model.md` — substantially expanded: object
  hierarchy, spatial model (multi-monitor/z-order), temporal reasoning
  (rolling window), uncertainty (shared confidence model), causal
  attribution (NOVA-caused vs. user-caused, formalized), explicit
  simulation limits (it is a state tracker, not a predictive simulator),
  and a scoped "predicted outcome preview" as the right-sized version of
  "prediction" for a desktop context — deliberately *not* a general
  counterfactual-reasoning capability, which does not apply to this
  domain.
- `docs/03-runtime/planner.md` — added the formal Decompose → Retrieve →
  Generate candidates → Score → Validate → Execute → Monitor algorithm,
  with candidate generation explicitly bounded to step-level tool choice,
  not full speculative multi-plan search.
- `docs/12-testing/testing-strategy.md`, `validation.md` — added the
  chaos-testing layer and a component × test-layer coverage matrix.
- `docs/16-extensibility/plugin-versioning.md` — added SDK version
  declaration and a compatibility matrix.
- `docs/16-extensibility/plugin-permissions.md` — added permission
  negotiation (required vs. optional permission sets, graceful
  degradation).
- `docs/16-extensibility/plugin-lifecycle.md` — added a `Deprecated`
  state mirroring the feature-flags maturity model.

### Noted, not duplicated
- Non-Goals, ADR rationale (why graph memory, why capability registry,
  why plugins, why the Communication Bus over direct calls), and
  Backup/Restore/Disaster Recovery already existed prior to this pass
  and were amended rather than re-created.

## [0.4.0] — Gap-analysis pass: extensibility, versioning, and memory
improvements

### Added
- `docs/16-extensibility/` (7 files) — plugin architecture, lifecycle,
  permissions, versioning, dependency management, marketplace, and
  sandboxing. Plugins are treated as untrusted by default, identical to
  the existing MCP trust model.
- `docs/05-ai/capability-registry.md` — a named-capability abstraction
  above the Tool Registry so the Planner never hardcodes tool selection.
- `docs/05-ai/episodic-replay.md` — runtime reuse of prior successful
  task plans as a starting draft, without exempting any step from
  existing safety/verification requirements.
- `docs/05-ai/prompt-versioning.md` — full version/changelog
  specification for every prompt template.
- `docs/06-tools/tool-schema-versioning.md` — input/output contract
  versioning for tools, including breaking-change handling for in-flight
  tasks.
- `docs/04-memory/memory-versioning.md` — schema versioning and lazy
  migration for memory records, with a hard "never break older
  memories" requirement.
- `docs/04-memory/memory-confidence.md` — confidence, source, and
  verification-status metadata generalized across all memory, not only
  preferences.
- `docs/04-memory/memory-conflict-resolution.md` — merge rules for
  directly contradictory statements (e.g., a stated preference
  reversing), retaining superseded records as queryable history rather
  than deleting them.
- `docs/14-development/feature-flags.md` — Experimental/Beta/Stable/
  Deprecated/Removed maturity lifecycle for safe capability rollout.
- `docs/14-development/module-contract-standard.md` — the standard
  input/output/errors/timeout/permissions contract new modules should
  document explicitly.
- `docs/03-runtime/failure-recovery.md` — consolidated retries,
  rollback, compensation, checkpoints, crash resumption, partial
  completion, idempotency, and timeout strategy.
- `docs/15-decisions/adr-0007-extensibility.md` — the ADR ratifying the
  plugin system and capability registry.

### Changed
- **Task state machine reconciled** (`docs/03-runtime/task-manager.md`,
  `docs/diagrams/runtime.md`, `docs/08-api/schemas.md`,
  `docs/09-ui/task-monitor.md`): added `WaitingResources`, `Paused`,
  `WaitingUser`, and `Retrying` as explicit states. `Created` replaces
  `Queued` as the initial state. `Archived` is deliberately *not* added
  as a peer execution state — it is documented as the existing memory-
  lifecycle Archive transition applied to terminal task records, to
  avoid inventing a second, conflicting concept.
- `docs/02-architecture/event-driven-architecture.md` — added event
  priority and deduplication detail, rather than creating a separate,
  overlapping "event bus" document.
- `docs/04-memory/memory-lifecycle.md` — added explicit expiration
  policy tiers (never-expires, expires-after-weeks, short-window)
  replacing the prior implicit retention description.
- `docs/05-ai/ai-architecture.md`, `docs/06-tools/tool-registry.md` —
  cross-referenced to the new capability registry, episodic replay, and
  plugin system.
- `docs/02-architecture/architecture-decisions.md` — added ADR-0007 to
  the index.

## [0.3.0] — Documentation Tier 3: complete specification

### Added
- `docs/07-observers/` (10 files) — the shared observer framework and
  every individual source (filesystem, applications, windows, browser,
  clipboard, notifications, keyboard, mouse), including the firm,
  explicit boundary that keyboard/mouse observation is activity/hotkey
  detection only and never keystroke or movement logging.
- `docs/08-api/` (7 files) — internal API, public SDK, REST, WebSocket,
  external event/webhook subscriptions, schemas, and versioning policy.
- `docs/09-ui/` (10 files) — every UI surface (desktop, overlay, chat,
  command palette, tray, Memory Explorer, Graph Explorer, Task Monitor)
  sharing one backend, plus the design system.
- `docs/10-security/` (9 files) — the full security model: authentication,
  authorization, permissions, encryption, secrets, sandboxing, audit
  trail, and a consolidated threat model naming each threat, its
  mitigation, and its honest residual risk.
- `docs/11-performance/` (7 files) — concrete numeric performance
  targets (query latency, resource budgets, cost optimization),
  replacing the earlier vague framing entirely.
- `docs/12-testing/` (7 files) — the four-layer testing strategy (unit,
  integration, end-to-end, simulation) plus validation/acceptance
  criteria.
- `docs/13-devops/` (7 files) — deployment, installation, updates,
  backup, disaster recovery, logging, and self-monitoring.
- `docs/14-development/` (7 files) — coding standards, branching, the
  full architecture-rules enforcement document, implementation order,
  milestones, the PR-level module checklist, and technical debt tracking.
- `docs/15-decisions/` (7 files) — the six formal ADRs (project scope,
  memory, runtime, AI, tool system, security) plus the ADR template.
- `docs/diagrams/` (8 files) — standalone diagram references for system,
  runtime, memory, knowledge graph, execution, observers, services, and
  UI.
- `docs/references/` (5 files) — research context (naming which problems
  this project takes a position on rather than solves), inspirations,
  categorical comparisons, an external-terminology glossary, and a
  bibliography.

### Notable decisions locked in this tier
- Keyboard and mouse observation is permanently restricted to activity/
  idle signal and explicit hotkey/position reads — never keystroke
  content or movement history, under any permission level.
- A consolidated threat model explicitly states residual risk for each
  mitigation rather than presenting any mitigation as a complete
  solution.
- Concrete performance budgets: <100ms Knowledge Graph queries, <20ms
  lock acquisition, <2s simple commands, <5s reasoning-required
  responses, <3% idle CPU, <600MB idle RAM.
- A four-layer testing strategy with simulation testing (recorded real-
  task replay) as a first-class layer, specifically to address testing
  an agent whose environment is the user's own live desktop.
- Six ratified ADRs, each naming the alternatives considered and
  rejected, not only the decision made.

## [0.2.0] — Documentation Tier 2: system specification

### Added
- `docs/02-architecture/` (9 files) — system architecture, runtime
  architecture, service architecture, communication model, event-driven
  architecture, execution pipeline, lifecycle, dependency map,
  architecture decisions index.
- `docs/03-runtime/` (12 files) — runtime manager, scheduler, task
  manager, planner, executor, verifier, observer framework, world model,
  state manager, resource manager, permission manager, service lifecycle.
- `docs/04-memory/` (13 files) — memory architecture, memory types,
  memory lifecycle, memory storage, retrieval engine, indexing,
  embeddings, knowledge graph, ontology, entity resolution, timeline,
  search, memory ranking.
- `docs/05-ai/` (11 files) — AI architecture, model router, planner-agent
  runtime, reasoning engine, context builder, prompt system, tool
  selection, deterministic-first, ambiguity resolution, hallucination
  prevention, model providers.
- `docs/06-tools/` (11 files) — tool system, tool registry, tool
  interface, execution priority, and the full seven-tier execution
  detail (native runtime, MCP, CLI, API, accessibility, vision,
  keyboard/mouse automation).

### Architectural decisions locked in this tier
- Single-host-process, multi-service deployment topology with one
  Communication Bus (named-pipe IPC), not a monolith and not fully
  separate installed Windows services.
- Four-tier memory (Working/Recent/Long-term/Archive) plus a fixed-schema
  Knowledge Graph, on hybrid storage (SQLite/Postgres + vector DB + graph
  DB + blob storage), all encrypted at rest.
- Deterministic (non-LLM) Model Router; Deterministic Before Intelligent
  and the ambiguity-resolution decision flow formally specified with
  worked examples.
- One parameterized Planner-Agent runtime replacing separately
  implemented agent types.
- Ground-truth-first verification with "Unverified" as a first-class,
  non-success terminal state.
- Fixed eight-tier execution priority chain (Native Runtime → Internal
  Functions → API → MCP → CLI → Accessibility → Vision → Keyboard/Mouse),
  with Vision and Keyboard/Mouse restricted to an explicit application
  allow-list and gated by mandatory confirmation for destructive actions.
- Resource-lock manager (exclusive-write locks, batch acquisition to
  avoid deadlock) and per-agent-instance permission scoping enforced
  independently of risk tier.

## [0.1.0] — Documentation baseline (Tier 1)

### Added
- Repository scaffolding: `README.md`, `LICENSE` (MIT), `CONTRIBUTING.md`,
  `CODE_OF_CONDUCT.md`, `SECURITY.md`, `CHANGELOG.md`, `ROADMAP.md`,
  `FAQ.md`.
- `docs/00-overview/` — vision, goals, non-goals, design principles,
  architecture summary, terminology, glossary.
- `docs/01-product/` — product specification, user personas, use cases,
  user journeys, feature list, feature priority, project scope, success
  metrics.

### Architectural decisions locked in this baseline
- Target platform for v1: Windows, single machine, single user.
- Fully open source (MIT); users supply their own AI provider API keys or
  run local models — no bundled paid inference service.
- Core identity principle: Observe → Remember → Reason → Act → Verify.
- Five foundational design principles adopted (see
  `docs/00-overview/design-principles.md`), with **Deterministic Before
  Intelligent** treated as the primary filter governing all the others.
- v1 core scope fixed to seven services: Observer, Memory, Knowledge Graph,
  Planner, Executor, Verifier, Tool Registry. Everything else (multi-device
  sync, non-Windows platforms, fine-tuned personalization) is explicitly
  deferred — see `docs/00-overview/non-goals.md`.
- GUI/vision-based execution exists in v1 but sits last in the execution
  priority chain and is never the first method attempted.
