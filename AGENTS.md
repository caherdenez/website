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
- Do not commit, push, publish, or deploy unless explicitly requested.
- Preserve unrelated worktree changes.
