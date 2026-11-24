# English Live-Coding Cheat Sheet (`fatal-pulse` Episodic Series)

Keep this open on a secondary monitor next to OBS.  
**Golden Rule:** Each stream is a focused **40–55 minute task batch** (~80–120 lines of Rust). State what you are about to build in **one clear sentence BEFORE typing**, type the block calmly, then summarize the result in one sentence. Never improvise complex grammar mid-keystroke.

---

## 1. Universal 45-Minute Episode Template

Every live episode follows the exact same low-stress rhythm:
- **00:00 – 05:00 | Recap & Today's Task Batch:** Pitch `@FatalExceptionTales` (`20+` Shorts + `Top 10` compilations), show where we are in the ASCII diagram, and state today's single deliverable.
- **05:00 – 22:00 | Core Implementation Part A:** Write the data structures / SQL / HTTP request path and run `cargo check`.
- **22:00 – 37:00 | Core Implementation Part B:** Wire the CLI command or UI view and test against real `@FatalExceptionTales` videos.
- **37:00 – 45:00 | Commit, Live Telemetry Check & Teaser:** Commit & push today's tag, inspect live `+views/hour` numbers, answer 2–3 chat questions, and tease the next episode.

---

## 2. Per-Episode Opening Hooks (Minutes 0–3)

- **Episode 1 (SQLite Core & OpSec):**
  *"Welcome in everyone! Today kicks off our `fatal-pulse` live-coding series in Rust. Now that `@FatalExceptionTales` has 20 Shorts and two 10-minute compilations live, we're building a terminal telemetry flight recorder—starting today with leak-proof config and our SQLite `WAL` time-series engine."*
- **Episode 2 (YouTube Batch Adapter):**
  *"Welcome back to `fatal-pulse` Episode 2! Last stream we locked in our SQLite `LAG()` velocity engine—today we're wiring up the YouTube Data API v3 batch adapter to pull up to 50 Shorts and compilations in a single quota unit."*
- **Episode 3 (TikTok + Studio Retention CLI):**
  *"Welcome to Episode 3! Today we go multi-platform: we're building an async `yt-dlp` adapter for TikTok counters plus `fpulse manual` so we can log private Studio retention like 'Stayed to watch' right inside SQLite."*
- **Episode 4 (Ratatui Fatal Dark Dashboard — `v0.1.0`):**
  *"Welcome to Episode 4—today is visual payoff day! We're taking every SQLite velocity row we built in Episodes 1 through 3 and rendering a live `ratatui` dashboard with sparklines to track our newest hardware disaster Short in real time."*
- **Episode 5 (Instagram Graph API v22+ & Bridge to `fatal-monitor`):**
  *"Welcome to Episode 5! Today we complete the three-platform triangle with Instagram Graph API v22—using the modern `views` and `reels_skip_rate` metrics—and show how these exact Rust patterns carry straight into `fatal-monitor`."*

---

## 3. Signposting Phrases (Say Before Typing)

- *"Today's scope is super focused—we only care about getting this single module rock-solid."*
- *"Let's zoom out for ten seconds so new viewers see where today's task fits in the architecture."*
- *"I'll wire up the happy path first, run `cargo check`, and then harden timeouts and error handling."*
- *"Let's compile right here and verify our assumptions on real channel data before moving on."*

---

## 4. Narrating SQLite & Velocity Math (Episodes 1 & 4)

- *"First thing we enable is **WAL mode**—Write-Ahead Logging—so background polling never blocks TUI rendering."*
- *"Notice `slug` + `platform` is unique: `'gtx970'` ties the exact same Short together across YouTube, TikTok, and Reels, and `format` lets us track our 10-minute 'Top 10' compilations right alongside the Shorts."*
- *"Instead of computing deltas in messy Rust loops, we let SQLite's `LAG()` window function grab the previous snapshot partitioned by `video_id`."*
- *"Dividing the view delta by elapsed `julianday()` hours gives us accurate `+views per hour` even when polling intervals drift."*

---

## 5. Narrating APIs & Stream OpSec (Episodes 1, 2, 3 & 5)

- *"Quick OpSec rule when streaming code: we send `YOUTUBE_API_KEY` inside the `X-Goog-Api-Key` header—never in the URL query string. Even if an HTTP error prints the URL on OBS, our key never leaks."*
- *"YouTube's `videos.list` endpoint lets us batch up to 50 video IDs in one call for a single quota unit—polling every five minutes barely uses 3% of the free daily quota."*
- *"For TikTok, spawning `yt-dlp --dump-json --no-download` with a 15-second timeout gives us reliable public counters without maintaining a brittle scraper."*
- *"On Instagram Graph API v22+, we query `views` and `reels_skip_rate`—never `plays` or `impressions`, which Meta deprecated in April 2025."*

---

## 6. Calm Compiler & Debugging Recovery Lines

When Rust or an API throws an error live, **pause, breathe, and read top-down**:
- *"Hold on—the compiler actually caught something helpful here. Let's read the primary error message from the top."*
- *"Classic borrow-checker catch: we're holding an immutable borrow across this call, so let's scope the statement cleanly."*
- *"That returned `0.0 views/hour`—which makes total sense on snapshot number one because `LAG()` has no prior row to compare against yet. Watch what happens on poll number two."*
- *"If an external endpoint throttles us mid-stream, we pass `--demo` to load our deterministic 24-hour curve and keep moving."*

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

## 8. Episode Sign-Off (Last 3 Minutes)

- *"That wraps today's task batch—clean build, zero warnings, and live channel telemetry flowing into SQLite."*
- *"I just pushed today's checkpoint to GitHub under the MIT license. Grab the link in the description, drop a star on `fatal-pulse`, and join us in the next episode where we tackle [next module]!"*
