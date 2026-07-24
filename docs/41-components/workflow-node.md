# Workflow Node — Component Spec

## Purpose

Visual representation of a workflow step/condition/gate; shows live execution status (pending/running/success/failed/skipped) with color + icon, never color alone (accessibility).

## States

Default, hover, focus, active/pressed, disabled, loading (if applicable) — all defined via design tokens (`docs/30-design/design-tokens.md`), never one-off styles.

## Accessibility

Full keyboard operability and correct ARIA role/label; verified against `docs/42-design-qa/accessibility-checklist.md` before merge.
