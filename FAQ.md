# Frequently Asked Questions

### What is NOVA in one sentence?
A persistent AI runtime for a single Windows PC that continuously observes,
remembers, understands, plans, acts, and verifies across the user's digital
workspace — preferring deterministic computation over AI reasoning whenever
a task can be solved without it.

### Is this a chatbot?
No. A chatbot only knows the current conversation. NOVA maintains
structured, long-lived memory and a knowledge graph of the user's projects,
files, and history, and can act on the user's behalf, not just answer in
text.

### Is this spying on me?
NOVA observes files, applications, windows, browser activity, and clipboard
content only with explicit, per-source, revocable permission (see
`docs/10-security/permissions.md`, Tier 3). Nothing is observed by default
before the user grants it, and the purpose is to maintain an accurate model
of the user's own workspace for the user's own benefit — not telemetry sent
elsewhere. See `docs/10-security/threat-model.md` for exactly how this is
enforced technically.

### Does it need the internet?
No, by design. NOVA supports fully local operation (local LLM, local
embeddings, local search, local execution). Cloud AI providers are
optional and only enhance capability when configured; see
`docs/02-architecture/` (Tier 2) for the offline behavior specification.

### What operating system does it run on?
Windows, for v1. Cross-platform support is explicitly out of scope for the
current phase — see `docs/00-overview/non-goals.md`.

### Is it free? Is it open source?
Fully open source (MIT license). The runtime itself is free. Any ongoing
cost comes only from the AI provider the user chooses to use — a local
model (no cost) or the user's own API key with a cloud provider. There is
no bundled subscription and no vendor lock-in.

### Does NOVA control my mouse and keyboard?
It can, but only as the last-resort execution method, after native APIs,
MCP, CLI, and accessibility-tree control have all been attempted. See
`docs/06-tools/execution-priority.md` (Tier 2). Destructive or irreversible
actions always require explicit confirmation regardless of which execution
method is used.

### What stops it from doing something destructive by accident?
Every action is classified into a risk tier (read-only, reversible,
destructive) before execution, and higher tiers require explicit user
confirmation. Every reversible action records enough state to be undone.
See `docs/10-security/permissions.md` and `docs/03-runtime/verifier.md`
(Tier 2/3).

### Does it get smarter by training on my data?
No fine-tuning is performed. Personalization comes from retrieval over
structured memory and the knowledge graph, not from adjusting model
weights. See `docs/05-ai/` (Tier 2). This is a deliberate choice, not a
current limitation waiting on future work.

### Can I use it across multiple computers?
Not in the current phase. v1 is explicitly single-machine. Multi-device
synchronization is on the long-term roadmap (Phase 5) but is not designed
yet — see `ROADMAP.md`.

### Why does documentation exist before any code?
Because most of the risk in a system with this much OS-level privilege is
architectural, not syntactic — the review process that produced this
repository's foundational decisions (see `docs/15-decisions/`, Tier 3)
found that the biggest risk to a project like this is unbounded scope, not
any single technical unknown. Writing the boundary down before writing code
is how that risk is managed.
