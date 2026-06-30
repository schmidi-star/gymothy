# Gymothy — Project Context for Claude Code

## What this is
Gymothy is a personal gym tracking web app for intermediate-to-advanced lifters who follow periodised, RPE-based training programs with a block structure (5 working weeks + 1 deload week). Built initially for personal use, with broader launch ambitions. Target user (User A) already follows a periodised program and understands RPE — this is explicitly NOT a beginner app. Core differentiators: block-level thinking over session-level logging, RPE-native autoregulation, full data ownership, no gamification or upselling.

North star: *"Intermediate lifters who train with intent complete more blocks with better progression data than they did before Gymothy."*

## Architecture
- Single-file vanilla JS app: `gym-tracker.html`. No framework, no build step. All CSS, HTML, and JS live in one file.
- `demo.html` — separate file with hardcoded dummy data, used for LinkedIn/portfolio purposes. Do not wire this to real data or localStorage.
- Data stored in `localStorage` under key `gymothy`, manually synced to `data.json` in a private GitHub repo (`gymothy-data`) via the GitHub Contents API.
- Live app: `https://schmidi-star.github.io/gymothy/gym-tracker.html`
- GitHub username: `schmidi-star`
- Dependency: Chart.js 4.4.1 via cdnjs. No other dependencies.

## Data model
All state is one JSON object:
```json
{
  "currentBlock": 1,
  "blocks": { "1": { "startDate": "YYYY-MM-DD", "label": "Block 1" } },
  "sessions": { "B1-W1-A-2026-06-22": { "sets": { "rdl": [{ "w":40,"r":10,"rpe":7 }] }, "comment": "" } },
  "weights": [{ "date": "YYYY-MM-DD", "kg": 68.5 }]
}
```
- Session key format (stable, don't change): `B{block}-W{week}-{day}-{YYYY-MM-DD}`
- Exercise IDs — Day A: `rdl`, `bss`, `ht`, `ghr`, `abd`. Day B: `idp`, `dlr`, `scr`, `lp`, `te`. Day C: `bs`, `db`, `slp`, `fp`, `core`.
- `settings` (ghUser, ghRepo, ghToken) live in localStorage only — NEVER write these to `data.json` or any export. Use `safeStateForSync()` pattern (strips `settings` key) for any GitHub save or JSON export. Credentials must never leave the device.

## Program structure
- Day A: posterior chain/glutes. Day B: upper push/pull. Day C: full body, squat-led.
- Block = 5 working weeks + 1 deload week (week 6).
- Deload week: 2 sets max per exercise, RPE cap ≤6, exercise names editable per-session for variations (e.g. SSB squat instead of back squat). These name edits are scoped to the session, not a separate persisted list.
- New block starts fresh; all historical data is preserved, never deleted.

## Known fixed bugs (don't reintroduce)
- Session auto-creation bug: viewing a week/day must NOT silently create an empty session. Sessions are only created when the user explicitly adds a set (`createIfMissing = true`). `renderExercises` and `getSessForCurrentView` must not auto-create.
- Credentials leaking into synced data: always strip `settings` before GitHub save or export.

## Known gaps / not yet implemented
- No strict RPE range clamping (1–10) on input.
- No partial set detection (weight entered but no reps, or vice versa).
- No visual indicator on week selector for which weeks have data.
- No multi-program support — exercises are hardcoded.
- No relative load tracking (e.g. squat at X% bodyweight) — planned.

## Dev workflow (follow this order, don't skip steps)
1. Make changes, commit to `dev` branch (never commit directly to `main`).
2. Verify locally with Live Server (localhost:5500).
3. Test on phone via GitHub Pages `dev` branch — real localStorage data with full GitHub sync only meaningfully exists on phone, so this step matters.
4. Once verified, PR/merge `dev` → `main`.

## Environment notes
- Terminal is PowerShell on Windows. Do NOT chain commands with `&&` — PowerShell needs separate commands or `;`.
- Git for Windows is installed; token is embedded in the remote URL already, so plain `git pull` / `git push` should work once on a non-blocked network.

## Working conventions
- Tea (the user) owns product design, wireframing, analytics decisions, and user research. A separate developer partner owns technical architecture/backend. Claude Code's job here is implementation against tickets, not product decisions.
- Tea writes structured tickets (issue description + acceptance criteria, P0/P1 flagged inline) before features get built — look for ticket-style instructions (e.g. "GYM-021") rather than vague requests.
- Backlog/bugs belong in GitHub Issues, not markdown files. `docs/ROADMAP.md` is for stable strategic direction only — don't add fast-changing backlog items there.
- Prefer the lowest-implementation-overhead solution when there's a choice (e.g. a pulsing CTA that focuses an existing form rather than building a new modal).
- Existing working screens in `gym-tracker.html` are the source of truth for UI patterns — match existing card/button/modal styles (see CSS custom properties at the top of the file) rather than introducing new patterns. New/unbuilt screens get wireframed in Figma first.
- New icons should be proper SVGs (not emoji), typically designed in Claude Design first, then dropped into the file.
- Documentation lives in `docs/SPEC.md` and `docs/ROADMAP.md` in the repo, plus a root `README.md`.

## On the horizon (context, not necessarily current sprint)
- PWA conversion as a bridge to a future native app (Capacitor or React Native).
- Backend migration to Supabase (auth, DB, storage) — deferred, but keep frontend/backend logic reasonably separated so this is low-effort later.
- Excel import/export as a possible differentiator (SheetJS + Claude API parsing) — exploratory, not decided.
- Onboarding flow for User A — efficient setup assuming prior program knowledge, not hand-holding.
