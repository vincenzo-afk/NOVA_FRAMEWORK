<p align="center">
  <img src="docs/assets/nova-logo.png" alt="NOVA logo" width="160"/>
</p>

# Code of Conduct

## Purpose

NOVA is built by people who will disagree about hard architectural
questions — that is expected and healthy. This document sets the baseline
for how disagreement, review, and collaboration happen without becoming
personal or exclusionary.

## Expected behavior

- Critique the architecture, not the person. "This retry logic doesn't
  handle a mid-execution provider failure" is welcome; attacking the
  contributor who wrote it is not.
- Assume good faith on a first pass. If a pull request seems to violate a
  non-negotiable in `CONTRIBUTING.md`, ask before assuming bad intent.
- Back architectural objections with the reasoning categories used
  throughout this repository: correctness, security, performance,
  maintainability, or consistency with an existing ADR. "I don't like it"
  is a starting point for discussion, not a blocking review comment on its
  own.
- Keep discussion of security-sensitive behavior (permission bypass,
  injection risks, credential handling) out of public issues; route it
  through `SECURITY.md`.

## Unacceptable behavior

- Harassment, personal attacks, or discriminatory language directed at any
  contributor.
- Deliberately reintroducing a pattern an accepted ADR has already
  rejected, without proposing a superseding ADR.
- Publicly disclosing a security vulnerability before the process in
  `SECURITY.md` has run its course.

## Enforcement

Maintainers may remove comments, close pull requests, or restrict a
contributor's participation for violations of this document. Decisions can
be appealed by opening a discussion tagged `conduct-appeal`.

## Scope

This applies to all project spaces: the repository, its issue tracker, and
any official communication channels associated with NOVA.
