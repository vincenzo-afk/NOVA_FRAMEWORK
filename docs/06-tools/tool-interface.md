# Tool Interface

## Purpose

The exact interface contract every tool integration must implement to be
registered in the Tool Registry, regardless of execution tier. This
document is the concrete schema referenced by `tool-system.md` and `tool-registry.md`.

## Scope

The interface contract itself: required metadata fields and the
structured result format every invocation must return.

## Required metadata (declared at registration)

```json
{
  "tool_id": "string, unique",
  "execution_tier": "native_runtime | internal_function | api | mcp | cli | accessibility | vision | keyboard_mouse",
  "deterministic": "boolean — true if this tool's output requires no AI reasoning to produce, per docs/05-ai/deterministic-first.md",
  "dependencies": ["array of other tool_id or capability_id values this tool requires to function"],
  "supported_actions": [
    {
      "action_id": "string",
      "risk_tier": "read_only | reversible_write | destructive_irreversible",
      "verification_signal": "exit_code | api_response | file_hash | accessibility_state | none",
      "lockable_resources": ["array of resource-type descriptors, if any"],
      "permission_scope": "string, referencing docs/10-security/permissions.md categories",
      "estimated_latency_ms": "integer, typical case",
      "estimated_cost_class": "free | low | medium | high, per docs/05-ai/model-routing-matrix.md cost dimension where the action involves an AI-assisted step",
      "timeout_ms": "integer, per docs/03-runtime/failure-recovery.md",
      "input_schema": "JSON Schema describing accepted parameters",
      "output_schema": "JSON Schema describing the evidence/value shape returned"
    }
  ]
}
```

A tool with more than one action (e.g., a file tool supporting both
"read" and "delete") declares each action separately with its own risk
tier and verification signal — risk and verification are properties of
the action, not the tool as a whole. `deterministic: false` marks a tool
whose action requires an AI-assisted step (e.g., a summarization tool);
`estimated_latency_ms` and `estimated_cost_class` feed directly into Tool
Selection's ranking (`docs/05-ai/tool-selection.md`) and the Planner's
candidate-scoring stage (`docs/03-runtime/planner.md`).

## Structured result contract (returned per invocation)

```json
{
  "tool_id": "string",
  "action_id": "string",
  "status": "success | failure | partial",
  "evidence": {
    "type": "exit_code | api_response | file_hash | accessibility_state | none",
    "value": "the actual signal, e.g. the exit code or hash"
  },
  "affected_resources": ["array of resource identifiers actually touched"],
  "error": "string, present only if status is failure or partial"
}
```

The Executor (`docs/03-runtime/executor.md`) requires every invocation to
return this shape — a tool that can only report a bare boolean "done" is
non-compliant and cannot be registered for unattended execution; at best
it registers with `verification_signal: "none"`, which restricts it to
confirmation-required execution per `tool-registry.md`.

## No unattended execution without a verification signal

This is a hard rule, not a preference: an action declared with
`verification_signal: "none"` is never eligible for automatic (no
confirmation) execution, regardless of its risk tier — see
`docs/03-runtime/permission-manager.md` for how this rule is enforced at
the gate. This closes the gap the project's foundational review
identified: a tool that can only report "done" with no independently
checkable evidence should not be trusted to run unattended.

## Partial status

`status: "partial"` is used for multi-step tool actions where some
sub-steps succeeded and others did not (e.g., a five-file batch operation
that completed on three files). `affected_resources` in this case lists
exactly which resources were actually touched, allowing the Verifier and
Planner to reason precisely about what state changed.

## Related documents

- `tool-system.md` — the conceptual tool abstraction this schema
  implements
- `tool-registry.md` — where this metadata is stored and validated
- `docs/03-runtime/verifier.md`, `permission-manager.md` — the consumers
  of the evidence and risk-tier fields above
