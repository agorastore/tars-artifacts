# TARS artifacts

**Pull requests, worth exploring.**

This repository is the permanent home for the *artifacts* of Agorastore's TARS pull requests:
rich, interactive, self-contained HTML pages that explain a substantial change the way its author
would at a whiteboard — the why, the bet, the design decisions, the things deliberately left out —
instead of the way a diff does. GitHub Pages publishes the repository root as-is, so every
artifact gets a stable URL with no hosting account, no runtime, no build step, and no expiry date.

Browse the catalog at **https://agorastore.github.io/tars-artifacts/**

## The concept

A pull request has two audiences with two attention budgets, so every substantial TARS change
ships two deliverables cut from the same research:

1. **The PR description** — the executive form. A succinct, design-first summary on GitHub that
   gives a reviewer the *right* context in two minutes: the pain, the design decisions, the one
   code block that is the entire integration surface, honest scope, and a link to the artifact.
2. **The artifact** — the long form, in this repository. A guided read with a chapter structure,
   real code from the branch, and interactive demos that re-implement the feature's actual logic
   in miniature — so a reviewer can *feel* a behavior (an active-view badge dropping on structural
   inequality, a prefetch warming a cache) instead of taking the description's word for it.

Both argue one thesis. The description links the artifact; the artifact's footer links the PR.

Why durable, static, and self-contained? Because review outlives review: artifacts double as
onboarding material, design-decision records, and the institutional memory of *why the code is
shaped this way*. Preview deployments rot, branches get deleted, hosting accounts lapse — a plain
HTML page in a repository does not. That's also why artifacts embed everything they need (the only
external request allowed is Google Fonts) and are never overwritten: later work gets a new
artifact for its own PR.

## Published artifacts

- [Marketplace V2 vs V1 — the platform benchmark](https://agorastore.github.io/tars-artifacts/artifacts/tars-monorepo/market-v2-benchmark/) —
  the complete old-vs-new marketplace comparison (performance, accessibility, SEO, AI/GEO
  readiness, sitemap architecture, live-rendered social previews) with interactive races and
  real captured assets. Companion PRs: tars-monorepo #154 and #155.
- [PR #145 — DataList Saved Views](https://agorastore.github.io/tars-artifacts/artifacts/tars-monorepo/pr-145/) —
  tables that capture, restore and share their working state as named views, with previews
  decoupled from form state.
- [PR #137 — The Server-State Rewrite](https://agorastore.github.io/tars-artifacts/artifacts/tars-monorepo/pr-137/) —
  one typed server-state architecture across customer, market and admin, with TanStack Query
  under Nuxt and a client generated from controllers.

## Layout and URLs

Every artifact is one directory, and the public URL mirrors the path exactly:

```text
artifacts/
  {repository}/          # the GitHub repository name, e.g. tars-monorepo
    pr-{number}/         # the pull-request number
      index.html         # the artifact — a plain, self-contained HTML page
      assets/            # optional, local to that artifact
  cross-repo/            # reserved — changes spanning several repositories
    {slug}/              # yyyy-mm-kebab-name, e.g. 2026-07-saved-views
      index.html         # the one canonical artifact for the whole change
```

```text
https://agorastore.github.io/tars-artifacts/artifacts/{repository}/pr-{number}/
https://agorastore.github.io/tars-artifacts/artifacts/cross-repo/{slug}/
```

A change that spans several repositories — contracts in one, consumers in another — is still one
story, so it gets **one canonical artifact** under `cross-repo/{slug}/` with a PR-strip hero
linking every member pull request. Each member PR keeps its own
`artifacts/{repository}/pr-{number}/` directory as a redirect stub to the canonical page, so
looking an artifact up by PR always works, and each PR's description states its role in the change
and links its siblings.

The root `index.html` is the visual catalog: every artifact must be registered there (and in this
README) to be discoverable — an unregistered artifact is invisible.

## Producing an artifact

Artifacts are not improvised; they follow a documented process. The
[`artifactize` skill](.agents/skills/artifactize/SKILL.md) defines the whole pipeline for people
and coding agents alike — point an agent at a branch and say *"artifactize this"*:

- **the process** — research the branch (commits, diff, docs, numbers) before writing a word; ask
  the author only about unrecoverable *whys*; find the thesis; then produce both deliverables;
- **the [writing guide](.agents/skills/artifactize/references/writing-guide.md)** — tone and
  per-component redaction rules, every rule with a good/bad example pair;
- **the page templates** — the uniform design system (fonts, palette, rail sommaire, hero stat
  strip, component vocabulary, demo scaffolding) every artifact starts from, extracted verbatim
  from the shipped pages: [single-repository PR](.agents/skills/artifactize/references/page-skeleton.html),
  [cross-repository change](.agents/skills/artifactize/references/page-skeleton-cross-repo.html)
  (PR-strip hero, aggregated stats), and the
  [per-PR redirect stub](.agents/skills/artifactize/references/redirect-stub.html);
- **a [PR-description exemplar](.agents/skills/artifactize/references/pr-description-example.md)** —
  PR #145's description, annotated section by section.

Uniformity is a hard rule: every artifact should look and read like the two above.

## Publishing

This repository does not use pull requests — commit directly on `main`:

1. Create `artifacts/{repository}/pr-{number}/` with its `index.html` (and any local assets).
2. Register it in the root `index.html` catalog (newest first) and in this README.
3. Verify: open `index.html` locally — styles, anchors, links, and every demo interaction.
4. Commit and push `main`. The **Deploy GitHub Pages** workflow publishes it automatically.

No install, no build: open any artifact straight from disk, or serve the repository root with any
static file server.

`AGENTS.md` holds the operational rules for people and coding agents; `CLAUDE.md` is a symlink to
it so both entry points stay aligned.
