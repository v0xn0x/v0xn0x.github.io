# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Personal blog/website for v0xn0x, a cybersecurity-focused technical blog. Built with Jekyll using the **Chirpy theme** (v6.5.2). Content is in Brazilian Portuguese (pt-BR). Deployed to GitHub Pages via GitHub Actions on push to master.

## Common Commands

- **Local dev server:** `bash tools/run` (runs `bundle exec jekyll s -H 0.0.0.0 -l` with live reload)
- **Build & test:** `bash tools/test` (production build + htmlproofer)
- **Build only:** `JEKYLL_ENV=production bundle exec jekyll b`
- **Install Ruby deps:** `bundle install`
- **Build JS assets:** `npm i && npm run build`
- **Lint SCSS:** `npm test` (stylelint)
- **Fix SCSS lint:** `npm run fixlint`

## Architecture

- **Theme:** `jekyll-theme-chirpy` defined as a gem in `jekyll-theme-chirpy.gemspec`. Layouts, includes, and sass come from the theme gem but can be overridden locally.
- **Posts:** `_posts/` — Markdown files with YAML front matter. Naming convention: `YYYY-MM-DD-slug.md`.
- **Tabs/Pages:** `_tabs/` — about, archives, categories, tags pages (sorted by `order` front matter).
- **Config:** `_config.yml` — site-wide settings (theme mode: dark, timezone: America/Sao_Paulo, locale: pt-BR).
- **Data:** `_data/` — authors, contact info, locales, share config.
- **JS bundling:** Rollup (`rollup.config.js`) builds JS to `assets/js/dist/`.
- **Images:** `img/` directory for post images and avatar.
- **CI/CD:** `.github/workflows/pages-deploy.yml` — builds with Ruby 3.2, runs htmlproofer, deploys to GitHub Pages.

## Post Front Matter

Posts use these front matter fields:
```yaml
---
title: Post Title
author: v0xn0x
date: YYYY-MM-DD HH:MM:SS -0300
categories: [CATEGORY, SUBCATEGORY]
tags: [TAG1, TAG2]
pin: true          # optional, pins to home page
math: true         # optional, enables math rendering
mermaid: true      # optional, enables mermaid diagrams
image:
  path: /img/image.png
  alt: "description"
---
```

## Key Details

- Site URL: `https://v0xn0x.com` (CNAME file present)
- Main branch: `master`
- Categories and tags use uppercase Portuguese conventions (e.g., `ESTUDO`, `C2`)
- PWA is enabled with offline cache
- The `_site/` directory is the build output (gitignored)
