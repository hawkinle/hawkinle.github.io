# Upper Horton Sports Club — Website Wiki

Reference documentation for the club website at **https://upperhortonsportsclub.com.au**.

This is a hand-written static site — four HTML pages, no build step, no framework. This wiki
covers what each page is, how it is hosted and deployed, how to edit the content safely, and
the specific traps that have already bitten this codebase.

---

## What this is

A small community-club site for the Upper Horton Sports Club (bar, meals, events) in the
Horton Valley, NSW. Its job is, in priority order:

1. **Tell a local whether the club is open tonight** and let them ring it in one tap.
2. **Say where it is** — address and directions.
3. **Show what's on** — weekly meals/raffle, annual events, and the Meat Fest archive.

Everything on the homepage is ordered around that. If you add a section, put it below those
three jobs, not above them.

---

## Key facts

| | |
|---|---|
| Live URL | https://upperhortonsportsclub.com.au |
| Hosting | GitHub Pages |
| Repo | `git@github.com:hawkinle/hawkinle.github.io.git` (branch `main`) |
| Custom domain | declared in `CNAME` (`upperhortonsportsclub.com.au`) |
| Build step | **none** — the HTML files are served as-is |
| Pages | `index.html`, `events.html`, `meatfest.html`, `rules.html` |
| Address | 22 Cobbadah Street, Upper Horton NSW 2347 |
| Phone | (02) 6782 7249 |
| Facebook | https://www.facebook.com/UpperHortonSportsClub |
| Messenger | https://m.me/UpperHortonSportsClub |
| Public email | none — phone and Messenger only |

---

## Page map

| File | Purpose |
|---|---|
| [`index.html`](../../index.html) | Homepage. Next-big-day promo, live "tonight" status, hours, what's on, address, contact. |
| [`calcutta.html`](../../calcutta.html) | Melbourne Cup Calcutta — Sunday 1 November 2026. Food menu, syndicates, how the day runs. |
| [`events.html`](../../events.html) | What's On — weekly rhythm, upcoming events, annual events, Meat Fest archive. |
| [`meatfest.html`](../../meatfest.html) | Meat Fest 2026 **archive**. Held 26 Sep 2026, sold out. No registration form. |
| [`rules.html`](../../rules.html) | Competition rules, rendered from [`australian_barbecue_rules.md`](../../australian_barbecue_rules.md). |
| [`australian_barbecue_rules.md`](../../australian_barbecue_rules.md) | **Source of truth** for the rules. `rules.html` is a rendered copy. |
| [`favicon.svg`](../../favicon.svg) | "UH" monogram favicon. |
| [`robots.txt`](../../robots.txt) / [`sitemap.xml`](../../sitemap.xml) | Search-engine plumbing. |
| [`images/`](../../images) | Banner art. See the hero warning in [content-editing.md](content-editing.md). |

---

## Reading order

- **[architecture.md](architecture.md)** — how hosting, the domain, the deployed files and the
  homepage status script fit together.
- **[content-editing.md](content-editing.md)** — how to change copy, hours, events and prices,
  plus the invariants you must not break.
- **[getting-started.md](getting-started.md)** — preview locally, deploy, and verify.
- **[diagrams/sequences.md](diagrams/sequences.md)** — deploy flow and the "tonight" status logic.

---

## Opening hours (the one thing people get wrong)

The club has **fixed opening times but no fixed closing time**.

- Opens Wednesday to Sunday. Monday and Tuesday closed.
- Opening is **5:00 PM**, or **5:30 PM** during daylight saving (NSW: first Sunday in October
  to the first Sunday in April). Sunday opens **12:00 PM**.
- **Closing depends on patronage** — the club stays open while there's a crowd. There is no
  closing time to publish.

> Do not put a hard closing time on this site. The Gwydir Shire council listing
> (`visitgwydir.com.au`) still shows "Thursday 5:00pm to 10.30pm" and "Sunday 12:00pm to 9:00pm"
> — those are wrong; the club has no defined close. If you're asked about the discrepancy,
> the club's position is that closing is patronage-dependent.
