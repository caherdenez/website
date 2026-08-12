# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Cesar Augusto's professional website (`https://cesarh.co/`), built with Hugo as a
dependency-free static site: plain HTML templates and plain CSS, no JS toolchain,
no package manager, no theme. Requires Hugo 0.158.0+ (Hugo Extended not required
for the current templates, but CI installs the extended build).

## Commands

```sh
hugo server          # local dev server at http://localhost:1313/
hugo --minify         # production build, output written to public/
git diff --check      # run before considering any change complete
```

There is no lint/test suite, package manager, or build step beyond Hugo itself.
Run `hugo --minify` after any template, content, config, or asset change to
confirm the site still builds.

`public/` and `dist/` are both gitignored build output (`dist/` is leftover from
a prior Astro implementation and is not part of the current site — do not treat
it as source). `.husky/` is also gitignored and unused since there is no
package.json.

## Architecture

- `hugo.toml` — site config: baseURL, title, params (site description used in
  the home template's meta description).
- `layouts/home.html` — the entire site is currently one template (the home
  page). Nav, hero, and a three-item "Focus" grid (Backend / Cloud / Applied AI).
- `static/css/main.css` — all styling, plain CSS, no preprocessor.
- `.github/workflows/hugo.yaml` — deploys to GitHub Pages on push to `master`.
  Pins the Hugo Extended version independently in the workflow's `HUGO_VERSION`
  env var (currently 0.164.0) — bump this alongside local Hugo upgrades to keep
  CI and local builds in sync.

## Deploy

- `.github/workflows/hugo.yaml` deploys to GitHub Pages (custom domain
  `cesarh.co`) automatically on push to `master`.
- The workflow builds with `--baseURL "${{ steps.pages.outputs.base_url }}"`,
  resolved at build time from the repo's current GitHub Pages settings. If
  those settings change (e.g. the custom domain is added/updated) without a
  new push, the previously-deployed HTML keeps the stale `baseURL` — this
  breaks asset paths (CSS/JS 404s) until the workflow re-runs. That was the
  root cause the one time `cesarh.co` loaded with no styles.
- To force a redeploy without a new commit, use GitHub CLI against remote
  `master`:
  ```sh
  gh auth status
  gh workflow run hugo.yaml --repo caherdenez/website --ref master
  gh run list --repo caherdenez/website --workflow=hugo.yaml --limit 5
  gh run watch <run-id> --repo caherdenez/website --exit-status
  ```
  If needed, authenticate with `gh auth login`. Obtain `<run-id>` from
  `gh run list`; inspect failures with
  `gh run view <run-id> --repo caherdenez/website --log-failed`. A manual run
  deploys only content already present on remote `master`; local and unmerged
  branch changes are not included. A push or merged PR to `master` triggers the
  workflow automatically.
- Keep the workflow actions on Node.js 24-compatible majors:
  `actions/checkout@v7`, `actions/configure-pages@v6`,
  `actions/upload-pages-artifact@v5`, and `actions/deploy-pages@v5`. Older
  Node.js 20 actions currently produce a warning and are forced onto Node.js 24
  by GitHub-hosted runners, but should be upgraded before compatibility is
  removed.
- Per "Working conventions" below: do not commit, push, publish, or deploy
  (including a manual `gh workflow run`) unless explicitly requested. Verify
  `https://cesarh.co/` and its asset URLs after a deploy rather than assuming
  success.

## Canonical content source

Professional facts, positioning, skills, and verified projects are maintained in
the **sibling `../source/` directory**, not in this repo. Before adding or
changing any public claim on the site (experience, skills, projects, bio):

- Treat `../source/` as canonical. Do not introduce facts absent from it unless
  the user explicitly confirms them and `../source/` is updated first.
- If site content conflicts with `../source/`, flag the conflict instead of
  silently picking a version.
- Update `../source/CHANGELOG.md` when canonical facts or positioning change.

## Working conventions

- Keep the site dependency-free unless a concrete requirement justifies adding a
  theme, JS toolchain, package manager, or external service.
- Prefer Hugo templates, semantic HTML, and plain CSS.
- `feat/astro` is a separate historical implementation (superseded by this Hugo
  site). Do not copy or merge it in unless explicitly requested.
- Primary positioning to preserve: Senior Backend / Full-Stack Engineer focused
  on Python, cloud, distributed systems, reliability, and AI-enabled
  applications. Lead with engineering capabilities and verified evidence over
  generic promotional claims.
- Preserve the established visual language unless a redesign is explicitly
  requested.
- Do not commit, push, publish, or deploy unless explicitly requested.
- Preserve unrelated worktree changes.
