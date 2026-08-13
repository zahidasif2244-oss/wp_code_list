# Chat Log — GitHub Pages Deployment of `build` Folder

## Context
- Project: static book listing site (`wp list`) built from `build/` folder
- Contents: `index.html`, `logo.png`, `data/` (485 JSON files: `index.json` + `p1.json` ... `p484.json`)
- The site is pure **HTML/CSS/JS** — no React, no Node.js, no server needed

## Problem
- User hosted the build folder on GitHub Pages
- Site HTML loads, but status line shows **"Network error."**
- `data/index.json` URL appears "empty" when opened in a browser

## Analysis & Findings
1. The site is fully static. `index.html` loads data via relative `fetch("data/index.json")` and `fetch("data/p{n}.json")` — relative paths work on GitHub Pages at any subpath.
2. `index.json` is 2.56 MB and is a single very long line — browsers often render it as a blank/frozen white page when opened directly, but `fetch()` reads it fine. So "empty" display is not necessarily an error.
3. Likely root cause of "Network error.": the GitHub web UI only allows **max 100 files per drag-and-drop**. The `data/` folder has **485 files**, so most `p*.json` files never uploaded → page JSON fetch 404s → "Network error."

## Files / Sizes
- `data/index.json`: 2,562,406 bytes
- `data/p1.json`: 70,016 bytes
- Total `data/`: 485 files, 35,634,564 bytes (~35.6 MB)

## Correct GitHub Pages Setup
Repo root must contain the **contents** of `build/` (not the folder itself):
```
index.html
logo.png
data/  (485 .json files)
```
1. Push files to the repo branch (`main`)
2. Settings → Pages → "Deploy from a branch" → select `main` / root `/`
3. Open `https://<username>.github.io/<repo>/`

## Recommended Upload Methods
### Option A — Git (recommended, reliable for many files)
```
git init
git add .
git commit -m "init"
git branch -M main
git remote add origin https://github.com/<user>/<repo>.git
git push -u origin main
```
### Option B — GitHub web UI
- Drag the `data/` folder contents in batches of **under 100 files** each
- GitHub does NOT auto-extract zip files — do not upload a zip

## Verification
- Check in browser: `<site-url>/data/index.json` should show JSON (not 404 page)
- Note: a giant single-line JSON can appear blank in a browser — inspect with DevTools → Network tab, or curl instead

## Status
- Waiting on user to re-upload all 485 files (via git or batched web upload) and share the site URL for final verification
