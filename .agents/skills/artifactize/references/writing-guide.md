# Writing guide — tone, thesis, and redaction rules

How to actually *write* both deliverables. Every rule below is example-driven: a ❌ that a
competent-but-generic writer would produce, and the ✅ this house style demands. The examples are
real (or near-real) lines from the shipped pr-137 and pr-145 pages.

## 1. The thesis

Before writing anything, compress the branch into one sentence that **argues**. Test: a reader who
disagrees with your thesis should be able to say so. If nobody could disagree, it's a description,
not a thesis.

- ❌ "This PR adds saved views to DataList." — true, inarguable, dead.
- ✅ "A view an operator rebuilds three times a day is not UI state — it's a work object; it
  deserves a name, a home, and a link."
- ✅ "Make TanStack Query the single owner of server state — and make it invisible to write."

Everything else in both deliverables is evidence for this sentence.

## 2. Voice

Written by the engineer who made the choices, for peers — confident, calm, precise, with a point
of view. Concretely:

**Declare decisions, including the rejected ones.**
- ❌ "We considered different approaches for storing presets."
- ✅ "The obvious implementation — a reactive array somewhere in the table engine — was rejected
  on day one."

**Name tensions honestly.** What is deliberately not configurable, what was deferred, what rides
along. Honesty is credibility; the reader trusts the wins because you volunteered the boundaries.
- ✅ "Scope honesty: offline persistence is deliberately *not* implemented in this PR."
- ✅ "`canDelete` only controls the button — the delete handler must still enforce authorization
  server-side."

**Anchor every claim.** A sentence of praise must be followed by a code sample, a number, or a
demo. Unanchored adjectives get cut.
- ❌ "The API is extremely flexible and type-safe."
- ✅ "Your `canDelete` sees `preset.isOwner` fully typed; nothing widens to the minimal shape." (+
  the code block proving it)

**Vivid but technical.** Craft metaphors are welcome when they sharpen a mechanism; decoration is
not.
- ✅ "Preview rendering was welded to live form state."
- ✅ "Fails closed: a deleted view degrades to the default table, not an error."
- ❌ "This is a game-changer for our users." (marketing register — never)

**Banned moves:** "This PR aims to…", "In this section we will…", apology, hedging ("should
hopefully"), file-by-file narration, exhaustive lists of everything that changed, exclamation
marks, and praise adjectives without evidence ("robust", "seamless", "powerful").

## 3. What is relevant / what is not

**Relevant** (in rough priority order):

1. The why, told as lived experience — what a person did before, and how often.
2. The bet: the central design decision and the alternative it beat.
3. Ownership boundaries — what the engine/library owns vs what the consumer owns, and *why the
   line is there*.
4. Type flow — where inference does work a human would otherwise do.
5. Before/after at real call sites — the migration a consumer actually performs.
6. What every consumer gets free (the list of affordances nobody has to build).
7. Deliberate scope: what does not ship, prototypes built-then-removed, opt-outs.
8. One honest validation summary, phrased as covered behavior.

**Not relevant** (cut without mercy):

- File-by-file walkthroughs; restating the diff in prose.
- CI mechanics, lint/format/build checklists, "all tests pass". (One line at most, at the end.)
- Internal refactor mechanics with no consumer-visible consequence — one "riding along" bullet.
- The history of your own work session ("first I tried…").
- Anything the reader can see by opening the file — describe *consequences*, not contents.

## 4. Redaction rules per component

### Chapter ledes (`.ch-lede`)

One sentence, thesis-bearing, no hedging. It must make the chapter skippable — a reader who
believes the lede can move on.

- ❌ "This chapter explains how presets work."
- ✅ "Three decisions shape the whole feature: presets live in the Query cache, the engine and the
  app split ownership along a strict line, and types flow past the minimal shape."
- ✅ "'Look at the table the way I see it' is now a URL."

### Stat labels (hero strip)

Lowercase, ≤ 5 words, and — for editorial stats — arguing something.

- ❌ "Number of tests: 11" · ❌ "LOC added"
- ✅ "1 — schema block to enable" · ✅ "0 — render callbacks to maintain" · ✅ "0 — legacy async
  helpers left"

### Pull quotes (`.pull`)

The one-sentence thesis of the chapter, italicized on its pivot word. One per chapter, maximum.
If the chapter has no sentence worth pulling, the chapter is weak — fix the chapter, not the quote.

- ✅ "Display used to be a side effect of a mounted form. Now it's a pure function of *schema +
  value + cache* — which is why a preset can describe itself anywhere."

### Code panels (`.code`)

- The bar shows either the **real file path** (`packages/shared-ui/…/useTablePresets.ts`) or an
  **editorial caption** when the point is the contract, not the location ("generated — nothing
  here is hand-written", "the query's concrete type reaches every callback — no casts").
- Trim with `// …filters, columns, source — unchanged…`; never paste 60 lines when 12 carry the
  point. Comments inside samples may editorialize: `if (!preset) return  // app cancelled its own
  modal — nothing happened`.
- Code must be *real* — from the branch, not idealized pseudo-code. If you must simplify, simplify
  by deletion, not invention.

### Before/after pairs (`.ba.cols`)

Only for genuine call-site migrations. Both sides minimal, aligned line-for-line so the eye diffs
them. Tag captions editorialize the cost/benefit:

- ✅ BEFORE "one-shot promise, refetched per mount" → AFTER "cached, deduplicated, invalidated
  with its controller"
- ❌ BEFORE "old code" → AFTER "new code"

### Win cards (`.wins`)

Four is the ideal count. Kicker: one word or two ("Anywhere", "Warm cache", "Shared"). Title: a
claim, 2–5 words ("Labels never flicker"). Body: ≤ 2 sentences, at least one concrete mechanism —
a card with no mechanism is an advertisement.

### Notes (`.note`)

Guard rails and honest caveats only: default behaviors, security boundaries, opt-outs. A note that
merely repeats the body text gets cut.

- ✅ "**Guard rails included.** Without `delete`, no delete affordance renders at all."

### Tables (`.tbl`)

Use for contracts (callback → contract → why it lives where it lives), status matrices, and
covered-behavior validation. Column heads are lowercase mono; first column is the anchor concept.
Never use a table where prose ordering carries meaning.

### Diagrams (`.diagram`)

Ownership and layering only — boxes with `own` labels ("engine owns" / "app owns"), one arrow
direction. If the diagram needs more than 5 boxes, it's two diagrams or none.

### Demo hints (`.hint`)

State exactly what is real, in one sentence. This is the trust contract of the whole demo.

- ✅ "Everything above is real logic from the feature, re-implemented in miniature:
  structural-equality active detection, order-insensitive column capture, diacritic-insensitive
  search."
- ❌ "Try clicking around!" (says nothing, promises nothing)

### Validation sections

Phrase everything as **covered behavior**, never as process.

- ❌ "✔ lint ✔ typecheck ✔ build ✔ tests pass ✔ CI green"
- ✅ "Preset capture — visible leaf columns only, no internal group keys; required columns
  preserved on application."
- ✅ (whole PR-description budget) "11 focused vitest cases (capture/application, cache
  upsert/removal, metadata type flow, form-free label resolution, cached-label revalidation),
  lint, typecheck, and the playground exercised end-to-end."

## 5. PR-description specifics

The description is the artifact's executive form — same thesis, same vocabulary, ~15% of the
length. Two structural devices carry it:

**Bold-led design paragraphs.** Each design decision is one paragraph opening with a bolded
claim, then 2–4 sentences of evidence:

> **Presets are server state, not UI state.** `presets.query()` returns canonical TanStack
> `queryOptions`, and the engine mounts a real Query observer on it. Successful `create`/`delete`
> write back through typed cache upserts/removals — no duplicate local list to keep in sync…

**The one code block that is the entire integration surface.** Every feature PR has one — the
schema block, the macro, the config object. Show it early, caption it as such ("That's it. The
bookmark control appears automatically."). The reader should think: *that's all I have to write?*

And the two hard invariants:

- The **published artifact URL appears near the top** ("Full write-up with interactive demos: …").
  A description without the artifact link is an incomplete deliverable.
- The description must stand alone if the artifact link is never clicked — it is not a teaser, it
  is the summary a busy reviewer acts on.

## 6. Length calibration

| Change size | Description | Artifact |
| --- | --- | --- |
| Feature (≈ 1–2k lines) | 60–90 md lines | 5–7 chapters, 1–2 demos (pr-145: 6 chapters) |
| Subsystem/migration (≈ 10k+ lines) | 90–120 md lines | 8–10 chapters, 1–2 demos + proof visuals (pr-137: 10 chapters) |
| Small fix / chore | Don't artifactize. Write three honest sentences and stop. |
