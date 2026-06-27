# My Agent Skills

A catalog of custom [Claude Agent Skills](https://github.com/anthropics/skills) I've built or installed.
Each entry below summarizes what the skill does, when it triggers, how to invoke it, and what it
depends on. The full skills (SKILL.md + scripts) live in my local Claude skills library.

> Last updated: 2026-06-27

| Skill | One-liner | Invoke | Source |
|-------|-----------|--------|--------|
| [`api-scout`](#api-scout) | Full extraction of any developer portal's API docs into a code-ready markdown reference | `/api-search <url>` | Built with skill-creator |
| [`watch`](#watch) | Let Claude actually watch a video (frames + transcript) and answer questions about it | `/watch <url-or-path> [question]` | [bradautomates/claude-video](https://github.com/bradautomates/claude-video) (MIT) |

---

## api-scout

**What it does.** Extracts comprehensive API documentation from any developer portal —
endpoints, auth methods, base URLs, rate limits, request/response examples, SDKs, and pricing
tiers — and formats it as a structured markdown reference you can paste directly next to your
code and start building from. The goal is a *full extraction*, not a summary.

**When it triggers.** Whenever you paste a developer-portal URL (e.g. `developer.bestbuy.com`,
`stripe.com/docs`), ask "find the APIs for X", "what can I build with X's API", "scout this
developer portal", or type `/api-search`. It also handles comparison requests like
"compare Best Buy API vs Amazon API".

**How to invoke.**
```
/api-search https://developer.bestbuy.com/
find the APIs on stripe.com/docs
what can I build with the Twilio API?
compare Best Buy and Amazon product APIs
```

**How it works.** A five-phase pipeline:
1. **Portal identification** — known portal (fast-path) vs unknown; map the doc architecture.
2. **Discovery crawl** — for unknown portals, inventory every API section before extracting.
3. **Full extraction** — per section: identity, auth, endpoints (with real request/response
   examples), rate limits, SDKs, pricing, errors, and webhooks.
4. **Fallback behavior** — synthesizes labeled examples and notes gaps when docs are thin
   (never omits a section).
5. **Comparison mode** — runs full extraction on two portals and appends a side-by-side table
   plus per-use-case recommendations.

**Output.** A structured markdown API reference, delivered both inline in chat and as a
downloadable `[portal-name]-api-reference.md` file. Runs a quality checklist before presenting
(every endpoint has a request + response example, auth header/param is exact, base URL is
versioned, etc.).

**Dependencies.** Web access for fetching/crawling portal pages. No API keys required.

---

## watch

**What it does.** Gives Claude a video input. A Python script downloads the video, extracts
auto-scaled frames as JPEGs with ffmpeg, pulls a timestamped transcript (native captions first,
Whisper API as fallback), and hands the frames + transcript to Claude so it can answer questions
about what's in the video.

**When it triggers.** When you paste a video URL (YouTube, Vimeo, X, TikTok, Twitch, most
yt-dlp-supported sites), point at a local video file (`.mp4`, `.mov`, `.mkv`, `.webm`, …), say
"watch this" / "summarize this video" / "what happens at 2:30", or type `/watch`.

**How to invoke.**
```
/watch https://youtu.be/abc what language is this in?
/watch ./demo.mp4
/watch "$URL" --start 2:15 --end 2:45     # focus on a section at higher frame rate
```

**How it works.**
- Downloads with `yt-dlp`, extracts frames with `ffmpeg`, then reads each frame so Claude
  "sees" the video.
- Transcript comes from native captions (free, preferred) or, if none exist, by sending the
  extracted audio to a Whisper API (Groq `whisper-large-v3` preferred, OpenAI `whisper-1`
  fallback).
- Frame budget auto-scales by duration (hard caps: 100 frames, 2 fps) to control token cost;
  `--start`/`--end` switch to a denser focused mode for a specific moment.

**Dependencies.**
- System binaries on PATH: `ffmpeg`, `ffprobe`, `yt-dlp`
  (Windows: `winget install yt-dlp` and `winget install ffmpeg`).
- Optional Whisper API key (Groq or OpenAI) in `~/.config/watch/.env` for videos without
  captions — skippable with `--no-whisper`.
- On Windows, use `python` rather than `python3` (the skill handles this).

**License.** MIT — adapted from [bradautomates/claude-video](https://github.com/bradautomates/claude-video).
