# Publishing a knowledge article

This is a runbook for adding a new article to `/knowledge/`. Follow it exactly —
every step exists because skipping it broke something in the past (see "Why
these steps exist" at the bottom). Written so an agent (or a human) with no
other context can execute it end to end.

## What this site is

A static HTML site (no build step, no framework) deployed via GitHub Pages
from the `master` branch of `rerenoob/long-an-solutions`, served at
`https://longansolutions.com/`. Pushing to `master` deploys automatically,
usually live within a minute or two. There is nothing to build or install.

## Files a new article touches

Publishing one article means editing **four** places, not one. Missing any of
these is the exact kind of drift this repo has had before (see bottom).

1. `knowledge/<slug>.html` — the new article file itself
2. `knowledge.html` — add a card to the grid, a link in the table of contents,
   and an entry in the `hasPart` JSON-LD list
3. `sitemap.xml` — add a `<url>` entry
4. `git add`, commit, push — nothing is live until this happens

## Step 1: Pick a slug

Lowercase, ASCII, hyphen-separated, no Vietnamese diacritics, matching the
article's Vietnamese title. Examples already in use:
`huong-dan-hoa-don`, `thay-doi-chinh-sach-thue-2026`, `ky-to-khai-tren-dien-thoai`.

The file will live at `knowledge/<slug>.html` and be reachable at
`https://longansolutions.com/knowledge/<slug>.html`.

## Step 2: Create the article from this template

Copy the block below into `knowledge/<slug>.html` and fill in every
`{{PLACEHOLDER}}`. Do not deviate from this structure — see "Rules" below for
why each part matters.

```html
<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>{{TITLE}} | Long An Solutions</title>
  <meta name="description" content="{{DESCRIPTION}}">
  <link rel="canonical" href="https://longansolutions.com/knowledge/{{SLUG}}.html">
  <link rel="icon" href="../favicon.ico" sizes="any">
  <link rel="icon" type="image/png" href="../apple-touch-icon.png" sizes="180x180">
  <link rel="apple-touch-icon" href="../apple-touch-icon.png">
  <meta property="og:type" content="article">
  <meta property="og:title" content="{{TITLE}} | Long An Solutions">
  <meta property="og:description" content="{{DESCRIPTION}}">
  <meta property="og:image" content="https://longansolutions.com/og-image.png">
  <meta property="og:url" content="https://longansolutions.com/knowledge/{{SLUG}}.html">
  <meta name="twitter:card" content="summary_large_image">
  <link rel="stylesheet" href="../assets/knowledge.css">
  <script type="application/ld+json">
  {
    "@context": "https://schema.org",
    "@type": "Article",
    "headline": "{{TITLE}}",
    "description": "{{DESCRIPTION}}",
    "image": "https://longansolutions.com/og-image.png",
    "datePublished": "{{DATE_ISO}}",
    "dateModified": "{{DATE_ISO}}",
    "inLanguage": "vi",
    "author": {"@type": "Organization", "name": "Long An Solutions", "url": "https://longansolutions.com/"},
    "publisher": {"@type": "Organization", "name": "Long An Solutions", "logo": {"@type": "ImageObject", "url": "https://longansolutions.com/logo.png"}},
    "mainEntityOfPage": {"@type": "WebPage", "@id": "https://longansolutions.com/knowledge/{{SLUG}}.html"}
  }
  </script>
  <!-- Google Analytics -->
  <script async src="https://www.googletagmanager.com/gtag/js?id=G-XXEYJRVGB8"></script>
  <script>
    window.dataLayer = window.dataLayer || [];
    function gtag(){dataLayer.push(arguments);}
    gtag('js', new Date());
    gtag('config', 'G-XXEYJRVGB8');
  </script>
</head>
<body>
<nav>
  <div class="container nav-inner">
    <a href="../" class="logo">Long An Solutions</a>
    <ul>
      <li><a href="../knowledge.html">← Kiến thức</a></li>
      <li><a href="../">Trang chủ</a></li>
    </ul>
  </div>
</nav>

<main class="container">
  <h1>{{TITLE}}</h1>
  <p class="sub">{{ONE_SENTENCE_SUBTITLE}}</p>
  <p class="date">Đăng: {{DATE_VN}} · Cập nhật: {{DATE_VN}}</p>

  <article>
    <h2>{{Section heading}}</h2>
    <p>Body text…</p>

    <h2>{{Next section heading}}</h2>
    <ul>
      <li>Point one</li>
      <li>Point two</li>
    </ul>
  </article>

  <p style="margin:32px 0;text-align:center"><a href="../knowledge.html" style="color:#0d6e6e;font-weight:600">← Quay lại trang kiến thức</a></p>
</main>

<footer>
  <div class="container">
    <p><a href="../">Long An Solutions</a>, AI-powered tax filing for Vietnamese household businesses and SMEs</p>
    <p style="margin-top:4px">© 2025-2026 · Long An Solutions · MST: 001088040737</p>
  </div>
</footer>
</body>
</html>
```

Placeholder reference:

| Placeholder | Example |
|---|---|
| `{{SLUG}}` | `ky-to-khai-tren-dien-thoai` |
| `{{TITLE}}` | Ký tờ khai thuế trên điện thoại |
| `{{DESCRIPTION}}` | One or two sentences, ~150–160 characters, used for the meta description AND `og:description` — write it as the thing you'd want to show up in a Google result |
| `{{ONE_SENTENCE_SUBTITLE}}` | The line shown under the H1 on the page itself. Can repeat/expand the description. |
| `{{DATE_ISO}}` | Today's date, `YYYY-MM-DD` (e.g. `2026-09-15`) — used in both `datePublished` and `dateModified` on first publish |
| `{{DATE_VN}}` | Today's date, `DD/MM/YYYY` (e.g. `15/09/2026`) — same date, Vietnamese display format, used for both "Đăng" and "Cập nhật" on first publish |

### Rules for the body content

- **Exactly one `<h1>`** — the title, set in the `<main>` block above the
  `<article>`. Never put the title inside `<article>` as an `<h2>`. (This was
  the single biggest bug found in a 2026-09 audit: 7 of 9 articles had no
  `<h1>` at all because the title was buried as an `<h2>` inside `<article>`.)
- Inside `<article>`, sections are `<h2>`, subsections under a section are
  `<h3>`. Don't skip a level.
- Never add an inline `<style>` block. Everything needed already exists in
  `assets/knowledge.css` (`.step-box`, `.highlight`, `.highlight.green`,
  `details.faq-item` for FAQ accordions, plain `<table>`/`<th>`/`<td>` for
  tables, `.back-top` for a "back to top" link). If a new article needs a
  visual pattern that doesn't exist yet, add a class to
  `assets/knowledge.css` — do not paste CSS into the article file.
- Link to other articles with a relative path, e.g. `<a href="ky-so.html">`
  (you're already inside `/knowledge/`, so no `../knowledge/` prefix needed
  between articles).
- If the article is FAQ-style (a list of questions people ask), also add a
  second `<script type="application/ld+json">` block with `FAQPage` schema —
  see `knowledge/faq.html` for the exact pattern to copy.

## Step 3: Register it on the hub (`knowledge.html`)

Three separate edits, all in `knowledge.html`:

1. **Card grid** — inside `<div class="card-grid">`, add:
   ```html
   <a href="knowledge/{{SLUG}}.html" class="card-link">
     <h3 style="font-size:1rem;font-weight:700;color:#1a1a2e;margin-bottom:8px">{{TITLE}}</h3>
     <p style="font-size:.85rem;color:#666;line-height:1.6;margin:0">{{DESCRIPTION}}</p>
   </a>
   ```
2. **Table of contents** — inside `<nav class="sticky-nav">`, add a short-label
   link: `<li><a href="knowledge/{{SLUG}}.html">{{Short label}}</a></li>`
3. **JSON-LD `hasPart`** — add one line to the `CollectionPage` JSON-LD array
   near the top of the file:
   ```json
   {"@type": "Article", "headline": "{{TITLE}}", "url": "https://longansolutions.com/knowledge/{{SLUG}}.html"}
   ```

## Step 4: Add it to `sitemap.xml`

Append a `<url>` block:

```xml
<url>
  <loc>https://longansolutions.com/knowledge/{{SLUG}}.html</loc>
  <lastmod>{{DATE_ISO}}</lastmod>
  <changefreq>monthly</changefreq>
  <priority>0.7</priority>
</url>
```

## Step 5: Ship it

```bash
cd ~/Projects/long-an-solutions
git add knowledge/{{SLUG}}.html knowledge.html sitemap.xml
git commit -m "knowledge: add {{TITLE}}"
git push
```

GitHub Pages rebuilds automatically. Check `https://longansolutions.com/knowledge/{{SLUG}}.html`
after a minute or two to confirm it's live.

## Editing an existing article later

Update the content, then update **both**:
- the visible `<p class="date">Đăng: … · Cập nhật: {{new date}}</p>` line
  (keep the original "Đăng" date, only change "Cập nhật")
- the `"dateModified"` field in that article's JSON-LD block

Do not touch `datePublished` once it's set.

## Why these steps exist

A 2026-09 audit of this site found: 7 of 9 articles had no `<h1>` (title was
buried as an `<h2>` inside `<article>`); the hub's table of contents linked to
`#anchor` fragments that no longer existed after articles were split into
separate files; one published article was missing from the table of contents
entirely; no article had a canonical tag, Open Graph tags, JSON-LD, or a
visible date; Google Analytics was missing from every knowledge page; and the
CSS was copy-pasted in full into all 9+ files instead of shared. All of that
was fixed and consolidated into this template and `assets/knowledge.css`.
Following this doc keeps the next article from re-introducing the same gaps.
