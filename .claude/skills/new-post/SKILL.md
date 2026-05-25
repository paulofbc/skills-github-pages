---
name: new-post
description: Turn a draft markdown file into a properly-formatted pfbc-blog post. Reads an input .md file, extracts or infers title/category/tags, derives a slug + today's date, and writes the result to _posts/YYYY-MM-DD-slug.md following the conventions in CLAUDE.md and WRITING.md. Use when the user wants to publish or convert a draft to a post.
---

# new-post

Use when the user wants to publish a draft markdown file as a blog post
on pfbc-blog.

## Inputs

- **Required**: path to a markdown file containing the draft. The path
  is passed as the skill argument (e.g. `/new-post drafts/foo.md` or
  `/new-post /tmp/notes.md`).
- The file may or may not have YAML frontmatter. Anything already in
  frontmatter wins over inference.

If no path was given, ask the user for one. Don't guess.

## Steps

### 1. Read and parse the input file

- Read the file at the provided path.
- If the file starts with `---`, the YAML frontmatter block is between
  the first and second `---`. Everything after is the body.
- If there's no frontmatter, the whole file is the body.

### 2. Determine the title

In order of preference:

1. `title:` in frontmatter.
2. First H1 (`# Title`) line in the body. If used, **strip that line**
   from the body so it isn't duplicated under Hydejack's auto-rendered
   page title.
3. Ask the user: "What title should this post have?"

### 3. Determine the category

The blog uses **one** category per post (it sets the URL prefix). The
category must come from the existing set when possible — only invent a
new one if nothing fits.

1. If frontmatter has `category:` (singular) or `categories:` with one
   entry, use it.
2. Otherwise, list the current categories by running:
   ```sh
   grep -h '^category:' /Users/pc/pfbc-blog/_posts/*.md | sort -u
   ```
   Match the draft's topic against that set. Pick the best fit.
3. If nothing fits, propose a new category name to the user and
   confirm before using it. Keep it short, lowercase, kebab-case
   (`backend`, `data`, `devops`, `tools`, `notes`).

Never write a post without a category.

### 4. Determine tags (optional)

- If frontmatter has `tags:`, use them.
- Otherwise, infer 2–4 lowercase tags from the content (technologies,
  concepts). Don't pad — fewer good tags beats a wall of tags.
- Skip the field entirely if you can't think of anything specific.

### 5. Build the slug and filename

- Slug: kebab-case version of the title — lowercase, alphanumeric
  segments separated by `-`, no leading/trailing `-`. Drop articles
  (`a`, `the`) only if the title is long; otherwise keep them.
- Date: today's date in `YYYY-MM-DD`. Use the `currentDate` from the
  conversation context if present; otherwise `date +%Y-%m-%d`.
- Filename: `_posts/YYYY-MM-DD-<slug>.md` (always under the repo's
  `_posts/` directory).

If a file with that name already exists, append `-2`, `-3`, … to the
slug until unique.

### 6. Determine the description

- If frontmatter has `description:`, use it.
- Otherwise, write one yourself: a single-sentence summary that would
  make sense on a listing page. Aim for under ~140 characters.

### 7. Compose the post

Frontmatter:

```yaml
---
title: "<title>"
date: <YYYY-MM-DD>
category: <category>
tags: [<tag>, <tag>]      # omit if empty
description: "<one-line summary>"
---
```

Body: the input file's body, with the first H1 line removed if that's
where the title came from. Do not rewrite or restructure the user's
prose — only adjust frontmatter and the leading H1. If the body has no
TL;DR and looks like it would benefit from one, you may suggest one to
the user but do not invent content on your own.

**Insert a table-of-contents marker** at the very top of the body
(before the TL;DR / first heading). Hydejack anchors its sidebar TOC
at the marker's vertical position, so placing it at the top pins
the TOC to the top of the page on wide screens.

```markdown
* TOC
{:toc}
```

Use plain Markdown — the `* TOC` line is a list item and `{:toc}`
is the Kramdown attribute that turns it into a generated TOC at
build time.

### 8. Write the file and confirm

- Write to `_posts/YYYY-MM-DD-<slug>.md`.
- Print a short summary: title, category, tags, target filename, and
  the URL it will be published at:
  `https://paulofbc.github.io/pfbc-blog/<category>/<year>/<month>/<slug>/`.
- Ask the user whether to commit + push, or stop here so they can edit
  first. Do **not** commit without explicit go-ahead.

## Things to avoid

- Don't use `layout: welcome` — it's Hydejack PRO and breaks. Posts
  don't need a `layout:` key at all; the default `post` layout is
  applied by `_config.yml`'s `defaults`.
- Don't put multiple values in `category:`. Use tags for secondary
  facets.
- Don't auto-commit. Even if the user previously said "go" for an
  unrelated push, ask again for each new post.
- Don't move or delete the input draft. Leave it where it is unless
  the user asks otherwise.
