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

## Local preview

```bash
bundle install
bundle exec jekyll serve   # http://127.0.0.1:4000
```

## Deploy

Push to `main`. GitHub Pages builds and serves automatically.
```
