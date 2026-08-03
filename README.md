# CiteReady — landing site

Static one-page trust site for **CiteReady** (Pancratic Enterprises), the AI-visibility
audit service for personal-injury law firms.

- `index.html` — the landing page.
- `pricing.html` — full pricing: the $300 Teardown, three retainer tiers, Answer Page Pack.
- `teardown.html` — anonymized sample AI Visibility Teardown, linked from the Proof section.
- `styles.css` — shared styles. No build step; the only JavaScript is the analytics beacon.

## Deploy

Static site — no build step. Hosted on **Cloudflare Pages** (free tier), project `citeready`,
live at **https://citeready.pages.dev**. Deployed via **direct upload** with Wrangler (not
Git-connected), so pushing to GitHub does NOT auto-deploy — run the deploy command after changes:

A direct upload replaces the whole site, so `.deploy/` must contain every page — the globs
below pick up new pages automatically. Don't list files by name here; a page left out of the
copy goes 404 on the live site.

```sh
# one-time auth (opens browser): wrangler login
mkdir -p .deploy && cp *.html styles.css .deploy/
CLOUDFLARE_ACCOUNT_ID=e5364e31fb8a612de4177fecd38d657c \
  npx wrangler pages deploy .deploy --project-name citeready --branch main --commit-dirty=true
rm -rf .deploy
```

## Analytics

**Cloudflare Web Analytics**, added manually as a `<script>` at the bottom of each page.
The site token is public by design (it ships in the page source), so it is committed.

It is added by hand rather than with the Pages *Metrics → Web Analytics* toggle on purpose —
see below. If the pages ever get a real `<body>`, switch to the toggle and delete the script
tags.

## Known issue — no HTML document structure

None of the pages have `<!DOCTYPE html>`, `<html>`, `<head>`, or `<body>`. They begin at
`<meta charset="utf-8">` and end at `</footer>`. Two consequences:

- **The site renders in quirks mode** (`document.compatMode === "BackCompat"`), so the CSS is
  currently tuned against legacy box-model and line-height rules.
- **Cloudflare Pages cannot auto-inject the analytics beacon**, because it inserts before
  `</body>` and there is none — hence the manual script tags above.

Adding a DOCTYPE is the correct fix, but it flips all three pages into standards mode and
**can shift the layout** (spacing and line-height most likely). Do it as its own change, with
before/after screenshots at desktop and mobile widths — not as a drive-by edit.

## Launch checklist

- **Contact email:** set to `boris.pan+citeready@gmail.com` (free-preview CTAs use `mailto:`). ✅
- **Stripe Payment Links:** live on every paid CTA — the $300 Teardown, all three retainer
  tiers, and the Answer Page Pack. ✅
- **Analytics:** Cloudflare Web Analytics on all three pages. ✅
- **DOCTYPE / document structure:** still missing — see *Known issue* above. ⬜
