<p align="center">
  <img src="docs/assets/nova-logo.png" alt="NOVA logo" width="160"/>
</p>

# NOVA — Personal AI Runtime

> Observe → Remember → Reason → Act → Verify

NOVA is a persistent, provider-agnostic AI runtime that lives across the
user's devices — desktop and Android — and the channels the user already
communicates through (voice, Telegram, Discord, WhatsApp, email,
calendar), continuously builds a structured understanding of the user's
digital life, and performs actions on the user's behalf using the safest
available execution method — preferring deterministic computation over AI
reasoning, and a local/private provider over a cloud one, whenever
possible and configured.

NOVA is not a chatbot, not a generic automation tool, and not a coding
assistant. It is the orchestration layer that sits above all of those things
and above the individual applications the user already runs — and, as of
the v5 architecture evolution
(`docs/15-decisions/adr-0008-v5-architecture-evolution.md`), above every
device and channel the user connects it to, through a provider-agnostic
interface that lets any capability be served by a local model or a cloud
API interchangeably.

This repository is documentation-only. It is the single source of truth for
NOVA's architecture, decisions, and specifications. No source code lives
here.

## Before writing any code

If you are an AI agent (or a human) about to implement any part of NOVA,
read these in this order:

0. **`docs/00-implementation-governance/`** — the entire folder,
   starting with `ai-constitution.md`. This is the highest-precedence
   rule set in this repository: the Constitution, the Decision Authority
   Matrix, allowed/forbidden decisions, the technology and architecture
   locks, the ambiguity policy, code-generation rules, documentation
   precedence, canonical patterns, definition of done, quality gates,
   project constraints, and the implementation checklist. Every other
   document defers to it.
1. `docs/43-ai-development/implementation-order.md` — what to build, and
   in what sequence, so nothing is built against an interface that
   doesn't exist yet.
2. `docs/45-code-perfection-failure-modes/INDEX.md` — the concrete,
   subsystem-by-subsystem landmines that make generated code *look*
   correct while being subtly wrong. Read the file for the subsystem
   your task touches before writing code, not after something breaks.
3. `docs/26-system-reference/21-canonical-doc-index.md` — before citing
   or relying on any document for a cross-cutting concept (contracts,
   state machines, permissions, versioning, etc.), check this index to
   confirm you're reading the canonical source and not a summary or a
   stale duplicate.

## How this repository is organized

| Path | Contents |
|---|---|
| `docs/00-implementation-governance/` | **Read this folder first, always.** The Constitution, Decision Authority Matrix, allowed/forbidden decisions, technology and architecture locks, ambiguity policy, code-generation rules, documentation precedence, canonical patterns, definition of done, quality gates, project constraints, implementation checklist |
| `docs/00-overview/` | Vision, goals, non-goals, design principles, engineering principles, AI implementation philosophy, success criteria, constraints, terminology |
| `docs/01-product/` | Product specification, personas, use cases, scope, success metrics |
| `docs/02-architecture/` | System, runtime, and service architecture |
| `docs/03-runtime/` | Core services: planner, executor, verifier, observer, world model |
| `docs/04-memory/` | Memory tiers, knowledge graph, retrieval, ranking (ontology v2 adds Person/Goal/Device as first-class graph entities) |
| `docs/05-ai/` | Model routing, reasoning, deterministic-first principle, ambiguity resolution, decision/confidence contracts, verification pipeline and stop conditions, escalation rules |
| `docs/06-tools/` | Tool registry and execution-priority chain |
| `docs/07-observers/` | Per-source observation (filesystem, browser, clipboard, etc.) |
| `docs/08-api/` | Public SDK, REST, WebSocket, schemas |
| `docs/09-ui/` | Desktop app, overlay, chat, command palette |
| `docs/10-security/` | Security model, permissions, encryption, threat model |
| `docs/11-performance/` | Performance goals and budgets |
| `docs/12-testing/` | Testing strategy |
| `docs/13-devops/` | Deployment, backup, recovery, monitoring, consolidated persistence spec (storage/caching/sync/conflict resolution/transactions/migration/backup/restore) |
| `docs/14-development/` | Coding standards, implementation order, milestones, locked technology stack, library/pattern/communication/state-management rules, error handling/tagging/performance rules |
| `docs/15-decisions/` | Architecture Decision Records (ADRs) |
| `docs/16-extensibility/` | Plugin/extension system: lifecycle, permissions, versioning, dependencies, marketplace, sandboxing, extension points (what's customizable/fixed/forbidden) |
| `docs/17-workflow/` | The workflow engine: branching, parallel execution, human-approval gates, rollback for tasks too complex for the linear planning loop |
| `docs/18-providers/` | Provider interface, capability management, routing, hardware detection, local/cloud model & credential management, MCP server management (v5) |
| `docs/19-setup/` | First-time setup wizard and the persistent configuration system it writes to (v5) |
| `docs/20-devices/` | Multi-device architecture, cross-device memory, Android companion, screen streaming, remote control, AI phone (v5), distributed task scheduling across multiple Full Peers |
| `docs/21-channels/` | Messaging platform adapters (Telegram, Discord, WhatsApp, extensible), email, calendar, phone calls (v5) |
| `docs/22-voice/` | Always-listening voice assistant, wake word, streaming/barge-in, local & cloud speech models (v5) |
| `docs/23-autonomy/` | Autonomous plugin/MCP discovery, automatic software installation, self-growing capability, strategy evaluation (comparison/promotion/retirement of recurring-task strategies), goal tracking (persistent multi-week objectives), personal analytics, adaptive personalization, background life assistant (v5) |
| `docs/24-collaboration/` | Multi-agent collaboration and the browser as a first-class reasoning surface (v5) |
| `docs/25-failure-modes/` | Project-wide failure-mode catalog: every way each subsystem can break, with trigger conditions, detection methods, severity, mitigation, and recovery procedures. Read the relevant `FM-NN-*.md` file before implementing any subsystem — start at `docs/25-failure-modes/INDEX.md` |
| `docs/26-system-reference/` | Build-order dependency tree, startup/shutdown sequences, state transition tables, data ownership map, error catalog, event catalog, configuration reference, version compatibility matrix, feature maturity table, sequence diagrams, cross-cutting data models catalog, consolidated build contracts / lifecycle & state-machine index / event & internal-API contract matrix / failure & recovery contracts / ordering, concurrency & retry rules / versioning contracts, and the documentation-lint/CI process that keeps all of it honest — every file also documents how *it itself* can drift from reality (see `docs/25-failure-modes/FM-24-documentation-and-reference-integrity.md`) |
| `docs/27-cli/` | The `nova` developer CLI: bootstrap (`init`/`doctor`/`diagnostics`/`upgrade`/`repair`), dev infrastructure (devcontainer/Nix/Docker/one-line installers/`env`/`config`), AI developer tools (`context`/`task`/`impact`/`docs`/`graph`), observability commands, plugin & AI SDKs, and the hidden-gold commands (`sandbox`/`benchmark`/`explain`/`migrate`/`report`/`verify`/`clean`) plus the full CI/CD check pipeline — see `docs/25-failure-modes/FM-25-cli-infrastructure.md` |
| `docs/28-multi-device-protocol/` | Cross-device sync, device pairing, session continuity/handoff, presence & capability negotiation, networking/discovery, global state & sync timing, cross-device permissions/notifications, file transfer & media streaming, config/secrets/plugin distribution, identity & workspace, recovery & backup, cross-subsystem lifecycle patterns, resource arbitration & offline mode, time & version compatibility, migration, and operational extras — see `docs/25-failure-modes/FM-26-multi-device-protocol.md` |
| `docs/diagrams/` | Standalone Mermaid diagrams referenced across the repo |
| `docs/references/` | Research, inspirations, comparisons, bibliography |
| `docs/29-product/` | Product specification layer: vision, principles, personas, JTBD, feature catalog/priorities/flags, onboarding, accessibility, localization, settings, privacy, licensing, roadmap |
| `docs/30-design/` | Design system: principles, tokens, typography, spacing, color, motion, dark mode, interaction/feedback patterns, navigation, branding |
| `docs/31-user-flows/` | Step-by-step flows for chat, voice, memory, plugins, pairing, workflows, tool execution, provider selection, recovery, onboarding, updates, auth, workspaces |
| `docs/40-screens/` | One spec per screen — layout, components, interactions, and every required state (loading/empty/error/offline/permission/partial) |
| `docs/41-components/` | Shared UI component specs (button, card, list, modal, prompt box, memory/workflow/plugin/provider cards, etc.) |
| `docs/42-design-qa/` | Visual regression, accessibility, responsive, spacing, animation, and typography QA checklists |
| `docs/35-analytics/` | Event taxonomy, funnels, north-star/guardrail metrics, retention definition, privacy-safe telemetry rules |
| `docs/36-failure-catalog/` | Categorized quick-reference failure lists per subsystem |
| `docs/37-edge-cases/` | Concrete edge-case scenarios (power loss, disk full, clock skew, sync/file conflicts, corruption, empty/oversized repos, symlink loops, invalid plugins, conflicting instructions), indexed in `00-index.md`, each requiring a test |
| `docs/38-disaster-recovery/` | Complete/partial recovery, backup, restore, rollback, migration, crash & state recovery |
| `docs/39-performance-budgets/` | Numeric latency/resource budgets for chat, memory, startup, shutdown, CPU, GPU, memory, battery |
| `docs/46-ai-evaluation/` | Benchmarks for reasoning, tool use, memory recall, planning, workflows, providers, hallucination, grounding, and safety |
| `docs/47-runbooks/` | Symptom → detection → logs → root cause → recovery → escalation runbooks for the most likely on-call scenarios |
| `docs/48-incident-response/` | Incident lifecycle, severity levels, communication style, postmortem template, root-cause-analysis method |
| `docs/43-ai-development/` | **Start here before generating any code.** Implementation order, coding guidelines, architecture index, dependency map, task/context generation, acceptance criteria, definition of done, coding + review checklists, common AI-agent pitfalls, AI decision authority (what AI may/must never decide, ambiguity policy, decision authority matrix) |
| `docs/44-product-design-failure-cases/` | Product-level failure scenarios and the full required-state checklist every screen must implement |
| `docs/45-code-perfection-failure-modes/` | **The most important directory for correctness.** Code-generation-time landmines (not runtime failures) per subsystem: memory/state, planner/executor/verifier, model router/providers, async/concurrency, tool execution/permissions, workflow engine, plugins/sandboxing, multi-device/sync, UI/state binding, data validation/schemas, error handling/logging, testing blind spots |

**Status:** Core v1 specification (three gap-analysis passes) plus the
**v5 architecture evolution** are complete (see `CHANGELOG.md` for the
full list per pass). v5, ratified by
`docs/15-decisions/adr-0008-v5-architecture-evolution.md`, expands NOVA
from a single-machine deterministic assistant into a provider-agnostic,
multi-device, multi-channel AI runtime: a full Provider/Capability layer,
a first-time setup wizard covering every capability, multi-device sync
and an Android companion, messaging/email/calendar channel assistants, an
always-listening voice interface, autonomous plugin/software discovery
bounded by explicit confirmation, and multi-agent collaboration — all
built on the original safety, sandboxing, and confirmation-gate
scaffolding rather than replacing it.

At this point, most newly reported gaps are refinements to an already-
complete architecture rather than missing foundations — read
`docs/00-overview/normative-precedence.md` and `docs/15-decisions/adr-0008-v5-architecture-evolution.md` first if
something still seems ambiguous or contradictory; a genuine remaining gap
is likely better found through actual implementation (per
`docs/14-development/implementation-order.md`) than further paper
review, since implementation surfaces the specific ambiguities that
matter in practice rather than the ones that are merely conceivable.

## Reading order for a new contributor or implementing engineer

1. `docs/00-overview/vision.md` — what NOVA is and why it exists
2. `docs/00-overview/non-goals.md` — what NOVA deliberately does not do
   (v2 — read alongside ADR-0008 to see what changed from v1)
3. `docs/01-product/project-scope.md` — the authoritative v2 boundary
4. `docs/00-overview/design-principles.md` — the five principles every
   architectural decision must satisfy
5. `docs/02-architecture/system-architecture.md` — the system as a whole
6. `docs/18-providers/provider-interface.md` — the common abstraction
   every v5 capability is built on

## License

MIT. See `LICENSE`. NOVA is fully open source; users provide their own AI
provider API keys or run local models — there is no bundled paid service and
no vendor lock-in.

## Contributing

See `CONTRIBUTING.md`. Security disclosures follow `SECURITY.md`, not the
public issue tracker.
