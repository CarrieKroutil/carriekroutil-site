# Deferred Work

Items surfaced during reviews that are intentionally not addressed now. Each notes its source and why it's deferred.

## Deferred from: code review of story-1.2 (2026-06-22)

- **Light-mode accent AA** — the fuchsia accent (`--hx-color-primary-500/600`) on Hextra's white light-mode surface measures ~3.4–3.9:1, below the AA 4.5:1 floor for normal text. Root-scoped `--primary-hue` makes the accent apply in light mode too. Deferred per project decision: dark is the AA-guaranteed v1 path; light-mode AA is explicitly unverified/deferred in DESIGN.md and the architecture spine. Revisit if/when light mode is tuned.
- **Focus-ring application** — `--ck-focus-ring` (`2px solid #d946ef` on `:focus-visible`) is declared as a token but no base `:focus-visible` rule is emitted yet. Deferred to Story 1.3+ where real interactive elements (nav, search, theme toggle, social links) land and the focus ring is applied with them. **Resolved in Story 1.3** — site-wide `:focus-visible` ring + scoped search-input override.

## Deferred from: code review of story-1.3 (2026-06-23)

- **Double focus ring on Hextra-managed controls** — the brand site-wide `:focus-visible { outline: 2px solid #d946ef }` rule can render *in addition to* Hextra's own focus ring (a Tailwind `ring` = box-shadow) on controls Hextra manages with `hextra-focus-visible*` classes (search input, hamburger button). Result is a possible doubled ring on those specific controls. Deferred because a visible 2px fuchsia ring is present on every control (AC2 satisfied) and the effect is cosmetic; to collapse to a single ring, neutralize Hextra's ring on those controls (e.g. `[class*="hextra-focus-visible"]:focus-visible { --tw-ring-shadow: 0 0 #0000; }`). Revisit if the doubled ring proves visually distracting. [assets/css/custom.css:205-216]
