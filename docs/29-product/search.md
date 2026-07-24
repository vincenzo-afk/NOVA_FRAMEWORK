# Search

Unified search spans chat history, memory, tasks, and files indexed by observers, ranked by `docs/04-memory/memory-ranking.md`. Search must degrade gracefully to keyword-only matching if the embedding/ranking service is unavailable — never a blank/error result for a basic case.
