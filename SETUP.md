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
   and publishes `public/`. The app gets a default `*.amplifyapp.com` URL first. *If the build
   fails, read the build log top-to-bottom for the first failing command (usually a version
   pin that 404s, or a network blip on `hugo mod get`) and fix that before retrying.*

5. **Custom domain via Route 53.** Amplify → **Hosting** → **Custom domains** → **Add domain**
   → `carriekroutil.com`. Because the hosted zone is in Route 53 on the same account, Amplify
   provisions the ACM certificate and writes the DNS records automatically. Map the apex
   `carriekroutil.com` **and** add `www` → apex redirect (so `www.carriekroutil.com` resolves
   rather than erroring — a common entry point). Wait for the domain to activate (cert
   validation can take a few minutes to tens of minutes).

6. **Verify.** Visit **https://carriekroutil.com/** — it should serve the built site over HTTPS.

## Publishing a post — the push-to-publish path (Story 1.6)

Once the pipeline above is live, publishing is just:

1. Write a markdown post under `content/posts/` (see [README → Writing a post](README.md#writing-a-post)).
2. Commit and `git push` to `main`.

Pushing to `main` automatically triggers an Amplify build — the Amplify GitHub App notifies
Amplify on every push to the connected branch. When the build succeeds (a few minutes later),
Amplify swaps in the new `public/` and the post is live at its Post Page and in the Post Stream
at `/posts/`. No CMS login, web editor, or manual file copy/upload is involved: the whole path
is **write markdown → git push**.

Drafts (`draft: true` front matter) are withheld from the production build, so pushing a draft
deploys everything *except* that post — preview drafts locally with `hugo server -D` first.

## Build-failure alerts (Story 1.7)

A failed publish must never silently leave the old site up without Carrie knowing. Amplify's
native behavior plus one notification toggle covers this:

- **A failed build keeps the prior good site live.** Amplify swaps `public/` in only on a
  *successful* build; a failed build leaves the last good deploy serving. *(Native — nothing
  to configure.)*
- **Email on failure** — enable once in the console *(manual, one-time — only Carrie can do
  this in the AWS account)*:
  1. AWS Console → **Amplify** → this app → **App settings** → **Notifications**.
  2. **Add notification** → enter Carrie's email → save. Amplify creates the SNS topic and
     sends a confirmation email — **click the confirm link** or notifications won't send.
  3. Keep the **failure** notification on (start/success can be left off).
- **Distinguishing outcomes.** A failed build shows **red** in the Amplify console *and* sends
  the failure email; a successful build shows **green** and (with success notifications off)
  sends nothing. So "failed" is always distinguishable from "succeeded" — including the case
  where a build succeeds but published no new post (e.g. the post was left `draft`): that's
  still a green, no-email success, clearly not a failure.

## Scope — what this setup does and doesn't cover

- **In scope (Story 1.5):** the single Amplify pipeline, build spec, correct brand, custom domain.
- **Story 1.6 — Publish by push:** the push-to-publish path, documented above. Proven live —
  every post under `content/posts/` reached carriekroutil.com via `write markdown → git push`.
- **Story 1.7 — Build-failure alerts:** documented above. Failed builds keep the prior good
  site live (native); enabling email-on-failure is the one manual console toggle still on
  Carrie — follow the Notifications steps above.

## Upgrading Hugo / Go later (deliberate, never automatic)

Versions are pinned in [`amplify.yml`](amplify.yml) (`HUGO_VERSION`, `GO_VERSION`) and the
Hextra theme is locked in `go.mod`. To upgrade: bump the pin(s) and/or run
`hugo mod get -u github.com/imfing/hextra`, re-test locally (`hugo --gc --minify`), then push.
Never float to `latest`.

When bumping Go, keep `go.mod`'s `go` directive **≤** the installed `GO_VERSION`. If the
directive ever exceeds the installed toolchain, modern Go silently downloads a matching
toolchain over the network mid-build — an unpinned fetch that defeats the point of pinning.
