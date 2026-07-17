# TARS artifacts

This repository is the permanent, zero-infrastructure home for rich pull-request artifacts from Agorastore's TARS projects. Every artifact is a plain static HTML page, and GitHub Pages publishes it directly from this repository, so reviewers get a stable URL without a separate hosting account, deployment service, application runtime, or build step.

## Browse

- [PR #145 — DataList Saved Views](https://agorastore.github.io/tars-artifacts/artifacts/tars-monorepo/pr-145/)
- [PR #137 — The Server-State Rewrite](https://agorastore.github.io/tars-artifacts/artifacts/tars-monorepo/pr-137/)

## URL and directory convention

Every artifact is a static site stored in the following location:

```text
artifacts/
  {repository}/
    pr-{number}/
      index.html
```

For example, the artifact for `agorastore/tars-monorepo#137` lives at `artifacts/tars-monorepo/pr-137/index.html` and is published at:

```text
https://agorastore.github.io/tars-artifacts/artifacts/tars-monorepo/pr-137/
```

Use the repository name as `{repository}` for Agorastore projects. Every artifact must remain a plain static HTML page; keep it self-contained where possible by placing images, fonts, stylesheets, and scripts beside its `index.html` instead of depending on a temporary preview deployment.

## Adding an artifact

1. Create `artifacts/{repository}/pr-{number}/`.
2. Add `index.html` and any local assets it needs.
3. Add the artifact to the browsable catalog in the root `index.html` and to this README.
4. Push or merge to `main`. The **Deploy GitHub Pages** workflow publishes it automatically.

`AGENTS.md` contains the operational rules for people and coding agents. `CLAUDE.md` is a symlink to the same file so both entry points stay aligned.

## Local preview

Because these are plain static files, open an artifact directly in a browser or serve the repository root with any static server. No install or build step is required.

## Publishing

GitHub Actions deploys the root of `main` to GitHub Pages. The deployed site is available at [agorastore.github.io/tars-artifacts](https://agorastore.github.io/tars-artifacts/).
