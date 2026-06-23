# Deferred Work

Items surfaced during reviews that are intentionally not addressed now. Each notes its source and why it's deferred.

## Deferred from: code review of story-1.2 (2026-06-22)

- **Light-mode accent AA** — the fuchsia accent (`--hx-color-primary-500/600`) on Hextra's white light-mode surface measures ~3.4–3.9:1, below the AA 4.5:1 floor for normal text. Root-scoped `--primary-hue` makes the accent apply in light mode too. Deferred per project decision: dark is the AA-guaranteed v1 path; light-mode AA is explicitly unverified/deferred in DESIGN.md and the architecture spine. Revisit if/when light mode is tuned.
- **Focus-ring application** — `--ck-focus-ring` (`2px solid #d946ef` on `:focus-visible`) is declared as a token but no base `:focus-visible` rule is emitted yet. Deferred to Story 1.3+ where real interactive elements (nav, search, theme toggle, social links) land and the focus ring is applied with them.
