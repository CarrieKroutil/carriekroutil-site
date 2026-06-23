# Deploy setup — AWS Amplify + Route 53

The one-time infrastructure setup for carriekroutil.com. For local authoring/preview,
see [README.md](README.md) instead.

**The pipeline (one rule):** exactly **one** AWS Amplify app builds this repo from source
on push to **`main` only**, and serves it at **carriekroutil.com** via Route 53. There is
no other deploy path — no S3-sync action, no GCP App Engine. The build itself is defined
once in [`amplify.yml`](amplify.yml) at the repo root; the console must **not** override it.

## What's automated vs. manual

| Piece | Where | Status |
| --- | --- | --- |
| Build spec (Hugo extended + Go install, `hugo mod get`, `hugo --gc --minify`, `public/`) | [`amplify.yml`](amplify.yml) in this repo | ✅ committed |
| Creating the Amplify app + connecting GitHub + custom domain | AWS Console (steps below) | ⬜ manual, one-time |

The console steps below can only be done by Carrie in the AWS account — they're a checklist
to follow once, then keep for reference.

## Prerequisites

- This repo is on GitHub at **`CarrieKroutil/carriekroutil-site`**, default branch **`main`**.
- [`amplify.yml`](amplify.yml) is committed to `main`.
- The **carriekroutil.com** domain is registered and its hosted zone lives in **Route 53**
  in the same AWS account you'll create the Amplify app in (lets Amplify auto-provision the
  TLS cert + DNS records).

## One-time console setup

1. **Create the app and connect GitHub** (an authorization flow via the Amplify GitHub App):
   1. AWS Console → **Amplify** → **Create new app** (a.k.a. *Deploy an app*).
   2. On *Choose source code provider*, pick the **GitHub** tile → **Next**/**Continue**.
   3. A **github.com** tab opens asking to **authorize AWS Amplify** → choose **Authorize**.
   4. You're prompted to **install the Amplify GitHub App** in your GitHub account →
      select your account → choose **Only select repositories** → pick
      **`carriekroutil-site`** → **Install** / **Save**. (Selecting just this repo is
      tighter than granting access to *All repositories*.) *First connection only — once
      the GitHub App is installed on the account, later apps skip straight to the next step.*
   5. Back in the Amplify console on *Add repository branch*, select **repository =
      `CarrieKroutil/carriekroutil-site`** and **branch = `main`** (and only `main`) →
      **Next**.

2. **Use the repo's build spec.** Amplify auto-detects the repo-root `amplify.yml`. Accept it
   as-is — **do not** edit/override the build commands in the console. `amplify.yml` is the
   single source of build truth; overriding it in the console creates drift.

3. **Keep it a single production pipeline.**
   - Do **not** connect any other branches.
   - Disable branch auto-detection / pattern-based branch builds (no `feature/*` etc.).
   - Turn **pull-request previews OFF**.
   - One environment: production. (Local `hugo server` is the only pre-publish preview.)

4. **First deploy.** Save and deploy. Watch the build log — `preBuild` should print a Hugo
   version line containing **`+extended`** and a Go version; `build` runs `hugo --gc --minify`
   and publishes `public/`. The app gets a default `*.amplifyapp.com` URL first.

5. **Custom domain via Route 53.** Amplify → **Hosting** → **Custom domains** → **Add domain**
   → `carriekroutil.com`. Because the hosted zone is in Route 53 on the same account, Amplify
   provisions the ACM certificate and writes the DNS records automatically. Map the apex
   `carriekroutil.com` (and, if desired, `www` → apex redirect). Wait for the domain to
   activate (cert validation can take a few minutes to tens of minutes).

6. **Verify.** Visit **https://carriekroutil.com/** — it should serve the built site over HTTPS.

## Scope — what this setup does and doesn't cover

- **In scope (Story 1.5):** the single Amplify pipeline, build spec, correct brand, custom domain.
- **Story 1.6 — Publish by push:** verifying that adding a markdown post and pushing `main`
  puts it live with no further steps. (The pipeline above is what makes that work.)
- **Story 1.7 — Build-failure alerts:** enabling Amplify's native **email-on-failure** so a
  broken push is never silent, and confirming a failed build keeps the prior good site live.
  Set that up when you do Story 1.7 — not part of this initial setup.

## Upgrading Hugo / Go later (deliberate, never automatic)

Versions are pinned in [`amplify.yml`](amplify.yml) (`HUGO_VERSION`, `GO_VERSION`) and the
Hextra theme is locked in `go.mod`. To upgrade: bump the pin(s) and/or run
`hugo mod get -u github.com/imfing/hextra`, re-test locally (`hugo --gc --minify`), then push.
Never float to `latest`.
