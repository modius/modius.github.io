# modius.io

Personal site for Geoff Bowers. Built with Jekyll, hosted on GitHub Pages.

## Stack

- **Framework:** Jekyll (GitHub Pages native — no build step, no CI/CD)
- **Hosting:** GitHub Pages with custom domain (`modius.io`)
- **Styling:** Custom SCSS — Inter for body, JetBrains Mono for code/accents
- **Plugins:** jekyll-sitemap, jekyll-feed, jekyll-seo-tag (all GitHub Pages whitelisted)

## Structure

```
.
├── _config.yml          # Site config, collections, defaults
├── _layouts/
│   ├── default.html     # Base layout (header, footer, nav)
│   ├── post.html        # Blog post / relic layout
│   ├── page.html        # Static page layout
│   └── zero.html        # Private /0 area (dark theme, noindex)
├── _relics/             # Archive posts ("Dusty Relics" collection)
├── _posts/              # New blog posts go here
├── _sass/
│   └── main.scss        # All styles in one file
├── assets/
│   ├── css/main.scss    # SCSS entry point
│   └── images/          # Post images
├── 0/                   # Private sharing area (not indexed, not linked)
│   ├── index.html       # Landing page listing private items
│   └── bgg/             # Example: BGG@SEA cruise itinerary
│       └── index.html
├── about/index.md       # About page
├── relics.html          # Dusty Relics archive listing
├── index.html           # Homepage
├── 404.md               # Custom 404
├── robots.txt           # Blocks /0/ from crawlers
├── CNAME                # Custom domain config
└── inbox/               # Gitignored — dump raw content here for remastering
```

## Writing

**New blog post:** Add a markdown file to `_posts/` with the naming convention `YYYY-MM-DD-slug.md`:

```markdown
---
title: "My Post Title"
date: 2026-02-09
tags: [topic, another]
description: "A brief description."
---

Post content here.
```

**New private page:** Create a subdirectory under `0/` with an `index.html` (for clean URLs like `/0/abc/`). Use `layout: zero` in front matter. Pages under `/0` are:

- Not in the sitemap
- Not indexed by search engines (`noindex, nofollow`)
- Blocked by `robots.txt`
- Not linked from the main site navigation
- Dark themed with their own layout

Share the direct URL (e.g. `modius.io/0/bgg/`) with whoever needs it.

## Inbox

The `inbox/` directory is gitignored. Drop raw content (markdown, JSX, whatever) in there as a staging area for content to be remastered into the site.

## Publishing

Push to `master` — GitHub Pages builds and deploys automatically. No CI, no Actions, no build commands. Just git push.

## Local Development

```bash
bundle install
bundle exec jekyll serve
# => http://localhost:4000
```
