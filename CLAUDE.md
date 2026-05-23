# pfbc-blog

Personal blog and knowledge base for Paulo Cavalcanti. Hosted on GitHub
Pages (Jekyll, Hydejack theme).

## Purpose

- Primary: gather professional knowledge and short how‑tos, mostly about
  technical topics Paulo is currently learning or has recently learned.
- Secondary: a CV page accessible from the site, with a downloadable PDF.
- Posts are grouped by **categories** (topic prefix in URL).

## Stack

- Jekyll, served by GitHub Pages.
- Theme: **Hydejack (free, v9)** via `remote_theme: hydecorp/hydejack@v9`.
  Pulled in by `jekyll-remote-theme`, which is on the GH Pages plugin
  allow‑list — no custom build pipeline needed.
- No comments, no analytics.
- No custom domain (site stays at `paulofbc.github.io`).
- Language: English only.

## Site map

```
/                                    Home — intro + recent posts
/posts/                              Full archive
/categories/                         Category index (counts per category)
/<category>/<year>/<month>/<slug>/   Post permalink
/cv/                                 CV page (+ link to /assets/cv.pdf)
```

Permalink in `_config.yml`: `permalink: /:categories/:year/:month/:title/`.
Posts without a category fall back to `/:year/:month/:title/`.

## Conventions

- Post filenames: `_posts/YYYY-MM-DD-kebab-case-title.md`. Jekyll rejects
  invalid dates and the site build will fail.
- Frontmatter on every post:
  ```yaml
  ---
  title: "…"
  date: YYYY-MM-DD
  category: <one-category>     # required — sets the URL prefix
  tags: [tag-a, tag-b]         # optional, for finer-grained filtering
  description: "1-line summary shown on listing pages"
  ---
  ```
- Use a **single** category per post (Jekyll concatenates multiples into
  the URL, which gets noisy). Use tags for cross-cutting facets.
- Drafts live in `_drafts/` and are excluded from builds by default.
- Posts follow: **TL;DR → context → how‑to → references**.

## Layout

- `_config.yml` — site metadata, theme, permalink, plugin list, menu.
- `Gemfile` — pins `github-pages` so local builds match production.
- `index.md` — home page (`layout: welcome`).
- `posts.md` — full archive at `/posts/`.
- `categories.md` — category index at `/categories/`.
- `cv.md` — CV page at `/cv/`.
- `assets/cv.pdf` — downloadable CV. Source of truth is
  `/Users/pc/Documents/CV/paulo_cavalcanti_cv_eng.pdf`; sync with
  `cp` when the local PDF changes.
- `_posts/` — blog posts.

## Running locally

```sh
bundle install
bundle exec jekyll serve --drafts
```
