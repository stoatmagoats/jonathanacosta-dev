# Contributing

This is a personal blog — contributions are primarily from the site owner. That said, here are the conventions used in this project.

## Content Guidelines

### Blog Posts

- Use **page bundles**: each post gets its own directory under `content/posts/`
- Include frontmatter: `title`, `date`, `summary`, `tags` (at minimum)
- Place images in the same directory as the post's `index.md`
- Use Hugo shortcodes for embeds (e.g., `{{</* youtube VIDEO_ID */>}}`)

### Pages

- Static pages (About, Projects) live as `index.md` or `_index.md` under their section
- Use frontmatter to control per-page display options (e.g., `showDate: false`)

## Development Workflow

1. **Branch** from `main` for content or config changes
2. **Preview** with `hugo server -D` (the `-D` flag includes draft posts)
3. **Verify** the build completes without errors: `hugo --minify`
4. **Commit** with conventional commit messages:
   - `feat:` — new content or feature
   - `fix:` — bug fixes or corrections
   - `docs:` — documentation changes
   - `chore:` — maintenance, dependency updates
5. **Push** to `main` — DigitalOcean auto-deploys on push

## Theme Customization

This site uses the [Congo](https://github.com/jpanther/congo) theme via Hugo modules.

- **Don't modify theme files directly** — they live in the Go module cache
- **Override templates** by creating matching files in `layouts/`
- **Override styles** by adding CSS in `assets/css/custom.css` (Congo supports this)
- **Update the theme** with `hugo mod get -u`

## Local Overrides

The `layouts/_partials/functions/warnings.html` file is a local override that fixes a compatibility issue between Congo v2.13.0 and Hugo v0.158+. This can be removed once Congo releases a version that addresses the deprecated `.Site.Author` field.

## Code Style

- TOML for all configuration (not YAML)
- Markdown for all content
- Conventional commits for git messages
