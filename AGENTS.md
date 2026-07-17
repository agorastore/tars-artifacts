# Artifact repository guide

This repository contains durable, static pull-request artifacts for Agorastore's TARS projects. Artifacts must always be plain static HTML pages. It deliberately has no application runtime, package manager, or build step: GitHub Pages deploys the repository root exactly as it is committed.

## Layout

```text
artifacts/
  {repository}/
    pr-{number}/
      index.html
      assets/              # optional, local to that artifact
```

For Agorastore repositories, `{repository}` is the GitHub repository name, such as `tars-monorepo`. `{number}` is the GitHub pull-request number. The public URL always mirrors this path below `https://agorastore.github.io/tars-artifacts/`.

## Rules for artifacts

- Create exactly one directory per pull request: `artifacts/{repository}/pr-{number}/`.
- Put the entry page at `index.html`. It must be a plain HTML document, never an application requiring a package install, bundler, server render, or deployment platform configuration.
- Prefer a self-contained document. An artifact should still work if the source PR branch, a preview deployment, or a third-party host disappears.
- Do not overwrite an existing artifact as a way to describe later work. Add a new artifact for the relevant pull request instead.
- Keep the artifact independent of an app build, secrets, API calls, or local development tooling. It must render as a static site on GitHub Pages.
- Update the root `index.html` and README when adding a published artifact so it is discoverable. The root index is the practical visual catalog for every artifact, not a placeholder landing page.

## Verification

Before committing, open the new `index.html` locally and verify that relative images, styles, anchors, and links work. The GitHub Actions workflow in `.github/workflows/deploy-pages.yml` is the only deployment mechanism; do not add another hosting provider configuration here.

`CLAUDE.md` is a symlink to this file. Edit `AGENTS.md` only, so the two agent entry points remain identical.
