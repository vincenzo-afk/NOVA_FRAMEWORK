<p align="center">
  <img src="docs/assets/nova-logo.png" alt="NOVA logo" width="160"/>
</p>

# Roadmap

This roadmap governs both the software build order and, at present, the
documentation build order. Nothing in a later phase should be implemented
before the phases before it are working and trusted — this is a hard rule,
not a suggestion, because every later phase's design assumes the safety and
verification scaffolding from earlier phases already exists.

## Phase 0 — Scoping and safety framework
**Status:** Complete (this documentation baseline).
Target OS, risk-tiering model, fixed core knowledge-graph schema, and
consent/retention policy are settled. See `docs/01-product/project-scope.md`
and `docs/15-decisions/adr-0001-project-scope.md` (Tier 3).

## Phase 1 — Passive observation and structured memory, zero execution
**Status:** Documentation pending (Tier 2/3).
Deliverable: file/app/window observers, event capture, working/recent/
long-term memory tiers, the fixed-schema knowledge graph, and a retrieval/
Q&A interface with no write capability at all.
Why first: validates that persistent personal context is actually useful
before any autonomy is introduced.

## Phase 2 — Narrow, whitelisted execution via safe channels only
**Status:** Documentation pending.
Deliverable: a small set of hand-built tool integrations using native APIs,
CLI, or MCP only — no GUI or vision control — with full audit logging and
an undo mechanism for every write action.

## Phase 3 — Planning/agent loop generalization
**Status:** Documentation pending.
Deliverable: the hierarchical iterative planner chaining Phase 2's
whitelisted tools for multi-step tasks, with per-step structured
verification and the resource-lock manager for overlapping actions.

## Phase 4 — GUI/vision-based control, narrowly scoped
**Status:** Documentation pending.
Deliverable: keyboard/mouse/vision control for an explicit, short list of
supported applications, with accessibility-tree control preferred over
pixel-vision wherever available, and mandatory confirmation for first-time
or destructive actions.

## Phase 5 — Provider-agnostic, multi-device, multi-channel evolution (v5)
**Status:** Documentation complete; ratified by
`docs/15-decisions/adr-0008-v5-architecture-evolution.md`. This phase was
previously deferred (see `docs/00-overview/non-goals.md` v1) and is now
in scope, superseding that deferral.
Deliverable: the Provider/Capability layer (`docs/18-providers/`), the
Setup Wizard (`docs/19-setup/`), multi-device support including the
Android companion (`docs/20-devices/`), messaging/email/calendar
channels (`docs/21-channels/`), the voice pipeline
(`docs/22-voice/`), autonomous capability growth
(`docs/23-autonomy/`), and multi-agent collaboration
(`docs/24-collaboration/`) — all built on the Phase 1-4 foundation
without discarding its safety and verification scaffolding.
Sequencing within Phase 5 still follows the hard rule above: the
Provider/Capability layer (5a) lands before anything that depends on it
(voice, channels, devices — 5b), which lands before autonomous capability
growth and multi-agent orchestration (5c), since 5c's proposal/approval
flow assumes the Capability Registry and permission-escalation gates from
5a/5b already exist.

## Phase 6 — Full-peer mobile runtime and background life assistant maturity
**Status:** Scoped, not yet implemented. See `docs/20-devices/ai-phone.md`
and `docs/23-autonomy/background-life-assistant.md`.
Deliverable, when undertaken: the phone graduating from Companion mode to
a Full-peer runtime where hardware supports it
(`docs/20-devices/multi-device-architecture.md`), and the background
assistant's proactive briefing maturing from scheduled digests into
adaptively-timed, cross-channel daily preparation
(`docs/23-autonomy/adaptive-personalization.md`).

## Documentation delivery tracking

| Tier | Contents | Status |
|---|---|---|
| Tier 1 | Root files, `00-overview`, `01-product` (23 files) | Complete |
| Tier 2 | `02-architecture` through `06-tools` (56 files) | Complete |
| Tier 3 | `07-observers` through `references` (84 files) | Complete |
| Gap-analysis pass 1 | Extensibility, capability registry, versioning strategy, memory improvements | Complete |
| Gap-analysis pass 2 | System invariants, ownership boundaries, assumptions, dependency/layer rules, configuration precedence, failure taxonomy, expanded World Model/Planner, chaos testing, operational docs | Complete |
| Gap-analysis pass 3 | Normative precedence, end-to-end walkthrough, Planner-Executor contract, boot sequence detail and service state machine, event bus/IPC/thread-model consolidation, job scheduler, storage layout, configuration schema, workflow engine, model-context-assembly ordering, model routing matrix, tool capability/retry detail, error codes, permission escalation, supply chain security, time semantics, ID generation strategy, navigation map | Complete |
| v5 architecture evolution (`docs/15-decisions/adr-0008-v5-architecture-evolution.md`) | Provider interface/routing/hardware detection/local & cloud model & credential/MCP management (`18-providers`), setup wizard/configuration system (`19-setup`), multi-device/cross-device memory/Android companion/screen streaming/remote control/AI phone (`20-devices`), messaging/email/calendar/phone-call channels (`21-channels`), voice assistant/local speech models (`22-voice`), autonomous plugin discovery/automatic install/self-growing capability/personal analytics/adaptive personalization/background life assistant (`23-autonomy`), multi-agent collaboration/browser-agent/vision-everywhere (`24-collaboration`, `06-tools`) | Complete |

**The core specification and three gap-analysis passes are complete,
internally consistent, and cross-referenced.** Each pass was reconciled
against existing content — where a request already existed (Non-Goals,
most ADR rationale, Memory GC, Backup/Restore/Disaster Recovery, API
versioning, feature flags) it was noted and amended rather than
duplicated; where a request would have created a second, conflicting
specification (a second task state machine, a second event bus document)
the existing one was extended instead.

## A note on further gap-analysis

Three passes in, most newly identified gaps are refinements to an
already-complete architecture, not missing foundations. Continuing to
run further rounds of pure paper gap-analysis without implementation
feedback risks becoming the same unbounded-scope problem this project's
very first review warned about — just relocated from features into
documentation exhaustiveness. The higher-leverage next step is
implementation, per `docs/14-development/implementation-order.md`,
which will surface the specific gaps that actually matter in practice
rather than every gap that is merely conceivable in the abstract.

## What comes next

Implementation, following `docs/14-development/implementation-order.md`
and tracked against `docs/14-development/milestones.md`, starting with
Phase 1 (passive observation and memory, zero execution).

See `CHANGELOG.md` for the exact file-level breakdown of each tier.
