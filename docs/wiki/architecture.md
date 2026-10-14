# Architecture

The site is four static HTML files served by GitHub Pages, behind a custom domain. There is no
build step, no framework, no bundler and no server-side code — `git push` is the whole deploy
pipeline. Each page carries its own inline `<style>` block and, where needed, an inline
`<script>`; there is no shared stylesheet.

That means **CSS is duplicated across pages**. Colour tokens (`--primary`, `--accent`,
`--amber`, `--bg-dark`, …) are redefined in each page's `:root`. Changing a token on one page
does not change it on the others — see the checklist in [content-editing.md](content-editing.md).

## Components

- **GitHub Pages** — serves the repo's `main` branch over HTTPS. Rebuilds in roughly 1–2
  minutes after a push. No CI workflow is required for a static site.
- **`CNAME`** — pins the custom domain `upperhortonsportsclub.com.au`. Requests to
  `hawkinle.github.io` 301-redirect to it.
- **`index.html`** — homepage. Carries the only stateful code on the site: the inline
  "Tonight at the Club" status script (below).
- **`events.html`, `meatfest.html`, `rules.html`** — static content pages, each self-contained.
- **`_config.yml`** — Jekyll config. The HTML files are served **verbatim** because none of them
  have YAML front matter; Jekyll copies them through untouched. The config is therefore inert
  for these pages, but is kept tidy.

## System Diagram

```mermaid
flowchart TD
    Dev([Author edits HTML]) --> Push["git push origin main"]
    Push --> Pages{{GitHub Pages}}
    Pages --> CNAME["CNAME: upperhortonsportsclub.com.au"]
    CNAME --> Visitor([Visitor's browser])

    Visitor --> Home["index.html"]
    Visitor --> Events["events.html"]
    Visitor --> Meat["meatfest.html"]
    Visitor --> Rules["rules.html"]

    Home --> Status["Inline JS: tonight status"]
    Status --> SydneyTZ{{Intl: Australia/Sydney}}
    Home --> Maps{{Google Maps link}}
    Home --> Tel["tel: (02) 6782 7249"]
    Home --> Messenger{{m.me/UpperHortonSportsClub}}
    Rules -.-> RulesSrc["australian_barbecue_rules.md"]
```

## Data Flow — the homepage "tonight" status

`index.html` contains one inline IIFE at the end of `<body>`. On load it:

1. Reads the current date/time **in the club's timezone** (`Australia/Sydney`) via
   `Intl.DateTimeFormat(...).formatToParts()`. Using the club timezone, not the visitor's, means
   a traveller sees the club's real state.
2. Works out whether NSW daylight saving is active, using the "first Sunday in October →
   first Sunday in April" rule.
3. Looks the day up in a small in-file schedule table and writes one of three states into the
   card: *Closed today*, *Opens today/tonight at X*, or *Open now*.
4. Adds `.today` to the matching row in the hours list so today is highlighted.

The schedule table maps **opening times only**. It deliberately has no `close` field — closing
is patronage-dependent. See [content-editing.md](content-editing.md) before touching it.

`aria-live="polite"` is set on the card so the status is announced to screen readers after the
script runs.

## Key Design Decisions

- **Utility first.** The page leads with open-tonight status, phone and address. Events come
  after. This was a deliberate change from the original design, which led with whatever event
  was happening and buried the phone number two screens down.
- **No hard closing times anywhere** — not in the HTML, and not in the structured data. The
  `openingHoursSpecification` in the JSON-LD block carries `opens` but **no `closes`**, so a
  Google result can never claim the club closes at a time it doesn't.
- **Archive, don't delete.** Meat Fest 2026 is kept as a dated archive page. The rule applied on
  the homepage is that nothing leads with a date in the past.
- **Serve the club name once.** The site's hero uses `images/hero_banner.jpg`, not
  `images/banner.jpg` — see the hero warning in [content-editing.md](content-editing.md).
- **Structured data is real.** The JSON-LD `SportsActivityLocation` block on the homepage
  duplicates the address, geo coordinates, phone and hours that appear in the visible copy, so
  search engines and the page agree.
