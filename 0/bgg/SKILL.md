# BGG@SEA Remastering Skill

Remaster raw source documents into the `/0/bgg/` private area of modius.io.

## Source Files

Drop updated source files into `/inbox/`:

- `BGG-at-SEA-Cruise-Checklist.md` — Markdown checklist with `[ ]`/`[x]` checkboxes, sections for critical tasks, packing, boarding, during cruise, decisions, contacts
- `BGG-at-SEA-Trip-Dashboard.jsx` — React component with itinerary data, interactive filters, port guides, perks, booking details

## Output Structure

```
0/bgg/
├── index.html           # Landing page — intro, countdown, links to schedule + checklist
├── schedule/index.html  # Interactive dashboard (JSX → vanilla HTML/CSS/JS)
├── checklist/index.html # Full checklist with interactive checkboxes (localStorage persistence)
└── SKILL.md             # This file
```

## Remastering Rules

1. **Layout:** All pages use `layout: zero` with `sitemap: false` in front matter
2. **No frameworks (default):** Convert JSX to vanilla HTML/CSS/JS — no build step needed.
   **Alternative — Babel CDN:** For faster turnaround, you can use React + Babel directly in the browser:
   ```html
   <script src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
   <script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
   <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
   <div id="root"></div>
   <script type="text/babel">
     // paste JSX component here — remove the import and add ReactDOM.render() at the end
   </script>
   ```
   This adds ~800KB page weight but is fine for a private page with 1-2 viewers
3. **Dark theme:** Background `#111`, cards `#1a1a1a`, borders `#222`, text `#ccc`/`#fff`
4. **Fonts:** Inter (body) + JetBrains Mono (times/code) — already loaded by zero layout
5. **Colour coding:** BGG=orange, Cruise=blue, The Key=amber, Work=pink, Birthday=green, Scenic=emerald
6. **Interactive features to preserve from JSX:**
   - Countdown timer to Feb 15, 2026
   - Stats grid (sea days, port days, scenic, NZ ports)
   - Visual timeline bar — clickable, colour-coded by day type, orange stripe for BGG events
   - Day type filter buttons (All / Ports / Sea / Scenic)
   - Category filter buttons (All / BGG / Cruise / Key / Work)
   - Expandable/collapsible day cards with grouped todos
   - BGG convention highlights section
   - The Key VIP perks grid
   - Sea Class perks grid
   - Port-by-port DIY guide with highlights, costs, transport, dining
   - Booking details
   - Colour key/legend
7. **Checklist features:**
   - Render `[x]` as checked, `[ ]` as unchecked interactive checkboxes
   - Persist checkbox state in localStorage (key: `bgg-checklist`)
   - Preserve all sections, blockquotes, tables, and links from the markdown
   - Add a "Reset all" button and progress counter
   - Back link to `/0/bgg/`
8. **Landing page:** Brief intro, countdown, two card links (schedule + checklist), quick reference booking details
9. **Navigation:** Each sub-page has a back link to `/0/bgg/`. No site-wide nav.
10. **Mobile responsive:** Cards stack, grids collapse to single column below 600px

## How to Remaster

```
1. Read the source files from /inbox/
2. Read this SKILL.md for structure and rules
3. Regenerate schedule/index.html from the JSX data
4. Regenerate checklist/index.html from the markdown
5. Update landing page if structure changed
6. Clear /inbox/ when done
7. Commit and push
```

## Key Data Points (for reference)

- Ship: Anthem of the Seas
- Dates: Feb 15–27, 2026 (12 nights)
- Departure: Sydney
- Booking: 6044580
- Cabin: Junior Suite, Deck 6, 6638
- Muster: D1
- Badge: #175 Geoffrey Bowers
- Sail date for countdown: `new Date(2026, 1, 15)` (JS months are 0-indexed)
