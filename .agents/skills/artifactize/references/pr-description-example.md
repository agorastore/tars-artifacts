# PR-description exemplar — tars-monorepo PR #145

The shipped description for PR #145 (DataList saved views), with structural annotations in
`> 📐` blockquotes. Everything else is the deliverable, verbatim. Its artifact counterpart is
[`artifacts/tars-monorepo/pr-145/`](../../../artifacts/tars-monorepo/pr-145/index.html) — same
thesis, same vocabulary, ~15% of the length.

---

> 📐 Title: the feature as a plain noun phrase. No "feat:", no ticket id.

# DataList saved views

> 📐 The pitch: what it is + the design thesis in one paragraph, bolds landing on the thesis words.

Every DataList can now save, restore, and share its entire working state — filters, quick
filters, search, sort, and visible columns — as named **saved views**. One schema block turns it
on; the engine owns everything else.

> 📐 "Try it" + the artifact link. Both mandatory, both near the top.

**Try it:** shared-ui playground → `/dev/table` (invoice operations table, presets pre-seeded).
**Full write-up with interactive demos:** https://agorastore.github.io/tars-artifacts/artifacts/tars-monorepo/pr-145/

> 📐 Why: pain as lived experience (who does what, how often), then what becomes possible.
> Two short paragraphs — no history lesson.

## Why

Operators live in our tables, and they rebuild the same setups all day: status filter, sort by
issue date, hide the noise columns — again after lunch, again tomorrow. Table state is
URL-persistent but ephemeral: navigate away with different filters and the morning ritual starts
over. And there was no way to hand a colleague *"the table exactly as I'm looking at it"*.

Saved views make those working setups first-class: capture the current state under a name, return
to it in one click, or send a link that opens the table pre-configured.

> 📐 How it's designed: opens with THE one code block that is the entire integration surface,
> then bold-led paragraphs — one real design decision each, claim first, evidence after.

## How it's designed

```ts
// any table schema — this is the entire integration surface
presets: defineDataListPresets({
  query: () => $client.tableViews.list.queryOptions({ input: { tableKey } }),
  create: requestPresetCreation,   // optional — app-owned modal, resolves preset or null
  delete: requestPresetDeletion,   // optional — app-owned confirmation, false cancels
  canDelete: (preset) => preset.isOwner, // optional — gates the affordance per preset
})
```

**Presets are server state, not UI state.** `presets.query()` returns canonical TanStack
`queryOptions`, and the engine mounts a real Query observer on it. Successful `create`/`delete`
write back through typed cache upserts/removals — no duplicate local list to keep in sync, and
persistence stays wherever the app wants it (API, localStorage in the playground, anything).

**Strict ownership split.** The engine owns what's generic: state capture, application,
active-view detection (structural equality against current state), the selector UI, per-preset
loading states, cache synchronization. The app owns what's product-specific: persistence, the
creation modal, deletion confirmation, authorization. `create(state)` can open any UI it wants and
resolve `null` on cancel; `delete(preset)` returns `false` to abort. There are deliberately **no
schema keys and no rendering callbacks** — nothing to configure means nothing to misconfigure, and
every table gets the identical, polished affordance.

**Type-safe past the minimal shape.** A preset only needs `{ id, label, state }`, but real presets
carry metadata — ownership, scope, permissions. A `Preset` generic threaded through
`defineTableSchema` (declared via `defineDataListPresets`) makes the query's *concrete* item type
flow into `create`, `delete`, and `canDelete`. Your `canDelete` sees `preset.isOwner` fully typed;
nothing widens to the minimal shape.

**Shareable by construction.** A preset-enabled table accepts a one-shot `?preset=<id>` deep link:
on init it waits for the preset query to settle, applies the matching state, and consumes the
parameter through the shared route-query manager. Invalid ids are consumed without touching table
state.

> 📐 Feature-specific sections, named by what they are — never "Details" or "Implementation".

## The selector

A bookmark control appears automatically in the table header when `presets` is configured
(standard `controls` opt-out applies): active-view badge, diacritic-insensitive search,
locale-aware `Intl.Collator` ordering — derived in the UI so persistence order stays an API
concern — and a rich state preview per view: sort, visible column count, search, and real filter
tags with resolved labels. Keyboard accessible, translated in all 5 locales.

> 📐 The load-bearing side-story gets its own section when it pays for itself beyond the feature.
> Note the before/after at a real call site — the migration a consumer actually performs.

## The decoupling that makes previews possible

The preset selector has to answer *"what does this view contain?"* with human labels — outside any
mounted filter form. That forced a fix on a long-standing coupling, and it's the part of this PR
that pays for itself beyond presets.

**Before**, filter tags resolved display labels through the mounted filter form's `FieldApi` —
preview rendering depended on live form state, so it only worked inside a mounted DataList, and
labels were re-fetched with the form lifecycle.

**Now**, filter option sources are canonical TanStack `queryOptions` — the exact contract form
fields already use (shared `isFieldOptionQuery` / `buildOption`):

```ts
// customer-front manufacturer filter — before: one-shot promise per mount
options: () => $client.manufacturers.list.call().then(mapToOptions)

// after: cached, deduplicated, shared with any form field on the same key
options: () => $client.manufacturers.list.queryOptions({ select: mapToOptions })
```

A new `useFilterOptions` composable resolves static arrays, sync/async functions, or query sources
against the app QueryClient with `keepPreviousData`. `preview.render(value)` and
`preview.tagProps(value)` are value-only now (the `FieldApi` parameter is gone). The result: live
filter tags, preset previews, and app modals all use one resolver; cached labels render instantly
during background revalidation; only a genuinely cold query shows a skeleton — and options
participate in controller invalidation like every other query.

`DataListPresetStatePreview` ships as a standalone export: pass it a schema and a state, get the
full summary anywhere — the playground uses it inside its creation modal.

> 📐 Consuming it: the reference implementation, trimmed. The reader should finish thinking
> "that's all I have to write?"

## Consuming it

The playground page is the reference implementation: `presets.query` over localStorage, a
`$formApi` modal for creation with a live `DataListPresetStatePreview`, a confirm dialog for
deletion, and `canDelete` protecting a shared view.

```tsx
async function requestPresetCreation(state: DataListPresetState) {
  const result = await $formApi.createForm(defineFormSchema({
    title: 'Save current view',
    layout: { displayMode: 'modal', gridSize: 1 },
    fields: [
      { key: 'label', label: 'View name', type: 'text', required: true },
      { key: 'preview', type: 'custom-component', omit: true,
        render: () => <DataListPresetStatePreview state={state} schema={schema} /> },
    ],
  }))
  if (!result.isCompleted) return null
  return persistPreset({ id: crypto.randomUUID(), label: result.formData.label, state })
}
```

> 📐 Scope: deliberate boundaries stated as decisions; ride-alongs get exactly one bullet.

## Scope

- Ships the engine, the playground reference, docs (`tars-shared-ui-table` skill + schema
  anatomy), and tests. A customer-front saved-item-views prototype was built to validate the API
  end-to-end, then pulled from this PR — app adoption lands separately with real persistence.
- Riding along: directive-safe overlay roots for `PersistantDrawer`, and a Sentry router
  instrumentation fix.

> 📐 Validation: two lines, phrased as covered behavior. Never a checklist of lint/build/CI steps.

## Validation

11 focused vitest cases (capture/application, cache upsert/removal, metadata type flow, form-free
label resolution, cached-label revalidation), lint, typecheck, and the playground exercised
end-to-end.
