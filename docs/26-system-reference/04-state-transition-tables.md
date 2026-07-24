# State Transition Tables

## Purpose

Explicit state-machine tables for every stateful entity in NOVA, so an
implementing agent can build a switch/match statement directly from this
document instead of inferring the state machine from paragraphs scattered
across other files. Every entry below is a **hard invariant**: a
transition not listed in a table is invalid and must be rejected (see
`FM-15-019`, Invalid state transition).

## Task / Agent Lifecycle

> **Known documentation conflict, flagged not resolved** — see
> `docs/03-runtime/task-manager.md`'s Task state machine for the
> conflicting version and the full note; a human decision is needed on
> which framing is authoritative before either is treated as final.

```
Idle → Thinking → Planning → Executing → Waiting → Completed
                                 ↓            ↓
                              Failed      Cancelled
                                 ↓
                             Unverified → (retry) → Verifying → Completed
                                                          ↓
                                                       Failed
```

| Current State | Event | Next State | Guard / Condition |
|---|---|---|---|
| `Idle` | New goal received | `Thinking` | — |
| `Thinking` | Intent classified | `Planning` | — |
| `Thinking` | Ambiguity above threshold | `Idle` | Clarifying question sent; returns to `Idle` awaiting reply, not a new hidden state |
| `Planning` | Plan validated | `Executing` | Plan passes dependency/cycle/capability validation (`docs/03-runtime/planner.md`) |
| `Planning` | Plan invalid | `Failed` | No valid plan constructible |
| `Executing` | Step needs external input | `Waiting` | e.g. human approval (`FM-18`), external API response |
| `Executing` | All steps done | `Verifying` | Never transitions directly to `Completed` — see `FM-05-016` |
| `Executing` | Unrecoverable step error | `Failed` | After exhausting retry policy |
| `Executing` | User cancels | `Cancelled` | — |
| `Waiting` | Input received | `Executing` | — |
| `Waiting` | Timeout | `Failed` or `Cancelled` | Per `FM-18-016`'s timeout policy |
| `Verifying` | Verification passes | `Completed` | Independent verifier, per `FM-05-016` |
| `Verifying` | Verification fails | `Unverified` | Never `Failed` directly — `Unverified` is distinct, see `docs/01-product/success-metrics.md` |
| `Unverified` | Retry policy allows | `Planning` | Bounded retry count |
| `Unverified` | Retry exhausted | `Failed` | — |
| `Completed`, `Failed`, `Cancelled` | — | — | Terminal states; no outgoing transitions |

## Provider / Circuit Breaker

| Current State | Event | Next State | Guard / Condition |
|---|---|---|---|
| `Closed` (healthy) | Error rate crosses threshold | `Open` (tripped) | Per `docs/18-providers` health monitoring |
| `Open` | Cooldown timer elapses | `HalfOpen` | — |
| `HalfOpen` | Trial request succeeds | `Closed` | — |
| `HalfOpen` | Trial request fails | `Open` | Cooldown timer resets |

## Plugin Lifecycle

Canonical state names and narrative detail for each state:
`docs/16-extensibility/plugin-lifecycle.md`. This table is the formal
transition table derived from that document — if the two ever disagree,
`docs/16-extensibility/plugin-lifecycle.md` is correct and this table is
stale; fix this table to match it, per
`docs/00-implementation-governance/documentation-precedence.md`.

| Current State | Event | Next State | Guard / Condition |
|---|---|---|---|
| `Discovered` | Manifest validated | `Installed` | Passes schema + signature check (`FM-12-016`) |
| `Installed` | User/policy enables | `Enabled` | Consent flow completed (`FM-12-007`); sandbox init succeeds |
| `Installed` | Sandbox init fails | `Failed` | — |
| `Enabled`, `Deprecated` | User/policy disables | `Disabled` | Process stopped, tools deregistered, package retained |
| `Disabled` | User/policy re-enables | `Enabled` | — |
| `Enabled` | New version applied | `Updating` | — |
| `Updating` | Update succeeds | `Enabled` | — |
| `Updating` | Update fails | `Failed` | — |
| `Failed` | Manual review resolves | `Disabled` | Never auto-transitions out of `Failed` |
| `Enabled` | Marked deprecated by publisher/policy | `Deprecated` | Still functional; see `docs/16-extensibility/plugin-lifecycle.md` |
| `Deprecated` | Un-deprecated | `Enabled` | — |
| `Disabled`, `Deprecated` | Uninstall requested | `Uninstalled` | Cleanup verified (`FM-19-008`) |
| `Uninstalled` | — | — | Terminal state |

## Workflow Node

| Current State | Event | Next State | Guard / Condition |
|---|---|---|---|
| `Pending` | Dependencies satisfied | `Ready` | — |
| `Ready` | Scheduler dispatches | `Running` | — |
| `Running` | Node completes | `Succeeded` | — |
| `Running` | Node errors | `Retrying` or `Failed` | Per node's retry policy (`FM-02-017`) |
| `Retrying` | Retry attempted | `Running` | Bounded by max-retry ceiling |
| `Retrying` | Retries exhausted | `DeadLettered` | — |
| `Succeeded`, `Failed`, `DeadLettered` | — | — | Terminal for this node (workflow-level rollback may re-trigger a fresh node instance, not resurrect this one) |

## Session

| Current State | Event | Next State | Guard / Condition |
|---|---|---|---|
| `Active` | Idle timeout | `Idle` | Conversation timeout only — does not affect any in-progress Task's state (`FM-06-019`) |
| `Idle` | New message | `Active` | — |
| `Idle` | Extended idle timeout | `Expired` | — |
| `Expired` | New message | `Active` (new session, prior linked) | Per `FM-06-020` reconnect-vs-new-session handling |

## Related documents

- `docs/03-runtime/task-manager.md`, `docs/03-runtime/executor.md`,
  `docs/03-runtime/verifier.md` — full prose detail behind the Task/Agent table
- `docs/03-runtime/service-lifecycle.md` — per-service lifecycle state
  machine (Starting/Running/Degraded/Failed/Stopping/Stopped)
- `docs/16-extensibility/plugin-lifecycle.md` — full plugin lifecycle detail
- `docs/17-workflow/workflow-engine.md` — full workflow node detail

## Where This Breaks

This document is itself a build artifact an AI agent relies on. If it drifts from the real system, every agent that trusts it inherits the drift silently. The failures below are specific to *this document going stale or being wrong*, not to the subsystem it describes (see the cross-referenced FM files for that).

| ID | Failure | Trigger | Detection | Severity | Mitigation | Recovery |
|---|---|---|---|---|---|---|
| **FM-24-010** | Table omits a state/transition that exists in code | Implementation adds a new state (e.g. a new `Paused` task state) without updating this table. | State-machine conformance test enumerates all states/transitions reachable in code and diffs against this table. | High | Generate this table from a single machine-readable state-machine definition where feasible, rather than hand-maintaining prose and code separately. | Update the table; add the missing transition to the invalid-transition-rejection tests referenced by `FM-15-019`. |
| **FM-24-011** | Agent implements an undocumented 'convenience' transition | Implementer adds a shortcut transition (e.g. `Executing` directly to `Completed`, skipping `Verifying`) believing it's a harmless optimization. | Code review, or `FM-05-016`'s false-success-reporting detection catching a task marked complete with no independent verification. | Critical | State explicitly (as done above) that `Verifying` is a mandatory hop, never skippable, so 'convenience' shortcuts are recognizable as invariant violations, not implementation choices. | Revert the shortcut transition; treat any task that went through it as `Unverified` retroactively. |
| **FM-24-012** | Two tables in this document disagree with each other on a shared boundary | e.g. Session table's 'Active' interacting with Task table's states isn't cross-checked for consistency. | Manual review during the next revision of either table catches an inconsistency at the boundary. | Low | Cross-reference boundary conditions explicitly (as done in the Session table's guard column) rather than describing each state machine in total isolation. | Reconcile the two tables' boundary description; clarify which is authoritative for the overlapping behavior. |
