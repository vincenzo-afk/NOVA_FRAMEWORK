# Plugin Sandboxing

## Purpose

Specifies the process isolation model for plugin code, extending
`docs/10-security/sandboxing.md`'s process-isolation principle to
third-party plugin execution specifically.

## Scope

Plugin-specific process isolation. General sandboxing model is
`docs/10-security/sandboxing.md`.

## Process isolation

Every enabled plugin runs as its own external process, never loaded
in-process with any NOVA service — identical in principle to how MCP
servers are isolated (`docs/06-tools/mcp.md`,
`docs/10-security/sandboxing.md`). A plugin process cannot directly
access NOVA's own service process memory, Memory storage, or Knowledge
Graph storage; all interaction happens through the tool-invocation
interface the Executor calls into (`docs/03-runtime/executor.md`).

## Resource limits

Each plugin process is subject to a configurable CPU/memory ceiling,
independent of the core resource budget in
`docs/11-performance/resource-usage.md` — a misbehaving or resource-heavy
plugin is contained to its own limit and does not compete with or
degrade core NOVA services' resource allocation.

## Filesystem and network access

A plugin process receives no filesystem or network access beyond what is
explicitly required by its granted permission scope
(`plugin-permissions.md`) — this is enforced at the OS process level
(restricted process token / network namespace, depending on platform
capability) wherever the underlying OS supports it, not only at the
application logic level, providing defense in depth consistent with
`docs/10-security/threat-model.md`'s general posture of not relying on a
single layer of control.

## Crash isolation

A plugin process crashing is handled the same way any external tool
source failing is handled (`docs/06-tools/mcp.md`'s trust boundary
model) — it does not affect any core NOVA service, and any in-flight
task depending on that plugin's tools is reported to the Planner as a
failed step requiring replanning, per `plugin-lifecycle.md`'s
deregistration behavior.

## Related documents

- `docs/10-security/sandboxing.md` — the general process-isolation model
  this document extends
- `plugin-permissions.md` — the permission scope enforced at the OS level
  here
- `docs/06-tools/mcp.md` — the analogous external-process trust model for
  MCP servers
