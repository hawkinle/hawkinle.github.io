# Getting Started

## Prerequisites

- `python3` — only to serve the files locally for preview
- `git`, with **SSH** access to GitHub as `hawkinle`

There is **no** Node, npm, bundler or build step. Nothing to install.

Verify SSH access (should greet you as `hawkinle`):

```bash
ssh -T git@github.com
```

## Repository

```bash
cd /home/leith/AI/Meat_fest/club_site
git remote -v          # origin  git@github.com:hawkinle/hawkinle.github.io.git
git branch --show-current   # main
```

The remote must be the **SSH** URL. The HTTPS remote fails non-interactively because there are
no stored credentials.

## Local preview

Always preview over HTTP. Opening the HTML files directly from disk mostly works for this site
(it's plain static HTML), but serving is what the deploy looks like, and it is what the browser
tooling can verify against:

```bash
cd /home/leith/AI/Meat_fest/club_site
python3 -m http.server 8096
```

Then open **http://localhost:8096/** in a browser.

To stop the server, `Ctrl-C`.

## Deploy

```bash
cd /home/leith/AI/Meat_fest/club_site
git add -A
git commit -m "Describe the change"
git push origin main
```

GitHub Pages rebuilds automatically from `main`. **Allow ~1–2 minutes**, then hard-refresh the
live site (the CDN caches for a few minutes; the `cache-control` max-age is 600s).

## Verify the deploy actually landed

Don't trust the push alone — check the artifact:

```bash
# the commit is on the remote
git log --oneline -1 origin/main

# the site answers
curl -s -o /dev/null -w "%{http_code}\n" https://upperhortonsportsclub.com.au/

# and the specific page/asset you changed is live
curl -s -o /dev/null -w "%{http_code}\n" https://upperhortonsportsclub.com.au/events.html
```

A `200` on the homepage but a stale page usually just means the CDN hasn't expired yet.

## Checks worth running before a push

There is a test suite of sorts: these are the checks that have caught real bugs here.

**1. No horizontal overflow, desktop and mobile.** The single worst bug in this repo's history
was a mobile grid regression that squeezed the page into three columns.

**2. Contrast, against the real backdrop.** For anything sitting over the hero photo, contrast
must be measured against actual rendered pixels, not against `--bg-dark`. A method that works:
screenshot the region twice — once normally and once with the text set `transparent` — then
compare the two with `PIL`/`numpy`.

**3. Opening hours contain no closing time.** Grep should find no time ranges on the homepage:

```bash
grep -nE "[0-9]:[0-9][0-9] (PM|AM) *(–|-|—) *[0-9]" index.html    # expect no matches
```

**4. No leftover registration form or endpoint.**

```bash
grep -rn "script.google.com\|GOOGLE_SHEET_URL\|DINER_FEAST_RESERVATION" *.html   # expect nothing
```

**5. Every internal link and asset resolves.** In particular `favicon.svg`,
`images/hero_banner.jpg` and `rules.html`.

**6. Page titles are clean and each page's `og:url` is its own URL.**

## Where to go next

- [README.md](README.md) — what the site is, page map, key facts
- [architecture.md](architecture.md) — hosting, domain, the status script
- [content-editing.md](content-editing.md) — how to edit copy, and the invariants
- [diagrams/sequences.md](diagrams/sequences.md) — deploy and status flows
