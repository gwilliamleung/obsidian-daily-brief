# Obsidian Daily Brief Agent

## Agent Instructions

Does two things: processes yesterday's journal dump, then builds today's brief.

### Step 1 — Process yesterday's journal

1. Find yesterday's daily note in the vault root. It is named `YYYY-MM-DD.md` (e.g. `2026-05-11.md`). Yesterday's date = today minus 1 day.
2. Read it and extract:
   - **Tasks/things done** — anything that reads as an activity, errand, or project work
   - **People mentioned** — any names. For each: search recursively through all subfolders inside `04 People/` for `<Name>.md`. If not found anywhere, create a minimal person note at `04 People/<Name>.md`. If found, append a line to their `## updates` section linking to the daily note.
   - **Projects touched** — search recursively through all subfolders inside `03 Projects/` for any note whose filename or folder name matches. If found and no "done", "finished", or "shipped" language present, if note mentions said project completion or percentage, flag said number otherwise flag as incomplete.
   - **Food/meals mentioned** — extract every food or drink item mentioned for macro estimation in Step 2. Inform in section that food has not been mentioned.
3. Move the raw file from the vault root to `02 Daily/YYYY/Month/YYYY-MM-DD.md` as-is — do not reformat or rewrite its contents. Simply relocate the file. Create year/month subfolders if they don't exist. Month folder is the full month name (e.g. `May`, `June`).

---

### Step 2 — Build today's brief

Prepend a new day entry to `01 Updates/📌 Daily Brief.md` (above any previous entries).

#### Sections to include

- **Oura** — header: `Authorization: Bearer YOUR_OURA_TOKEN`
  - `GET https://api.ouraring.com/v2/usercollection/daily_sleep?start_date=TODAY&end_date=TODAY` → `score` (sleep score) — use today's date
  - `GET https://api.ouraring.com/v2/usercollection/sleep?start_date=TODAY&end_date=TODAY` → `bedtime_start`, `bedtime_end`, `total_sleep_duration` (÷3600 → Xh Ym), `latency` (÷60 → Xm), `readiness.score` — use today's date
  - `GET https://api.ouraring.com/v2/usercollection/daily_activity?start_date=YESTERDAY&end_date=YESTERDAY` → `score` (activity score), `steps`, `active_calories`, `equivalent_walking_distance` (÷1609 → X.X mi) — use yesterday's date. If anything returns no data, omit it with a relevant emoji or `syncing...`

  Score indicator rule — applies to Readiness, Sleep, and Activity scores:
  - 85+ → 👑
  - 80–84 → ✅
  - <80 → 🔻

- **Weather** — short line: temperature, conditions, what to wear. No key needed:
  ```
  GET https://api.open-meteo.com/v1/forecast?latitude=YOUR_LAT&longitude=YOUR_LON&current=temperature_2m,weathercode,windspeed_10m&temperature_unit=fahrenheit&timezone=America%2FNew_York
  ```
  Use `current.temperature_2m` for temp and `weathercode` to describe conditions.

- **Tasks** — one flat list pulled from `01 Updates/Priority List.md`. All items appear together under a single `[!note]- Tasks` callout. Skip duplicates.
  - **Must Do** items appear first, no label.
  - **In Mind** and **Reach** items appear after, each marked *(optional)*. Cap at 3.

  Apply a default action verb if item doesn't have one:
  - Learning/language → "Practice ___ for 45 min"
  - Physical/rehab → "Do ___ exercises"
  - Projects → "Work on ___"
  - Applications/admin → "Progress ___"

  Annotate progress in plain words:
  - 80–99% → "almost done"
  - 50–79% → "halfway there"
  - 20–49% → "in progress"
  - <20% → "just started"

  Annotate deadlines where detectable (e.g. `— due May 30`).

- **Calendar** — today's events with time + brief context. Skip trivial recurring items (morning sequence, evening walk, nightly plank).

- **Yesterday's wins** — open with a single mood line: `**Mood:** [emoji] [2–3 words]`. Base it on the tone of the journal entry. Then write 1–2 sentences of specific, warm encouragement naming the actual things they did. Tone: a therapist who genuinely hypes you up — warm, fired up, and specific. Not tepid, not generic. Then list raw wins as terse bullets beneath. Use a `[!success]-` callout.
  - `[steps] steps · [active cal] active cal · [X.X mi]` as the first bullet.

- **Macros** — pull food extracted in Step 1. Assume all food is Chinese/Asian unless clearly stated otherwise — default to Hong Kong/Chinese portion sizes and cooking styles. Use generous, realistic estimates on the lower side. Daily goal: **1500 cal**, split: **P 150g / C 131g / F 42g** (40/35/25). Output inside a `[!note]-` Macros callout: totals as one line in the body — `~[X] cal ([+/-][Y]) · P [g] · C [g] · F [g]` — followed by a nested `[!note]-` Breakdown sub-dropdown with one bullet per food item. If no food was mentioned, skip this section.

---

### Callout types

- `[!note]-` — default for all sections
- `[!success]-` — Yesterday's wins
- `[!warning]-` — urgent, needs action today

---

### Format rules

- Group days under `## Month YYYY` headers (e.g. `## May 2026`). No `###` day headings — ever.
- Every day is wrapped in its own callout.
- Today uses `> [!note] [[MM-DD-YY Day]]` — always open, non-foldable.
- All previous days use `> [!note]- [[MM-DD-YY Day]]` — collapsed by default.
- Each run: (1) find the first `[!note] ` (no modifier) in the file and change it to `[!note]-`, then (2) prepend the new day as `[!note] `.
- Today's sections are nested one level inside using `> >`. Macros sub-dropdowns are nested one further level using `> > >`.
- Set `unread: true` and update `updated:` in frontmatter on every run.
- **Never add `# Title` headings** to any note. Obsidian shows the filename as the title.
- **Never repeat frontmatter in the body.**

---

### Example output structure

```
## May 2026

> [!note] [[05-13-26 Wed]]
>
> > ✅ Readiness 84 · 👑 Sleep 90 · 👑 Activity 100
> > 😴 12:47 AM → 9:43 AM · 8h 12m · 23m latency
>
> ☀️ 68°F and sunny, light jacket optional.
>
> > [!note]- Tasks
> > - [ ] Work on [[Project]] — halfway there
> > - [ ] Practice [[Japanese]] for 45 min *(optional)*
>
> > [!note]- Calendar
> > - 10:00am — 🏃 Run: Easy + Form Cue
>
> > [!success]- Yesterday's wins
> > **Mood:** 🔥 locked in
> > Pushed the project to the finish line and got a run in.
> > - 👟 17,136 steps · 1,077 active cal · 11.8 mi
> > - worked on project (~85–90% done)
>
> > [!note]- Macros
> > ~1710 cal (+210) · P 111g · C 145g · F 77g
> >
> > > [!note]- Breakdown
> > > - Half Buldak — 210 cal
> > > - Fried egg — 70 cal

> [!note]- [[05-12-26 Tue]]
> ...
```

---

## Vault folder structure

| Folder | Purpose |
|---|---|
| `01 Updates/` | Daily research updates by topic. Newest entries at top. |
| `02 Daily/YYYY/Month/` | Daily notes, named `MM-DD-YY ddd.md`. |
| `03 Projects/` | One folder per project with a root note. |
| `04 People/` | Person notes. |
| `05 Research/` | Deep research documents. |
| `06 References/` | Clippings, articles, external knowledge. |
| `07 Summaries/` | Summary notes. |
| `08 Places/` | Place notes. |
| `Archive/` | Completed or inactive projects. |
| `_Attachments/` | All media files. |
| `_Templates/` | Reusable note templates. |
