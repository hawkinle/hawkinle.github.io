# Upper Horton Sports Club — website

Static site for the Upper Horton Sports Club (bar, meals, events), Upper Horton NSW.
Live at **https://upperhortonsportsclub.com.au**.

Hand-written HTML — no framework, no build step. `git push` is the deploy.

## Pages

| File | Purpose |
|---|---|
| `index.html` | Homepage — live "open tonight" status, promo for the next big day, hours, what's on, address, contact |
| `calcutta.html` | Melbourne Cup Calcutta — Sunday 1 November 2026 event page (food, syndicates, how the day runs) |
| `events.html` | What's On — weekly rhythm, upcoming events, annual events, Meat Fest archive |
| `meatfest.html` | Meat Fest 2026 archive (held 26 Sep 2026, sold out) |
| `rules.html` | Competition rules, rendered from `australian_barbecue_rules.md` |

## Quick start

```bash
python3 -m http.server 8096     # preview at http://localhost:8096/
git add -A && git commit -m "..." && git push origin main
```

Pushing to `main` republishes the site via GitHub Pages in ~1–2 minutes.

## Documentation

Full wiki in **[`docs/wiki/`](docs/wiki/README.md)**:

- [Overview and page map](docs/wiki/README.md)
- [Architecture](docs/wiki/architecture.md) — hosting, domain, the homepage status script
- [Editing the content](docs/wiki/content-editing.md) — how to change copy, **and the invariants not to break**
- [Getting started](docs/wiki/getting-started.md) — preview, deploy, verify
- [Sequence diagrams](docs/wiki/diagrams/sequences.md) — deploy and status flows

## Social media images

Ready-made Facebook/social graphics live in `social/`:

| File | Size | Use |
|---|---|---|
| `social/calcutta-post-square.png` | 1080×1080 | Square feed post — the event announcement |
| `social/calcutta-post-menu.png` | 1080×1080 | Square feed post — the food and prices |
| `social/calcutta-landscape.png` | 1200×630 | Landscape — link posts and page headers |

Each PNG is rendered from the matching `.html` in the same folder. To change the wording, edit
the HTML and re-render:

```bash
cd social
google-chrome --headless=new --no-sandbox --disable-gpu --hide-scrollbars \
  --force-device-scale-factor=1 --window-size=1080,1080 --virtual-time-budget=8000 \
  --screenshot=calcutta-post-square.png "file://$PWD/calcutta-square.html"
```

The render depends on Google Fonts loading, so keep the `--virtual-time-budget` or you will get a
fallback font. Those HTML files are `noindex` and are not in the sitemap.

## Site rules worth knowing up front

- **No closing times.** The club opens at a set time but closes when the crowd thins. Closing is
  never published — not in the copy, not in the structured data.
- **Nothing leads with a past date.** Finished events move to an archive page.
- **Open, phone, address above everything else.** The site exists so a local can find out if the
  club is open tonight and ring it in one tap.
- Contact is **phone and Facebook Messenger only** — the club has no public email address.

## Key facts

- Address: 22 Cobbadah Street, Upper Horton NSW 2347
- Phone: (02) 6782 7249
- Facebook: https://www.facebook.com/UpperHortonSportsClub
- Messenger: https://m.me/UpperHortonSportsClub
- Hours: Wednesday–Sunday, from 5:00 PM (5:30 PM in daylight saving; Sunday from 12:00 PM).
  Monday and Tuesday closed.
