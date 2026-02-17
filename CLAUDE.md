# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

PDF-Flip is a browser-based PDF viewer that renders PDFs with 3D page-flip animations. It is a self-contained library with no build tools, package managers, or external CDNs. All dependencies are bundled.

## Running Locally

This is a static site — no build step. Serve `example_flip/` with any HTTP server:

```bash
# From the repo root
python3 -m http.server 8000 --directory example_flip
# Then open http://localhost:8000
```

A file server is required (not `file://`) because PDF.js uses Web Workers and fetch.

## Architecture

**Entry points:**
- `example_flip/index.html` — Single PDF viewer. Loads a PDF via a `<div class="PDFFlip" source="PDF.pdf">` element.
- `example_flip/bookshelf.html` — Gallery view showing multiple book covers that open into the PDF viewer.

**Configuration (no code changes needed):**
- `example_flip/settings.js` — All viewer options: page mode, controls, display, autoplay, zoom, background, i18n text. Exposes `option_PDFF` and `pdfflipLocation` globals.
- `example_flip/toc.js` — Table of contents entries. Appends `outline` array to `option_PDFF`.

**Core library (`example_flip/pflip/`):**
- `js/pdfflip.js` — Main library (minified). Creates `window.pdfflip` global with constants like `DIRECTION`, `PAGE_MODE`, `SINGLE_PAGE_MODE`, `CONTROLSPOSITION`. Renders PDF into the `#PDFF` container.
- `js/bookshelf.js` — jQuery plugin (`$.fn.bookshelf()`) that wraps cover images into shelf rows and handles hover animations.
- `js/libs/` — Bundled dependencies: jQuery, PDF.js (+ worker), Three.js, compatibility shim, utils.

**Rendering pipeline:** PDF.js parses the PDF → pages rendered to canvas → Three.js provides WebGL 3D flip animation (falls back to 2D HTML mode on older browsers).

## Key Conventions

- PDFs are referenced via the `source` attribute on the container div, not in JS config.
- The `pdfflipLocation` variable in `settings.js` must point to the `pflip/` directory relative to the HTML file.
- Bookshelf entries in `bookshelf.html` use `<a class="_PDFF_link" source="...">` with thumbnail `<img>` children.
- Thumbnails live in `example_flip/thb/` named numerically (`01.jpg`, `02.jpg`, etc.).
- Background image is set via `backgroundImage` in settings or as an attribute on bookshelf links.
- The `allControls` and `hideControls` strings in settings are comma-separated control names that determine toolbar layout.
