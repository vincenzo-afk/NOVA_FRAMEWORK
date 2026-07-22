<p align="center">
  <img src="docs/assets/nova-logo.png" alt="NOVA logo" width="160"/>
</p>

# Security Policy

This file covers vulnerability reporting for the NOVA project itself. For
the full security architecture (threat model, permission system, encryption,
sandboxing), see `docs/10-security/`.

## Why this matters more than average for NOVA

NOVA runs continuously with the logged-in user's OS privileges, observes
files, clipboard, browser activity, and screen content (with permission),
and can execute actions ranging from API calls to direct keyboard/mouse
control. A vulnerability here has a materially larger blast radius than a
typical desktop application bug. Report accordingly — err on the side of
reporting privately even if you are unsure whether something qualifies.

## Reporting a vulnerability

Do not open a public issue for a suspected security vulnerability.

Instead:

1. Report privately through the repository's private vulnerability
   reporting channel (GitHub Security Advisories) or the security contact
   address published on the project's official page.
2. Include: affected component (reference the relevant `docs/` path if
   known), reproduction steps, observed impact, and whether it requires
   local access, network access, or malicious observed content
   (e.g., a crafted file or webpage) to trigger.
3. Do not include working exploit code against a live, non-test system in
   the initial report.

## What qualifies as a security issue here specifically

Beyond standard categories (RCE, privilege escalation, credential leakage),
NOVA's threat model in `docs/10-security/threat-model.md` treats the
following as security issues, not ordinary bugs:

- Any path by which content NOVA merely *observes* (a webpage, a
  downloaded file, clipboard text) can cause an *action* to execute without
  going through the planner's instruction channel. This is the prompt
  injection boundary described in `docs/10-security/threat-model.md` and is
  treated as critical severity regardless of exploit complexity.
- Any path by which the GUI/vision/keyboard-mouse execution tier can be
  reached for a task that a safer tier (native API, MCP, CLI, accessibility)
  could have handled, bypassing a permission check that tier would have
  enforced.
- Any path by which one OS user's memory, knowledge graph, or credentials
  become readable by another OS user on a shared machine.

## Response process

- Acknowledgment target: 72 hours.
- Triage and severity assignment against the categories in
  `docs/10-security/threat-model.md`: 7 days.
- Fix or mitigation timeline communicated after triage, scaled to severity.
- Public disclosure happens only after a fix is available, coordinated with
  the reporter.

## Supported versions

Security fixes are provided for the current stable release line only,
until NOVA reaches a 1.0 release, after which a supported-versions table
will be published here.
