---
name: artifactize
description: >-
  Turn a branch or pull request into its two review deliverables: a succinct, design-first PR
  description and a durable, interactive HTML artifact page in this repository. Use when asked to
  "artifactize" a branch or PR, to write or rewrite a PR description, or to produce an artifact
  page for a substantial change.
---

# Artifactize

One process, two deliverables, one narrative. "Artifactizing" a branch means producing:

- **Deliverable A — the PR description.** The executive form. Succinct, design-first markdown that
  gives a reviewer the *right* context, and always links the artifact.
- **Deliverable B — the artifact page.** The long form. A self-contained interactive HTML page at
  `artifacts/{repository}/pr-{number}/index.html`, published on GitHub Pages.

Both come from the same research pass and argue the same thesis. Never produce one without at
least planning the other. The canonical shipped examples are
[`artifacts/tars-monorepo/pr-137/`](../../../artifacts/tars-monorepo/pr-137/index.html)
(migration-scale, 10 chapters) and
[`artifacts/tars-monorepo/pr-145/`](../../../artifacts/tars-monorepo/pr-145/index.html)
(feature-scale, 6 chapters). **Uniformity with them is a hard requirement**, not a style
suggestion: same fonts, palette, layout chrome, component vocabulary, and interaction scaffolding.

## Required reading, in order

1. `references/writing-guide.md` — tone, thesis, and per-component redaction rules with good/bad
   examples. Read it before writing a single sentence of either deliverable.
2. `references/page-skeleton.html` — the starting file for every artifact. Copy it; never restyle
   it, never rebuild the CSS or scaffolding from scratch.
3. `references/pr-description-example.md` — PR #145's description, annotated section by section.

## Process

### 1. Research before writing anything

Work the branch until you could defend every design decision in review. Order matters:

1. **The existing PR description**, if any. You are probably replacing it, but mine it for intent,
   links, and vocabulary the team already uses.
2. **The commit list** on the branch (`git log --oneline merge-base..HEAD`). Granular commits are
   the narrative skeleton: they tell you what was built first, what was reverted, what was a
   prototype (a `feat` later removed by a `refactor` is a *deliberate scope decision* — that's
   content, not noise).
3. **The full diff** against the merge base (`git diff --stat` first, then the files). Deep-read in
   this order: types → engine/composables → components → consumers/apps → tests → docs. Docs and
   skill files changed in the PR state the contracts in reviewable prose — quote them, don't
   paraphrase them badly.
4. **The numbers.** Files changed, +/− lines, commit count, test counts, locales touched, deploy
   versions — plus candidates for *editorial stats* (see the hero contract below).
5. **The living feature.** Run the playground/dev server if it is cheap. If you cannot run it,
   plan faithful interactive recreations from the component code — never screenshots of nothing,
   never lorem mockups.

### 2. Ask questions — optionally, and only about the why

Ask the author only when the *why* is unrecoverable from code and context: business intent, an
unexplained scope boundary, a prototype removed without a trace of the plan. Never ask about
mechanics you can read in the diff. Batch questions (2–3 max) and keep researching while you wait.

### 3. Find the thesis

One sentence that *argues*, not describes. Every chapter of the artifact and every section of the
description must serve it. The writing guide shows how to derive and test it.

### 4. Write the PR description (Deliverable A)

Follow the structure contract below and the exemplar. Write it *after* research, *before* the
artifact: if the description doesn't hold together, the artifact won't either.

### 5. Build the artifact (Deliverable B)

Copy `references/page-skeleton.html` to `artifacts/{repository}/pr-{number}/index.html` and fill
it. Follow the page anatomy contract below.

### 6. Register, verify, ship

Follow the checklist at the end of this file. This repository takes commits directly on `main` —
no pull requests (see `AGENTS.md`).

## Deliverable A — the PR description contract

Target 60–120 lines of markdown. Prose-first; bullets only where enumeration is real. Adapt the
skeleton to the change — a small PR gets fewer sections, never padded ones.

| Section | Budget | Content |
| --- | --- | --- |
| `# Title` | 1 line | The feature, as a plain noun phrase. No "feat:", no ticket numbers. |
| Pitch | 1 paragraph | What it is + the one-line design thesis, bolded where it counts. |
| Try it + artifact link | 2 lines | Where to see it running, and the **published artifact URL. Mandatory. Always.** |
| `## Why` | 2 short paragraphs | The pain as lived experience, then what the change makes possible. |
| `## How it's designed` | 2–4 bold-led paragraphs | The real design decisions, each anchored by evidence. Include **the one code block that is the entire integration surface**. |
| Feature-specific sections | as needed | Name them by what they are ("The selector", "The decoupling…"), never "Details" or "Implementation". |
| `## Consuming it` | 1 snippet | The reference implementation, trimmed with `// …unchanged…`. |
| `## Scope` | 3–5 bullets | What deliberately does *not* ship; prototypes built-then-removed; ride-alongs (one bullet, max). |
| `## Validation` | ≤ 2 lines | What the tests *cover*, in words. Never a checklist of lint/build/CI steps. |

## Deliverable B — the artifact page contract

### Page anatomy (all of it comes with the skeleton)

- **Left rail sommaire** — sticky numbered table of contents with subsections, scroll-spy
  highlighting, and a mobile overlay. Generated from your section ids: chapters are `id="word"`,
  subsections `id="word-sub"`. Keep ids short, semantic, stable.
- **Hero** — eyebrow, H1, lede, stat strip (contract below).
- **Chapters** — 4–10 numbered chapters (`.ch-num` 01…), each with a `ch-head`, a one-sentence
  `ch-lede` thesis, and `h3` subsections numbered `n.m`.
- **Component vocabulary** — code panels with a path bar and COPY button; before/after pairs
  (`.ba.cols`); comparison tables (`.tbl`); ownership diagrams (`.diagram`); win cards (`.wins`);
  pull quotes (`.pull`); guard-rail notes (`.note`). Use these and only these; the writing guide
  gives per-component redaction rules.
- **Interactive demos** — at least one, ideally two (rules below).
- **Footer** — `{title} · {repo} PR #{n} (linked) · {branch} · {Month Year}`.

### The hero contract (generalized)

```
eyebrow   {repository} · PR #{number} · {branch-name}
H1        Short noun phrase + amber dot:  "Saved views."  "The server-state rewrite."
lede      1–2 sentences, 2–4 <b> bolds landing on the thesis words. No third sentence.
```

Each eyebrow segment is a **link** — repository → the GitHub repo, `PR #{n}` → the pull request,
branch name → the branch tree — styled to look exactly like plain eyebrow text (color inherited,
no underline) with a subtle hover (color lift + offset underline). The skeleton ships the markup
and the `.eyebrow a` rules; keep all three hrefs real.

**Stat strip — 4 to 6 stats, two families:**

1. **Mechanical stats** (always first, in this order): files changed (animated `data-count`),
   `+X −Y` lines (green/red spans), granular commits (`data-count`).
2. **Editorial stats** (1–3): numbers that *argue the thesis*. `0` and `1` are the strongest
   numbers on the page — "1 schema block to enable", "0 render callbacks to maintain", "0 legacy
   async helpers left". Large counts prove rigor — "497 api-sdk tests". A stat whose label could
   appear on any PR ("tests added") is not editorial; cut it.

Optionally, below the strip: one *proof visual* (e.g. Lighthouse rings) — only when a real
measurement exists. Never invent one.

### The chapter arc

Chapters follow a narrative order, not the diff order:

1. **Why** — always chapter 01. Pain as lived experience, then the bet.
2. **The design / The wins** — the decisions and their rationale.
3. **Deep dives** — one chapter per load-bearing mechanism (deep links, a decoupling, a compiler…).
   Each exists only if it changed how consumers think.
4. **Consuming it / patterns** — the reference implementation and what every consumer gets free.
5. **What this unlocks** — optional; only with a real roadmap consequence.
6. **Scope & proof** — always last. Deliberate boundaries first, then validation as a table of
   *covered behavior*, not a CI log.

### Interactive demo rules

- **Faithful recreation of real logic in miniature.** Re-implement the actual algorithms —
  structural-equality checks, cache flows, state application — in vanilla JS inside the page's
  IIFE. If the demo lies about how the feature behaves, delete it.
- **Two visual registers, already in the skeleton:** the light `appmock` (product UI recreations —
  panels, popovers, tables, modals) and the dark console/`linkdemo` (lifecycle and protocol
  replays with staged, timed log lines).
- Namespace each demo's DOM ids (`m1-…`, `ld-…`) and keep its logic in one labelled block.
- Always: a replay/reset control, and a `hint` caption that states plainly what is real
  ("Everything above is real logic from the feature, re-implemented in miniature: …").
- Never: videos, GIFs, screenshots of placeholder data, lorem content, or demos requiring network.

### Uniformity hard rules

- Start from the skeleton. Do not change fonts, palette variables, layout grid, or scaffolding JS.
- Additions go in the *second* `<style>` block (demo-specific CSS only), like pr-145's `linkdemo`.
- Self-contained: Google Fonts is the only permitted external request. No CDNs, no images that
  could rot, no API calls.
- Escape `<` and `&` inside `<pre><code>`; the skeleton's regex highlighter does the rest.

## Register, verify, ship

1. `artifacts/{repository}/pr-{number}/index.html` — exactly one directory per PR; never overwrite
   an existing artifact to describe later work.
2. **Register it — mandatory, an unregistered artifact is invisible.** The root `index.html` is
   the practical visual catalog GitHub Pages displays: add the new artifact there (newest first,
   `meta` line + `h2` + one-sentence `p` in the existing card format) and to the README "Browse"
   list. An artifactize run that skips this step is incomplete.
3. Verify mechanically: balanced tags and no duplicate ids; `node --check` on the extracted
   script; a jsdom smoke test that clicks through *every* demo interaction (open, apply, mutate,
   reset) and asserts zero errors; then a visual pass in a browser.
4. Cross-link: the PR description carries the published artifact URL
   (`https://agorastore.github.io/tars-artifacts/artifacts/{repository}/pr-{number}/`); the
   artifact footer links back to the PR.
5. Commit directly on `main` — artifact directory + catalog updates in one commit. Deliver the PR
   description to the PR body (or hand it to the requester); it does not live in this repository.
