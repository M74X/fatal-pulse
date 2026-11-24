# fatal-pulse (`fpulse`)

[![License: MIT](https://img.shields.io/badge/License-MIT-red.svg)](LICENSE)
[![Built with Rust](https://img.shields.io/badge/Built%20with-Rust-dca282.svg)](https://www.rust-lang.org/)
[![TUI: Ratatui](https://img.shields.io/badge/TUI-Ratatui-ffd60a.svg)](https://ratatui.rs/)

**Real-time terminal telemetry (`+views/hour` velocity & retention flight recorder) for synchronized short-form video launches across YouTube Shorts, TikTok, and Instagram Reels.**

> **Ecosystem:** Official open-source prequel to `fatal-monitor` — built in public for the [@FatalExceptionTales](https://www.youtube.com/@FatalExceptionTales) engineering & hardware disaster community.

---

## Why `fatal-pulse` Exists

When publishing synchronized vertical videos across **YouTube Shorts**, **TikTok**, and **Instagram Reels** at the exact same minute, native studio dashboards force you to refresh three separate tabs—and hide raw **velocity (`+views/hour`)** and **first-24h acceleration**.

`fatal-pulse` is a lightweight, single-binary Rust CLI & Terminal UI (`ratatui` + `SQLite WAL`) that polls public platform counters, merges manual first-24h retention metrics (*Viewed vs. Swiped away*, *Average Percentage Viewed*, *0–2s Drop-off*), and renders live cross-platform velocity curves right inside your terminal.

---

## Planned Architecture & Stack

- **Language & Runtime:** Rust (`tokio` async runtime, single static binary `fpulse`)
- **Terminal UI:** `ratatui` + `crossterm` (Fatal Dark palette: `#0D1117` obsidian, `#FF3B30` fatal crimson, `#FFD60A` neon yellow)
- **Time-Series Storage:** `rusqlite` (`bundled`, `WAL` journal mode, zero external DB server) using SQL window functions (`LAG()`) for drift-tolerant `+views/hour` & `+views/hour²` computation
- **Modular Ingestion Adapters (Built Across Episodic Live-Coding Streams):**
  - **Episodes 1–4 (`v0.1.0` Core Series — Starts After Short #20):**
    - **Episode 1 — Time-Series Core:** OBS-safe redacted config (`fpulse doctor`) + SQLite `WAL` schema & `LAG()` `+views/hour` velocity queries seeded with real Batch 01–02 telemetry.
    - **Episode 2 — YouTube Data API v3:** Batch `videos.list` (`<= 50` video IDs per single `1-unit` quota request, header-authenticated via `X-Goog-Api-Key`).
    - **Episode 3 — TikTok & Studio CLI:** Subprocess adapter over `yt-dlp --dump-json --no-download` + `fpulse manual` instant CLI injection for private early retention metrics (`yt_stayed_to_watch`, `yt_apv`, `tt_avg_watch_sec`, `tt_2s_drop`).
    - **Episode 4 — Ratatui Live Dashboard:** Fatal Dark terminal tables, `+views/h` velocity deltas, and sparklines tracking live Batch 03–05 Short & Long-Form drops (`v0.1.0` tag).
  - **Episode 5 (`v0.2.0` Expansion & Bridge to `fatal-monitor`):**
    - **Instagram Graph API v22+:** Native Reels insights (`views`, `reach`, `likes`, `comments`, `shares`, `saved`, `ig_reels_avg_watch_time`, `reels_skip_rate` — compliant with April 2025 `plays`/`impressions` deprecation).
    - **YouTube Analytics API v2:** Automated OAuth2 daily backfill (`engagedViews`, `averageViewPercentage` filtered by `SHORTS_FEED`).

---

## Documentation

- [Technical Specification & ADR (`docs/PROJECT_SPEC.md`)](docs/PROJECT_SPEC.md) — SQLite schema, window queries, adapter contracts, and OBS live-coding security rules.
- [Episodic Live-Stream Roadmap (`docs/LAUNCH_ROADMAP.md`)](docs/LAUNCH_ROADMAP.md) — Preparation sprints and 5 modular task-batch stream episodes (`40–55 min` each) running in parallel with active channel releases.
- [Live-Coding Narration Guide (`docs/STREAM_CHEATSHEET_EN.md`)](docs/STREAM_CHEATSHEET_EN.md) — Per-episode run-of-show and natural spoken English walkthrough notes.

---

## Security by Design (Live-Stream Safe)

- API keys are transmitted strictly via HTTP headers (`X-Goog-Api-Key`), never in URL query parameters where error logs could expose them on stream.
- All credentials are wrapped in redacted types (`***REDACTED***` on `Debug`/`Display`) and loaded from `.env` (see [`.env.example`](.env.example)).

---

## License

Released under the [MIT License](LICENSE).
