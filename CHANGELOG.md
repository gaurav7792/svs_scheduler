# Changelog

All notable changes to the SVS Prep Scheduler are documented here.

Format follows [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

---

## [1.1] - 2026-05-03

### Added
- **Export to XLSX** button in the sidebar. Produces `SVS_Schedule_Export.xlsx` with the following tabs:
  - Raw Data — all chiefs and their stats
  - Day 1 — Construction (schedule + full leaderboard)
  - Day 2 — Research (schedule + full leaderboard)
  - Day 4 MOE (schedule + full leaderboard)
  - Day 4 VP (schedule + full leaderboard)
  - Alliance Summary
  - Output Messages — all alliance chat messages, all days
  - Only tabs with generated data are included
- **GitHub repository card** added to the Guide panel links grid
- **First-time user banner** in the Guide panel with a direct download link to sample SVS data (`SVS_Dummy_Data.xlsx`)
- **v1.0 version label** added to the page header
- `CHANGELOG.md` and `README.md` added to the repository

### Changed
- Guide panel links grid expanded from 2 columns to 3 to accommodate GitHub card
- Running a schedule no longer auto-navigates away from the current tab

### Removed
- Live tool URL removed from header subtitle (redundant — you're already on it)
- "Live Tool" link removed from the demo banner (same reason)

---

## [1.0] - 2026-04-24

### Initial release
- Upload Google Form response XLSX via drag-and-drop or file picker
- Deduplication by Chief ID — latest submission wins
- Alliance name normalisation (WLD, EVL, REV, BEE, PHX, HWZ, VKS)
- **Day 1 — Construction** schedule: top 48 by construction SU assigned 30-min UTC slots
- **Day 2 — Research** schedule: ranked by Research Points formula `(Eff Shards × 1000) + (Eff SU × 1440 × 30)`, with steel cap applied
- **Day 4 — Training** schedule: top 48 → MOE (30% buff), next 48 → VP (10% buff)
- Slot assignment: most time-constrained chiefs first, preferred windows honoured, closest available slot as fallback with midnight wrap-around
- Leaderboards for Day 1, Day 2, Day 4 with cutoff line at rank 48
- Alliance Summary with stacked bar chart and top 10 chiefs table
- Chief Lookup by name or ID — shows stats and assigned slots across all days
- Output Messages tab — pre-formatted alliance chat messages, chunked to 5 chiefs per message
- Coordinator Overrides — click any chief row to edit submitted values; changes logged in Overrides tab with export/import (JSON)
- Stale schedule banners when data is edited after a schedule has been run
- Copy Schedule Text — copies all schedules to clipboard as tab-separated text
