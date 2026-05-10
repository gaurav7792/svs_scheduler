# Changelog

All notable changes to the SVS Prep Scheduler are documented here.

Format follows [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

---

## [1.4] - 2026-05-10

### Added
- **Mobile layout** at viewport widths ≤600px:
  - Sidebar stacks above the main content area instead of sitting beside it
  - Day schedule tabs (Day 1, Day 2, Day 4 MOE, Day 4 VP), Leaderboards, and Alliance Summary top-10 render as stacked cards instead of horizontal tables
  - Header subtitle and run-button sub-labels hidden to save vertical space
  - Toolbars (search/filter rows above tables) stack vertically
  - Output Messages grid collapses to a single column
- Each schedule card highlights the assigned slot pill on its own line with a dashed divider, making "find my slot" a one-glance task on a phone.
- **"Best viewed on desktop" notice** in the header on mobile (gold subtitle) directing users to the toggle button.
- **Floating desktop-view toggle** (bottom-right corner, touch devices only). Tap ⛶ to render the page as if on a 1200px desktop monitor; the device scales it down to fit. Tap 📱 to return to the mobile card layout. Choice persists in localStorage (`svs_view_mode_v1`).

### Changed
- Raw Data table on mobile uses horizontal scroll (`min-width: 1100px`) rather than card layout, since the comparison-across-columns workflow doesn't translate well to cards. Coordinator-only view.

### Notes
- The desktop-view toggle uses the viewport meta tag, the same mechanism as the browser's built-in "request desktop site" option. It does not load a separate stylesheet or render path — same code, different layout assumptions.
- The toggle button only appears on touch devices (`@media (pointer: coarse)`), not on laptops/desktops with a mouse, regardless of window width. This ensures users in desktop-view mode can still toggle back when the mobile media query no longer fires.
- Card layout uses CSS-only transformation (`display: block` on table elements + `data-label` attributes for cell labels). No JS render branching — the same HTML works at both breakpoints.

---

## [1.3] - 2026-05-10

### Removed
- **Coordinator Data Overrides system** removed entirely. The Google Sheet is now the single source of truth — edit cells in the sheet directly, then click Sync. Specifically removed:
  - "Overrides" tab and its UI
  - Click-to-edit panel on Raw Data rows
  - localStorage persistence (`svs_overrides_v3`)
  - JSON import/export of overrides
  - Stale-schedule banners (no longer applicable — schedules are always fresh after sync since data is no longer mutable in-app)

### Changed
- Raw Data table is now read-only; rows still hover-highlight but no longer respond to clicks.
- Internal: `getChief()` / `getEffectiveChiefs()` retained as plain passthroughs over `chiefs[]` so callers don't need to change.

### Notes
- One-time cleanup runs on load to delete the legacy `svs_overrides_v3` localStorage key. Existing users will see overrides disappear on first load of v1.3 — schedules reflect raw sheet values from then on.
- If you previously relied on overrides, edit the corresponding cells in the Google Sheet before clicking Sync. The change then persists for everyone, not just your browser.
- Net code reduction: ~480 lines, ~22 KB.

---

## [1.2] - 2026-05-10

### Added
- **Sync from Sheet** button in the sidebar. Fetches form responses directly from a hardcoded Google Sheet via the `gviz/tq?tqx=out:csv` endpoint, removing the need to download an XLSX manually. Sheet URL is set via the `SHEET_URL` constant near the top of the sync section in `index.html`.
- **Last-sync timestamp** persisted in localStorage and shown beneath the Sync button.
- **Auto-run on sync.** Clicking Sync now fetches the sheet and automatically runs Day 1, Day 2, Day 4 (MOE → VP), and Alliance Summary in a single action. Day 4 MOE-before-VP ordering is preserved. One toast reports the combined result.
- **Locale-safe CSV timestamp parser** (`parseSheetTimestamp`). Strict regex parser for the `M/D/YYYY H:mm:ss` format Google Sheets emits, with field-range validation and 12h AM/PM support. Uses `Date.UTC()` (locale-independent) instead of `Date.parse()` (locale-dependent for non-ISO strings).

### Changed
- Sidebar layout: Sync from Sheet button is now the primary data ingress; XLSX drag-and-drop is retained below as a fallback for testing or one-off data sources.

### Fixed
- **Determinism across browsers and locales.** Replaced `Date.parse()` in the CSV ingress path with the explicit parser above. Previously, the same sheet input could produce different dedup outcomes across browsers because `Date.parse()` is implementation-defined for non-ISO date formats. Now: same sheet input → identical chief data and schedules everywhere.
- **`processRows()` timestamp handling.** String timestamps are now parsed correctly. Previously string timestamps were silently treated as `0`, meaning dedup-by-Chief-ID picked whichever row appeared last in the sheet rather than the actual latest submission. Affects CSV imports and any XLSX where the timestamp column wasn't a true date type.

### Notes
- Sheet must be set to "Anyone with the link can view" for the sync endpoint to return CSV. If access is restricted, the request returns an HTML login page and the app reports "Sheet not public — check share settings".
- Sync requires the page to be served from a real origin (GitHub Pages, localhost, etc.). It will not work when opening `index.html` directly via `file://` due to browser CORS rules on null origins.
- The same-second tiebreaker in dedup (two submissions with identical timestamps from the same chief) was reviewed and intentionally left as-is. Probability of occurrence is effectively zero at this app's scale.

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
- Coordinator Overrides — click any chief row to edit submitted values; changes logged in Overrides tab with export/import (JSON) *(removed in v1.3)*
- Stale schedule banners when data is edited after a schedule has been run *(removed in v1.3)*
- Copy Schedule Text — copies all schedules to clipboard as tab-separated text
