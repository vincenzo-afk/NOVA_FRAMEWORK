# Testing Strategy

## Purpose

The overall approach to verifying NOVA's correctness across four
distinct testing layers, each addressing a different class of risk
identified throughout this repository — from unit-level correctness to
the specific challenge of testing an agent whose environment is the
user's own live, mutating desktop.

## Scope

The relationship between testing layers. Each layer's specifics are
detailed in its own document in this folder.

## Testing layers

```mermaid
flowchart TD
    A[Unit Tests<br/>unit-tests.md] --> B[Integration Tests<br/>integration-tests.md]
    B --> C[End-to-End Tests<br/>e2e-tests.md]
    C --> D[Simulation Tests<br/>simulation-tests.md]
    D --> E[Chaos Tests<br/>chaos-tests.md]
```

- **Unit Tests** — individual service logic in isolation, per
  `unit-tests.md`.
- **Integration Tests** — cross-service interaction over the real
  Communication Bus, per `integration-tests.md`.
- **End-to-End Tests** — the full observation-to-verified-result pipeline
  against a real (test) OS environment, per `e2e-tests.md`.
- **Simulation Tests** — replay of recorded real-world tasks and golden
  datasets to catch regressions that only manifest against realistic,
  messy input, per `simulation-tests.md`.
- **Chaos Tests** — deliberate fault injection (killing a service
  mid-task, corrupting a message, denying a permission mid-flow) to
  confirm the failure-recovery mechanisms in
  `docs/03-runtime/failure-recovery.md` actually behave as documented
  under real disruption, not just in the clean scenarios the other four
  layers primarily exercise. See `chaos-tests.md`.

## Why five layers, not fewer

Unit and integration tests alone cannot catch the failure modes specific
to this project — a Planner that makes technically-valid but practically
wrong tool selections, or a Vision-tier interaction that works against a
clean test fixture but fails against a real application's actual current
UI state. Simulation testing against recorded real-world scenarios exists
specifically to catch this gap. Chaos testing exists for a distinct
reason: every other layer tends to test the happy path plus a handful of
deliberately authored failure cases, whereas chaos testing injects faults
the test author did not necessarily anticipate, at points the other
layers may not think to probe (mid-checkpoint process kill, a resource
lock held past its timeout, a dead-lettered message per
`docs/02-architecture/communication-model.md`).

## Test coverage matrix

| Component | Unit | Integration | Simulation | Chaos | Benchmark |
|---|---|---|---|---|---|
| Planner (`docs/03-runtime/planner.md`) | Required | Required | Required | Required (mid-plan kill) | Required |
| Executor (`docs/03-runtime/executor.md`) | Required | Required | Required | Required (mid-step kill) | Required |
| Verifier (`docs/03-runtime/verifier.md`) | Required | Required | Required | Optional | Required |
| Memory / Knowledge Graph (`docs/04-memory/`) | Required | Required | Required | Required (corruption injection) | Required |
| Tool Registry / individual tools (`docs/06-tools/`) | Required | Required | Required (GUI-tier only) | Optional | Optional |
| Plugin system (`docs/16-extensibility/`) | Required | Required | Optional | Required (crash isolation) | Optional |
| UI surfaces (`docs/09-ui/`) | Optional | Required | Optional | Optional | Optional |
| Observers (`docs/07-observers/`) | Required | Required | Required (event storms) | Optional | Required |

"Required" means a component cannot pass `validation.md`'s acceptance
checklist without coverage at that layer; "Optional" means coverage is
valuable but not a merge-blocking requirement, left to reviewer judgment
per the specific change's risk.

## Every layer maps to a component's acceptance criteria

Per `validation.md`, no component is considered complete until it has
passing coverage at every layer marked "Required" for it above — a Tool
Registry integration, for example, requires unit tests for its metadata
validation, integration tests confirming it is correctly discoverable by
Tool Selection, and, if it is a GUI-tier tool, simulation tests against
recorded interaction scenarios for its specific target application.

## Testing and the deterministic-first principle

Because deterministic paths are, by construction, fully specified and
reproducible, they receive full unit-test coverage as a baseline
expectation. LLM-involving paths (`docs/05-ai/ambiguity-resolution.md`)
are additionally covered by simulation testing against golden datasets,
since their non-deterministic nature makes exhaustive unit-level
assertion less meaningful on its own.

## Related documents

- `unit-tests.md`, `integration-tests.md`, `e2e-tests.md`,
  `simulation-tests.md`, `chaos-tests.md` — full detail per layer
- `validation.md` — acceptance criteria tying these layers together
- `docs/11-performance/benchmarks.md` — the performance-specific
  measurement this testing strategy complements
- `docs/03-runtime/failure-recovery.md` — the mechanisms chaos testing
  specifically validates
