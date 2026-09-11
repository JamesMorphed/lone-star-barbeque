# Lone Star Barbeque — website

Static homepage for Lone Star Barbeque (13 Northwood Road, Ramsgate, Kent CT12 6RR). Plain HTML/CSS/JS, no build step, no framework — this repo *is* the deploy output.

## Structure

```
index.html      the whole page (styles + markup + behaviour)
fonts/          self-hosted Bevan + Noto Sans (woff2)
img/            photography, logo lockups, Just Eat badge, ticket-fringe pattern, menu.pdf
_headers        Cloudflare Pages cache-control rules for /fonts and /img
```

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

## Local preview

No build tooling needed — open `index.html` directly in a browser, or serve the folder:

```
python3 -m http.server 8080
```
