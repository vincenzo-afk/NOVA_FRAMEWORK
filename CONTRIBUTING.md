<p align="center">
  <img src="docs/assets/nova-logo.png" alt="NOVA logo" width="160"/>
</p>

# Contributing to NOVA

## Before you write code

NOVA is documentation-first. Every component described in `docs/` must have
its behavior, interfaces, and edge cases specified before implementation
begins. If you are proposing a new capability:

1. Check `docs/01-product/project-scope.md` to confirm it is in scope for
   the current phase. If it isn't, it belongs in `ROADMAP.md`, not in a pull
   request.
2. Check `docs/15-decisions/` for an existing ADR that already settles the
   question. Do not silently contradict an accepted ADR — propose a new ADR
   that supersedes it instead.
3. If the change affects a documented component's contract (its inputs,
   outputs, or guarantees), update the relevant document in the same pull
   request that changes the behavior. A behavior change without a
   documentation change will not be merged.

## Architectural non-negotiables

These are load-bearing across the whole codebase and are not up for
per-PR debate (see `docs/00-overview/design-principles.md` for the
reasoning behind each):

- **Deterministic Before Intelligent.** If a task can be solved by a
  lookup, a parser, an index, or a native function call, it must be. An LLM
  call is only acceptable when the ambiguity-resolution decision flow in
  `docs/05-ai/ambiguity-resolution.md` actually requires one.
- **Risk-tiered execution.** Any new tool integration must declare its risk
  tier (read-only / reversible / destructive) and respect the confirmation
  gates defined in `docs/10-security/permissions.md`.
- **Execution priority.** New tool integrations must slot into the existing
  priority chain (Native Runtime → Internal Functions → API → MCP → CLI →
  Accessibility → Vision → Keyboard/Mouse) — see
  `docs/06-tools/execution-priority.md`. A new integration is not permitted
  to skip ahead of a safer method that could accomplish the same task.
- **No silent schema changes.** The knowledge graph ontology is fixed and
  versioned (`docs/04-memory/ontology.md`). Proposing a new node or edge
  type requires an ADR, not a migration slipped into an unrelated PR.

## Coding standards, branching, and review

Full detail lives in `docs/14-development/` (coding-standards.md,
branching.md, module-checklist.md). At minimum, every pull request must:

- Reference the documentation section it implements.
- Include unit tests for the component and, if it touches execution or
  verification, an entry in the simulation test suite
  (`docs/12-testing/simulation-tests.md`).
- Pass the module checklist for its layer before requesting review.

## Reporting issues

Functional bugs and feature proposals go through the public issue tracker.
Security vulnerabilities do not — see `SECURITY.md`.

## Code of conduct

All contributors are expected to follow `CODE_OF_CONDUCT.md`.
