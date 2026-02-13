# Project Instructions

Jekyll site deployed to modius.io via GitHub Pages.

## /0 — Ephemeral Pages

`/0/` hosts private, unlisted pages. Not indexed. Not linked. Just shared.

### Publishing from inbox/

Files dropped into `inbox/` are source material to publish under `/0/`. The `inbox/` directory is excluded from the Jekyll build and git.

**Workflow:**

1. Check `inbox/` for new files
2. Create a subdirectory under `/0/` (3–5 characters, e.g. `/0/monk/`, `/0/prpc/`)
3. Publish files with minimal modification — just prepend front matter:
   - **Standalone HTML** (has its own `<!DOCTYPE>`, `<head>`, `<body>`): use `layout: null`
   - **Markdown files**: use `layout: zero` with `title` and `sitemap: false`
4. If multiple files, create a landing page at `/0/{name}/index.html` using `layout: zero` — dark cards linking to subpages (follow the pattern in `/0/bgg/index.html` or `/0/prpc/index.html`)
5. Add an entry to `/0/index.html` listing
6. Commit, push, and delete the inbox files (`/bin/rm` to bypass the shell alias)

**Defaults for /0 path** (from `_config.yml`):
- `layout: zero`
- `sitemap: false`

### Key conventions

- Subdirectory names: 3–5 characters
- Standalone HTML gets `layout: null` (bypasses the zero layout wrapper)
- Markdown/content gets `layout: zero` (dark theme, Inter + JetBrains Mono fonts)
- The `rm` command is aliased to `rm -i` on this system — use `/bin/rm` to delete without prompts
