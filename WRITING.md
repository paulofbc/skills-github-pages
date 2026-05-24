# Writing a new post

A quick recipe for future-me. Keep this short. If the recipe gets long,
the workflow is wrong — fix the workflow, not the doc.

## 1. Pick the shape of the post

- **Category** (one, required). Sets the URL prefix. Existing ones:
  `backend`. New categories are fine — just use them in the frontmatter
  and they'll start appearing on `/categories/` automatically.
- **Tags** (optional, multiple). For cross-cutting facets like
  `python`, `redis`, `performance`. They don't affect the URL.

## 2. Create the file

Filename format (Jekyll is strict — wrong date = build fails):

```
_posts/YYYY-MM-DD-kebab-case-title.md
```

For drafts you're not ready to ship, drop them in `_drafts/` instead.
They won't be built or published until moved into `_posts/` with a
proper date.

## 3. Frontmatter template

Copy this to the top of the new file:

```yaml
---
title: "Title of the post"
date: YYYY-MM-DD
category: backend
tags: [tag-a, tag-b]
description: "One-line summary that shows up on listing pages."
---
```

Notes:

- `category` is **singular** — one per post, sets the URL.
- `description` shows up on `/posts/` and `/categories/`. Skip it and
  the listing entry has no summary line.
- Date must match the filename's date.

## 4. Body structure

The convention for this blog:

1. **TL;DR** — one blockquote, the punchline of the post.
2. **Why** — short paragraph: what problem made you write this.
3. **The how-to** — code first, prose second. Runnable snippets beat
   explanation.
4. **Gotchas** (optional) — bullet list of things that bit you.
5. **References** — bullet list of links worth keeping.

Posts shorter than the section list above are fine. Don't pad.

## 5. Preview locally (optional but recommended)

```sh
bundle exec jekyll serve --drafts
```

Open <http://127.0.0.1:4000/pfbc-blog/>. Drafts are included so you
can preview before promoting them to `_posts/`.

If `bundle` isn't set up yet, run `bundle install` once.

## 6. Publish

```sh
git add _posts/YYYY-MM-DD-kebab-case-title.md
git commit -m "Add post: <title>"
git push
```

GitHub Pages rebuilds in ~60s. The site is at
<https://paulofbc.github.io/pfbc-blog/>.

## Images

Put them under `assets/img/` and reference with a `relative_url`:

```markdown
![alt text]({{ '/assets/img/my-screenshot.png' | relative_url }})
```

## Editing an old post

- Bump the `updated:` field in frontmatter (add it if missing) — useful
  for "living notes" that get revised over time.
- Don't rename the file; that breaks the permalink.

## When something doesn't render right

- Bare `<p>` tags with no theme = layout typo. Make sure you're using
  one of `post` (default for posts), `page`, `home`, or `list`. Avoid
  `welcome` — that one is Hydejack PRO and silently fails on free.
- Theme assets 404 in DevTools = `baseurl` got dropped from
  `_config.yml`. Should be `/pfbc-blog`.
- Build failed entirely = check the **Actions** tab on GitHub; the
  error usually points at a bad date or YAML typo.
