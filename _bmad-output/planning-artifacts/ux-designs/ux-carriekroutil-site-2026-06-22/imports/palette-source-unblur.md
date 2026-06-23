# Palette source — unblur.carrie.dev (Carrie's existing site)

Reference Carrie cited as "playful": purple/pink/orange on dark.
Exact values lifted from `~/code/unblur`:

- `components/logo.tsx`: gradients `from-violet-500 to-fuchsia-500` and `from-fuchsia-500 to-amber-500`
- `app/icon.svg` linearGradient stops: `#8b5cf6` (0%) → `#d946ef` (50%) → `#f59e0b` (100%)
- `app/globals.css` dark: background `#0a0a0a`, foreground `#ededed`

Tokens (Tailwind 500-weights):
- violet-500  #8b5cf6
- fuchsia-500 #d946ef
- amber-500   #f59e0b
- bg (dark)   #0a0a0a
- fg (light)  #ededed

Signature treatment: left-to-right violet→fuchsia→amber gradient on near-black.
Note: unblur also uses a soft-blur effect (its namesake) — gimmick, not necessarily carried to carriekroutil.com.
