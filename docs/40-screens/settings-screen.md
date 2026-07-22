# Settings — Screen Spec

## Purpose

Central configuration hub organized to match the settings taxonomy in `29-product/settings.md`.

## Layout

Primary content region + persistent sidebar nav; see `30-design/navigation.md`. Specific layout detail is implementation-defined against `30-design/design-tokens.md`, not hardcoded here.

## Components used

See `41-components/` for the shared components this screen composes; this screen does not define any one-off component — new visual patterns are added to the component library first.

## Interactions

Primary actions and their keyboard equivalents are listed in `29-product/keyboard-shortcuts.md`.

## Required states (every screen must implement all of these — see `45-code-perfection-failure-modes/09-ui-and-state-binding.md` item 5)

- Loading
- Empty (with reason + next action)
- Populated
- Error (mapped to `26-system-reference/06-error-catalog.md`)
- Offline / degraded
- Permission denied (if applicable)
- Partial data (if the underlying query can return incomplete results)

## Accessibility

Focus order, screen-reader labels, and reduced-motion behavior follow `30-design/accessibility.md`; keyboard-only navigation must reach every action on this screen without a mouse.

## Analytics

Emits events per `35-analytics/events.md` for: screen view, primary action taken, error encountered. No event includes raw user content — see `29-product/privacy.md`.
