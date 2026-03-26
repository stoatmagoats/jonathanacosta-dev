# AGENTS.md

Instructions for AI coding assistants working on this project.

## Project Overview

This is a Hugo static site using the Congo theme (v2) for a personal blog at jonathanacosta.dev. It is **not** a software application — it's a content site built with configuration files and markdown.

## Tech Stack

- **Hugo** (extended edition) — static site generator
- **Congo v2** — theme, vendored locally in `_vendor/` (managed via Hugo modules)
- **Go** — required locally for Hugo module management (not needed at build time)
- **TOML** — all configuration files
- **Markdown** — all content

## Key Directories

| Directory | Purpose |
|-----------|---------|
| `config/_default/` | All site configuration (split across multiple TOML files) |
| `content/` | All site content (markdown page bundles) |
| `content/posts/` | Blog posts — each post is a directory with `index.md` |
| `layouts/` | Local template overrides (takes precedence over theme) |
| `static/` | Static files served as-is (favicon, etc.) |
| `assets/` | Files processed by Hugo's asset pipeline (custom CSS) |
| `_vendor/` | Vendored Congo theme (committed to git, do not edit directly) |
| `docs/plans/` | Design documents and implementation plans |

## Configuration Files

| File | What it controls |
|------|-----------------|
| `config/_default/hugo.toml` | Base Hugo settings, module imports, outputs, privacy |
| `config/_default/params.toml` | Theme appearance: color scheme, layout, features, article display |
| `config/_default/menus.en.toml` | Navigation menu items and ordering |
| `config/_default/languages.en.toml` | Site title, author info, social links, date format |
| `config.toml` | Empty marker file for DigitalOcean Hugo buildpack detection |

## Content Conventions

### Blog Posts

Posts use **page bundles** — each post is a directory:

```
content/posts/my-post/
├── index.md        # Post content with frontmatter
├── image1.jpg      # Images referenced in the post
└── featured.jpg    # Optional featured/hero image
```

Required frontmatter:

```yaml
---
title: "Post Title"
date: YYYY-MM-DD
summary: "Brief description"
tags: ["tag1", "tag2"]
---
```

### YouTube Embeds

Use Hugo's built-in shortcode:

```
{{</* youtube VIDEO_ID */>}}
```

### Static Pages

- `content/_index.md` — Home page (profile layout, minimal frontmatter)
- `content/about/index.md` — About page
- `content/projects/_index.md` — Projects listing

## Development Commands

```bash
# Start dev server (includes drafts)
hugo server -D

# Build for production
hugo --minify

# Update Congo theme (then re-vendor)
hugo mod get -u
hugo mod vendor

# Check Hugo version
hugo version
```

## Known Issues

### Congo v2.13.0 + Hugo v0.158+ Compatibility

The file `layouts/_partials/functions/warnings.html` is a local override that removes a reference to the deprecated `.Site.Author` field. This override can be removed when Congo releases a fix. Check by running `hugo mod get -u` and testing if the build succeeds without the override.

## Important Notes

- **Don't commit `go.mod` or `go.sum`** — they're in `.gitignore` to prevent DigitalOcean's Go buildpack from activating. They exist locally for Hugo module management only
- **Don't delete `config.toml`** in the project root — it's a marker file for DigitalOcean's Hugo buildpack detection
- **Don't edit files in `_vendor/`** directly — update the theme with `hugo mod get -u` then `hugo mod vendor`
- **Don't modify files in the Go module cache** (`~/.cache/hugo_cache/`) — override templates locally in `layouts/` instead
- **Don't use YAML for config** — this project uses TOML exclusively
- **Don't add `public/` or `resources/` to git** — they're in `.gitignore`
- **Production builds exclude drafts** — only `hugo server -D` shows draft posts
- The `baseURL` in `hugo.toml` must match the production domain for correct URL generation
- Deployment is handled by DigitalOcean App Platform (auto-deploys on push to `main`)
