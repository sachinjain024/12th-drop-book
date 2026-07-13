# CLAUDE.md — 12th Drop Website

Guide for AI-assisted edits to the **12th Drop** book website. Read this before changing anything.

## What this is

A single static page (`index.html`) marketing the Hindi memoir *12th Drop · बारहवीं ड्रॉप*. All HTML and CSS live in that one file. There is no framework, no build step, and no JavaScript beyond a one-line footer-year script. Keep it that way — the whole point is that it opens by double-clicking and deploys by drag-and-drop.

## Layout of the file

`index.html` is ordered top-to-bottom as clearly-labelled comment blocks:
`NAV → HERO → ABOUT → VILLAGE STRIP (SVG) → REVIEWS → BUY → CONTACT → FOOTER`.
All colours are CSS custom properties in `:root` at the top of the `<style>` block.

## Flip-book reader (hero)

The hero centerpiece is an interactive page-flip book built on **StPageFlip** (`assets/vendor/page-flip.browser.js`, global `St.PageFlip`, bundled locally — do not swap to a CDN without reason). The logic lives in the last `<script>` in `index.html`.

- **Pages:** built in JS as DOM `.page` elements, then `flip.loadFromHTML(...)`. Order is `cover.png` (hard) → `pages/page-001.jpg … page-141.jpg` → a generated hard "back" page = **143** nodes total. `showCover:true` makes the first/last hard pages act as covers. Images use `loading="lazy"` so only visible pages download.
- **Page images:** `assets/pages/page-###.jpg`, pre-rendered from `assets/book- v1-greyscale.pdf` at 720px wide, **then tinted white→cream (`#f0e0c6`)** via a numpy multiply blend so pages match the site background (no jarring white). Full regenerate + tint commands are in `WEBSITE.md` (the technical docs; `README.md` is about the book itself). If the page count changes, update `var TOTAL` in the script.
- **3D open-book look:** the library ships no CSS, so the required base rules (`.stf__parent/.stf__block/.stf__item/...`) are inlined in the `<style>` — do not remove them or the flip breaks. The hardcover binding, edge thickness, and ground shadow are `box-shadow` layers on `.book-reader .stf__parent`; the centre gutter/fold is a `::after` gradient shown only when `.book-reader[data-mode="landscape"]` (JS sets `data-mode` from `flip.getOrientation()`), so it never appears in mobile single-page mode.
- **State:** closed by default (`#bookClosed` visible, `#bookReader`/`#bookToolbar` have `hidden`). "Open Book" reveals the reader **then** inits StPageFlip (the container must be visible/have width before init, or sizing breaks). Controls: `#prevPage`/`#nextPage` (also ← → keys), `#closeBook`, `#restartBook`.
- **Progress:** saved to `localStorage` key **`td_page`** (the current page index) on every `flip`/`changeState`. Opening resumes via `turnToPage(saved)`; a fresh open animates with `flipNext()` from the cover. (Separate from the language key `td_lang`.)
- **Responsive / mobile:** `size:'stretch'` + `usePortrait:true` → two-page spread on desktop/tablet, single page on mobile. Don't hard-code pixel widths; adjust via the `minWidth/maxWidth/minHeight/maxHeight` config.
- **"Open Book" is a CTA → stays English in both languages** (untagged), like the other CTAs.

## Language toggle (how i18n works)

The page is Hindi-by-default with a **हिंदी / EN** switch in the nav. Rules:

- `<body data-lang="hi">` sets the default; CSS `[data-lang="hi"] .en-only{display:none}` and `[data-lang="en"] .hi-only{display:none}` do the showing/hiding. A tiny inline script flips `data-lang`, updates `<html lang>`, toggles the buttons' `aria-pressed`, and remembers the choice in `localStorage` (`td_lang`).
- **Content is tagged, labels are not.** Any translatable content exists as a paired `.hi-only` + `.en-only` element. Navigation links and CTAs (About, Reviews, Contact, Read on Kindle, Read what it's about, Available Now, Write a Review) are deliberately left **untagged so they stay English in both modes** — do not wrap them in `.hi-only/.en-only`.
- **Always add content in pairs.** If you add a translatable line, add both a `.hi-only` and an `.en-only` version. Use the `.hindi` class (Tiro Devanagari font) on the Hindi one. The counts of `hi-only` and `en-only` should stay equal.
- Keep `data-lang="hi"` on `<body>` so the page reads correctly with JavaScript disabled.

## Design rules (match the book cover)

- **Palette is locked to the cover.** Edit only via the `:root` variables: `--paper` (cream), `--ink` (deep brown), `--terracotta` (roof/accent), `--gold`, and the `--sky-1..4` sunset gradient. Don't introduce off-theme colours.
- **Bilingual, Hindi-first for emotional text.** Hindi uses the `Tiro Devanagari Hindi` font via the `.hindi` class; English display uses `Marcellus`; body uses `Mukta`. The book's own voice is Hindi — keep Hindi primary in the hero and About blurb, with English as support.
- **Tone:** warm, humble, literary. No motivational-poster language, no exclamation-heavy marketing. This mirrors the book's rule: emotion comes from concrete detail, not editorializing.

## Common edits

- **Add / replace a review:** find the `REVIEW #1 / #2 / #3` comment blocks in the REVIEWS section. A real review is an `<article class="review">` with `.stars`, a `.quote` (add `hindi` class for Devanagari), and a `.reviewer` name. A "coming soon" slot is `<article class="review placeholder">`. To add a real review, convert a `placeholder` article into a full `review` article using the #1 card as the template.
- **Change the Kindle URL:** it is `https://www.amazon.in/dp/B0H7CKSGB2` and appears in **four** places — nav button, hero CTA, BUY section button, and footer. Update all four.
- **Change the contact email:** `sachinjain024@gmail.com` appears in the CONTACT `mailto:` link and the footer. Update both.
- **Swap the cover image:** replace `assets/cover.jpg` (keep the filename) or update the `<img src>` in the hero and the `og:image` meta tag.

## Cautions

- The **About blurb is the official back-cover text** (verbatim Hindi). Do not paraphrase the Hindi; if the back cover changes, copy the new text exactly. The English paragraphs beneath it are a translation for reach — those may be reworded.
- Keep everything in **one file** with the assets folder alongside; don't split CSS/JS into separate files.
- Author/credits: book by **Sunil Kumar Jain**; told by **Sachin & Rahul Jain**. Keep these accurate.
