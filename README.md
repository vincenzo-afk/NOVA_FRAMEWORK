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

## How this repository is organized

| Path | Contents |
|---|---|
| `docs/00-overview/` | Vision, goals, non-goals, design principles, terminology |
| `docs/01-product/` | Product specification, personas, use cases, scope, success metrics |
| `docs/02-architecture/` | System, runtime, and service architecture |
| `docs/03-runtime/` | Core services: planner, executor, verifier, observer, world model |
| `docs/04-memory/` | Memory tiers, knowledge graph, retrieval, ranking (ontology v2 adds Person/Goal/Device as first-class graph entities) |
| `docs/05-ai/` | Model routing, reasoning, deterministic-first principle, ambiguity resolution |
| `docs/06-tools/` | Tool registry and execution-priority chain |
| `docs/07-observers/` | Per-source observation (filesystem, browser, clipboard, etc.) |
| `docs/08-api/` | Public SDK, REST, WebSocket, schemas |
| `docs/09-ui/` | Desktop app, overlay, chat, command palette |
| `docs/10-security/` | Security model, permissions, encryption, threat model |
| `docs/11-performance/` | Performance goals and budgets |
| `docs/12-testing/` | Testing strategy |
| `docs/13-devops/` | Deployment, backup, recovery, monitoring |
| `docs/14-development/` | Coding standards, implementation order, milestones |
| `docs/15-decisions/` | Architecture Decision Records (ADRs) |
| `docs/16-extensibility/` | Plugin/extension system: lifecycle, permissions, versioning, dependencies, marketplace, sandboxing |
| `docs/17-workflow/` | The workflow engine: branching, parallel execution, human-approval gates, rollback for tasks too complex for the linear planning loop |
| `docs/18-providers/` | Provider interface, capability management, routing, hardware detection, local/cloud model & credential management, MCP server management (v5) |
| `docs/19-setup/` | First-time setup wizard and the persistent configuration system it writes to (v5) |
| `docs/20-devices/` | Multi-device architecture, cross-device memory, Android companion, screen streaming, remote control, AI phone (v5), distributed task scheduling across multiple Full Peers |
| `docs/21-channels/` | Messaging platform adapters (Telegram, Discord, WhatsApp, extensible), email, calendar, phone calls (v5) |
| `docs/22-voice/` | Always-listening voice assistant, wake word, streaming/barge-in, local & cloud speech models (v5) |
| `docs/23-autonomy/` | Autonomous plugin/MCP discovery, automatic software installation, self-growing capability, strategy evaluation (comparison/promotion/retirement of recurring-task strategies), goal tracking (persistent multi-week objectives), personal analytics, adaptive personalization, background life assistant (v5) |
| `docs/24-collaboration/` | Multi-agent collaboration and the browser as a first-class reasoning surface (v5) |
| `docs/diagrams/` | Standalone Mermaid diagrams referenced across the repo |
| `docs/references/` | Research, inspirations, comparisons, bibliography |

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
`docs/00-overview/normative-precedence.md` and
`docs/15-decisions/adr-0008-v5-architecture-evolution.md` first if
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
