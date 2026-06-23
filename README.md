# Gymothy

Personal gym tracker for a structured strength program. Logs sets, tracks progress over time, and syncs data to GitHub.

Built because Excel is clunky at the gym and existing apps have too much noise.

---

## Why

I was tracking training in Excel — one sheet per week, manual volume calculations, no easy way to visualize progress over time. It worked but it was slow to use mid-session on a phone and annoying to maintain. Existing apps like Strong or Hevy would cover most needs but come with social features, upsells, and data locked in someone else's system. This does exactly what I need and nothing else.

---

## Features

- Set-by-set logging — weight (kg), reps, RPE per set
- Program hardcoded to my current split: Day A (posterior chain/glutes), Day B (upper push/pull), Day C (full body squat-led)
- RPE-based, most sets at RPE 7–8 with top sets at 8–9
- Block system — 5 working weeks + 1 deload week per block, new blocks preserve full history
- Deload week mode — 2 set cap, RPE ≤6 warning, editable exercise names for variations (e.g. SSB squat instead of back squat)
- "Last time" hint on each exercise showing previous week's top set as reference
- Per-exercise progress charts — top set weight and volume over time across all blocks
- Session notes field — fatigue, aches, anything worth remembering
- Body weight log with 7-day averages and trend chart
- GitHub sync — one tap saves all data to a private repo
- Export/import JSON backup

---

## How it works

Single HTML file with no build step or framework. All logic, styles, and markup in one file.

**Data flow:**
- While logging at the gym, data auto-saves to `localStorage` on the device
- At the end of a session, one tap writes everything to `data.json` in a private GitHub repo via the GitHub API
- On next open, the app reads from `localStorage` — if switching devices, load from GitHub to restore

**Session keys** are structured as `B{block}-W{week}-{day}-{date}` (e.g. `B1-W3-A-2026-01-15`) so every session is uniquely identified and historical data is never overwritten.

**Credentials** (GitHub username, repo name, token) are stored in `localStorage` only and are never written to `data.json` or any exported file.

---

## Setup

**Repos:**
- Public repo (this one) — hosts the HTML file via GitHub Pages
- Private repo (`gymothy-data`) — stores `data.json`, never public

---

## Usage

1. Open the app, select the current block and week
2. Tap Day A, B, or C
3. Tap an exercise to expand it → add sets as you go
4. Tap the exercise name to see progress over time (chart + full history)
5. Add session notes at the bottom
6. Tap ☁️ → Save to GitHub when done

To start a new block after completing 6 weeks, tap **+ New** next to the block selector. Previous block data is preserved and visible in the progress view.

---

## Future ideas

- Relative load tracking — e.g. squat 70kg at 60kg bodyweight
- Validations — RPE range clamping, partial set detection
- Confirmation prompt before loading from GitHub
- Multiple program support / editable exercise list per block
