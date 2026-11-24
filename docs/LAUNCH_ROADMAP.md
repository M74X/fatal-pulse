# Episodic Launch & Live-Stream Roadmap (`fatal-pulse`)

**Format:** 5 modular live-coding episodes (`40–55 minutes` per stream, 100% in English) instead of a single marathon session.  
**Start Trigger:** Immediately after `@FatalExceptionTales` publishes **Batch 02 (`20` Shorts + `2` horizontal `8–10 min` "Top 10" compilations)**.  
**Why Parallel Execution Works:** Starting at 20 Shorts seeds SQLite with `60` real cross-platform time series (`YT` + `TT` + `IG`) on Day 1, while Batches 03–05 (`Shorts #21–#50` + `3` more "Top 10" compilations) launch live **alongside** Episodes 1–5 so viewers watch `fpulse` measure real releases in real time.

---

## Pre-Stream Prep Window (14 Days Alongside Batch 02 Publishing)

### Week 1 (Days 1–7): Private Reference Prototype & Starter Checkpoints
| Day | Engineering Prep (40–50 min) | Spoken English Drill (20 min, Out Loud) |
| :--- | :--- | :--- |
| **Day 1** | Public `fatal-pulse` repo live (`README.md`, `LICENSE`, `.env.example`, `docs/`). Create local off-stream `proto` branch. | **60-Second Series Pitch (5x):** Explain why we build in modular task-batch episodes while Batches 03–05 go live. |
| **Day 2** | Prototype **Task Batch 1** (`config.rs` `SecretString`, `fpulse doctor`, `sql/001_init.sql`, `db.rs` `LAG()` velocity query). | **SQLite Narration:** Practice explaining `WAL` mode, `PARTITION BY`, and `julianday()` velocity math aloud. |
| **Day 3** | Prototype **Task Batch 2** (`src/ingest/youtube.rs` header-authenticated batch `videos.list` + `fpulse poll`). | **API & OpSec Narration:** Explain `X-Goog-Api-Key` header security and `1-unit` batching for 50 IDs. |
| **Day 4** | Prototype **Task Batch 3** (`src/ingest/tiktok.rs` `yt-dlp --dump-json` + `fpulse manual` Studio retention CLI). | **CLI Walkthrough:** Narrate subcommands and why early *Stayed to watch* needs a clean manual override. |
| **Day 5** | Prototype **Task Batch 4** (`src/tui/` Fatal Dark `ratatui` tables, `+views/h` badges, velocity sparklines, `--demo`). | **TUI Narration:** Describe terminal constraints, keyboard controls (`r` poll, `q` quit), and live velocity spikes. |
| **Day 6** | Tag clean starter state `ep01-start` (`Cargo.toml` + pre-warmed `target/` + module stubs) and test OBS layout (`>= 18pt` font). | **10-Min Recorded OBS Check:** Record a short dry run; verify zero leaked env vars and crisp microphone audio. |
| **Day 7** | Freeze private reference prototype; catalog the first `20` Short IDs + `2` Long-Form compilation IDs in a seed file. | **Shadowing & Rest:** Shadow 15 minutes of a concise English systems programmer stream and rest. |

### Week 2 (Days 8–14): Episode 1 Rehearsals & Series Kickoff
| Day | Rehearsal / Broadcast Block | Spoken English Focus |
| :--- | :--- | :--- |
| **Day 8** | **Episode 1 Dry Run #1 (40 min):** Code `SecretString`, `001_init.sql`, and `db.rs` from `ep01-start` while speaking aloud. | State intent in one sentence *before* typing each function (zero dead air > 8s). |
| **Day 9** | **Compiler & SQL Recovery Drill (25 min):** Practice calm recovery from a borrow-checker diagnostic and an empty-delta `LAG()` row. | Automatic calm debugging transitions (*"Let's read the top compiler diagnostic first..."*). |
| **Day 10** | **Episode 1 Full Dress Rehearsal (45 min in OBS):** Uninterrupted local recording from intro hook to `git push`. | Review at `1.5x` speed: check pacing, clarity, and confirm a comfortable 40–45 min runtime. |
| **Day 11** | **Buffer / Channel Sync:** Publish Batch 02's final Shorts + *"Top 10"* `8–10 min` compilation and link *Related Video*. | Practice introducing the channel's `20` Shorts + `2` compilations dataset in English. |
| **Day 12** | **Episode 2 Preview Walkthrough (30 min):** Light dry run of `youtube.rs` so you already know where Episode 1 hands off. | Smooth episode-to-episode cliffhanger phrasing. |
| **Day 13** | **Pre-Flight Check Only:** Warm `cargo build`, verify `fpulse doctor`, schedule **Episode 1** YouTube Live room. | 10-minute light vocal warm-up only; rest early. |
| **Day 14** | **LIVE — Episode 1 (45 min):** Ship the SQLite Time-Series Engine & OBS-safe CLI foundation live on stream! | Calm, unhurried 4-part structure (`5m` Hook -> `30m` Build -> `5m` Seed Query Demo -> `5m` Q&A). |

---

## Modular Live-Stream Episode Guide (40–55 Minutes Each)

Run **1–2 episodes per week** alongside Batches 03–05 (`Shorts #21–#50` + Compilations `#3–#5`). Before each episode, do **one 30-minute rehearsal the day prior** focusing only on that episode's vocabulary.

| Episode | Duration | Task Batch Scope (~80–120 LOC) | Live Channel Tie-In (`@FatalExceptionTales`) | Deliverable / Tag |
| :--- | :---: | :--- | :--- | :--- |
| **Episode 1** | `45 min` | Workspace setup, `SecretString` + `fpulse doctor`, `sql/001_init.sql` (`WAL`), `db.rs` inserts & `LAG()` `+views/h` query. | Seed DB with baseline snapshots from **Batches 01–02** (`20` Shorts + `2` Top-10 compilations). | `v0.1.0-alpha.1` |
| **Episode 2** | `45 min` | `src/ingest/youtube.rs` (`X-Goog-Api-Key` header, 50-ID `1-unit` batching) + `fpulse add` & `fpulse poll`. | Poll live counters for **Batch 03** Shorts + compare Short vs. `8–10m` Related Video growth. | `v0.1.0-alpha.2` |
| **Episode 3** | `40 min` | `src/ingest/tiktok.rs` (`yt-dlp --dump-json` w/ `15s` timeout) + `fpulse manual` private Studio retention CLI. | Compare YouTube vs. TikTok velocity side-by-side and log real *Stayed to watch* (`>65%` target). | `v0.1.0-beta.1` |
| **Episode 4** | `50 min` | `src/tui/` (`ratatui` Fatal Dark dashboard, `+views/h` delta badges, sparklines, background poll tick, `--demo`). | Monitor a newly published **Batch 04** Short live inside the terminal TUI and cut first stable release! | **`v0.1.0` Release** |
| **Episode 5** | `50 min` | `src/ingest/instagram.rs` (Graph API v22+ `views` & `reels_skip_rate`) + YT Analytics v2 backfill + architecture handoff. | Full 3-platform telemetry across **Batch 05 (`Short #50`)** + official bridge to **`fatal-monitor`**. | **`v0.2.0` Release** |
