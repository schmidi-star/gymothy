# Gymothy — Roadmap

This document captures product direction, core principles, and strategic bets. It is not a sprint backlog — day-to-day tasks live in GitHub Issues.

---

## What Gymothy Is

A strength training tracker built for intermediate-to-advanced lifters who already follow periodized programs and understand RPE. It assumes you know what you're doing and gets out of your way.

Gymothy is built around three ideas:
- **Block-level thinking** — training is organized in blocks, not just individual sessions
- **RPE-native** — effort is a first-class input, not an afterthought
- **You own your data** — no accounts, no cloud lock-in, no upselling

---

## What Gymothy Is Not

- A beginner app. It does not explain RPE, suggest programs, or hold your hand through setup.
- A social platform. No sharing, no leaderboards, no streaks.
- A gamified experience. No badges, no achievements, no engagement loops.
- A subscription product (for now). No paywalls, no freemium tiers.

---

## Core Principles

**Simplicity over completeness** — one HTML file, no framework, no build step. Every added dependency needs to earn its place.

**Data transparency** — the user can always see, export, or delete their data. Credentials never leave the device.

**Respect the user's intelligence** — the target user has an existing program and training history. The app should feel like a capable tool, not a guided experience.

---

## North Star

The long-term vision is for Gymothy to become the go-to tracker for serious lifters who are tired of spreadsheets but don't want a dumbed-down app. Two things would need to be true for that to happen:

**Removing the setup tax** — the biggest drop-off point for any new tracker is re-entering an existing program. A lot of intermediate lifters already track in Excel. An import flow — where you upload your spreadsheet and the app intelligently extracts your program — would remove that friction entirely and be a meaningful differentiator. This is not a near-term feature; it requires user research to validate that import friction is actually the primary barrier, and technical exploration to handle the diversity of how people structure their spreadsheets.

**A data layer that works for non-developers** — the current GitHub sync model works well but requires a personal access token, which limits who can realistically use Gymothy. If the app ever grows beyond a small technically-comfortable audience, a proper backend would be needed. What that looks like — self-hosted, a managed service, something else entirely — is an open question that should only be answered once there is clear evidence of demand.

These two bets are related: solving setup friction only matters if there's a data layer that can support the broader audience that comes with it. Neither is worth building speculatively.

---

## Known Technical Constraints

**Single-file architecture** — `gym-tracker.html` contains all CSS, HTML, and JS. This is a deliberate constraint for simplicity and GitHub Pages hosting. It should be preserved unless there is a strong reason to change it.

**No build step** — no npm, no bundler, no transpilation. Dependencies are loaded via CDN (currently Chart.js only). New features should follow this pattern.

**localStorage as primary store** — fast and zero-latency, but limited to ~5MB and tied to a single browser/device. GitHub sync is the workaround for multi-device use. If data complexity grows, IndexedDB should be evaluated as a complement.

**GitHub API for sync** — works well for the current user. Requires a personal access token, which limits adoption to technically comfortable users. Any expansion of the user base requires a different sync solution.

**Session key format** — `B{block}-W{week}-{day}-{YYYY-MM-DD}`. Changing this format would require a data migration for existing users. Treat it as stable.
