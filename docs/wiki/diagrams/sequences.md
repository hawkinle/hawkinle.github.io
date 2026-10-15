# Sequence Diagrams

## Workflow: Deploy

What happens between editing a file and a visitor seeing it. There is no build step — Pages
serves the repo files directly.

```mermaid
sequenceDiagram
    participant Author
    participant Local as Local repo
    participant GitHub
    participant Pages as "GitHub Pages"
    participant Visitor
    Author->>Local: edit HTML
    Author->>Local: verify preview on localhost:8096
    Author->>Local: git add -A && git commit
    Author->>GitHub: git push origin main (SSH)
    GitHub->>Pages: rebuild
    Note over Pages: roughly 1-2 minutes
    Visitor->>Pages: GET upperhortonsportsclub.com.au
    Pages-->>Visitor: index.html and assets
```

### Walkthrough

1. **Edit** — the author edits a page in [`../../../index.html`](../../../index.html) or a sibling page.
2. **Preview** — served locally per [../getting-started.md](../getting-started.md).
3. **Push** — `main` is the publish branch; the SSH remote is required.
4. **Rebuild** — Pages reprocesses the repo. The HTML has no front matter, so Jekyll passes the
   files through byte-for-byte.
5. **Serve** — `CNAME` binds the custom domain; `hawkinle.github.io` redirects to it.

A graphic change rides the same pipeline, with a local render step first — see the next workflow.

## Workflow: Rendering a social graphic

No server, no build tool. A hand-authored HTML template on disk is screenshotted by headless
Chrome into a PNG, which is then committed alongside the template and posted to Facebook.

```mermaid
sequenceDiagram
    participant Author
    participant Template as "social/*.html"
    participant Chrome as "headless Google Chrome"
    participant Fonts as "Google Fonts CDN"
    participant PNG as "social/*.png"
    participant GitHub
    Author->>Template: edit wording / prices
    Author->>Chrome: --screenshot --window-size=W,H --virtual-time-budget=8000
    Chrome->>Template: load (file:// URL)
    Template->>Fonts: request Space Grotesk / Outfit
    Fonts-->>Template: webfont files
    Note over Chrome: budget keeps the shot waiting for the fonts
    Chrome->>PNG: write at exactly WxH
    Author->>PNG: eyeball the image
    Author->>GitHub: commit template + PNG, push
```

### Walkthrough

1. **Edit the template** — one of the files in [`../../../social/`](../../../social). Templates are
   `noindex` and sized via `html, body { width; height; }`.
2. **Screenshot** — `--window-size` must equal the CSS size, `--force-device-scale-factor=1` keeps
   it 1:1, and `--virtual-time-budget=8000` is what stops you getting a fallback font.
3. **Review the PNG** — it is going out as an image; nobody will re-read the HTML afterwards.
4. **Commit both** — the template *and* the regenerated PNG, in one commit.

Full flags and pitfalls: [../social-graphics.md](../social-graphics.md).

## Workflow: the "Tonight at the Club" status

The only stateful code on the site. Runs once, on homepage load, in
[`../../../index.html`](../../../index.html) at the end of `<body>`.

```mermaid
sequenceDiagram
    participant Browser
    participant Script as "Inline IIFE"
    participant Intl as "Intl.DateTimeFormat"
    participant Card as "tonight-card DOM"
    Browser->>Script: DOM ready, page load
    Script->>Intl: formatToParts(now, Australia/Sydney)
    Intl-->>Script: weekday, hour, minute, y/m/d
    Script->>Script: compute NSW DST (first Sun Oct to first Sun Apr)
    Script->>Script: look up opening time for that weekday
    alt Monday or Tuesday
        Script->>Card: "Closed today"
    else before opening time
        Script->>Card: "Opens today/tonight at X"
    else open
        Script->>Card: "Open now" plus the patronage note
    end
    Script->>Card: add .today to the matching hours row
```

### Walkthrough

1. **Read the clock in club time** — `Intl.DateTimeFormat` with `timeZone: 'Australia/Sydney'`,
   so a visitor in another timezone still sees the club's real state rather than their own.
2. **DST test** — compares the club's local date against the first Sunday in October and the
   first Sunday in April.
3. **Schedule lookup** — a small in-file table keyed by weekday, holding an **opening** time
   only. Deliberately no closing time — see [../content-editing.md](../content-editing.md).
4. **Write the status** — headline and sub-line text are set directly. The sub-line always ends
   with the patronage note, because the club never has a defined close.
5. **Highlight today** — `.today` is added to the matching row of the hours list.
