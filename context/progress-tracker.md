# Progress Tracker

Update this file after every meaningful implementation change.

## Current Phase

- Foundation setup

## Current Goal

- Define Unit 2 (next feature unit after the design system foundation).

## Completed

- Unit 1: Design System (`context/feature-specs/1-design-system.md`)
  - Installed and configured `shadcn/ui` (`components.json`, style `base-nova`, base color `neutral`, icon library `lucide-react`).
  - Added `components/ui/`: `button`, `card`, `dialog`, `input`, `tabs`, `textarea`, `scroll-area`.
  - Installed `lucide-react`.
  - `lib/utils.ts` re-exports `cn()` from the `cn` package (shadcn's own clsx+tailwind-merge replacement) — this is the CLI's generated convention, not hand-rolled.
  - Rewrote `app/globals.css`: replaced the default light/dark neutral oklch palette with the dark-only hex palette from `context/ui-context.md`, mapped onto shadcn's semantic tokens (`background`, `card`, `popover`, `primary`, `secondary`, `muted`, `accent`, `destructive`, `border`, `input`, `ring`, `sidebar-*`, `chart-*`) and onto the project's own utility tokens (`bg-base`, `bg-surface`, `text-copy-primary`, `text-brand`, etc.).
  - Fixed `--font-sans`/`--font-mono` in `@theme inline` to point at the actual `--font-geist-sans`/`--font-geist-mono` variables set in `app/layout.tsx` (previously self-referential and would have fallen back to a generic sans-serif).
  - Added the `dark` class to `<html>` in `app/layout.tsx` since the app is dark-only (no light mode), so `dark:`-tuned variants in generated components apply.
  - Verified: `npx tsc --noEmit` clean, `npm run build` clean, and a temporary smoke-test render of all 7 components (fetched via curl from the running dev server) showed the dark theme applied with no runtime errors, then reverted `app/page.tsx` back to its prior placeholder content since the real homepage is out of scope for this unit.
  - Did not modify any generated `components/ui/*` file.

## In Progress

- None.

## Next Up

- Scope and write the spec for Unit 2.

## Open Questions

- None yet.

## Architecture Decisions

- `ui-context.md`'s token-to-Tailwind-utility mapping was only given by example (`bg-base`, `text-copy-primary`, etc.), not exhaustively. Resolved the full mapping and documented it directly in `context/ui-context.md` (new table) rather than guessing silently — see that file for the complete CSS-variable-to-utility table.
- shadcn's semantic tokens (`primary`, `card`, `popover`, `muted`, `destructive`, ...) are mapped onto the same dark palette rather than kept as a separate theme, so generated `components/ui/*` files render correctly themed without ever being edited.
- Bug found and fixed (post-completion check of `app/globals.css`): `--color-base` had been registered as a generic `@theme` color token for `bg-base`. Tailwind generates `bg-/text-/border-/ring-*` for every `--color-*` key, and `base` collides with Tailwind's own built-in font-size scale key (`text-base` = 1rem). The collision made `.text-base` compile to `color: var(--bg-base)` instead of `font-size: 1rem` — breaking `Input`, `Textarea`, `CardTitle`, and `DialogTitle` (all use `text-base`), making their text both the wrong size and the same near-black as the page background. Fixed by dropping `--color-base` from `@theme` and giving `bg-base` its own `@utility` rule instead, scoped to `background-color` only. Verified via the compiled CSS: `.text-base` now outputs `font-size`/`line-height` again, `.bg-base` still outputs `background-color`.

## Session Notes

- Context files (`project-overview.md`, `architecture-context.md`, `ui-context.md`, `code-standards.md`, `ai-workflow-rules.md`, `progress-tracker.md`) and `feature-specs/1-design-system.md` were copied into `context/` from the Six-File Context Methodology kit to match the entry point referenced in `AGENTS.md`.
- The project's own dev server was already running on port 3000 (PID 3272) during verification; an `npm run dev` invocation used for testing detected the port in use and exited on its own without binding — no process was left behind.
