# SVS Prep Scheduler

A browser-based scheduling tool for State vs State (SVS) events in Whiteout Survival. Automates slot assignment, leaderboards, alliance summaries, and output messages from Google Form response data.

**Live tool:** [gaurav7792.github.io/svs_scheduler](https://gaurav7792.github.io/svs_scheduler/)

Developed by VKS 3156.

---

## What it does

Chiefs submit their speedups and time zone preferences via a Google Form ahead of each SVS event. The scheduler reads form responses **directly from the Google Sheet** and:

- Deduplicates submissions by Chief ID (latest entry wins)
- Ranks chiefs by day-relevant speedups with correct formulas
- Assigns each of the top 48 chiefs a 30-minute UTC buff slot, respecting their preferred time windows
- Generates pre-formatted alliance chat messages ready to paste into the game
- Exports all schedules and leaderboards to a single XLSX file

The Google Sheet is the single source of truth — edits made in the sheet flow into the app on the next sync.

---

## Event days covered

| Day | Activity | Zone | Buff |
|-----|----------|------|------|
| Day 1 | Construction | VP | Construction speed |
| Day 2 | Research | VP | Research speed |
| Day 4 | Training | MOE | 30% training speed |
| Day 4 | Training | VP | 10% training speed |

Day 4 has two tiers. MOE takes the top 48 by training SU. VP takes the next 48.

---

## How to use

1. Open the [live tool](https://gaurav7792.github.io/svs_scheduler/)
2. Click **⟳ Sync from Sheet** in the sidebar — pulls form responses directly from the configured Google Sheet and runs all schedules + the alliance summary in one action
3. Browse the schedule, leaderboard, and output-messages tabs
4. Click **Export to XLSX** to download all schedules and leaderboards

The XLSX drag-and-drop upload remains available as a fallback for testing or one-off data sources.

**Configuring the sheet:** the URL is hardcoded as the `SHEET_URL` constant near the top of the sync section in `index.html`. The sheet must be set to "Anyone with the link can view" so the CSV export endpoint is reachable.

**First time?** Download the [sample data file](https://raw.githubusercontent.com/gaurav7792/svs_scheduler/main/SVS_Dummy_Data.xlsx) and drag it into the upload zone to try the tool without setting up a sheet.

---

## Mobile

At viewport widths ≤600px, schedule, leaderboard, and alliance-summary tables render as stacked cards. The assigned slot is highlighted on its own line in each card, so chiefs can find their slot at a glance. Raw Data keeps a horizontal-scroll table since its purpose (column comparison) doesn't translate to cards.

A floating ⛶ button in the bottom-right (touch devices only) toggles to a desktop-style layout for coordinators who occasionally need to run schedules from a phone.

---

## Day 2 ranking formula

Research is ranked by **Research Points**, not raw speedups:

```
Research Points = (Eff Shards × 1000) + (Eff SU × 1440 × 30)

Eff SU     = MIN(raw SU, steel cap)
Steel cap  = (steel in millions × 1,000,000) ÷ 33,000
```

Chiefs with zero construction SU have all Fire Crystals converted to FC Shards at 1 FC = 1.3 FCS.

---

## Slot assignment logic

1. Chiefs sorted by number of preferred slots ascending (most time-constrained first)
2. First available slot inside preferred windows is assigned
3. If all preferred slots are taken, the nearest free slot by time distance is used (including midnight wrap-around)

The output is deterministic: same sheet input produces the same schedules across all browsers and OS locales.

---

## Repository contents

| File | Description |
|------|-------------|
| `index.html` | The full application — single self-contained file |
| `SVS_Dummy_Data.xlsx` | Sample form response data for testing |
| `CHANGELOG.md` | Version history |
| `README.md` | This file |

---

## Changelog

See [CHANGELOG.md](CHANGELOG.md) for full version history.

**Current version: 1.4**
