# Coding Guidelines for NOVA


## Purpose

Concrete, checkable coding rules derived from NOVA's design principles
(`docs/00-overview/design-principles.md`). These are stricter than a
generic style guide because NOVA's failure modes are unusually expensive:
it holds long-lived private user data and takes real-world actions.

## Non-negotiable rules

1. **Every function that can fail must return a typed result, never throw
   past its own module boundary uncaught.** NOVA is a long-running
   background process — an uncaught exception in one observer must not
   crash the runtime. Wrap all observer/tool/plugin entry points in a
   supervised boundary (see `docs/03-runtime/failure-recovery.md`).
2. **No function calls an LLM without going through the Model Router.**
   Direct provider SDK calls anywhere outside `docs/05-ai/model-router.md`'s
   implementation are a defect, full stop — they bypass deterministic-first
   evaluation, cost tracking, and fallback.
3. **No write to memory tiers without going through the tier's documented
   write path.** Direct writes to the Knowledge Graph store or vector
   index outside `memory-storage.md`'s API break versioning
   (`memory-versioning.md`) and lineage (`memory-lineage.md`) invariants.
4. **Every tool execution must be checked against the permission model
   before invocation, not after.** `permission-manager.md` is a gate, not
   an audit log. Code that executes-then-checks is a security defect.
5. **Timestamps are always stored and compared in UTC**; conversion to
   the user's local time is a presentation-layer concern only (see
   `docs/00-overview/time-semantics.md`). Comparing a stored UTC timestamp to
   `Date.now()` in local time is a recurring, real bug class in this kind
   of system — treat any naive `Date`/`datetime` construction as a code
   smell.
6. **IDs are never reused, never re-derived from mutable fields.** Every
   entity (memory, task, workflow node, device) gets a ULID/UUID at
   creation; never key anything by a title, filename, or timestamp that
   could collide or change.
7. **All cross-process and cross-device messages are versioned and
   schema-validated on receipt**, per `28-multi-device-protocol/` and `docs/08-api/schemas.md`. Never trust a payload shape because "we control
   both ends" — versions will drift the moment one device updates before
   another.
8. **Idempotency is required for anything that can be retried** — task
   execution, sync operations, workflow steps. If a step has side effects,
   it must carry an idempotency key and the executor must dedupe on it.
9. **Concurrency primitives are explicit.** Any shared mutable state
   (task queue, memory cache, connection pool) documents its locking
   strategy in a comment referencing the owning doc. "It's probably fine
   because it's single-threaded" is not acceptable reasoning — NOVA is
   explicitly multi-process/multi-device.
10. **No silent truncation or default-substitution of user data.** If a
    field is missing or malformed, the code surfaces a typed error the
    caller must handle — never substitutes `""`, `0`, or `[]` and
    continues, since that hides data loss.

## Style baseline

- Small, single-purpose functions; a function that both fetches and
  mutates state is a refactor target, not a pattern.
- Comments explain *why*, not *what* — the code must read the *what*.
- Every public interface (tool, service method, event shape) is
  documented in the corresponding `docs/` file *before* the code is
  written, and the code cites the doc in a header comment.
