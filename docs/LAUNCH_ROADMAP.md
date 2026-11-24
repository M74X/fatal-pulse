# 14-Day Launch & Live-Stream Preparation Roadmap

**Target Event:** 90-minute live-coding stream (100% in English) building `fatal-pulse` (`fpulse`) for the `@FatalExceptionTales` community.

---

## Sprint 1 (Days 1–7): Specification Lock, Private Prototype & `stream-starter` Branch

| Day | Engineering Milestone (45–60 min) | Spoken English Practice (20–30 min, Out Loud) |
| :--- | :--- | :--- |
| **Day 1** | Claim public `fatal-pulse` GitHub repo with `README.md`, `LICENSE`, `.gitignore`, `.env.example`, and `docs/`. Initialize private `proto` workspace locally. | **60-Second Hook Drill (5x):** Deliver the opening pitch aloud—why synchronized launches need `+views/h` telemetry and how `fatal-pulse` sets up `fatal-monitor`. |
| **Day 2** | Implement `sql/001_init.sql` + `src/db.rs` (`WAL` mode, `videos`, `snapshots`, `LAG()` velocity & acceleration query) in private prototype. | **SQLite Narration:** Practice explaining `PRAGMA journal_mode = WAL`, `PARTITION BY`, and `julianday()` deltas naturally in English. |
| **Day 3** | Build `src/ingest/youtube.rs` (`X-Goog-Api-Key` header batching) and `src/ingest/tiktok.rs` (`yt-dlp --dump-json` with 15s timeout). | **API & OpSec Narration:** Practice explaining *why* keys belong in headers rather than query strings while coding. |
| **Day 4** | Implement `fpulse add`, `fpulse manual`, `fpulse doctor`, and `fpulse --demo` deterministic seed mode. | **CLI Walkthrough:** Narrate subcommands and flags aloud as if onboarding a viewer who just joined chat. |
| **Day 5** | Build `src/tui/ui.rs` (`ratatui` Fatal Dark layout, `+views/h` color badges, sparklines) and trim total prototype under ~450 LOC. | **UI Layout Narration:** Practice describing terminal layout constraints, keyboard shortcuts (`r` refresh, `q` quit), and non-blocking loops. |
| **Day 6** | Distill clean **`stream-starter`** branch: `Cargo.toml` + pre-compiled `target/` cache + `SecretString` + clean module stubs (`todo!("...")`). | **Recorded 10-Min OBS Audit:** Record yourself coding one module in OBS; verify zero leaked secrets, readable font (`>= 18pt`), and clear mic levels. |
| **Day 7** | Freeze private prototype as off-screen reference sheet. Zero new features added after today. | **Shadowing & Rest:** Shadow 15 minutes of a top English systems streamer (pacing, signposting, pauses) and rest. |

---

## Sprint 2 (Days 8–14): Timed Modular Rehearsals & Live Broadcast

| Day | Timed Rehearsal Block | Spoken English Focus |
| :--- | :--- | :--- |
| **Day 8** | **Act 1 Rehearsal (25 min):** Reset `stream-starter` -> live-code `001_init.sql` and `db.rs` velocity queries while speaking nonstop. | State intent *before* typing each block to eliminate dead air > 8 seconds. |
| **Day 9** | **Act 2 Rehearsal (30 min):** Live-code `youtube.rs` + `tiktok.rs` + `fpulse manual` override flow against real `@FatalExceptionTales` Shorts. | Smooth transitions between HTTP payloads, JSON structs, and SQLite inserts. |
| **Day 10** | **Act 3 Rehearsal (25 min):** Wire `db.rs` rows into `ratatui` tables and velocity sparklines; test `--demo` fallback switch. | High-energy delivery during the visual TUI payoff when real metrics light up. |
| **Day 11** | **Chaos & Recovery Drill (25 min):** Deliberately trigger a borrow-checker error, a missing `yt-dlp` binary, and an HTTP 403—recover calmly aloud. | Automatic calm debugging phrases (*"Let's read the first compiler diagnostic from the top..."*). |
| **Day 12** | **Full 90-Minute Dress Rehearsal (Local OBS Recording):** Uninterrupted run from `git checkout stream-starter` to final commit. | Watch playback at 1.5x: check WPM, filler words, terminal contrast, and confirm 80–85 min runtime buffer. |
| **Day 13** | **Pre-Flight Check Only:** Reset `stream-starter`, warm `cargo build`, run `fpulse doctor`, schedule YouTube live room & community post. | Light 10-minute vocal warm-up only. Protect voice and sleep early. |
| **Day 14** | **LIVE STREAM (90 Minutes):** Build `fatal-pulse` live, track active Shorts velocity in real time, and push `v0.1.0` to GitHub! | Execute the 4-Act run-of-show (`0–10m` Hook/Arch, `10–35m` SQLite, `35–65m` Adapters, `65–90m` TUI Reveal + Q&A). |
