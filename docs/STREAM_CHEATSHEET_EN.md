# English Live-Coding Cheat Sheet (`fatal-pulse` 90-Min Stream)

Keep this open on a secondary monitor next to OBS.  
**Golden Rule:** State what you are about to build in **one clear sentence BEFORE typing**, type the block cleanly, then summarize the result in one sentence. Never improvise complex grammar mid-keystroke.

---

## 1. Stream Run-of-Show (90 Minutes)

- **00:00 – 08:00 | Act 0 — Hook & Architecture:** Pitch `@FatalExceptionTales`, show the ASCII data flow, run `fpulse doctor` on `stream-starter`.
- **08:00 – 33:00 | Act 1 — SQLite Time-Series Engine:** Write `001_init.sql` (`WAL` mode) + `db.rs` inserts and `LAG()` velocity query.
- **33:00 – 63:00 | Act 2 — Ingestion Adapters:** Implement batch YouTube Data API v3 (`X-Goog-Api-Key`), `yt-dlp` TikTok JSON parser, and `fpulse manual`.
- **63:00 – 83:00 | Act 3 — Ratatui Live Dashboard:** Wire `VelocityRow` data into the Fatal Dark table + sparklines; poll live `@FatalExceptionTales` videos!
- **83:00 – 90:00 | Act 4 — Ship & Q&A:** Push `v0.1.0` to GitHub, preview Phase 2 (Instagram Graph API v22+) and tease `fatal-monitor`.

---

## 2. Opening Pitch (Minutes 0–5)

- *"Welcome in everyone! Today we're building `fatal-pulse` live in 90 minutes—a terminal telemetry dashboard in Rust that tracks `+views per hour` across YouTube Shorts, TikTok, and Instagram Reels."*
- *"On `@FatalExceptionTales`, I drop hardware disaster Shorts at the exact same minute across all three platforms. Native studio apps hide real-time velocity and force you to juggle tabs—so we're building our own SQLite flight recorder."*
- *"Think of this project as the open-source prequel to `fatal-monitor`, our upcoming Rust hardware telemetry app."*

---

## 3. Signposting Phrases (Say Before Typing)

- *"Let's zoom out for ten seconds so everyone sees the full data flow."*
- *"Before we touch a single HTTP request, let's lock down our SQLite schema."*
- *"I'll wire up the happy path first, and then we'll harden timeouts and error handling."*
- *"Let's compile right here and verify our assumptions before moving to the UI."*
- *"Now comes the fun part—plugging real SQLite rows straight into `ratatui`."*

---

## 4. Narrating SQLite & Velocity Math

- *"First thing we enable is **WAL mode**—Write-Ahead Logging—so background ingestion never blocks TUI rendering."*
- *"Notice `slug` + `platform` is unique: `'gtx970'` ties the exact same Short together across YouTube, TikTok, and Reels."*
- *"Instead of computing deltas in Rust loops, we let SQLite's `LAG()` window function grab the previous snapshot partitioned by `video_id`."*
- *"Dividing the view delta by elapsed `julianday()` hours gives us accurate `+views per hour` even when polling intervals aren't exact."*
- *"Early retention metrics like 'Stayed to watch' aren't on public endpoints in hour one, so `fpulse manual` lets us stamp them right alongside automated snapshots."*

---

## 5. Narrating APIs & Stream OpSec

- *"Quick OpSec rule when streaming code: we send `YOUTUBE_API_KEY` inside the `X-Goog-Api-Key` header—never in the URL query string. Even if an HTTP error prints the URL on OBS, our key never leaks."*
- *"YouTube's `videos.list` endpoint lets us batch up to 50 video IDs in one call for a single quota unit—polling every five minutes barely uses 3% of the free daily quota."*
- *"For TikTok in Phase 1, spawning `yt-dlp --dump-json --no-download` with a 15-second timeout gives us reliable counters without maintaining a brittle scraper."*
- *"In Phase 2 we'll add Instagram Graph API v22—using the unified `views` and `reels_skip_rate` metrics since Meta deprecated `plays` and `impressions`."*

---

## 6. Calm Compiler & Debugging Recovery Lines

When Rust or an API throws an error live, **pause, breathe, and read top-down**:
- *"Hold on—the compiler actually caught something helpful here. Let's read the primary error message from the top."*
- *"Classic borrow-checker catch: we're holding an immutable borrow across this call, so let's scope the statement cleanly."*
- *"That returned `0.0 views/hour`—which makes total sense on snapshot number one because `LAG()` has no prior row to compare against yet. Watch what happens on poll number two."*
- *"If an external endpoint throttles us mid-stream, we pass `--demo` to load our deterministic 24-hour curve and keep building the UI."*

---

## 7. Technical Pronunciation Quick-Reference

| Term | Natural Spoken Pronunciation |
| :--- | :--- |
| **SQLite** | *"S-Q-L-ite"* or *"sequel-lite"* (pick one and stay consistent) |
| **Idempotent** | *"eye-dem-POH-tent"* (`CREATE TABLE IF NOT EXISTS`) |
| **Schema** | *"SKEE-muh"* (hard **k** sound) |
| **Deprecated** | *"DEP-ruh-kay-ted"* (stress on first syllable) |
| **Asynchronous** | *"ay-SIN-kruh-nuhs"* |
| **Tuple / Struct / Trait** | *"tuh-pull"* · *"strukt"* · *"trayt"* (crisp final **t**) |

---

## 8. Closing & Call to Action (Minutes 85–90)

- *"There it is—live cross-platform `+views/hour` telemetry running inside a single Rust binary."*
- *"I just pushed `v0.1.0` under the MIT license—grab the repo link in the description, drop a star if you want to join Phase 2 for Instagram Graph API, and subscribe to `@FatalExceptionTales` so you don't miss `fatal-monitor`!"*
