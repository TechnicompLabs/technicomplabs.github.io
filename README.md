# Technicomp Labs

Source for [technicomplabs.io](https://technicomplabs.io), a Jekyll site with its own small theme (no remote theme, no JavaScript). Light mode is graph paper, dark mode is a chalkboard; the layout is in `_layouts/`, the stylesheet in `assets/css/main.css`.

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
