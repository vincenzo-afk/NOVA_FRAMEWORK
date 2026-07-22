# AI-Generated Code Review Checklist


## Purpose

What a reviewer (human or a second AI pass) must specifically verify for
code generated against these docs, beyond generic code review.

## Checklist

1. **Traceability** — does every non-trivial function cite, in a comment
   or PR description, the doc section it implements? Code with no
   traceable spec is either undocumented scope creep or a sign the spec
   needs updating.
2. **Interface fidelity** — does the function signature actually match
   what `architecture-index.md` and the target doc specify, including
   error/Result types, not just the happy-path return type?
3. **Failure mode coverage** — cross-check the PR's handled failure cases
   against the full list in the relevant `25-failure-modes/` and
   `45-code-perfection-failure-modes/` doc. Flag any failure mode listed
   in docs but not visibly handled in the diff.
4. **No silent scope narrowing** — did the implementation quietly drop a
   documented requirement (e.g., an edge case, a permission check) because
   it was inconvenient, without flagging it in the PR description?
5. **No silent scope expansion** — did the implementation add behavior
   not in any doc? If so, either the doc needs updating first, or the
   code needs trimming — undocumented behavior is untested-by-spec
   behavior.
6. **Consistency with `dependency-map.md`** — if a high fan-in component
   was touched, are all listed consumers verified unaffected or updated?
7. **Test-to-criteria mapping** — does each acceptance criterion in
   `acceptance-criteria.md` format have a corresponding test, not just
   "tests pass"?
8. **No new duplicated logic** — search for whether the behavior being
   added already exists elsewhere (a second date-parsing routine, a
   second retry loop) — NOVA's failure-mode docs exist precisely because
   duplicated ad-hoc logic is where inconsistent error handling creeps in.
