# Lone Star Barbeque — website

Static homepage for Lone Star Barbeque (13 Northwood Road, Ramsgate, Kent CT12 6RR). Plain HTML/CSS/JS, no build step, no framework — this repo *is* the deploy output.

## Structure

```
index.html          the whole homepage (styles + markup + behaviour)
404.html            branded not-found page (fixes the soft-404 issue, see below)
robots.txt          allows all crawling, points at sitemap.xml
sitemap.xml         single-URL sitemap for Search Console submission
site.webmanifest    icons + theme colour for "add to home screen"
favicon.ico / favicon-*.png / apple-touch-icon.png   real favicon, built from the Texas star mark
fonts/              self-hosted Bevan + Noto Sans (woff2)
img/                photography, logo lockups, Just Eat badge, ticket-fringe pattern, menu.pdf, og-image.jpg
_headers            Cloudflare Pages cache-control rules
```

## SEO notes (September 2026 audit)

Fixed in this pass: real favicon (was a blank `data:,` placeholder), canonical tag, `robots.txt` + `sitemap.xml` (neither existed), a proper `404.html` (Cloudflare Pages was falling back to the homepage with a 200 status for every unmatched URL, a "soft 404" that confuses crawlers and Search Console), Open Graph + Twitter Card tags with a real 1200×630 share image, `schema.org` Restaurant structured data (name, address, phone, price range, cuisine, menu link), a keyword-bearing `<title>`, and explicit `width`/`height` on the two images that weren't inside an aspect-ratio-boxed container (avoids layout shift).

Deliberately not added:

- **`aggregateRating` / review schema** — Google's structured-data guidelines don't allow self-served review markup on your own site unless it's pulling genuine, attributable third-party reviews. The 4.9★ marquee is fine as content; it shouldn't be marked up as machine-readable review schema without a real review-platform integration behind it.
- **`openingHoursSpecification`** — the exact opening time was never confirmed (see the "Friday–Sunday, until 9:30pm" caveat), so hours schema was left out rather than guessed. Add it once the opening time is confirmed, it's a genuine local-search ranking factor.
- **`sameAs` social links** — still pointing at `#` in the footer; add the real Instagram/Facebook/TikTok URLs there and they can go into the schema too.

Still open, worth doing when there's time: this is a single page, so there's no dedicated indexable URL for "menu", "about" or "find us" search intent, splitting those into real pages (or at least real subpages behind the current anchors) would help beyond what one homepage can rank for. Image files could also be served as WebP/AVIF with a build step for a Core Web Vitals bump, and the two Just Eat/social placeholders noted below are still outstanding.

## Deploying on Cloudflare Pages

1. In the Cloudflare dashboard: **Workers & Pages → Create → Pages → Connect to Git**, pick this repo.
2. Build settings:
   - **Framework preset:** None
   - **Build command:** *(leave blank)*
   - **Build output directory:** `/`
3. Deploy. Every push to `main` redeploys automatically.
4. Add the custom domain under **Custom domains** once it's live and the client's happy with it.

There's nothing to configure beyond that — no environment variables, no functions, no database.

## Known placeholders to fill in before launch

- **Just Eat link** — every "Order" button points at `https://www.just-eat.co.uk/`. Swap for the restaurant's actual Just Eat listing URL wherever it appears in `index.html` (search for `just-eat.co.uk`).
- **Social links** — Instagram / Facebook / TikTok icons in the footer point at `#`. Add the real handles when confirmed.
- **Hero photography** — no pit/fire photography was supplied, so the hero currently uses a platter shot (`img/hero.jpg`). Swap in pit/flame photography if the client shoots some.

## Live reviews (FeedSpring)

The reviews section on the homepage (`.proof`) is wired up for FeedSpring's Google Reviews "Attributes" (headless) integration, using the real feed ID:

- `<script src="https://scripts.feedspring.com/google-reviews-attrs.js" async defer></script>` is loaded in `<head>`.
- The section container carries `feedspring="google_0Az0aYJ6hQamjl3oqk1Yp"` and `feed-options="render:dynamic|limit:6"`.
- A single `<article feedspring="post" class="review-card">` acts as the template FeedSpring clones once per review it returns; `feed-field` attributes inside it (`review`, `name`, `star`/`star-inactive`) get filled in per review, and `feed-field="average-rating"` / `feed-field="total"` at the container level show the aggregate score and review count.
- Star glyphs use plain `★`/`☆` text characters rather than FeedSpring's default SVG stars, to match the brand system's rule that the star glyph is never an SVG or image.
- Until the script loads (or if it's ever blocked), the static fallback text shows instead — it never fabricates a fake review, just says reviews are loading.

This covers **Google reviews only**. Facebook Page reviews aren't available through FeedSpring, so that's left off for now.

There's also an Instagram carousel ("Follow the smoke", just above the footer), using FeedSpring's Instagram Attributes integration:

- `<script src="https://scripts.feedspring.com/instagram-attrs.js" async defer></script>` is loaded in `<head>`.
- The container carries `feedspring="inst_ItTv0xubV8xcaxzzKTNTu"` and `feed-options="render:dynamic|limit:10"`.
- A single `<a feedspring="post" class="insta-card" feed-field="link">` is the template FeedSpring clones per post — `feed-field="link"` sets the card's `href` to the post's Instagram permalink, and the `<img feed-field="img">` inside it sets the image `src`.
- It's a plain CSS horizontal scroller (`scroll-snap`, no JS slider library), so it works the same on touch and with a mouse/trackpad without any extra dependency, in keeping with the rest of the site having no build step.

## Local preview

No build tooling needed — open `index.html` directly in a browser, or serve the folder:

```
python3 -m http.server 8080
```
