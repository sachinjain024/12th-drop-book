# 12th Drop — Website

A single-page website for the book **12th Drop · बारहवीं ड्रॉप — _A Story of Every Common Man_** by Sunil Kumar Jain.

It is a static, self-contained page — no build step and no server. The only third-party code is a small page-flip library bundled locally in `assets/vendor/` (no CDN needed). Just open the file.

## Files

| Path                        | What it is                                                                 |
| --------------------------- | -------------------------------------------------------------------------- |
| `index.html`                | The entire website (HTML + CSS + JS in one file).                          |
| `assets/cover.png`          | The book cover — shown as the closed book in the hero and the flip cover.  |
| `assets/cover.jpg`          | Cover used for the social-share (Open Graph) preview.                      |
| `assets/pages/page-###.jpg` | The 141 book pages, pre-rendered from the PDF for the flip-book reader.     |
| `assets/vendor/page-flip.browser.js` | The StPageFlip library (bundled locally, v2.0.7).                 |
| `assets/book- v1-greyscale.pdf` | Source PDF the page images were rendered from.                         |

## How to view / publish

- **View locally:** double-click `index.html` (or open it in any browser).
- **Publish free:** drag the whole `website/` folder into [Netlify Drop](https://app.netlify.com/drop), or push it to a GitHub repo and enable **GitHub Pages**. Either gives you a live URL in minutes.

## Language toggle

The header has a **हिंदी / EN** switch. The page loads in **Hindi by default**; readers can switch to English. Only the *content* changes language — the navigation labels and calls-to-action (**About the Book, Reviews, Contact, Read on Kindle, Read what it's about**) stay in English in both modes, by design. The choice is remembered on the reader's device, and the page still shows Hindi correctly even if JavaScript is off.

## The flip-book reader (hero)

The hero is an interactive **flip-book**. It starts as a closed book (the cover) with an **Open Book** button. On open it turns the pages with a realistic flip animation; readers move with the on-screen **‹ ›** arrows or the keyboard arrow keys. It works on desktop, tablet, and mobile (mobile shows one page at a time in portrait).

**Reading progress is saved** to the browser's `localStorage` (key `td_page`). When a reader returns, opening the book jumps straight back to the page they left off on. **Close** collapses back to the cover; **Restart** goes to page 1.

The pages come from `assets/pages/` — 141 JPEGs pre-rendered from `assets/book- v1-greyscale.pdf`, then tinted from white to the site's cream paper tone (`#f0e0c6`) so they blend with the background. To regenerate them after updating the PDF:

```
# 1) render pages
pdftoppm -jpeg -jpegopt quality=78 -scale-to-x 720 -scale-to-y -1 "assets/book- v1-greyscale.pdf" assets/pages/page

# 2) tint white -> cream (multiply blend, keeps text & photos)
python3 - <<'PY'
import numpy as np, glob
from PIL import Image
paper=(0xf0,0xe0,0xc6)
for f in sorted(glob.glob('assets/pages/page-*.jpg')):
    g=np.asarray(Image.open(f).convert('L')).astype(np.uint16)
    out=np.empty(g.shape+(3,),np.uint8)
    for i,ch in enumerate(paper): out[...,i]=(g*ch//255).astype(np.uint8)
    Image.fromarray(out,'RGB').save(f,quality=84,optimize=True)
PY
```

If the page count changes, update `var TOTAL = 141` in the flip-book script inside `index.html`.

## Sections

1. **Hero** — title, toggled subtitle, and the interactive flip-book reader (see above).
2. **About the Book** — the official back-cover blurb (Hindi) with an English translation shown per language, and four thematic highlights (₹60 schooling · 2 dropped years · 23 interviews · 36 chapters).
3. **Reviews** — one review card (Hindi + English quote) plus two "coming soon" placeholder cards.
4. **Footer** — credits and the contact email `sachinjain024@gmail.com` (Kindle link also here).

## Theme

Colours and mood are pulled straight from the book cover: cream paper, an ochre sunset gradient, terracotta roof accents, and deep-brown ink. Fonts: *Marcellus* (English display), *Tiro Devanagari Hindi* (Hindi, the book's own font), and *Mukta* (body), loaded from Google Fonts.

## Things you'll want to edit later

Everything editable is clearly commented in `index.html`. See `CLAUDE.md` for exact instructions. The two most common edits:

- **Add a real review** — replace the `REVIEW #1` card, or swap a `placeholder` card for a real one.
- **Change the Kindle link** — the URL `https://www.amazon.in/dp/B0H7CKSGB2` appears in the nav and footer.
- **Update the book pages** — re-render `assets/pages/` from the PDF (command above) and adjust `TOTAL` if the page count changed.
