# CiteReady — landing site

Static one-page trust site for **CiteReady** (Pancratic Enterprises), the AI-visibility
audit service for personal-injury law firms.

- `index.html` — the landing page.
- `pricing.html` — full pricing: the $300 Teardown, three retainer tiers, Answer Page Pack.
- `teardown.html` — anonymized sample AI Visibility Teardown, linked from the Proof section.
- `styles.css` — shared styles. No build step, no JavaScript.

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

## Launch checklist

- **Contact email:** set to `boris.pan+citeready@gmail.com` (free-preview CTAs use `mailto:`). ✅
- **Stripe Payment Links:** live on every paid CTA — the $300 Teardown, all three retainer
  tiers, and the Answer Page Pack. ✅
