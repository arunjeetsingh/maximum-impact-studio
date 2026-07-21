# Maximum Impact Studio — website

The studio umbrella site at **https://maximumimpact.studio**, hosted on GitHub
Pages. Path-based: each app lives at `/<slug>/`. Built with Jekyll, driven by a
single catalog file so adding an app is a small, reviewable change.

## Structure

```
_config.yml            Site + studio identity
_data/apps.yml         App catalog — the single source of truth
_layouts/
  base.html            Shared chrome (header/footer/meta)
  default.html         Generic prose pages (privacy, etc.)
  app.html             Product page — renders from _data/apps.yml by slug
index.html             Studio homepage (app grid)
privacy.md             Studio-wide privacy page  (/privacy/)
<slug>/index.md        Per-app product page (layout: app, slug: <slug>)
<slug>/privacy.md      Per-app privacy page (Apple wants a stable URL)
assets/css/style.css   Styles
assets/img/<slug>-icon.png  App icons
CNAME                  Apex domain for GitHub Pages
```

## Launching a new app

1. Append a block to `_data/apps.yml` (copy an existing one).
2. Drop `assets/img/<slug>-icon.png`.
3. Create `<slug>/index.md`:
   ```yaml
   ---
   layout: app
   slug: <slug>
   title: <Name>
   permalink: /<slug>/
   ---
   Optional long-form copy here.
   ```
4. (If iOS) add `<slug>/privacy.md` and point App Store Connect's privacy URL at
   `https://maximumimpact.studio/<slug>/privacy/`.

The homepage grid and the product page both render from the catalog — no other
edits needed.

## Writing a blog post

Posts live in `_posts/` as `YYYY-MM-DD-slug.md` and use `layout: post`. The
permalink is `/:year/:month/:title/` (set in `_config.yml`).

Front-matter template:

```yaml
---
layout: post
title: "Your Title"
date: YYYY-MM-DD
author: Arun Singh            # optional; defaults via _config.yml
description: "1–2 sentence summary for search engines."
image: /assets/img/posts/<slug>-cover.png   # social-share card ONLY (see below)
excerpt: "Teaser shown on the post list."    # or use excerpt_separator: <!--more-->
---
```

### ⚠️ The cover image will NOT appear on the page from front-matter alone

`image:` in the front-matter is consumed **only** by `jekyll-seo-tag` to emit
the `og:image` / `twitter:image` meta tags (the thumbnail shown when the link
is shared on social/chat). The `post` layout (`_layouts/post.html`) does **not**
render it anywhere in the page body.

To actually show the cover at the top of the article, you must **embed it in
the post body** yourself, right after the front-matter, using the shared
`post-figure` pattern:

```html
<figure class="post-figure">
  <img src="{{ '/assets/img/posts/<slug>-cover.png' | relative_url }}"
       alt="Describe the image for accessibility / SEO" loading="lazy">
</figure>
```

See `_posts/2026-06-30-two-ai-coders-cross-review.md` and
`_posts/2026-07-20-chal-rickshaw-monetization-journey.md` for working examples.
For a video hero instead, use `<figure class="post-video">` with a `<video>`
tag and a `poster` still (see the Android beta post).

### Cover art conventions

- Store post images under `assets/img/posts/` named `<slug>-cover.png`.
- Standard cover size is **1200×630** (og:image friendly). Crop with
  `sips --resampleWidth 1200 file.png && sips -c 630 1200 file.png`.
- Use `{{ '...' | relative_url }}` around every asset path (never hardcode
  the domain) so links work in local preview and on Pages.

## Local preview

```bash
bundle install
bundle exec jekyll serve   # http://127.0.0.1:4000
```

## Deploy

Push to `main`. GitHub Pages builds and serves automatically.
```
