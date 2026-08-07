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

A direct upload replaces the whole site, so `.deploy/` must contain every file the site
serves. The copy below is asset-agnostic — it takes everything except dotfiles and this
README, so new pages, images, `robots.txt`, and stylesheets are all picked up without
touching this command. Never narrow it to a list of filenames: anything left out of the
copy 404s on the live site.

The steps are `&&`-chained and guarded on `.deploy/` being non-empty, so a failed copy
aborts instead of publishing an empty directory over a live site that takes payments.

```sh
# one-time auth (opens browser): wrangler login
mkdir -p .deploy \
  && rsync -a --exclude='.*' --exclude='README.md' ./ .deploy/ \
  && [ -n "$(ls -A .deploy)" ] \
  && CLOUDFLARE_ACCOUNT_ID=e5364e31fb8a612de4177fecd38d657c \
     npx wrangler pages deploy .deploy --project-name citeready --branch main --commit-dirty=true
rm -rf .deploy
```

`rm -rf .deploy` is deliberately left unchained so the staging directory is cleaned up even
when the deploy fails — a stale `.deploy/` would otherwise resurrect deleted pages on the
next run.

## Analytics

**Cloudflare Web Analytics**, added manually as a `<script>` at the bottom of each page.
The site token is public by design (it ships in the page source), so it is committed.

There is no build step, so the token is duplicated in all three pages. **If it is ever
rotated — or a second Web Analytics site is created for a custom domain — update every
page.** Missing one is silent: the dashboard keeps showing traffic from the others, so it
reads as a drop in interest rather than broken instrumentation. Check with:

```sh
grep -c cloudflareinsights *.html   # expect 1 per page
```

The manual tags predate the pages having a real `<body>`. Now that they have one, the Pages
*Metrics → Web Analytics* toggle would also work — if you switch to it, delete these script
tags. Do not do both: the toggle injects its own beacon, and leaving these tags in place
would double-count every pageview.

## Document structure

All pages have `<!DOCTYPE html>`, `<html lang="en">`, `<head>`, and `<body>`, and render in
standards mode (`document.compatMode === "CSS1Compat"`). Keep the DOCTYPE — removing it, or
replacing it with a legacy/malformed one, drops the page back to quirks mode. Comments and
whitespace *before* the DOCTYPE are harmless in modern browsers (verified), so a header
comment above it is fine; putting it first is convention, not a requirement.

This was fixed after the pages shipped without it. Page-height change caused by the switch,
measured at 13 viewport widths from 320px to 1280px, including both sides of the 760px and
640px breakpoints:

| page | height change | what changed |
|---|---|---|
| index.html | 0px at every width (+1px at 320) | nothing; a few elements moved 1px (rounding) |
| pricing.html | +6px at every width | hero block 6px taller, uniform shift below |
| teardown.html | +75px wide → +213px at 375 | the scorecard table (see below) |

Deltas are what to re-check; absolute page heights are not reproducible across measurement
setups (device emulation and frame size shift them by ~100px without anything changing).

Only the teardown table changed visibly. In quirks mode a table does not inherit
`line-height` from the body, so its cells fell back to `normal` (~18px) and ignored the
stylesheet's 25.08px. Standards mode inherits correctly, so the rows are taller and the table
grew 267px → 337px. That is the CSS finally applying as written, not a regression.

Separately — and **not** caused by the standards-mode switch — that table overflows the
viewport below about 370px. Its min-content width is 346px, and `.doc table` in `styles.css`
sets `width: 100%` with no `overflow-x` handling, so at a 320px viewport the page scrolls
sideways by 50px and the SCORE / NOTES columns are clipped. Measured identically before and
after the DOCTYPE change. Tracked in the checklist below.

## Launch checklist

- **Contact email:** set to `boris.pan+citeready@gmail.com` (free-preview CTAs use `mailto:`). ✅
- **Stripe Payment Links:** live on every paid CTA — the $300 Teardown, all three retainer
  tiers, and the Answer Page Pack. ✅
- **Analytics:** Cloudflare Web Analytics on all three pages. ✅
- **DOCTYPE / document structure:** added; pages render in standards mode. ✅
- **Teardown table on narrow screens:** overflows the viewport below ~370px (pre-dates the
  DOCTYPE fix). Needs `overflow-x: auto` on a wrapper, or a stacked layout under 480px. ⬜
- **Custom domain:** still on `citeready.pages.dev`. ⬜
- **SEO / AI-crawler files:** no `robots.txt`, `sitemap.xml`, JSON-LD, or `llms.txt`. ⬜
- **OG tags / favicon:** `index.html` lacks `og:image` and `og:url`; the other two pages have
  no OG tags at all; no favicon or canonical URLs anywhere. ⬜
