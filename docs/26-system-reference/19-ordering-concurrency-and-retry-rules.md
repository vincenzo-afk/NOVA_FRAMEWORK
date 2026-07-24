# Ordering, Concurrency, Retry, Timeout & Resource Limit Rules

## Purpose

Concrete, numeric operational rules for the five closely related
concerns in Sections 11 through 15 of the master documentation outline.
Where other documents describe *that* something is retried, ordered, or
bounded, this document states the actual policy — so an implementer
does not have to invent a backoff curve or a timeout value.

## Scope

System-wide defaults. A subsystem may declare a documented, justified
override in its own contract; where it doesn't, these defaults apply.

## Ordering guarantees

The canonical mutation order for a single task's lifecycle is fixed and
never reordered:

```
Observation → Reflection → Memory Update → Checkpoint → Notification
```

Within this chain, each stage is only invoked after the previous stage's
event is durably recorded (`persistence.md`) — a Checkpoint is never
written before the corresponding Memory Update event exists, because a
component recovering from a checkpoint must be able to trust that
everything the checkpoint implies already happened. Across *different*
tasks in the same workspace, no ordering guarantee is made or needed —
concurrent tasks are independent unless they declare a shared resource
lock (see Concurrency Rules).

## Concurrency rules

- **Can run simultaneously by default:** Planner, Verifier, Indexer,
  Search, and Memory reads — none of these hold an exclusive lock on
  shared mutable state.
- **Serialized by default:** Executor steps that declare overlapping
  `required_locks` (`docs/03-runtime/resource-manager.md`) — two steps
  that touch the same file or the same external resource never execute
  concurrently.
- **Plugins:** run concurrently with each other and with core
  subsystems by default, each in its own sandbox; a plugin never blocks
  a core subsystem's progress (isolation guarantee,
  `docs/16-extensibility/plugin-sandboxing.md`).
- **Lock acquisition:** advisory locks held by Resource Manager, one
  holder at a time per resource identifier; a second requester blocks
  (with a timeout, see below) rather than proceeding unsynchronized.
- **Race prevention:** every mutation to a shared entity goes through
  its owning component's API (`constraints.md`) — there is no direct
  shared-memory access between components, which is what makes most
  races structurally impossible rather than merely unlikely.

## Retry policies

Default policy for any retryable operation, unless its own contract
states otherwise:

- **Max retries:** 3.
- **Backoff:** exponential, base 500ms, multiplier 2x (500ms, 1s, 2s).
- **Jitter:** ±20% randomized, to avoid synchronized retry storms across
  a workspace's components.
- **Timeout per attempt:** see Timeouts table below, by operation class.
- **Circuit breaker:** after 5 consecutive failures to the same
  external dependency (a provider, a plugin, a remote service), the
  circuit opens for 60 seconds — further calls fail fast rather than
  queuing, and the dependency is marked degraded
  (`docs/18-providers/provider-interface.md`'s `status` field).
- **Non-idempotent operations:** never auto-retried by the system; the
  caller must supply an idempotency key (see
  `17-event-and-internal-api-contracts.md`) or explicitly re-confirm.

## Timeouts (default per operation class)

| Operation class | Default timeout |
|---|---|
| Internal API call (e.g., `createTask()`) | 5s |
| Planning (single planning pass) | 30s |
| Tool execution, native/internal | 15s |
| Tool execution, external API/MCP | 30s |
| Tool execution, CLI subprocess | 60s |
| Memory retrieval query | 2s |
| Indexing (single file) | 5s |
| Plugin capability call | 10s |
| Model/provider inference call | 60s (streaming: 60s to first token) |

A timeout is always a specific, configured value per operation class —
never an implicit "however long it takes." An operation with no
documented timeout is treated as a specification gap to fix, not as
"unbounded by design."

## Resource limits (default per workspace)

| Resource | Default limit |
|---|---|
| Memory (process working set) | Per `docs/39-performance-budgets/memory-usage.md` |
| Disk (workspace cache + index) | Per `docs/39-performance-budgets/budgets.md` |
| Concurrent threads (background work) | Per `docs/39-performance-budgets/cpu.md` |
| Concurrent network requests | 10 per provider, system-wide cap per `docs/39-performance-budgets/budgets.md` |
| Tokens (per single model call) | Per `docs/05-ai/model-routing-matrix.md`'s per-model context limits |
| Open files (Observer/Indexer) | OS default minus a reserved headroom, per `docs/07-observers/observer-framework.md` |
| Concurrent contexts / workspaces loaded | Per `docs/39-performance-budgets/memory-usage.md` |

Exceeding any limit above triggers graceful degradation (queue, reject
new work, or shed the lowest-priority task) — never an unbounded
allocation and never a hard crash; see
`18-failure-and-recovery-contracts.md`.

## Maintenance rule

Any operation introduced without an entry in the Timeouts or Resource
Limits tables above is treated as unspecified and must be given a
default here before being considered implementation-ready.
