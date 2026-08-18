# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A static portfolio site for Zachary Lu-Ming Fan (UI/UX designer). Plain HTML/CSS, zero JavaScript, no build tooling, no package manager. Deployed as-is via GitHub Pages — pushing to `main` on `origin` (`https://github.com/zachfan-96/zachfan-96.github.io`) publishes directly to `https://zachfan-96.github.io/`, with no build/CI step in between.

## Running locally

There's no dev server built into the repo (no `package.json`, no Python/Node guaranteed on the host). Serve the directory with whatever static server is available, e.g.:
- `npx serve .` or `python -m http.server 8000` if Node/Python are present, **or**
- a minimal `System.Net.HttpListener`-based static file server in PowerShell if neither is installed (this has been needed before on Windows hosts here — check for `python`/`node` first before assuming they exist).

Then open `index.html` (or any `*_case_study.html`) through that server rather than via `file://`, so relative asset paths resolve correctly.

## Architecture

Four standalone HTML pages, each self-contained:
- `index.html` — homepage (hero, selected work list, about, footer)
- `cov_case_study.html`, `ctr_case_study.html`, `homescope_case_study.html` — one case study per project

**CSS is split two ways:**
- `styles.css` — rules that are byte-identical across multiple pages: reset, CSS custom properties (`--ink`, `--paper`, etc.), nav, the shared `.case-hero`/`.case-content`/`.case-tags`/`.case-bottom-nav` case-study scaffolding, the `fadeUp` keyframes, and the parts of the mobile breakpoint that are common to all pages.
- Each page's inline `<style>` block — rules unique to that page only (e.g. `index.html` keeps its own `.hero`/`.work`/`.about`/`footer` rules; each case study keeps its own content-grid/feature-row/component-grid rules).

When editing shared visual elements (nav, case-study hero band, tag pills, bottom nav), change `styles.css` once rather than editing each page — that's the reason the split exists. When editing something page-specific, it stays inline in that page.

**Known CSS gotcha — do not remove:** `styles.css` has `img { height: auto; }` near the top. Every `<img>` across the site carries explicit `width`/`height` HTML attributes (added to prevent layout shift), but none of the page CSS declares an explicit `height` for images — sizing instead relies on `aspect-ratio`/`object-fit`/percentage `width`. Without `img { height: auto; }` overriding it, browsers use the `height` HTML attribute as a literal pixel value, which nullifies `aspect-ratio` (it only applies when a dimension is `auto`) and badly distorts every image on the site. If you add a new image size rule, make sure it either sets its own explicit `height` or continues to rely on this global override.

**Assets** live at the repo root (not in a subfolder) and are named kebab-case (e.g. `cov-slide-1-opening-slide.png`, `portfolio-cov.png`). Match that convention for anything new — the repo previously had spaces in filenames and they were all renamed for URL/CLI safety.

**No JavaScript, no framework.** Interactivity (hover states, animations, mobile nav collapse) is CSS-only (`:hover`, `@keyframes`, `@media (max-width: 768px)`). Don't introduce a JS dependency for something that can stay CSS-driven, to keep the site's zero-build-step deployment intact.

**Large media files** (`ctr_demo.mp4`, `passive_cooling_prototype.mp4`, the `GIF_HomeScope_*.gif` files) are unoptimized (multi-MB) and referenced directly by case study pages via `<video>`/`<img>`. There's no image/video pipeline in this repo — if you replace or add media, optimize it before committing (no automated compression step exists).
