# `fatal-pulse` — Technical Specification & Architecture Decision Record

**Binary:** `fpulse`  
**Tagline:** Real-time terminal telemetry (`+views/h` velocity & retention tracker) for synchronized short-form video launches across YouTube Shorts, TikTok, and Instagram Reels.  
**Ecosystem Role:** Public open-source prequel to `fatal-monitor` (`@FatalExceptionTales`).  
**License:** MIT

---

## 1. Architecture Decision Record (ADR-001: Host Stack Selection)

### Context
`fatal-pulse` will be built live on stream in **90 minutes** (100% in English) as the open-source prequel to `fatal-monitor` (a native hardware telemetry monitor built in **Rust + `egui`**). We evaluated two candidate stacks:
1. **Rust:** `ratatui` + `crossterm` + `reqwest` + `rusqlite` (`bundled`) + `clap` + `tokio`
2. **Python 3.12+:** `textual` / `rich` + `httpx` + `sqlite3` + `typer`

### Comparison Matrix

| Criterion | **Rust (`ratatui` + `rusqlite` + `tokio`)** | **Python (`textual` + `httpx` + `sqlite3`)** |
| :--- | :--- | :--- |
| **Audience & Brand Authority** | **High (10/10).** Aligns directly with low-level hardware/systems engineering ethos (`r/rust`, `r/commandline`, `HN`). | **Moderate (6.5/10).** Fast to write, but perceived as a standard API script rather than systems tooling. |
| **Code & Pattern Reuse in `fatal-monitor`** | **Direct (100%).** Time-series SQLite ring-buffers, `tokio::mpsc` collector channels, redacted config types, and crate idioms carry straight into `fatal-monitor`. | **Conceptual Only (0% code reuse).** Only SQL queries transfer. |
| **90-Minute Live-Coding Risk** | **Low (with `stream-starter` branch).** Pre-compiled Cargo cache (`~1s` incremental builds) + synchronous `rusqlite` (`bundled`) avoids async lifetime traps on stream. | **Very Low.** Minimal boilerplate (~280 LOC), zero borrow-checker friction. |
| **Distribution** | Single zero-dependency static binary (`fpulse`). | Requires Python 3.12+ / `uv` runtime environment. |

### Decision
**Selected Stack: Rust (`ratatui` + `reqwest` + `rusqlite` + `clap` + `tokio`).**  
To guarantee a smooth 90-minute live build without compiler stalls:
- Use **`rusqlite` (`features = ["bundled"]`)** instead of compile-time `sqlx` macros.
- Prepare a clean **`stream-starter`** branch with pre-warmed dependencies and TUI theme constants so live coding focuses strictly on SQLite time-series math, ingestion adapters, and live TUI sparkline wiring.

---

## 2. System Architecture & Repository Layout

```text
  +----------------------+   +------------------------+   +-------------------------+
  | YouTube Data API v3  |   | TikTok Public Metadata |   | Manual Studio CLI Input |
  | videos.list (1 unit) |   | yt-dlp --dump-json     |   | fpulse manual --slug .. |
  +----------+-----------+   +-----------+------------+   +------------+------------+
             |                           |                             |
             +---------------------------+-----------------------------+
                                         |
                                         v
                         +-------------------------------+
                         |   Ingestion Service (Tokio)   |
                         |  Redacted Headers · Timeouts  |
                         +---------------+---------------+
                                         |
                                         v
                         +-------------------------------+
                         |     SQLite Time-Series DB     |
                         |  WAL Mode · LAG() Velocity    |
                         +---------------+---------------+
                                         |
                                         v
                         +-------------------------------+
                         |     Ratatui Terminal UI       |
                         | +views/h · 24h Accel · Ratios |
                         +-------------------------------+
```

### Target Directory Structure
```text
fatal-pulse/
├── Cargo.toml
├── .env.example
├── .gitignore
├── LICENSE
├── README.md
├── sql/
│   └── 001_init.sql          # Idempotent schema + velocity views
├── src/
│   ├── main.rs               # Clap CLI entrypoint (`tui`, `poll`, `add`, `manual`, `--demo`)
│   ├── config.rs             # Env loader with redacted `SecretString`
│   ├── db.rs                 # rusqlite WAL connection, inserts & window queries
│   ├── models.rs             # Video, Snapshot, VelocityRow, Platform enum
│   ├── ingest/
│   │   ├── mod.rs            # Modular ingestion interface
│   │   ├── youtube.rs        # Phase 1: YouTube Data API v3 batch `videos.list`
│   │   ├── tiktok.rs         # Phase 1: `yt-dlp --dump-json --no-download`
│   │   └── instagram.rs      # Phase 2: IG Graph API v22+ (`views`, skip rate)
│   └── tui/
│       ├── mod.rs            # Crossterm terminal lifecycle & tick loop
│       └── ui.rs             # Fatal Dark tables, velocity deltas & sparklines
└── docs/
    ├── PROJECT_SPEC.md
    ├── LAUNCH_ROADMAP.md
    └── STREAM_CHEATSHEET_EN.md
```

---

## 3. SQLite Time-Series Schema (`sql/001_init.sql`)

Configured on connection open with:
- `PRAGMA journal_mode = WAL;` (concurrent TUI reads during background writes)
- `PRAGMA synchronous = NORMAL;`
- `PRAGMA foreign_keys = ON;`

```sql
CREATE TABLE IF NOT EXISTS videos (
    id            INTEGER PRIMARY KEY AUTOINCREMENT,
    slug          TEXT NOT NULL,                          -- Cross-platform key (e.g., 'gtx970')
    title         TEXT NOT NULL,
    platform      TEXT NOT NULL CHECK (platform IN ('youtube', 'tiktok', 'instagram')),
    external_id   TEXT NOT NULL,                          -- YT video ID, TikTok URL, or IG media ID
    published_at  TEXT NOT NULL,                          -- ISO-8601 UTC timestamp
    created_at    TEXT NOT NULL DEFAULT (strftime('%Y-%m-%dT%H:%M:%SZ', 'now')),
    UNIQUE (platform, external_id),
    UNIQUE (slug, platform)
);

CREATE TABLE IF NOT EXISTS snapshots (
    id                    INTEGER PRIMARY KEY AUTOINCREMENT,
    video_id              INTEGER NOT NULL REFERENCES videos(id) ON DELETE CASCADE,
    captured_at           TEXT NOT NULL DEFAULT (strftime('%Y-%m-%dT%H:%M:%SZ', 'now')),
    views                 INTEGER NOT NULL DEFAULT 0,
    likes                 INTEGER NOT NULL DEFAULT 0,
    comments              INTEGER NOT NULL DEFAULT 0,
    shares                INTEGER NOT NULL DEFAULT 0,
    saved                 INTEGER NOT NULL DEFAULT 0,
    -- Private Studio / Phase-2 retention metrics (nullable on public counter polls)
    yt_stayed_to_watch    REAL,                           -- Viewed vs. Swiped away % (target > 65%)
    yt_apv                REAL,                           -- Average Percentage Viewed %
    tt_avg_watch_sec      REAL,                           -- TikTok average watch time (s)
    tt_2s_drop            REAL,                           -- TikTok 0-2s drop-off %
    ig_avg_watch_sec      REAL,                           -- ig_reels_avg_watch_time / 1000.0 (s)
    ig_skip_rate          REAL                            -- reels_skip_rate %
);

CREATE INDEX IF NOT EXISTS idx_snapshots_video_time
    ON snapshots (video_id, captured_at DESC);
```

### Velocity (`+views/h`), First-24h Acceleration & Engagement Query
```sql
WITH deltas AS (
    SELECT
        s.video_id,
        s.captured_at,
        s.views,
        s.likes,
        s.comments,
        s.shares,
        s.saved,
        LAG(s.views) OVER (PARTITION BY s.video_id ORDER BY s.captured_at) AS prev_views,
        LAG(s.captured_at) OVER (PARTITION BY s.video_id ORDER BY s.captured_at) AS prev_time,
        ROW_NUMBER() OVER (PARTITION BY s.video_id ORDER BY s.captured_at DESC) AS rn
    FROM snapshots s
),
velocities AS (
    SELECT
        d.*,
        CASE
            WHEN d.prev_time IS NOT NULL
             AND (julianday(d.captured_at) - julianday(d.prev_time)) * 24.0 > 0.01
            THEN (d.views - d.prev_views) / ((julianday(d.captured_at) - julianday(d.prev_time)) * 24.0)
            ELSE 0.0
        END AS views_per_hour
    FROM deltas d
),
with_accel AS (
    SELECT
        vel.*,
        LAG(vel.views_per_hour) OVER (PARTITION BY vel.video_id ORDER BY vel.captured_at) AS prev_vph
    FROM velocities vel
)
SELECT
    v.slug,
    v.platform,
    v.title,
    w.views,
    w.likes,
    w.comments,
    ROUND(w.views_per_hour, 1) AS views_per_hour,
    ROUND(w.views_per_hour - COALESCE(w.prev_vph, w.views_per_hour), 1) AS vph_delta,
    ROUND(CAST(w.likes AS REAL) * 100.0 / MAX(w.views, 1), 2) AS like_rate_pct,
    ROUND(CAST(w.comments AS REAL) * 100.0 / MAX(w.views, 1), 3) AS comment_rate_pct,
    (
        SELECT s2.yt_stayed_to_watch FROM snapshots s2
        WHERE s2.video_id = v.id AND s2.yt_stayed_to_watch IS NOT NULL
        ORDER BY s2.captured_at DESC LIMIT 1
    ) AS latest_yt_stayed
FROM videos v
JOIN with_accel w ON w.video_id = v.id AND w.rn = 1
ORDER BY v.slug, v.platform;
```

---

## 4. Ingestion Adapter Contracts

### Phase 1 (90-Minute Live Stream MVP)
1. **YouTube Data API v3 (`src/ingest/youtube.rs`):**
   - Endpoint: `GET https://www.googleapis.com/youtube/v3/videos?part=statistics,snippet&id={ids}`
   - Auth: `X-Goog-Api-Key` request header (strictly forbidden in query parameters).
   - Quota cost: **1 unit** per batch of up to 50 video IDs (`10,000` daily free quota).
2. **TikTok Adapter (`src/ingest/tiktok.rs`):**
   - Invokes `yt-dlp --dump-json --no-download --no-warnings <video_url>` via `tokio::process::Command` with a `15s` hard timeout.
   - Parses `view_count`, `like_count`, `comment_count`, `repost_count`, `save_count`.
3. **Manual Studio Metrics CLI (`fpulse manual`):**
   - Command: `fpulse manual --slug gtx970 --yt-stayed 64.2 --yt-apv 78.5 --tt-2s-drop 31.0 --tt-avg-watch 29.4`
   - Appends a snapshot preserving the latest public counters while recording private retention percentages from YouTube/TikTok Studio.

### Phase 2 (Post-Stream Roadmap)
1. **Instagram Graph API v22+ (`src/ingest/instagram.rs`):**
   - Endpoint: `GET https://graph.facebook.com/v22.0/{ig-media-id}/insights?metric=views,reach,likes,comments,shares,saved,ig_reels_avg_watch_time,reels_skip_rate`
   - **Deprecation Rule:** Never query `plays` or `impressions` (deprecated and removed across Meta Graph API v22+ in April 2025). Convert `ig_reels_avg_watch_time` from milliseconds to seconds.
2. **YouTube Analytics API v2 (`reports.query`):**
   - Daily OAuth2 backfill of `engagedViews`, `views`, and `averageViewPercentage` filtered by `insightPlaybackLocationType==SHORTS_FEED` after the 24–48h studio lag window.

---

## 5. Live-Coding Security Rules (OBS Leak Prevention)

1. **Zero Secrets in URLs:** All API tokens travel in HTTP headers (`X-Goog-Api-Key`, `Authorization: Bearer ...`) so reqwest error traces never leak credentials on stream.
2. **Redacted Wrapper Types:** `SecretString` implements `fmt::Debug` and `fmt::Display` as `"***REDACTED***"`.
3. **Pre-Stream Environment Verification:** Never run `env`, `printenv`, or `cat .env` in an on-screen terminal; bind `.env` validation to `fpulse doctor` which prints only boolean presence (`YOUTUBE_API_KEY: [SET]`).
4. **Offline Resilience (`--demo`):** Built-in deterministic seed mode populates realistic 24h curves for `gtx970`, `rtx4090`, and `newworld_3090` if external APIs throttle mid-stream.
