# Technicomp Labs

Source for [technicomplabs.io](https://technicomplabs.io), a Jekyll site with its own small theme (no remote theme, no JavaScript). The design draws on Epcot Center as it opened in 1982: a spectrum stripe, roundel section markers, and a navy panel with a faint geodesic pattern. Dark mode follows the visitor's system setting. The layout is in `_layouts/` and the stylesheet in `assets/css/main.css`.

## Run locally

```bash
bundle install
bundle exec jekyll serve --livereload --drafts
```

Then open http://localhost:4000.

## Write a post

Add `_posts/YYYY-MM-DD-slug.md`:

```yaml
---
title: "Post title"
excerpt: "One or two sentences shown in the post list."
categories: [performance]
---
```

Posts publish at `/blog/YYYY/MM/slug/`. Drafts live in `_drafts/` and only build with `--drafts`.

## Pages

Top-level pages live in `_pages/` with `title`, `permalink`, and an optional `subtitle`. Navigation is `_data/navigation.yml`.
