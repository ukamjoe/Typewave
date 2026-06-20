# TypeWave

AI-powered transcription workspace — load audio, let AI transcribe it in real time, edit and export.

## Files

- **`index.html`** — Landing page with a live animated demo
- **`typewave.html`** — The transcription app itself (React, runs via CDN — no build step needed)

The two pages are cross-linked: the landing page's "Launch app" buttons open `typewave.html`, and the TypeWave logo inside the app links back to `index.html`.

## Running locally

No install required. Just open `index.html` in a browser (Chrome or Edge recommended — AI transcription uses the Web Speech API, which isn't supported in Firefox/Safari).

```bash
# from the repo folder
python3 -m http.server 8000
# then visit http://localhost:8000
```

## Deploying

Works on any static host — GitHub Pages, Vercel, Netlify, S3, etc. Just upload both files to the same folder; the relative links (`typewave.html`, `index.html`) will resolve automatically.

### GitHub Pages
1. Push this repo
2. Repo → Settings → Pages → Source: `main` branch, root folder
3. Your landing page will be live at `https://<username>.github.io/Typewave/`

## Features

- Real-time AI transcription (30 languages) via the Web Speech API
- Waveform audio player with speed control (0.5×–1.5×) and seek
- Timestamps, speaker tagging, and unclear/crosstalk flags
- Auto-save to localStorage, plain-text export
- Fully responsive — desktop 3-column layout, mobile drawer + bottom nav

## Stack

Built with React 18 (via CDN + Babel standalone) so it runs as static HTML with zero build tooling. A full Next.js/TypeScript version of the same app (with proper hooks, typed components, and a real build pipeline) is available on request for production deployment with SSR and API routes.
