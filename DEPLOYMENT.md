# Deployment

This site is deployed as a **static site** on [DigitalOcean App Platform](https://www.digitalocean.com/products/app-platform) (free tier).

## How It Works

1. Push to `main` on GitHub
2. DigitalOcean detects the push and triggers a build
3. Hugo builds the site with `hugo --minify`
4. The `public/` directory is served as a static site
5. Live in ~30 seconds

## DigitalOcean App Platform Setup

### Initial Setup

1. Log in to [DigitalOcean](https://cloud.digitalocean.com/)
2. Go to **Apps** → **Create App**
3. Connect your GitHub account and select `stoatmagoats/jonathanacosta-dev`
4. Configure as **Static Site** (not Web Service):

| Setting | Value |
|---------|-------|
| Branch | `main` |
| Build Command | `hugo --minify` |
| Output Directory | `public` |
| Environment Slug | `hugo` |

5. Add environment variables:

| Variable         | Value     | Scope      |
|------------------|-----------|------------|
| `HUGO_VERSION`   | `0.158.0` | BUILD_TIME |
| `HUGO_EXTENDED`  | `true`    | BUILD_TIME |

> **Important:** Set `HUGO_VERSION` to match your local version. Without this, DigitalOcean may use an older Hugo version that produces different results.

6. Deploy

### Custom Domain

1. In your app settings, go to **Settings** → **Domains**
2. Add `jonathanacosta.dev`
3. DigitalOcean provides a CNAME record — add it to your DNS provider
4. SSL is automatically provisioned via Let's Encrypt

### DNS Configuration

Point your domain to DigitalOcean App Platform:

```
Type: CNAME
Name: @  (or www)
Value: <your-app>.ondigitalocean.app.
TTL: 3600
```

> For apex domains (`jonathanacosta.dev` without `www`), you may need an ALIAS or ANAME record depending on your DNS provider. DigitalOcean provides specific instructions during domain setup.

## Verifying a Deployment

After pushing, check:

1. **DigitalOcean dashboard** — App → Activity tab shows build logs
2. **Build output** — should show `hugo --minify` completing successfully
3. **Live site** — visit `https://jonathanacosta.dev` (or the `.ondigitalocean.app` URL)

## Troubleshooting

### Go Buildpack Interfering ("no Go files" error)

DigitalOcean's buildpack detection activates the Go buildpack if `go.mod` is present. To prevent this:

1. Hugo modules are **vendored** via `hugo mod vendor` (stored in `_vendor/`)
2. `go.mod` and `go.sum` are in `.gitignore` — they exist locally but aren't pushed to git
3. The `environment_slug` must be `hugo`, not `go`

> **Critical:** Never commit `go.mod` to the repo. If you need to update the Congo theme, run `hugo mod get -u` locally, then `hugo mod vendor` to update the vendored copy.

### Buildpack Can't Detect Hugo Site

DigitalOcean's Hugo buildpack looks for `config.toml`, `config.yaml`, or `config.json` in the project root. Since this project uses `config/_default/` (Hugo's directory-based config), a minimal `config.toml` exists in the root as a detection marker. **Do not delete it.**

### Build Fails with "hugo: command not found"

Set the `HUGO_VERSION` environment variable in your app settings. DigitalOcean needs this to install Hugo.

### CSS/Styles Missing in Production

Verify `baseURL` in `config/_default/hugo.toml` matches your actual domain:

```toml
baseURL = "https://jonathanacosta.dev/"
```

### Content Not Updating

Hugo caches aggressively. If content seems stale:

1. Check that the post's `date` is not in the future
2. Ensure the post doesn't have `draft: true` in frontmatter (production builds exclude drafts)
3. Force a rebuild by pushing a new commit

## Local Production Build

To test the production build locally:

```bash
hugo --minify
# Serve the output
python3 -m http.server -d public 8080
```

Visit [http://localhost:8080](http://localhost:8080) to verify.

## Rollback

DigitalOcean App Platform keeps previous deployments. To rollback:

1. Go to your app → **Activity** tab
2. Find the previous successful deployment
3. Click **Rollback to this deployment**

Alternatively, revert the git commit and push.
