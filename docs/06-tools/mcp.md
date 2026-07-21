# MCP (Execution Tier 4)

## Purpose

Describes NOVA's integration with the Model Context Protocol: how MCP
servers are connected, how their capabilities are discovered and
registered as tools, and how the trust boundary between NOVA and a
third-party MCP server is enforced.

## Scope

MCP-specific connection, discovery, and trust mechanics. General tool
registration is `docs/06-tools/tool-registry.md`; this document covers
what is specific to MCP as a source.

## Connection and capability discovery

On configuration of a new MCP server (endpoint, authentication reference),
NOVA performs capability discovery per the MCP specification: enumerating
the server's exposed tools, resources, and prompts. Each discovered tool
is then mapped into NOVA's `tool-interface.md` schema — this mapping
requires the MCP server's tool descriptions to supply enough information
to populate risk tier and verification signal; where they do not, the
tool is registered conservatively as `verification_signal: "none"`,
restricting it to confirmation-required execution, per
`tool-interface.md`'s hard rule.

## Trust boundary

An MCP server is an external, potentially untrusted component. NOVA
enforces the trust boundary at two points, independent of whatever the
MCP server itself claims about its own safety:

1. **Permission scope** — a connected MCP server's tools are only ever
   invoked within the calling agent instance's configured tool allowlist
   (`docs/05-ai/planner-agent.md`); an MCP server cannot grant itself
   broader access than the instance invoking it already has.
2. **Secrets isolation** — authentication for an MCP server is stored in
   the OS credential vault (`docs/10-security/secrets.md`, Tier 3) and is
   never passed to or readable by the MCP server's own tool
   implementations beyond what that specific connection requires.

## Content from MCP results treated as observed content

Data returned by an MCP tool call is treated as observed content, not as
instructions, under the Prompt System's content/instruction separation
(`docs/05-ai/prompt-system.md`) — this closes a specific variant of the
prompt-injection risk where a compromised or malicious MCP server could
attempt to return content designed to influence subsequent planning
rather than merely answer the query it was asked.

## Multiple MCP servers, same capability

Where more than one connected MCP server exposes an overlapping
capability (e.g., two different servers both offering file search), Tool
Selection's tie-breaking rules (`docs/05-ai/tool-selection.md`) apply
identically to MCP-sourced tools as to any other tool at the same tier —
MCP servers receive no special preference purely for being MCP-sourced.

## Related documents

- `execution-priority.md` — MCP's place in the tier ordering
- `docs/06-tools/tool-registry.md` — how discovered tools are registered
- `docs/05-ai/prompt-system.md` — the content/instruction separation
  applied to MCP results
- `docs/10-security/secrets.md` (Tier 3) — credential handling for MCP
  authentication
