# Memory Ranking

## Purpose

Defines the weighted scoring model used to rank and merge results across
the Retrieval Fusion Engine's five search methods, and to score preference
confidence for User Preferences memory.

## Scope

Ranking formula and weighting only. The methods being ranked are
`retrieval-engine.md`; the preference records being scored are
`memory-types.md`.

## Retrieval ranking factors

Each candidate result is scored on a weighted combination of:

- **Semantic similarity** — vector distance from the query embedding.
- **Importance** — an explicit or inferred significance score (e.g., a
  Decision node is weighted higher than an incidental file-open event).
- **Recency** — more recent records score higher, decaying over a
  configurable half-life per memory tier.
- **Confidence** — from State Manager's confidence propagation
  (`docs/03-runtime/state-manager.md`) for facts derived from potentially
  conflicting observations.
- **Relationship distance** — how many graph hops separate this result
  from entities already established as relevant to the current context
  (closer = higher score).
- **Usage frequency** — records retrieved and actually used successfully
  in past tasks score higher than records that have never been useful.
- **Explicit pinning** — a user can manually pin a specific fact or
  record to always rank highly for relevant queries, overriding the
  computed score.
- **Project relevance** — a boost for records belonging to the project
  currently in focus, per the World Model
  (`docs/03-runtime/world-model.md`).

## Dynamic weighting

The relative weight of each factor is configurable, with sensible
defaults, rather than fixed — the Context Builder
(`docs/05-ai/context-builder.md`) may request a different weighting
profile depending on task type (e.g., a "what changed recently" query
weights recency far more heavily than a "what do I generally prefer for
X" query, which weights confidence and frequency more heavily).

## Preference confidence scoring

User Preferences (`memory-types.md`) use a related but distinct scoring
model to determine whether a stated preference is treated as durable:

- **Frequency** — how many times this preference has been observed or
  restated.
- **Consistency** — whether observed behavior actually matches the stated
  preference over time.
- **Recency** — a recently stated preference is weighted more than an old
  one, though preferences do not decay automatically (`memory-lifecycle.
  md`) — recency affects ranking, not deletion.
- **Explicit statements** — a directly stated preference outweighs one
  merely inferred from behavior.
- **Corrections** — an explicit user correction immediately supersedes
  the previous value regardless of the accumulated confidence the
  previous value had.

Only preferences crossing a configured high-confidence threshold are
treated as permanent defaults applied without re-confirmation; lower-
confidence preferences are still retrieved and considered but are weighted
accordingly and may prompt confirmation in ambiguous situations.

## Related documents

- `retrieval-engine.md` — where these rankings are applied during fusion
- `memory-types.md` — the User Preferences records scored here
- `docs/05-ai/context-builder.md` — the primary consumer of dynamic
  weighting profiles
