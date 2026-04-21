# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A suite of four standalone single-file web utility apps targeting Japanese users, served as static HTML:

- `index.html` — Landing page
- `clock.html` — Digital clock with Japanese calendar format
- `timer.html` — Fullscreen stopwatch
- `kioxia.html` — Real-time Kioxia (6600.JP) stock price display

## Running Locally

No build step required. Serve the files with any static HTTP server:

```bash
python3 -m http.server 8000
```

Then visit `http://localhost:8000`.

## Architecture

Each file is entirely self-contained (HTML + inline CSS + inline `<script>`). There is no shared code, no bundler, and no external libraries—everything is vanilla ES6+ and browser-native APIs.

### Conventions

- Language attribute: `lang="ja"` on all pages
- Dark theme default: `#111` background, white text
- Layout: `flexbox` with viewport units (`vw`, `dvh`) for responsive full-screen layouts
- CSS reset at top of every `<style>`: `* { margin: 0; padding: 0; box-sizing: border-box; }`
- DOM element IDs: camelCase (`timeEl`, `priceEl`, `changeEl`)
- Functions: camelCase (`fetchStooq`, `fetchDirect`)
- Real-time updates via `setInterval`
- Async data fetching via `fetch` with `async/await` and `try/catch`

### Stock Data Fetching (kioxia.html)

Uses a three-source fallback chain to work around CORS restrictions:

1. **Stooq API** (`stooq.com/q/l/?s=6600.jp`) — primary, no CORS issues
2. **allorigins.win proxy** — wraps Yahoo Finance to bypass CORS
3. **Yahoo Finance direct** (`query1.finance.yahoo.com`) — last resort

Color classes `.up` / `.down` are toggled on `#change` to style price movement.
