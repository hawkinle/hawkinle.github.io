# Social Media Graphics

The club's Facebook posts are not designed in Facebook — they are **HTML templates that are
screenshotted to PNG**. Every graphic in `social/` is a `.html` file rendered headlessly at a
fixed pixel size.

This exists because it is the only way to get the club's real typefaces (`Space Grotesk`,
`Outfit`) onto a post without a design tool, and because it keeps the wording under version
control with the rest of the site.

## The files

| Template | Rendered PNG | Size | Use |
|---|---|---|---|
| [`social/calcutta-square.html`](../../social/calcutta-square.html) | `social/calcutta-post-square.png` | 1080×1080 | Square feed post — the event announcement |
| [`social/calcutta-menu.html`](../../social/calcutta-menu.html) | `social/calcutta-post-menu.png` | 1080×1080 | Square feed post — the food and prices |
| [`social/calcutta-landscape.html`](../../social/calcutta-landscape.html) | `social/calcutta-landscape.png` | 1200×630 | Landscape — link posts and page headers |

Only the **PNGs** are posted. The HTML files are the source of truth for the artwork.

## How a template is built

Each file is a self-contained page, deliberately unlike the main site's pages:

- `html, body` are set to the **exact pixel size of the output** with `overflow: hidden`. The
  screenshot is the size of the viewport, so a stray pixel of overflow becomes a scrollbar
  baked into the PNG.
- The page is `noindex` and is **not** in `sitemap.xml`. It is technically reachable at
  `https://upperhortonsportsclub.com.au/social/…` because GitHub Pages serves the whole repo,
  so never put anything in there that shouldn't be public.
- Fonts come from Google Fonts over the network. **The render depends on that fetch succeeding** —
  see the `--virtual-time-budget` note below.
- The palette is the site's, plus the Calcutta gold: `#0a0e14` background, `#E8C46A` gold,
  `#FF4500` orange, `#f8f9fa` white, `#c7cdd3` dim text.

## Rendering a PNG

From the `social/` directory:

```bash
cd /home/leith/AI/Meat_fest/club_site/social

# 1080x1080 square — one per square template
google-chrome --headless=new --no-sandbox --disable-gpu --hide-scrollbars \
  --force-device-scale-factor=1 --window-size=1080,1080 --virtual-time-budget=8000 \
  --screenshot=calcutta-post-square.png "file://$PWD/calcutta-square.html"

google-chrome --headless=new --no-sandbox --disable-gpu --hide-scrollbars \
  --force-device-scale-factor=1 --window-size=1080,1080 --virtual-time-budget=8000 \
  --screenshot=calcutta-post-menu.png "file://$PWD/calcutta-menu.html"

# 1200x630 landscape — note the different window size
google-chrome --headless=new --no-sandbox --disable-gpu --hide-scrollbars \
  --force-device-scale-factor=1 --window-size=1200,630 --virtual-time-budget=8000 \
  --screenshot=calcutta-landscape.png "file://$PWD/calcutta-landscape.html"
```

### The three flags that matter

- **`--window-size=W,H` must match the template's `html, body` size.** Get this wrong and the
  layout reflows — a 1080×1080 template rendered at 1200×630 pushes the text off the frame.
- **`--virtual-time-budget=8000` gives Google Fonts time to load.** Without it Chrome screenshots
  as soon as the DOM is ready and you get **Arial instead of Space Grotesk**, which is obvious and
  ugly on a heading set at 118px.
- **`--force-device-scale-factor=1` keeps the output at the declared pixel size.** Drop it on a
  HiDPI machine and you get a 2× file (2160×2160), which Facebook re-compresses badly.

`file://$PWD/...` is correct here and is the one place on this project a `file://` load is right —
the template has no external CSS or JS to resolve, only the remote font.

## Changing the wording

1. Edit the `.html` in `social/`.
2. Re-render the matching PNG with the command above.
3. Preview the PNG before posting — it is a graphic, it will be read as an image.

Copy that must stay in sync with the live site:

- The **food and prices** on the menu graphic must match the "The food" section of
  [`calcutta.html`](../../calcutta.html). The graphic has no link back, so a wrong price on a
  posted image lives forever.
- The **date, times and address** on all three templates must match `calcutta.html`'s hero.

## Adding a new graphic

1. Copy the closest existing template — `calcutta-square.html` for a square post.
2. Set `html, body` to the new size and update the comment at the top of the file naming the
   output PNG.
3. Keep the `noindex` meta tag.
4. Render it, and add a row to the table in this page **and** to the "Social media images" table
   in the repo [`README.md`](../../README.md).
