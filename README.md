# jonathanacosta.dev

Personal blog and portfolio site for Jonathan Acosta, built with [Hugo](https://gohugo.io/) and the [Congo](https://github.com/jpanther/congo) theme.

## Tech Stack

- **Hugo** (extended) — static site generator
- **Congo v2** — theme (installed via Hugo modules)
- **Go** — required for Hugo modules
- **DigitalOcean App Platform** — hosting (free static site tier)

## Project Structure

```
.
├── config/_default/          # Hugo configuration (split per Congo convention)
│   ├── hugo.toml             # Base config (baseURL, modules, privacy)
│   ├── params.toml           # Theme parameters (colors, layout, features)
│   ├── menus.en.toml         # Navigation menu items
│   └── languages.en.toml    # Language config, author profile, social links
├── content/
│   ├── _index.md             # Home page (profile layout)
│   ├── about/index.md        # About page
│   ├── posts/                # Blog posts (page bundles)
│   │   └── hello-world/
│   │       └── index.md
│   └── projects/_index.md    # Projects listing
├── layouts/                  # Local template overrides
│   └── _partials/functions/
│       └── warnings.html     # Fix for Congo + Hugo v0.158+ compatibility
├── static/                   # Static assets (favicon, etc.)
├── docs/plans/               # Design and implementation docs
└── go.mod / go.sum           # Hugo module dependencies
```

## Prerequisites

- [Hugo](https://gohugo.io/installation/) (extended edition, v0.124.0+)
- [Go](https://go.dev/dl/) (v1.22+)

On Arch/CachyOS:

```bash
paru -S hugo go
```

## Quick Start

```bash
# Clone the repo
git clone https://github.com/stoatmagoats/jonathanacosta-dev.git
cd jonathanacosta-dev

# Start the dev server
hugo server -D
```

Visit [http://localhost:1313](http://localhost:1313) to preview the site.

## Writing a New Post

1. Create a new page bundle:

```bash
mkdir -p content/posts/my-new-post
```

2. Create `content/posts/my-new-post/index.md`:

```markdown
---
title: "My New Post"
date: 2026-03-25
summary: "A brief summary of the post."
tags: ["tag1", "tag2"]
---

Your content here.
```

3. Drop any images for the post in the same directory and reference them in markdown:

```markdown
![Alt text](image.jpg)
```

4. Embed YouTube videos with Hugo's shortcode:

```
{{</* youtube VIDEO_ID */>}}
```

5. Preview locally, then commit and push:

```bash
hugo server -D          # preview
git add content/posts/
git commit -m "feat: add new post"
git push
```

## Building for Production

```bash
hugo --minify
```

Output goes to the `public/` directory.

## Configuration

All configuration lives in `config/_default/`:

| File | Purpose |
|------|---------|
| `hugo.toml` | Base Hugo settings, module imports, privacy |
| `params.toml` | Theme appearance, layout options, feature toggles |
| `menus.en.toml` | Navigation menu items and order |
| `languages.en.toml` | Site title, author info, social links |

See the [Congo docs](https://jpanther.github.io/congo/docs/configuration/) for all available parameters.

## License

Content is © Jonathan Acosta. The site is built with open source tools.
