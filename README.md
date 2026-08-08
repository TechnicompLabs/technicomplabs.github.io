# Technicomp Labs

The Technicomp Labs blog — a Jekyll site using the [Minimal Mistakes](https://mmistakes.github.io/minimal-mistakes/) theme (the same theme family as the academicpages sites), configured as a blog.

## Run locally

```bash
bundle install
bundle exec jekyll serve --livereload
```

Then open http://localhost:4000. Drafts (in `_drafts/`) show with `--drafts`.

## Write a post

Add a file to `_posts/` named `YYYY-MM-DD-slug.md` with front matter:

```yaml
---
title: "Post title"
date: 2026-08-08
permalink: /posts/2026/08/slug/
excerpt: "One-line summary for listings and SEO."
categories:
  - performance
tags:
  - llama.cpp
  - moe
toc: true
---
```

## Deploy on GitHub Pages

1. Create the repo (e.g. `technicomp-labs`) and push this folder.
2. In **Settings → Pages**, set **Source: Deploy from a branch**, branch `main`, folder `/ (root)`.
3. GitHub builds it with Jekyll and serves it. The `remote_theme` and plugins in `_config.yml` are all GitHub Pages compatible, so no Actions workflow is required.
4. For a custom domain (e.g. `technicomplabs.io`): add a `CNAME` file containing the domain, set `url:`/`baseurl:` in `_config.yml`, and point DNS at GitHub Pages.

## Structure

| Path | Purpose |
|---|---|
| `_config.yml` | Site config, theme, author, defaults. |
| `_posts/` | Blog posts. |
| `_pages/` | Standalone pages (About, Categories, Tags). |
| `_data/navigation.yml` | Top navigation bar. |
| `index.html` | Home page (paginated post feed). |
| `assets/` | Images and static files. |
