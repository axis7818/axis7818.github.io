# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## About

Personal portfolio and blog site for Cameron Taylor at [camerontaylor.dev](https://camerontaylor.dev). Built with [Zola](https://www.getzola.org/) (a Rust-based static site generator) using the [tabi](https://github.com/welpo/tabi) theme (included as a git submodule at `themes/tabi/`).

Zola uses the [Tera](https://keats.github.io/tera/) templating engine, and all content is written in Markdown with TOML front matter.

## Development

```bash
# Start local dev server (hot reload)
zola serve

# Or via Docker (no local Zola install needed)
docker run -u "$(id -u):$(id -g)" -v $PWD:/app --workdir /app -p 8080:8080 ghcr.io/getzola/zola:v0.19.1 serve --interface 0.0.0.0 --port 8080 --base-url localhost
```

Pushing to `main` triggers GitHub Actions (`shalzz/zola-deploy-action`) which builds and deploys to the `gh-pages` branch automatically.

## Content

All content lives in `content/` as Markdown files with TOML front matter:

- `content/blog/` — Blog posts (sorted by date, 5 per page)
- `content/projects/` — Portfolio items (sorted by `weight` in front matter)
- `content/pages/` — Static pages (About, etc.)
- `content/archive/` — Archive section

Blog posts use `taxonomies.tags` for categorization. Projects use `[extra].link_to` for external links and `[extra].local_image` for card images.

## Customization

**SCSS / Styling:**
- `sass/skins/axis7818.scss` — Custom Catppuccin-based color skin (Latte for light, Frappe for dark)
- `sass/styles.scss` — Global custom styles (imports tabi's base)
- Post-specific styles can be added in `sass/styles/blog/` and referenced via `[extra].stylesheets` in front matter

**Templates:**
- `templates/tabi/` — Overrides for tabi theme templates (currently `extend_head.html` for favicons)
- `templates/shortcodes/` — Custom Tera shortcodes (`unsplashimg`, `top10albums`)

**Data files:**
- `data/top10albums.toml` — Data for the top albums shortcode

To override any tabi template, copy it from `themes/tabi/templates/` into `templates/tabi/` and edit there.

## Configuration

`config.toml` controls all Zola and tabi settings, including:
- Navigation menu items
- giscus comments (enabled by default for all posts, using GitHub Discussions)
- Search index (elasticlunr, JSON format)
- SASS compilation and HTML minification
- CSP headers and allowed embed domains (YouTube, Vimeo)
- Social links (GitHub, LinkedIn, Bluesky)
