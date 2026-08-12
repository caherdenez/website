# Website Repository Guidelines

## Repository Purpose

This repository contains Cesar's professional website. Its audience includes
technical recruiters, hiring managers, engineers, and readers of future technical
articles or case studies.

## Canonical Content

- Use `../source/` as the canonical source for professional facts, positioning,
  skills, and verified projects.
- Do not introduce facts that are absent from `../source/` unless Cesar explicitly
  confirms them and the canonical source is updated first.
- Flag conflicts with `../source/` instead of silently choosing one version.
- Do not describe planned, draft, or undocumented projects as completed or
  production-ready work.
- Update `../source/CHANGELOG.md` when canonical facts or positioning change.

## Current Architecture

- The active implementation uses Hugo and starts as a minimal static landing page.
- Keep the site dependency-free unless a concrete requirement justifies adding a
  theme, JavaScript toolchain, package manager, or external service.
- Prefer Hugo templates, semantic HTML, and plain CSS for the current scope.
- Keep configuration in `hugo.toml`, templates in `layouts/`, static assets in
  `static/`, and generated output out of Git.
- Treat `feat/astro` as a separate historical implementation. Do not copy or merge
  it into the Hugo branch unless explicitly requested.

## Content and Design

- Maintain the primary positioning: Senior Backend / Full-Stack Engineer focused
  on Python, cloud, distributed systems, reliability, and AI-enabled applications.
- Lead with engineering capabilities and verified evidence rather than generic
  promotional claims or exhaustive technology lists.
- The website may contain longer technical explanations, project documentation,
  writing, and case studies than the GitHub profile.
- Keep pages accessible, semantic, responsive, and usable on desktop and mobile.
- Preserve the established visual language unless a redesign is explicitly
  requested.

## Validation and Git

- Run `hugo --minify` after template, content, configuration, or asset changes.
- Run `git diff --check` and inspect the complete diff before considering changes
  complete.
- Follow Conventional Commits 1.0.0:
  `https://www.conventionalcommits.org/en/v1.0.0/`.
- Format commit subjects as `<type>[optional scope][!]: <description>`. Use
  `feat` for user-visible capabilities, `fix` for user-visible bug fixes,
  `perf` for performance improvements, `docs` for documentation-only changes,
  `refactor` for behavior-preserving code changes, `test` for tests, `build` for
  build-system changes, `ci` for workflow changes, and `chore` for maintenance
  that fits no more specific type. Mark breaking changes with `!` or a
  `BREAKING CHANGE:` footer.
- Choose the commit type from the change's purpose, not from whether it deploys.
  For example, `chore: update portrait` can affect the published site, while
  `feat: document future idea` may not.
- Do not commit, push, publish, or deploy unless explicitly requested.
- Preserve unrelated worktree changes.

## Deploy

- `.github/workflows/hugo.yaml` deploys to GitHub Pages (custom domain
  `cesarh.co`) automatically when a push to `master` changes deployable source:
  `hugo.toml`, `layouts/**`, `content/**`, or `static/**`.
- Changes limited to documentation, repository metadata, agent instructions, or
  the workflow itself do not deploy automatically. This normally covers
  `docs`, `ci`, and non-site `chore` commits. A commit of any type still deploys
  when it changes a deployable path; path impact is the source of truth.
- Use `workflow_dispatch` for an intentional redeploy or for a deploy-related
  change outside the automatic path set. Do not add `[skip deploy]` parsing or
  infer deployment solely from commit types.
- The workflow builds with `--baseURL "${{ steps.pages.outputs.base_url }}"`,
  resolved at build time from the repo's current GitHub Pages settings. If
  those settings change (e.g. the custom domain is added or updated) without a
  new push, the last-deployed HTML keeps the stale `baseURL` — this breaks
  asset paths (CSS/JS 404s) until the workflow re-runs.
- To force a redeploy without a new commit, first verify GitHub CLI access,
  dispatch the workflow explicitly from remote `master`, and watch the run:
  ```sh
  gh auth status
  gh workflow run hugo.yaml --repo caherdenez/website --ref master
  gh run list --repo caherdenez/website --workflow=hugo.yaml --limit 5
  gh run watch <run-id> --repo caherdenez/website --exit-status
  ```
  If authentication is missing, use `gh auth login`. Obtain `<run-id>` from
  `gh run list`; inspect failures with
  `gh run view <run-id> --repo caherdenez/website --log-failed`. Dispatching
  `master` deploys only content already present on remote `master`; local or
  unmerged branch changes are not included. A push or merged PR to `master`
  triggers the workflow only when it includes a deployable path.
- Keep third-party actions on Node.js 24-compatible major versions. The current
  migration targets are `actions/checkout@v7`, `actions/configure-pages@v6`,
  `actions/upload-pages-artifact@v5`, and `actions/deploy-pages@v5`; older
  Node.js 20 actions may run with a warning while GitHub forces Node.js 24, but
  should be upgraded before compatibility is removed.
- A manual workflow dispatch still counts as a deploy. Only do it when
  explicitly requested, and verify `https://cesarh.co/` and its asset URLs
  afterward rather than assuming success.
