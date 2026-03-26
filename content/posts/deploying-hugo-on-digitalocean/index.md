---
title: "Deploying a Hugo Site on DigitalOcean (And the Go Buildpack That Tried to Stop Me)"
date: 2026-03-25
summary: "What happens when DigitalOcean thinks your Hugo blog is a Go application, and how to fix it."
tags: ["hugo", "digitalocean", "deployment", "devops"]
---

**TL;DR:** If your Hugo site fails to deploy on DigitalOcean because of the Go buildpack, the fix is three things: vendor your modules with `hugo mod vendor`, add `go.mod`/`go.sum` to `.gitignore`, and put an empty `config.toml` in the project root so the Hugo buildpack can detect your site.

---

I recently migrated this blog from Next.js to Hugo. I had only written one post in the last three years, but that's beside the point. The migration itself was smooth. Hugo is fast, the [Congo theme](https://jpanther.github.io/congo/) looks great, and I went from a paid hosting setup to DigitalOcean's free static site tier. Everything was going perfectly until I tried to deploy.

## The First Error: Unknown Go Version

I pushed to `main`, DigitalOcean picked up the commit, and the build immediately failed:

```
Detected go modules via go.mod

Installing go1.26.1
!!
!! The requested file (go1.26.1.linux-amd64.tar.gz) is unknown to the buildpack!
```

Wait, what? I'm deploying a Hugo site, not a Go app. Why is it trying to install Go?

Here's what happened: Hugo uses Go modules under the hood to manage themes. When you install a Hugo theme via `hugo mod get`, it creates a `go.mod` file in your project root. DigitalOcean's buildpack detection saw that `go.mod` and thought, "ah, this is a Go application!" and immediately tried to compile it.

The Go version in my `go.mod` was `1.26.1` which was apparently too new for the Heroku Go buildpack that DigitalOcean uses so it failed before even getting to the Hugo build step.

## The Second Error: No Go Files

I downgraded the Go version in `go.mod` to `1.23`, which the buildpack recognized. But then:

```
No main packages found in module github.com/stoatmagoats/jonathanacosta-dev

Running: go install -v -tags heroku .
no Go files in /workspace

ERROR: failed to build: exit status 1
```

Progress, I guess? It successfully installed Go this time but then tried to `go install` my project, which has no `.go` files because *it's not a Go project*.

## The Third Error: No Buildpack Detected

I tried changing the `environment_slug` from `go` to `hugo` in the app spec. Same error. The Go buildpack was still running first and failing before Hugo ever had a chance to build.

So I took the nuclear option: remove `go.mod` from the repository entirely. But I still needed Hugo's module system to work locally for managing the Congo theme. The solution was **vendoring**.

Hugo has a built-in command for this:

```bash
hugo mod vendor
```

This downloads all your module dependencies into a `_vendor/` directory and commits them to git. Now Hugo can build without needing Go at all. It just reads from `_vendor/` directly.

I added `go.mod` and `go.sum` to `.gitignore`, removed them from git tracking, and pushed. The Go buildpack was gone. 🎉

But then:

```
✘ could not detect app files that match known buildpacks.
```

😐

## The Fourth Error: Can't Detect Hugo

With `go.mod` gone, DigitalOcean couldn't detect *any* buildpack. Turns out, its Hugo buildpack looks for `config.toml`, `config.yaml`, or `config.json` in the **project root** to identify a Hugo site. My configuration lives in `config/_default/` (Hugo's directory-based config layout) so there was nothing in the root for the buildpack to find.

The fix was dead simple: add an empty `config.toml` to the project root as a marker file:

```toml
# This file exists as a marker for DigitalOcean's Hugo buildpack detection.
# Actual configuration lives in config/_default/
```

Hugo merges configs from both the root and `config/_default/`, so an empty file doesn't break anything. It just gives DigitalOcean the signal it needs.

Pushed again. Build passed. Site deployed. 🎉🎉🎉

## The Working Setup

If you're deploying a Hugo site that uses Go modules (like any theme installed via `hugo mod get`) on DigitalOcean App Platform, here's the setup that works:

### 1. Vendor your modules

```bash
hugo mod vendor
```

This creates a `_vendor/` directory with your theme files. Commit this to git.

### 2. Hide `go.mod` from git

Add to your `.gitignore`:

```
go.mod
go.sum
```

Then remove them from tracking:

```bash
git rm --cached go.mod go.sum
```

Keep them locally. You still need them for `hugo mod get -u` when updating your theme. Just don't push them.

### 3. Add a root `config.toml` marker

If your Hugo config lives in `config/_default/`, add an empty `config.toml` to the project root so DigitalOcean can detect Hugo.

### 4. Configure the correct app spec

```yaml
static_sites:
- build_command: hugo --minify
  environment_slug: hugo
  envs:
  - key: HUGO_VERSION
    scope: BUILD_TIME
    value: "0.158.0"
  - key: HUGO_EXTENDED
    scope: BUILD_TIME
    value: "true"
  output_dir: public
```

The key settings are `environment_slug: hugo` (not `go`) and component type `static_sites` (not `services`).

## Updating Your Theme

When you want to update your Hugo theme in the future:

```bash
hugo mod get -u
hugo mod vendor
git add _vendor/
git commit -m "update theme"
git push
```

That's it. Run the update locally, re-vendor, and push the updated `_vendor/` directory.

## Takeaways

The root cause of all of this was a collision between Hugo's use of Go modules and DigitalOcean's buildpack detection. Hugo uses `go.mod` for theme management, not for building a Go application, but the buildpack doesn't know the difference.

Vendoring the modules and keeping `go.mod` out of the repo is a clean solution. It also makes your builds faster since DigitalOcean doesn't need to download the theme on every deploy.

Hope this saves you some headaches! 🚀
