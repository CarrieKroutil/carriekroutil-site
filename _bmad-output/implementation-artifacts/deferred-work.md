# Deferred Work

Items surfaced during reviews that are intentionally not addressed now. Each notes its source and why it's deferred.

## Deferred from: code review of story-1.2 (2026-06-22)

- **Light-mode accent AA** — the fuchsia accent (`--hx-color-primary-500/600`) on Hextra's white light-mode surface measures ~3.4–3.9:1, below the AA 4.5:1 floor for normal text. Root-scoped `--primary-hue` makes the accent apply in light mode too. Deferred per project decision: dark is the AA-guaranteed v1 path; light-mode AA is explicitly unverified/deferred in DESIGN.md and the architecture spine. Revisit if/when light mode is tuned.
- **Focus-ring application** — `--ck-focus-ring` (`2px solid #d946ef` on `:focus-visible`) is declared as a token but no base `:focus-visible` rule is emitted yet. Deferred to Story 1.3+ where real interactive elements (nav, search, theme toggle, social links) land and the focus ring is applied with them. **Resolved in Story 1.3** — site-wide `:focus-visible` ring + scoped search-input override.

## Deferred from: code review of story-1.3 (2026-06-23)

- **Double focus ring on Hextra-managed controls** — the brand site-wide `:focus-visible { outline: 2px solid #d946ef }` rule can render *in addition to* Hextra's own focus ring (a Tailwind `ring` = box-shadow) on controls Hextra manages with `hextra-focus-visible*` classes (search input, hamburger button). Result is a possible doubled ring on those specific controls. Deferred because a visible 2px fuchsia ring is present on every control (AC2 satisfied) and the effect is cosmetic; to collapse to a single ring, neutralize Hextra's ring on those controls (e.g. `[class*="hextra-focus-visible"]:focus-visible { --tw-ring-shadow: 0 0 #0000; }`). Revisit if the doubled ring proves visually distracting. [assets/css/custom.css:205-216]

## Deferred from: story-1.4 (2026-06-23)

- **Light-mode WCAG AA verification (open question)** — Story 1.4 makes the dark/light theme toggle functional with persistence, but light mode ships with **untuned Hextra defaults**; its AA verification is **explicitly deferred** and tracked here as an open question. Dark is the AA-guaranteed v1 path. This is the behavioral counterpart to the 1.2 "Light-mode accent AA" entry above (fuchsia accent ~3.4–3.9:1 on Hextra's white surface, below the 4.5:1 floor) — the same root issue, now reachable by a real visitor who toggles to light. Story 1.4's brand styling stays scoped to `.dark`; light is left to Hextra. Non-blocking for launch (dark is the default and the AA path); revisit together with the 1.2 entry if/when light mode is tuned. [Source: epics.md Story 1.4 AC; EXPERIENCE.md; DESIGN.md]

## Deferred from: code review of story-1.4 (2026-06-23)

- **Phantom border transition on the header blur layer in (future) light mode** — the Story 1.4 theme transition includes `.hextra-nav-container-blur` with `border-color` in its transitioned properties, but that element's styled border is defined only under `.dark .hextra-nav-container-blur` (custom.css:139-143). Today (dark default, light untuned/deferred) this is harmless. Once light mode is tuned, toggling will animate the blur layer's `border-color` from/to an unset value (a "phantom" border ease). Fix when tuning light mode: scope the blur's border transition to `.dark`, or give the light header an explicit border first. Tied to the light-mode AA deferral above — revisit together. [assets/css/custom.css:236-242]
