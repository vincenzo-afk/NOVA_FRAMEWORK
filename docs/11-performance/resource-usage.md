# Resource Usage

## Purpose

The detailed resource budget breakdown underlying the summary figures in
`performance-goals.md`, specifying how NOVA's CPU/RAM footprint is
allocated across services and enforced so the system never visibly
degrades the user's own foreground work.

## Scope

Resource budget allocation and enforcement. Summary targets are
`performance-goals.md`; scaling behavior as data grows is `scalability.md`.

## Idle budget allocation

At idle (no active task), the combined budget across all supervised
services is under 3% CPU and under 600MB RAM. Approximate allocation:
Observer services (event capture, minimal processing) — the largest
idle-time share, since they run continuously; Memory and Knowledge Graph
services — moderate, mostly serving occasional background indexing;
Runtime Manager, Scheduler, Task Manager, State Manager — minimal at
idle, since there is no active task to manage.

## Active-task budget

During active task execution, resource usage scales dynamically rather
than being capped at the idle figures — but per `performance-goals.md`,
NOVA must never monopolize the system. This is enforced by the Scheduler
(`docs/03-runtime/scheduler.md`) capping concurrent task execution and by
each service respecting OS-level process priority settings that keep
NOVA's foreground-visible impact secondary to the user's own actively
focused application.

## Background job budgeting

Indexing, embedding generation, and summarization
(`docs/04-memory/indexing.md`, `memory-lifecycle.md`) run at low OS
process priority and are explicitly designed to yield to foreground user
activity — detected via the World Model's activity signal
(`docs/03-runtime/world-model.md`) — rather than competing for CPU at
equal priority with whatever the user is actively doing.

## Self-monitoring and threshold alerting

The Runtime Manager (`docs/03-runtime/runtime-manager.md`) tracks actual
resource usage against these budgets continuously and surfaces a
degraded-status signal to the Tray UI (`docs/09-ui/tray.md`) if any
service persistently exceeds its allocation — this is the practical
enforcement mechanism, not merely a target stated in documentation with
no operational check behind it.

## Storage growth budget

While CPU/RAM are bounded as above, storage growth (`docs/04-memory/memory-storage.md`) is not artificially capped, since retention is a
user-controlled choice (`docs/04-memory/timeline.md`) rather than a
performance constraint — storage usage is surfaced to the user (via the
Memory Explorer, `docs/09-ui/memory-explorer.md`) so they can make an
informed retention decision rather than being silently capped without
visibility.

## Related documents

- `performance-goals.md` — the summary targets this document details
- `docs/03-runtime/scheduler.md`, `runtime-manager.md` — the enforcement
  mechanisms
- `docs/04-memory/memory-lifecycle.md` — background job scheduling this
  budget constrains
