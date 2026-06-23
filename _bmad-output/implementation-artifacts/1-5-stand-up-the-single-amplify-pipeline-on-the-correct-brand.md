---
baseline_commit: bf22e08
---

# Story 1.5: Stand up the single Amplify pipeline on the correct brand

Status: done

<!-- Note: Validation is optional. Run validate-create-story for quality check before dev-story. -->

## Story

As Carrie (builder),
I want exactly one correctly-branded deploy pipeline,
so that the site builds reproducibly with no leftover paths or branding from the retired project.

*Implements FR-11 (CAP-11) · AR-2, AR-3, AR-4 (AD-2, AD-10, AD-11).*

## Acceptance Criteria

**AC1 — `amplify.yml` is the single build truth (repo-side, dev-agent deliverable)**
**Given** the repo root
**When** `amplify.yml` is authored
**Then** `preBuild` runs `hugo mod get`, `build` runs `hugo --gc --minify` at a **pinned `HUGO_VERSION` (extended edition)**, and the artifact `baseDirectory` is `public/`
**And** the spec installs the toolchain Amplify's default image lacks (Go, for Hugo Modules; Hugo **extended**, for Hextra's SCSS) so the module-based build succeeds — it does not rely on Amplify's bundled Hugo.

**AC2 — Exactly one pipeline, production only (console-side = Carrie's manual step; repo-side = no second deploy path)**
**Given** the Amplify app
**When** configured (per the documented runbook)
**Then** it builds from source on the **default branch (`main`) only**, serves at `carriekroutil.com` via Route 53, has branch/PR auto-previews **off**, and has a single (production) environment
**And** there is **no** S3-sync GitHub Action and **no** GCP artifacts (`app.yaml` / `main.go` / `.gcloudignore`) or any second deploy path in the repo.

**AC3 — Correct brand, zero remnants**
**Given** the built site
**When** inspected
**Then** `baseURL`/`title` reflect carriekroutil.com with **zero** SheBytes / `code.shebytes.io` / ananke / quickstart remnants in tracked source
**And** no throwaway quickstart posts and no `ananke` theme are present anywhere in the repo.

**AC4 — The manual AWS setup is captured for future reference (user request)**
**Given** that AC2's app creation + custom domain are AWS-console actions an automated agent cannot perform
**When** the work is delivered
**Then** the exact Amplify + Route 53 console steps are documented in a durable in-repo runbook (`SETUP.md`, cross-linked from `README.md`) Carrie can follow now and re-reference later
**And** the runbook flags what is Story 1.5 scope vs. what later stories add (publish-by-push verification → 1.6; build-failure email → 1.7).

## Tasks / Subtasks

- [x] **Task 1 — Author `amplify.yml` at the repo root** (AC: 1)
  - [x] Create `amplify.yml` using the Amplify build-spec schema (`version: 1`, `frontend.phases.preBuild` / `.build`, `artifacts`, `cache`).
  - [x] Pin versions in `env.variables`: `HUGO_VERSION` to a current **extended-capable** release (**`0.163.3`** — matches the architecture Stack target "~0.163.x", ≥ the 0.146.0 Hextra floor) and `GO_VERSION` to a current Go (**`1.26.4`**, ≥ the 1.21 Modules floor). Added `HUGO_CACHEDIR` for build caching.
  - [x] **preBuild — install the toolchain Amplify's default image lacks:**
    - [x] Install **Go** `${GO_VERSION}` (Amplify's image ships **no Go**, so `hugo mod get` fails without it) and put it on `PATH`.
    - [x] Install **Hugo extended** `${HUGO_VERSION}` — **CRITICAL: download the `hugo_extended_${HUGO_VERSION}_linux-amd64.tar.gz` asset, NOT the plain `hugo_${HUGO_VERSION}_…`** (the latter is the standard edition and will fail Hextra's SCSS). Put it on `PATH`.
    - [x] Run `hugo mod get` (AD-11 requires it in `preBuild`; fetches/locks `github.com/imfing/hextra` per `go.mod`).
    - [x] Echo `hugo version` (confirm it prints `+extended`) and `go version` for build-log verification.
  - [x] **build:** `hugo --gc --minify` at the pinned extended Hugo.
  - [x] **artifacts:** `baseDirectory: public`, `files: ['**/*']`. **cache:** the Hugo cache dir.
  - [x] **Trim to this project:** dropped the Hugo-doc recipe's Node.js / `npm ci` / Dart Sass blocks — Hextra needs no Node, and extended Hugo's libsass covers SCSS. Spec is minimal: Go + Hugo extended + `hugo mod get` + build.

- [x] **Task 2 — Verify zero remnants and correct brand in tracked source** (AC: 2 repo-side, 3)
  - [x] `git grep -in 'shebytes\|code\.shebytes\|ananke\|quickstart'` over tracked site source (excluding `_bmad-output/` planning/spec docs that *describe the prohibition*) → **0 hits**.
  - [x] Confirmed absent: `app.yaml`, `main.go`, `.gcloudignore`, `.github/`, any S3-sync action, any second deploy path.
  - [x] Confirmed `hugo.toml` `baseURL = "https://carriekroutil.com/"` and `title = "carrie kroutil"`; `go.mod` requires only `github.com/imfing/hextra v0.12.3`; `content/` holds only `content/_index.md`.

- [x] **Task 3 — Write the AWS Amplify + Route 53 setup runbook** (AC: 4)
  - [x] Create **`SETUP.md`** at the repo root (deploy/infra runbook — keep `README.md` focused on local dev; cross-link the two). Document, as a numbered checklist Carrie can tick:
    1. **Prereqs** — repo is already on GitHub at `CarrieKroutil/carriekroutil-site` (PRs #1–#3 merged there); `amplify.yml` committed to `main`.
    2. **Create the Amplify app** — AWS Console → Amplify → *Create new app* → *GitHub* → authorize → select `CarrieKroutil/carriekroutil-site`, branch **`main` only**.
    3. **Build settings** — Amplify auto-detects the repo-root `amplify.yml`; use it, do **not** override the build spec in the console (AD-11: the file is the single source of build truth).
    4. **Single pipeline, production only** — do not connect additional branches; disable branch auto-detection / pattern-based branch builds; **turn pull-request previews off** (AD-2: exactly one environment).
    5. **Custom domain via Route 53** — Amplify → *Domain management* → add `carriekroutil.com`; because the domain's hosted zone is in Route 53 on the same account, Amplify provisions the ACM cert + DNS records automatically; map root (and `www` → root redirect as desired).
    6. **First deploy** — trigger/await the initial build; confirm the site serves over HTTPS at `https://carriekroutil.com/`.
  - [x] Add a short **scope note** in `SETUP.md`: publish-by-push verification is **Story 1.6**; native **email-on-build-failure** notifications are **Story 1.7** (don't enable/build those here).
  - [x] Add a one-line **"Deploying"** pointer in `README.md` linking to `SETUP.md`.

- [ ] **Task 4 — Carrie completes the console setup (MANUAL — not done by the agent)** (AC: 2 console-side) ⬅️ **OPEN: requires Carrie in the AWS console**
  - [x] **This is Carrie's manual AWS action, not an automated-agent task** — surfaced explicitly in the Dev Agent Record + `SETUP.md`; AC2 console items are NOT claimed done by the agent.
  - [ ] **Carrie:** execute the AC2 console checklist (create app, `main`-only, PR previews off, single env, Route 53 domain) via `SETUP.md`, then tick here. *(Left open intentionally — the agent cannot perform AWS-console actions.)*

- [x] **Task 5 — Verify the build the way Amplify will** (AC: 1, 3)
  - [x] Ran a clean local production build: `hugo --gc --minify` → exit 0, `public/` emitted (23 files), no errors/warnings — the same command `amplify.yml`'s `build` phase runs.
  - [x] Confirmed `amplify.yml` is valid YAML (`ruby -ryaml`).
  - [x] Recorded the exact commands and outcomes in the Dev Agent Record.

### Review Findings

*Code review 2026-06-23 (3-layer adversarial: Blind Hunter, Edge Case Hunter, Acceptance Auditor). 0 decision-needed · 2 patch · 0 defer · 4 dismissed. Acceptance Auditor: clean pass on all ACs. Site is live at carriekroutil.com, built by the new Amplify app from this `amplify.yml` (old manual-deploy app deleted first) — happy path empirically validated.*

- [x] [Review][Patch] Use `curl -fsSLO` (add `-f`) for the Go/Hugo downloads so a bad version pin fails fast instead of writing a 404 body that dies cryptically at `tar` [amplify.yml — 2 curl calls in preBuild] — **applied**
- [x] [Review][Patch] Harden `SETUP.md`: recommend mapping `www` → apex (not "if desired" — bare `www.carriekroutil.com` otherwise fails to resolve); add a "if the first build fails, read the build log for the failing command" pointer; note in the upgrade section to keep `go.mod`'s `go` directive ≤ the installed `GO_VERSION` (avoid silent toolchain auto-download) [SETUP.md] — **applied**

*Dismissed (recorded for traceability):* (1) **`export PATH` cross-phase persistence** (blind+edge) — empirically refuted: live site built from this spec; canonical gohugo.io pattern; Amplify runs frontend phases in one shell. (2) **`HUGO_CACHEDIR ${PWD}` vs relative cache path** — canonical gohugo.io pattern; worst case a cache no-op (perf), not a build break. (3) **`hugo mod get` no-arg** — valid (updates all configured modules; Hugo also resolves modules at build). (4) **`go.sum` uncommitted** — false positive: `go.sum` IS tracked with the Hextra `v0.12.3` checksum lock (reviewer couldn't see it from the diff).

## Dev Notes

### Scope guardrails (prevent overbuild / scope bleed)
Story 1.5 delivers **the pipeline definition + brand hygiene + the setup runbook**. Explicitly OUT of scope (each is its own story — do not pre-build):
- **Publish-by-push end-to-end verification** (push a post → see it live) → **Story 1.6**.
- **Build-failure email notifications** (Amplify native email-on-failure; AD-12/FR-17) → **Story 1.7**. Do not enable or document-as-done here beyond a forward pointer.
- Any content/posts, partials, layout overrides → Epics 2–4.
- The dev agent **cannot perform AWS-console actions**. It authors `amplify.yml`, cleans/verifies the repo, and writes `SETUP.md`; the actual Amplify app + Route 53 domain (AC2 console items) are Carrie's manual steps. Be honest about this split in the Dev Agent Record.

### Environment / current repo state (verified at story authoring, 2026-06-23)
- Local toolchain (from Story 1.1): Hugo `v0.151.2+extended` (darwin/arm64), Go `1.25.3`. Local `hugo --gc --minify` builds clean. [Source: story 1.1 Dev Agent Record]
- Repo **is** on GitHub (`CarrieKroutil/carriekroutil-site`) — PRs #1, #2, #3 merged on `main` (`git log`); default branch is `main`. So AC2's "connect repo" is *connect existing GitHub repo*, not *create remote*.
- **Already clean** (re-verify, don't assume): no `app.yaml` / `main.go` / `.gcloudignore`, no `.github/`, no S3-sync action; `hugo.toml` `baseURL`/`title` are correct; `go.mod` pins only Hextra `v0.12.3`; the only `shebytes|ananke|quickstart` grep hits are planning docs that *forbid* those strings. The dev task is to **verify and lock this in**, plus add `amplify.yml` + `SETUP.md`.
- **Casing note (from 1.1 review):** GitHub org/repo is `CarrieKroutil/carriekroutil-site` (mixed case) while the Go module path is lowercase `github.com/carriekroutil/carriekroutil-site`. No build impact (self-module is never fetched; GitHub URLs are case-insensitive). When wiring Amplify → GitHub, just pick the repo from the list. [Source: story 1.1 review, dismissed finding #3]

### Key architecture rules binding this story
- **AD-2 / AR-4 — Exactly one deploy pipeline, production only.** AWS Amplify is the *only* pipeline; builds from source on push to the **default branch only**; serves `carriekroutil.com` via Route 53; branch/PR auto-previews **off**; one environment (production). No S3-sync action, no GCP artifacts, no second deploy path. [Source: ARCHITECTURE-SPINE.md AD-2; epics.md AR-4]
- **AD-10 / AR-2 — Versions pinned, upgrades deliberate.** Hugo extended pinned via the Amplify build env (`HUGO_VERSION`); Hextra locked in `go.mod` (`v0.12.3`). Never `latest`/floating; upgrades only on purpose. [Source: ARCHITECTURE-SPINE.md AD-10]
- **AD-11 / AR-3 — `amplify.yml` is the single source of build truth.** Repo-root `amplify.yml`: `preBuild` fetches the Hextra module (`hugo mod get`), `build` runs Hugo extended (`hugo --gc --minify`) at the pinned `HUGO_VERSION`, artifacts = `public/`. Don't duplicate/override build config in the console. [Source: ARCHITECTURE-SPINE.md AD-11]
- **AD-12 — Build failure must notify (forward pointer only).** Native email-on-failure + "successful-but-no-new-post is distinguishable from failure" is **Story 1.7**, not 1.5. [Source: ARCHITECTURE-SPINE.md AD-12]
- **Conventions — branding.** `hugo.toml` `baseURL = https://carriekroutil.com/` and `title` reflect carriekroutil.com with zero SheBytes/`code.shebytes.io`/ananke/quickstart remnants. [Source: ARCHITECTURE-SPINE.md §Consistency Conventions — Branding]
- **Deferred (not this repo's job):** disabling the *old* `~/code/quickstart` S3-sync action + removing GCP artifacts from the retired project is Carrie's one-time external cleanup; FR-11 only requires they are **not carried into this repo** (they aren't). [Source: ARCHITECTURE-SPINE.md §Deferred — "Legacy deploy cleanup"]

### Verified `amplify.yml` recipe (web-checked 2026-06-23 against gohugo.io/host-and-deploy/host-on-aws-amplify/)
Authoritative source: the official Hugo "Host on AWS Amplify" doc. Two project-specific corrections to its canonical recipe:
1. **Use the EXTENDED Hugo asset.** The doc downloads `hugo_${HUGO_VERSION}_linux-amd64.tar.gz` (standard). This project requires **extended** (Hextra SCSS) → download `hugo_extended_${HUGO_VERSION}_linux-amd64.tar.gz`. Verify the build log prints `hugo ... +extended`.
2. **Drop Node.js + Dart Sass.** Hextra is pure Hugo Modules — no `package.json`/npm, and extended Hugo's libsass covers SCSS — so omit the doc's Node and Dart Sass install blocks. Keep only: Go install → Hugo-extended install → `hugo mod get` → build.

Why the installs are mandatory (the two classic Amplify-Hugo failure modes, both web-confirmed):
- Amplify's default image ships **Hugo 0.75.1** (Sept 2020) — far below the 0.146.0 Hextra floor → must install a pinned Hugo.
- Amplify's image ships **no Go toolchain** → `hugo mod get` / Module builds fail unless Go is installed in `preBuild`.

Target shape (the dev agent finalizes exact syntax; `amd64` is correct — Amplify's Linux build image is x86_64):
```yaml
version: 1
env:
  variables:
    HUGO_VERSION: 0.163.3      # extended; ≥0.146.0 Hextra floor; matches Stack "~0.163.x"
    GO_VERSION: 1.26.4         # ≥1.21 Hugo Modules floor
    HUGO_CACHEDIR: ${PWD}/.cache/hugo
frontend:
  phases:
    preBuild:
      commands:
        - mkdir -p "${HOME}/.local"
        # Go (Amplify image has none → needed for Hugo Modules)
        - curl -sLO "https://go.dev/dl/go${GO_VERSION}.linux-amd64.tar.gz"
        - tar -C "${HOME}/.local" -xf "go${GO_VERSION}.linux-amd64.tar.gz"
        - export PATH="${HOME}/.local/go/bin:${PATH}"
        # Hugo EXTENDED (note the _extended_ in the asset name)
        - curl -sLO "https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.tar.gz"
        - mkdir -p "${HOME}/.local/hugo"
        - tar -C "${HOME}/.local/hugo" -xf "hugo_extended_${HUGO_VERSION}_linux-amd64.tar.gz"
        - export PATH="${HOME}/.local/hugo:${PATH}"
        - hugo version   # must show +extended
        - go version
        - hugo mod get   # AD-11: fetch/lock Hextra in preBuild
    build:
      commands:
        - hugo --gc --minify
  artifacts:
    baseDirectory: public
    files:
      - '**/*'
  cache:
    paths:
      - .cache/hugo/**/*
```

### Anti-patterns to avoid (disaster prevention)
- ❌ Downloading plain `hugo_${VERSION}` (standard) → Hextra SCSS fails. ✅ `hugo_extended_${VERSION}`.
- ❌ Relying on Amplify's bundled Hugo (0.75.1) or assuming Go is present. ✅ Install both, pinned, in `preBuild`.
- ❌ `HUGO_VERSION: latest` / floating Go. ✅ Exact pins (AD-10).
- ❌ Overriding the build spec in the Amplify console. ✅ The repo-root `amplify.yml` is the single truth (AD-11).
- ❌ Connecting extra branches or enabling PR previews. ✅ `main` only, previews off, one environment (AD-2).
- ❌ Enabling email-on-failure / building 1.7 work here, or claiming the AWS-console steps were done by the agent. ✅ Forward-pointer to 1.7; honest dev/console split.
- ❌ Adding throwaway/quickstart posts or any `ananke`/SheBytes string. ✅ Verify zero remnants.
- ❌ Bloating `README.md` with infra; ✅ infra runbook in `SETUP.md`, cross-linked.

### Testing / verification standards
No unit-test framework applies. "Tests" = build + static verification, run and recorded (don't claim without running):
1. `hugo --gc --minify` exits 0 and writes `public/` (the exact `amplify.yml build` command).
2. `amplify.yml` parses as valid YAML.
3. `git grep -in 'shebytes\|code\.shebytes\|ananke\|quickstart'` → hits only in `_bmad-output/` planning/spec docs that forbid them; none in site source.
4. `ls` confirms no `app.yaml`/`main.go`/`.gcloudignore`/`.github/`; `go.mod` shows only Hextra `v0.12.3`.
5. `hugo.toml` `baseURL`/`title` correct.
6. `SETUP.md` exists with the 6-step runbook + scope note; `README.md` links it.
(The Amplify build itself is verified on Carrie's first deploy per `SETUP.md` — outside the agent's reach.)

### Project Structure Notes
- Adds `amplify.yml` (repo root — the deferred Story 1.5 artifact called out in the Structural Seed and in Story 1.1's "deferred-as-designed") and `SETUP.md` (repo root). No conflicts with existing structure; `public/`/`resources/` stay git-ignored. This is exactly where the Structural Seed places `amplify.yml`. [Source: ARCHITECTURE-SPINE.md §Structural Seed]

### References
- [Source: _bmad-output/planning-artifacts/epics.md#Story 1.5: Stand up the single Amplify pipeline on the correct brand] — story statement + ACs
- [Source: _bmad-output/planning-artifacts/epics.md#Additional Requirements] — AR-2, AR-3, AR-4 definitions
- [Source: ARCHITECTURE-SPINE.md#AD-2] — exactly one pipeline, default-branch-only, Route 53, previews off
- [Source: ARCHITECTURE-SPINE.md#AD-10] — pinned versions (`HUGO_VERSION`, Hextra in `go.mod`)
- [Source: ARCHITECTURE-SPINE.md#AD-11] — `amplify.yml` single build truth (`hugo mod get` → `hugo --gc --minify` → `public/`)
- [Source: ARCHITECTURE-SPINE.md#AD-12] — build-failure notification is Story 1.7 (forward pointer only)
- [Source: ARCHITECTURE-SPINE.md#Stack] — Hugo extended ~0.163.x pin, Hextra v0.12.3, Amplify build-from-source, Route 53
- [Source: ARCHITECTURE-SPINE.md#Structural Seed] — `amplify.yml` at repo root
- [Source: ARCHITECTURE-SPINE.md#Consistency Conventions] — branding, zero remnants
- [Source: ARCHITECTURE-SPINE.md#Deferred] — legacy deploy cleanup is external, not this repo
- [Source: https://gohugo.io/host-and-deploy/host-on-aws-amplify/] — canonical Amplify build spec (corrected here for extended + trimmed for Hextra)
- [Source: story 1.1 Dev Agent Record] — local toolchain, deferred-to-1.5 note, repo-casing note

## Dev Agent Record

### Agent Model Used

claude-opus-4-8[1m] (Opus 4.8, 1M context)

### Debug Log References

- Branch `story-1.5-amplify-pipeline` off `main` @ `bf22e08`.
- Repo-clean verification (AC2 repo-side / AC3): `git grep -in 'shebytes\|code.shebytes\|ananke\|quickstart'` over tracked site source (excluding `_bmad-output/`, `_bmad/`, `.claude/`) → **0 hits**. No `app.yaml` / `main.go` / `.gcloudignore` / `.github/` present. `go.mod` requires only `github.com/imfing/hextra v0.12.3`. `content/` holds only `content/_index.md` (no throwaway/quickstart posts). `hugo.toml`: `baseURL = "https://carriekroutil.com/"`, `title = "carrie kroutil"`.
- `amplify.yml` authored; validated as YAML (`ruby -ryaml YAML.load_file` → valid).
- Ran the exact `amplify.yml` build command locally: `hugo mod get` then `hugo --gc --minify` → **exit 0**, `public/` emitted (23 files), no errors/warnings. Local Hugo `v0.151.2+extended` (the pinned build Hugo is `0.163.3` extended, installed in `preBuild`).

### Completion Notes List

- **AC1 met (repo-side):** `amplify.yml` at repo root pins `HUGO_VERSION=0.163.3` (extended) and `GO_VERSION=1.26.4`; `preBuild` installs Go + **Hugo extended** (the two things Amplify's default image lacks — no Go, and Hugo 0.75.1) then runs `hugo mod get`; `build` runs `hugo --gc --minify`; artifacts `baseDirectory: public`. Caching via `HUGO_CACHEDIR`. Trimmed the Hugo-doc recipe's Node.js + Dart Sass blocks (Hextra needs neither; extended Hugo's libsass covers SCSS).
- **Critical correction applied:** download asset is `hugo_extended_${HUGO_VERSION}_…`, **not** the doc's standard `hugo_${HUGO_VERSION}_…` (standard edition would fail Hextra SCSS). `preBuild` echoes `hugo version` so the build log confirms `+extended`.
- **AC2 — repo-side met; console-side is Carrie's manual task (not done by agent):** there is no second deploy path in the repo (no S3-sync action, no GCP artifacts). Creating the Amplify app, restricting to `main`-only, disabling PR previews/extra branches, and wiring the Route 53 custom domain are **AWS-console actions an automated agent cannot perform** — documented as a tickable runbook in `SETUP.md` for Carrie to execute. **These console items are NOT claimed complete here.**
- **AC3 met:** zero SheBytes/`code.shebytes.io`/ananke/quickstart remnants in site source; no throwaway posts; no `ananke` theme; brand `baseURL`/`title` correct.
- **AC4 met:** `SETUP.md` created (6-step Amplify+Route 53 console runbook + automated-vs-manual table + scope note pointing publish-by-push → 1.6 and email-on-failure → 1.7 + a deliberate-upgrade note); `README.md` cross-links it (intro line + project-layout entry) and its stale "build pipeline lands in a later story" line is updated.
- **Deferred-as-designed (not this story):** publish-by-push verification → 1.6; Amplify email-on-failure / no-new-post-distinguishable-from-failure (AD-12/FR-17) → 1.7; retiring the *old* `~/code/quickstart` S3-sync + GCP artifacts is Carrie's external one-time cleanup (FR-11 only requires they're not carried into this repo — they aren't).
- **Pin provenance:** `HUGO_VERSION=0.163.3` / `GO_VERSION=1.26.4` are the values in the official gohugo.io Amplify doc (web-checked 2026-06-23) and match the architecture Stack target "~0.163.x"; both clear their floors (Hextra 0.146.0; Modules Go 1.21).

### File List

- `amplify.yml` (new) — AWS Amplify build spec; the single deploy pipeline (AD-2, AD-11)
- `SETUP.md` (new) — AWS Amplify + Route 53 one-time console runbook (AC4)
- `README.md` (modified) — deploy intro line → SETUP.md; `amplify.yml` added to project layout

## Change Log

| Date | Change |
| --- | --- |
| 2026-06-23 | Story 1.5 created (context-engineered): `amplify.yml` spec (Go + Hugo-extended install, `hugo mod get`, `hugo --gc --minify`, `public/`), remnant/brand verification, and `SETUP.md` Amplify+Route 53 runbook. Amplify recipe web-verified against gohugo.io and corrected for extended edition. Status → ready-for-dev. |
| 2026-06-23 | Story 1.5 implemented on branch `story-1.5-amplify-pipeline`: authored `amplify.yml` (extended-edition build spec), wrote `SETUP.md` runbook, cross-linked from `README.md`, verified repo clean (0 remnants, no GCP/second-pipeline artifacts) and `hugo --gc --minify` exit 0. Repo-side ACs (1, 3, 4) done; AC2 console items (Amplify app + Route 53 domain) left as Carrie's manual task per Task 4. Status → review. |
| 2026-06-23 | Code review (3-layer adversarial). Acceptance Auditor: clean pass on all ACs. 2 patches applied (`curl -fsSLO` fail-fast in `amplify.yml`; `SETUP.md` hardening — www→apex, first-build-failure pointer, go-directive note), 4 dismissed (headline `PATH`-cross-phase finding empirically refuted — live site built from this spec after old app deleted). Build re-verified exit 0. Status → done. |
