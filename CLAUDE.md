# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Static website for **Slav Forest** — a Ukrainian timber products company (https://www.slavforest.com.ua/). The site has two language versions and four HTML pages total:

| Page | UA | EN |
|------|----|----|
| Main (single-page) | `index.html` | `index_en.html` |
| Reports & Documents | `reports.html` | `reports_en.html` |

Both language versions of each page must be kept in sync when making content or structural changes.

## Deployment

Two GitHub Actions workflows handle deployment:

- **`static.yml`** — auto-deploys to GitHub Pages on every push to `master`
- **`ftp-deploy.yml`** — manually triggered (`workflow_dispatch`) FTP upload to `slavforest.com.ua` using secrets `FTP_SERVER`, `FTP_USERNAME`, `FTP_PASSWORD`; deploys to server-dir `./slavforest.com.ua`

There is no build step — all files are deployed as-is.

## Architecture

### Main pages (`index.html` / `index_en.html`)
Single-page layout with anchor-based navigation. Sections follow `<section id="..." class="page ...">`. Key files:

- `js/script.js` — navbar scroll behavior, smooth scroll, parallax, Owl Carousel, PrettyPhoto lightbox, counter animations; contains a dead contact form AJAX handler (posts to `contactform.php`) — the actual form is a 123formbuilder embed in `index.html`
- `js/_actions.js` — reusable AJAX loader utility (`load_objects(container)`) that reads `data-action`, `data-method`, `data-callback`, `data-target` from DOM elements
- `css/style.css` — primary stylesheet; `css/responsive.css` for breakpoints

### Reports pages (`reports.html` / `reports_en.html`)
Separate multi-section pages. Do **not** include `js/script.js` (it hides the navbar on scroll). Instead they use inline CSS to keep the navbar always visible and a minimal inline script for spinner, WOW.js, and PDF thumbnail rendering.

- PDF files live in `docs/`
- Thumbnails are rendered client-side via PDF.js 3.11 (CDN). Pages 1–2 are shown side by side. `encodeURI()` is applied to `data-pdf` paths before passing to `pdfjsLib.getDocument()` to handle Cyrillic and spaces in filenames.
- Each card uses `data-pdf="docs/filename.pdf"` for thumbnail rendering and a separate `href` on the download button.
- Documents are ordered newest-first within each group.

### Navbar on reports pages
The navbar is outside the parallax hero, so `margin-top: 0 !important` and `opacity: 1 !important` are set via inline `<style>`. Nav items link to `index.html#SECTION` (or `index_en.html#SECTION`) so clicking them navigates back to the main page at the correct section.

### Shared
- `sw.js` — minimal service worker registration (no caching strategy)
- `manifest.json` — PWA manifest; theme color `#a9773a`
- External CDN deps: Bootstrap 3.1.1, jQuery 1.11.3, Font Awesome 5, Google Fonts (Raleway, Source Sans Pro), Pixeden icon font, WOW.js

## Content Structure

- Main page sections (anchor IDs): `#HOME`, `#ABOUT`, `#PRODUCTS`, `#LOGISTICS`, `#CERTIFICATES`, `#CONTACT`
- Nav order: Домівка / Home → Про компанію / About Us → Продукція / Products → Логістика / Logistics → Сертифікати / Certificates → Звіти / Reports → Контакти / Contacts
- The contact form posts to `contactform.php` (server-side, not in this repo)
- Brand colors: green `#0a6609`, dark green `#3d4d35`, brown `#a9773a`
