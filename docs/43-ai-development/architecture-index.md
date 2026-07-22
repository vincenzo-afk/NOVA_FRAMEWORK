# Architecture Index for Code Generation


## Purpose

A single lookup table mapping "I am implementing X" to the doc(s) that
define its contract, so an AI agent never has to guess an interface shape.

| If you are implementing... | Read first | Then read |
|---|---|---|
| Any new tool | `06-tools/tool-registry.md` (execution-priority chain) | `10-security/permissions.md` |
| Any LLM call site | `05-ai/model-router.md` | `05-ai/deterministic-first.md`, `context-builder.md` |
| Any memory write | `04-memory/memory-storage.md` | `memory-versioning.md`, `memory-lineage.md` |
| Any memory read/query | `04-memory/retrieval-engine.md` | `memory-ranking.md`, `memory-confidence.md` |
| A new observer | `07-observers/` (pick the closest existing observer as template) | `03-runtime/observer.md` |
| A new UI screen | `09-ui/ui-overview.md` | `40-screens/` entry for that screen, `30-design/design-system.md` |
| A new plugin capability | `16-extensibility/plugin-architecture.md` | `plugin-sandboxing.md`, `plugin-permissions.md` |
| A new workflow node type | `17-workflow/workflow-engine.md` | `03-runtime/planner-executor-contract.md` |
| A new provider integration | `18-providers/provider-interface.md` | `capability-management.md`, `credential-management.md` |
| Any cross-device feature | `28-multi-device-protocol/` | `20-devices/multi-device-architecture.md` |
| Any voice feature | `22-voice/voice-assistant.md` | `local-speech-models.md` |
| Any autonomy/background feature | `23-autonomy/` (find closest matching doc) | `10-security/permission-escalation.md` |
| Any analytics/telemetry event | `35-analytics/events.md` | `10-security/privacy.md` (if exists) or `29-product/privacy.md` |
| Any error you plan to raise | `26-system-reference/06-error-catalog.md` | add the error there before using it |
| Any new state machine | `26-system-reference/04-state-transition-tables.md` | add the transition table before coding |

## Rule for adding new interfaces

If the table above has no row for what you're building, that is a signal
the architecture doc is missing, not permission to invent an ad-hoc
interface. Add the doc first (or extend the nearest existing one), get it
reviewed against `44-review-checklist.md`, then write code against it.
