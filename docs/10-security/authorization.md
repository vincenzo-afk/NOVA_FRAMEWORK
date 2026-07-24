# Authorization

## Purpose

Defines what an authenticated caller — the UI Layer, an external API
consumer, or a specific agent instance — is permitted to do, layered on
top of the identity established by `authentication.md` and independent of the risk-tier confirmation gating in `permissions.md`.

## Scope

Authorization boundaries between callers and between agent instances.
Risk-tier-based confirmation for the actions themselves is `permissions.md`.

## Two independent authorization axes

NOVA enforces two authorization checks that operate independently, and an
action must pass both:

1. **Caller-level scope** — what an external API consumer or the UI Layer
   is permitted to request at all (e.g., an external API consumer using
   a token scoped to read-only search cannot submit a task requiring
   write access, regardless of that task's risk tier).
2. **Agent-instance scope** — the tool allowlist configured for the
   specific agent instance handling a task
   (`docs/05-ai/planner-agent.md`), enforced by the Permission Manager
   (`docs/03-runtime/permission-manager.md`) independent of the risk-tier
   check.

## Caller-level scopes

Tokens issued for external API access (`authentication.md`) can be scoped
at issuance to a subset of capability — e.g., a read-only scope
permitting Memory/Knowledge Graph queries but not task submission, or a
scope permitting task submission but restricted to a specific project.
This scoping is checked at the API Gateway (`docs/02-architecture/service-architecture.md`) before a request is even forwarded to Task
Manager.

## Agent-instance scopes

As detailed in `docs/05-ai/planner-agent.md` and enforced per `docs/03-runtime/permission-manager.md`, every spawned agent instance
carries a tool allowlist that bounds what it can invoke regardless of
what the Planner might otherwise select — this prevents, for example, a
sub-task instance scoped to "read and summarize a document" from being
able to invoke a destructive file-deletion tool even if some upstream
reasoning error caused the Planner to select one.

## No privilege escalation across boundaries

An agent instance cannot grant itself a broader tool allowlist than it
was configured with, an external API caller cannot exceed their token's
scope by any request parameter, and an MCP server
(`docs/06-tools/mcp.md`) cannot grant its own tools broader access than
the invoking instance's allowlist already permits — each of these
boundaries is enforced independently, so a failure in one does not by
itself grant escalated privilege through another.

## Related documents

- `authentication.md` — identity verification this builds on
- `permissions.md` — the separate, risk-tier-based confirmation gate
- `docs/05-ai/planner-agent.md`, `docs/03-runtime/permission-manager.md`
  — agent-instance scope enforcement
