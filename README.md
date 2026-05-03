# SVS Prep Scheduler

A browser-based scheduling tool for State vs State (SVS) events in Rise of Kingdoms. Automates slot assignment, leaderboards, alliance summaries, and output messages from Google Form response data.

**Live tool:** [gaurav7792.github.io/svs_scheduler](https://gaurav7792.github.io/svs_scheduler/)

Developed by VKS 3156.

---

## What it does

Chiefs submit their speedups and time zone preferences via a Google Form ahead of each SVS event. The scheduler reads the exported responses and:

- Deduplicates submissions by Chief ID (latest entry wins)
- Ranks chiefs by day-relevant speedups with correct formulas
- Assigns each of the top 48 chiefs a 30-minute UTC buff slot, respecting their preferred time windows
- Generates pre-formatted alliance chat messages ready to paste into the game
- Exports all schedules and leaderboards to a single XLSX file

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
2. Download your Google Form responses as `.xlsx` (File → Download → Excel)
3. Drag the file onto the upload zone in the left sidebar
4. Click the schedule buttons (Day 1, Day 2, Day 4)
5. Use the Output Messages tab for pre-formatted alliance chat messages
6. Click **Export to XLSX** to download all schedules and leaderboards

**First time?** Download the [sample data file](https://raw.githubusercontent.com/gaurav7792/svs_scheduler/main/SVS_Dummy_Data.xlsx) to try it out.

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

**Current version: 1.1**
