# KubeNetLab Documentation

Source for [KubeNetLab docs](https://hujun-open.github.io/knldoc/), built with [Hugo](https://gohugo.io) and the [hugo-book](https://github.com/alex-shpak/hugo-book) theme.

## Prerequisites

- [Hugo Extended](https://gohugo.io/installation/) (required by hugo-book for SCSS)
- Recommended version: **0.153.2** (matches CI)
- Minimum: **0.146+** (per hugo-book)

Verify your install:

```bash
hugo version
```

The output should include `extended`.

## Clone

The theme is a git submodule and must be initialized:

```bash
git clone --recurse-submodules https://github.com/hujun-open/knldoc.git
cd knldoc
```

For an existing clone:

```bash
git submodule update --init --recursive
```

## Local preview

Start the Hugo development server with live reload:

```bash
hugo server --theme hugo-book
```

Open **http://localhost:1313/** in your browser. Hugo watches `content/` and rebuilds automatically when you save changes.

Optional flags that match the production build more closely:

```bash
hugo server --theme hugo-book --minify --gc
```

## Build static site

To generate the site without running a server:

```bash
hugo --theme hugo-book --minify --gc
```

Output is written to `public/`.

## Content

Add or edit Markdown under `content/docs/`. With `hugo server` running, save a file and refresh the browser — no restart needed.

## Deployment

Pushes to `main` trigger a GitHub Pages deploy via [`.github/workflows/hugo.yaml`](.github/workflows/hugo.yaml). No manual publish step is required for contributors.
