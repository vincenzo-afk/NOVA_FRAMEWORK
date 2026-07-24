# Lifecycle & State Machine Index

## Purpose

A single index of every object in NOVA that has (or should have) a
defined lifecycle and state machine, per Sections 3 and 4 of the master
documentation outline. Most of these already exist in their owning
document; this file's job is to make sure none were skipped and to give
each one a canonical one-line summary and diagram reference in one
place.

## Scope

Indexes lifecycle/state-machine documentation across the repository.
Does not restate full transition tables — those remain in
`docs/26-system-reference/04-state-transition-tables.md` and each
object's owning document.

## System-wide lifecycle

- **Startup/shutdown sequence:** `docs/02-architecture/lifecycle.md`
- **Per-service lifecycle contract:** `docs/03-runtime/service-lifecycle.md`
  (Created → Initialized → Running → Paused → Cancelled → Recovering →
  Destroyed, implemented uniformly by every supervised service)

## Object lifecycles and state machines

| Object | Lifecycle / states | Canonical document |
|---|---|---|
| Task | Idle → Thinking → Planning → Executing → Waiting → Verifying → Unverified → Completed/Failed/Cancelled (see `04-state-transition-tables.md` for the full guarded transition table — this is the canonical version; `docs/03-runtime/task-manager.md` must match it) | `04-state-transition-tables.md` |
| Workspace | Created → Initialized → Ready → Running → Completed → Archived | `docs/28-multi-device-protocol/10-identity-and-workspace.md` |
| Agent (Planner/Executor/Verifier instance) | Idle → Assigned → Active → Blocked → Complete/Aborted | `docs/03-runtime/runtime-manager.md` |
| Plugin | Discovered → Installed → Loaded → Running → Suspended → Unloaded → Removed | `docs/16-extensibility/plugin-lifecycle.md` |
| Session | Started → Active → Idle → Resumed/Expired → Ended | `docs/28-multi-device-protocol/03-session-continuity-and-handoff.md` |
| Checkpoint | Created → Valid → Superseded → (never mutated in place) | `docs/03-runtime/failure-recovery.md`, `system-invariants.md` |
| Memory Entry | Created → Indexed → Reinforced/Superseded → Archived/Deleted | `docs/04-memory/memory-lifecycle.md` |
| Permission Request | Requested → Pending → Approved/Denied → Expired | `docs/10-security/permissions.md` |
| Event | Published → Delivered → Acknowledged/Retried → Dead-lettered | `docs/26-system-reference/07-event-catalog.md` |
| Device (multi-device) | Discovered → Pairing → Paired → Active/Offline → Unpaired | `docs/28-multi-device-protocol/02-device-pairing-protocol.md` |

## Objects without a previously dedicated state machine

The following were implicitly covered by their owning document's prose
but lacked an explicit state list; captured here for completeness and
should be promoted into their owning document on next revision:

- **Permission Request** — states above were reconstructed from
  `docs/10-security/permissions.md`'s request/response flow.
- **Session** — states above were reconstructed from
  `docs/28-multi-device-protocol/03-session-continuity-and-handoff.md`.

## Rule for new objects

Any new long-lived object added to the system (per
`docs/26-system-reference/14-data-models.md`) must have its state
machine added to this index in the same change, per the module checklist
(`docs/14-development/module-checklist.md`) — an entity with lifecycle
implications but no state machine is treated as an incomplete spec, not
a minor gap, since state machines are exactly what AI implementers
translate into code most reliably when given one, and most unreliably
when left to infer one.
