# Tony_966

This repository contains the source for a static site built with
[Hugo](https://gohugo.io/) and the
[Reimu theme](https://github.com/D-Sketon/hugo-theme-reimu). The theme is
loaded as a Hugo Module, so it does not need to be cloned into a local
`themes` directory.

## Prerequisites

Install the following tools and ensure they are available on your `PATH`:

- [Hugo Extended](https://gohugo.io/installation/) 0.161.1 or later
- [Go](https://go.dev/doc/install) 1.26.4 or later
- [Dart Sass](https://sass-lang.com/install/) 1.99.0 or later

The versions above match the GitHub Pages deployment workflow. Confirm the
installations with:

```powershell
hugo version
go version
sass --version
```

## Install the theme module

From the repository root, download the Hugo Module dependencies:

```powershell
hugo mod get
```

Hugo will also resolve missing modules automatically when the site is first
built or served.

## Run locally

Start Hugo's development server:

```powershell
hugo server
```

Open <http://localhost:1313/> in a browser. Hugo watches the source files and
refreshes the site when they change.

To include draft content:

```powershell
hugo server --buildDrafts
```

Press `Ctrl+C` to stop the server.

## Build the site

Create an optimized production build:

```powershell
hugo build --gc --minify
```

The generated static site is written to the `public` directory. To preview the
production output locally, run:

```powershell
hugo server --renderToMemory --minify
```

## Project structure

- `content/` contains pages and posts.
- `static/` contains files copied directly into the generated site.
- `config/` and `hugo.toml` contain site and theme configuration.
- `data/` contains structured data used by templates and shortcodes.
- `public/` contains generated build output.

Pushing to the `main` branch triggers the GitHub Actions workflow that builds
the site and deploys it to GitHub Pages.
