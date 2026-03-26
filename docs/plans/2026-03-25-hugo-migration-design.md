# Hugo Migration Design — jonathanacosta.dev

## Goal

Migrate jonathanacosta.dev from a paid Next.js deployment to a free Hugo static site on DigitalOcean App Platform.

## Constraints

- **Zero hosting cost** — DigitalOcean App Platform free static site tier
- **Hugo + Congo theme** — developer-polished aesthetic, dark mode, syntax highlighting
- **Git-based workflow** — push to GitHub, auto-deploy

## Architecture

### Project Structure

```
jonathanacosta-dev/
├── config/_default/
│   ├── hugo.toml          # Base config (title, baseURL, language)
│   ├── params.toml        # Congo theme params (colors, layout, features)
│   ├── menus.toml         # Navigation menu items
│   └── languages.toml     # Language config
├── content/
│   ├── _index.md          # Home page
│   ├── about/
│   │   └── index.md       # About page
│   ├── posts/             # Blog posts (page bundles)
│   │   └── example-post/
│   │       ├── index.md
│   │       └── featured.jpg
│   └── projects/
│       └── _index.md      # Projects listing
├── assets/                # Custom CSS overrides if needed
├── static/                # Static files (favicon, etc.)
├── themes/congo/          # Congo theme (Hugo module)
└── .gitignore
```

### Key Decisions

- **Congo via Hugo modules** — cleaner than git submodules, easier updates
- **Page bundles** for posts — images live alongside their post content
- **Split config** — multiple `.toml` files per Congo convention

## Pages

| Page | Layout | Description |
|------|--------|-------------|
| Home | `profile` or `hero` | Name, tagline, recent posts |
| About | Default single | Bio, background |
| Projects | List | Project cards with title, description, tech, link |
| Blog posts | Default single | Markdown + syntax highlighting + YouTube embeds |
| Footer | — | Social links (GitHub, LinkedIn, Twitter/X, email) |

## Content Features

- **Syntax highlighting** via Hugo's built-in Chroma (Congo-styled)
- **YouTube embeds** via `{{</* youtube */>}}` shortcode
- **Images** via page bundles (drop in post folder, reference in markdown)
- **Tags/categories** for post organization (Congo renders automatically)
- **Dark mode** default with toggle
- **Table of contents** on longer posts

## What We're NOT Building

- No comments system
- No newsletter/email signup
- No analytics
- No search

## Git & Deployment

- `git init` locally, push to `github.com/jonathan-teamstatus/jonathanacosta-dev`
- DigitalOcean App Platform setup and DNS migration handled manually by owner
- Build command: `hugo --minify`, output dir: `public`

## Content Workflow

1. Write `.md` in `content/posts/my-post/index.md`
2. `hugo server` to preview locally
3. `git add`, `git commit`, `git push`
4. Auto-deploy on DigitalOcean (~30s)
