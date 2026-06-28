# My Agent Skills

A catalog of custom [Claude Agent Skills](https://github.com/anthropics/skills) I've built or installed.
Each entry below summarizes what the skill does, when it triggers, how to invoke it, and what it
depends on. The full skills (SKILL.md + any scripts) live in my local Claude skills library.

> Last updated: 2026-06-28

| Skill | One-liner | Invoke | Source |
|-------|-----------|--------|--------|
| [`api-scout`](#api-scout) | Full extraction of any developer portal's API docs into a code-ready markdown reference | `/api-search <url>` | Built with skill-creator |
| [`watch`](#watch) | Let Claude actually watch a video (frames + transcript) and answer questions about it | `/watch <url-or-path> [question]` | [bradautomates/claude-video](https://github.com/bradautomates/claude-video) (MIT) |
| [`prd-writer`](#prd-writer) | Turn a feature idea into a complete, Agile-ready Product Requirements Document | "write a PRD for…" | Built with skill-creator |
| [`qa-test-plan`](#qa-test-plan) | Turn requirements / user stories into a structured, executable QA test plan | "write test cases for…" | Built with skill-creator |

> **Tip:** `prd-writer` → `qa-test-plan` chain nicely — a PRD's Given/When/Then acceptance
> criteria feed directly into the test-plan generator.

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

---

## prd-writer

**What it does.** Turns whatever you give it — a brief description, scattered notes, a bullet
list, or a fully described feature — into a professional, Agile-ready **Product Requirements
Document** that a dev team can build from and a QA engineer can test against.

**When it triggers.** When you want to write/draft/create a PRD, product spec, feature spec, or
requirements doc; or say things like "define requirements for", "spec out this feature", "create
user stories for", "write acceptance criteria for", or "turn this idea into a spec". It also
triggers when you describe a feature conversationally and clearly want a formal document out of it.

**How to invoke.**
```
write a PRD for a wishlist-based recommendation feature
spec out this feature: [description]
turn these notes into a requirements doc
```

**How it works.** Asks only for genuinely missing context (name, problem, users, core
functionality, constraints, success definition), then generates a 14-section PRD: executive
summary, problem statement, goals & success metrics (OKR/KPI), personas, scope (in/out),
user stories with Gherkin (Given/When/Then) acceptance criteria + MoSCoW priority, functional &
non-functional requirements, technical considerations, dependencies, risks, Definition of Done,
and open questions.

**Output.** A Word document (`.docx`) by default (via the `docx` skill), or markdown on request.
Afterward it offers to break the PRD into sprint-ready stories, generate a test plan from the
acceptance criteria, or produce a one-page executive summary.

**Dependencies.** Uses the bundled `docx` skill for Word output. No external services.

---

## qa-test-plan

**What it does.** Bridges acceptance criteria (what the product *should* do) and a test
execution suite (how to *verify* it). Produces a complete, executable **QA test plan** — risk
assessment, test strategy, entry/exit criteria, detailed test cases, a traceability matrix, and
defect classification — from a PRD, user stories, or a plain feature description.

**When it triggers.** When you want to create a test plan, write test cases, define QA strategy,
build a regression/smoke suite, or document test coverage; or say things like "write test cases
for this", "I need a QA plan", "create a test matrix", "what should I test for this?", or "turn
my user stories into test cases". Also triggers when you describe a feature and ask how to ensure
it works.

**How to invoke.**
```
write test cases for the checkout flow
turn these user stories into a test plan
I need a QA plan for the recommendation engine
build a smoke test suite for the login feature
```

**How it works.** Uses risk-based testing — scores each area by likelihood × impact (P1–P4) and
allocates effort accordingly. Maps each Given/When/Then acceptance criterion to at least one
happy-path AND one negative/boundary test case. Builds a traceability matrix so every criterion
traces to a test case (flags coverage gaps), plus defect severity definitions and QA metrics
(execution rate, pass rate, defect leakage).

**Output.** Markdown by default (pasteable into Jira/Notion/Confluence); `.xlsx` test matrix
(via the `xlsx` skill) or `.docx` (via the `docx` skill) on request. Afterward it offers to
export the matrix to Excel, generate a P1-only smoke subset for CI/CD gating, or build a
regression checklist.

**Dependencies.** Optionally uses the bundled `xlsx` / `docx` skills for Excel/Word output.
No external services.
