# Editing the Content

There is no CMS and no build step. You edit the HTML directly and push.

## The two-minute version

```bash
cd /home/leith/AI/Meat_fest/club_site
# edit the file you want
python3 -m http.server 8096          # preview at http://localhost:8096/
git add -A && git commit -m "..." && git push origin main
```

Always preview before pushing. See [getting-started.md](getting-started.md).

## Where things live

| To change… | Edit |
|---|---|
| Opening hours, "tonight" behaviour | [`index.html`](../../index.html) — the hours list **and** the inline script **and** the JSON-LD block |
| The "next big day" promo box | [`index.html`](../../index.html) — the `.hero-promo` block at the top of `<main>` |
| The Calcutta event | [`calcutta.html`](../../calcutta.html) (the page) **and** the Calcutta card in [`events.html`](../../events.html) **and** the "What's on" card in [`index.html`](../../index.html) |
| The social media graphics | the matching `.html` in [`social/`](../../social) — then re-render the PNG (see the README) |
| Address, phone, Facebook/Messenger links | [`index.html`](../../index.html) contact card + footer; `events.html` and `rules.html` footers; `meatfest.html` nav/footer |
| The weekly/what's-on wording | [`index.html`](../../index.html) "What's on" card and [`events.html`](../../events.html) |
| Events | [`events.html`](../../events.html) |
| Meat Fest archive copy | [`meatfest.html`](../../meatfest.html) |
| Competition rules | [`australian_barbecue_rules.md`](../../australian_barbecue_rules.md) — **then** mirror into [`rules.html`](../../rules.html) |
| Page titles, descriptions, social preview | the `<head>` of each page |
| Colours | each page's `:root` block — **they are per-page, not shared** |
| Hero image | `images/` + the `header` `background` rule in `index.html` |

---

## Invariants — do not break these

These are all things that were wrong once, were fixed, and must stay fixed.

### 1. Opening hours: no closing time, ever

The club's closing time is patronage-dependent. So:

- The hours list shows **"Open from 5:00 PM"** — an opening time, never a range with a close.
- The inline status script's schedule table has an `open` field and **no `close` field**.
- The JSON-LD `openingHoursSpecification` carries `opens` and **no `closes`**.

If you add a `closes` value to the structured data, Google will happily advertise "Closes 9 PM"
on the club's search result, which is exactly the wrong thing for a patron to believe. Leave it
out.

Opening times *are* fixed and shift by 30 minutes with daylight saving (5:30 PM in summer,
5:00 PM in winter; Sunday 12:00 PM). Check the DST logic matches the published times.

### 2. Every full-width card must be reset inside the mobile media query

`index.html` has `.tonight-card { grid-column: span 3; }` and `.hero-promo { grid-column: span 3; }`
for the desktop bento grid. The `@media (max-width: 992px)` block sets
`main { grid-template-columns: 1fr; }` **and** resets those spanning children to `span 1`.

If a full-width card is left out of that reset list, a child spanning 3 columns against a
single-column template makes CSS Grid **invent two implicit columns**. The symptom is a
homepage that renders as three skinny columns on a phone, with the body text squeezed into a
~107px sliver. **Any new full-width card you add must also be added to that reset list.**

### 3. The hero uses `hero_banner.jpg`, not `banner.jpg`

`images/banner.jpg` is a 2×3 photo collage that has **"UPPER HORTON SPORTS CLUB" printed into a
navy band across its middle**. Using it as the hero background puts the club name directly
behind the `<h1>`, which also says "Upper Horton Sports Club" — a ghosted double exposure.

`images/hero_banner.jpg` is that same collage with the band removed. The hero uses it.

`banner.jpg` itself is still used for **`og:image`** on purpose: on a social share the baked-in
wordmark is useful. Don't "fix" it.

If you replace the hero image, supply at least **1920px wide**. The current source is only
1080px and is upscaled on large displays, so it looks soft.

### 4. The `<h1>` is solid white, not a gradient

It used to be a `linear-gradient(#fff, #ccc)` clipped to text. Measured against the actual
photo, the darker end of that gradient fell to 2.27:1 — below even the 3:1 large-text threshold.
It is now solid `#fff` over a 55% dark overlay, which measures 6.12:1 worst case.

If you add a text gradient anywhere over a photo, measure it against the photo first.

### 5. The hours times must stay high-contrast

The `.time-label` colour was `--primary` (`#0047AB`) on a `#0a0e14` background — **2.29:1**,
effectively invisible outdoors on a phone. It is now `--amber` (`#FFB347`) at **10.86:1**.
Never put the opening hours back into the dark-blue accent token.

### 6. There is deliberately no registration form

`meatfest.html` previously had a meal-reservation form posting to a Google Apps Script endpoint
writing to a Google Sheet. It has been **removed entirely** — form, endpoint URL, JS handler and
the now-dead CSS. Meat Fest 2027 registration is to be rebuilt from scratch. Do not re-add the
old endpoint; if you add a form, read the "no-cors" note below first.

> If a form is ever rebuilt: the old one used `fetch(..., { mode: 'no-cors' })`, which returns an
> opaque response. The page could not tell success from failure and showed the success state
> regardless — including if the endpoint was dead. Any replacement needs a real response check.

### 7. `.md` files are not web pages

`meatfest.html` used to link a raw `.md` file, which a browser renders as unstyled plain text.
Rules are now served as `rules.html`. If you add a document, give it a styled HTML page; don't
link the Markdown directly.

### 8. Absolute URLs point at the custom domain

Every `canonical`, `og:url` and `og:image` must use `https://upperhortonsportsclub.com.au/…`,
never `hawkinle.github.io` and never a `github.com/…?raw=true` URL (social scrapers won't render
those). Also: each page's `og:url` must be its **own** URL, not the homepage's.

---

## Adding a new page

1. Copy the structure of `events.html` (it is the simplest full page) to keep the styling
   consistent — dark background, glass cards, orange accent, Space Grotesk headings.
2. Set `<title>`, `<meta name="description">`, `<link rel="canonical">`, the `og:*` block,
   `<link rel="icon" href="favicon.svg">` and `<meta name="theme-color">`.
3. Link it from somewhere people will actually see it — the homepage top bar, the hero buttons
   or the events page. A page nobody links to is a page nobody reads.
4. Add it to [`sitemap.xml`](../../sitemap.xml).
5. Include the address and phone in the footer, as the other pages do.

## Accessibility baseline this site holds

Worth keeping, because it was measured:

- Body text ≥ 4.5:1; large text ≥ 3:1 against its real backdrop (measure against the photo for
  anything over the hero, not against the page colour).
- Interactive targets ≥ 24×24 CSS px (WCAG 2.5.8 AA). Inline links inside a sentence are exempt.
- One `<h1>` per page, and heading levels in order.
- `prefers-reduced-motion` is respected — the background blobs stop animating.
