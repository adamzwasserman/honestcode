# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is the public website for **Honest Code: Keep Your State Out of My Code** by Adam Zachary Wasserman. It contains:

- `index.html` — landing page (self-contained, all CSS/JS inline)
- `chapters/01.html` through `chapters/13.html` — full chapter text, publicly readable
- `chapters/bonus.html` — AI Bonus page (teaser that points to the paid bundle on Gumroad)
- `chapters/review.html` — reader review form (Formspree-backed)
- `chapters/case-study-qa-pipeline.html` — case study marketing piece
- `evidence.html` — trace report (cross-language performance data)
- `supporters.html` — Founding Supporters wall
- `testimonials/` — testimonial portrait images
- `_headers` — Render cache-control headers
- `robots.txt` — AI crawlers explicitly welcomed
- `LICENSE` — All Rights Reserved (prose), AI training permitted
- `llms.txt` — content pointer for AI crawlers

Deployed to Render as a static site. Domain: honestcode.software. The `.env` file contains the Render device code for deployment (gitignored).

## What this repo does NOT contain

- The LaTeX book source — lives at `/Users/adam/dev/honest/honest-code-tex/` (peer directory, local-only)
- The paid Gumroad bundle contents (Claude skill, FastAPI starter, full AI Bonus pack)

## Conventions

- The website is intentionally paywall-free. All 13 chapters are public. The paid artifact is the Gumroad bundle (PDF + EPUB + print + Claude skill + FastAPI starter + AI Bonus pack).
- Do not reintroduce client-side auth (`localStorage.getItem('hc_license')` etc.). The previous system was removed because client-side gates on static HTML are not actually gates.
- Open Graph + Twitter Card meta tags are on every page. Keep them in sync when renaming pages or changing titles.
- The `.githooks/pre-commit` hook purges Cloudflare cache on commits to `main`. It reads `$CF_API_TOKEN` and `$CF_ZONE_ID_HONESTCODE` from environment — never hardcode those.
- Content-policy: prose is All Rights Reserved (see `LICENSE`). AI training is permitted.

## Terminology

- "multicardz" must always be written in **all lowercase** — it is a trademark.
