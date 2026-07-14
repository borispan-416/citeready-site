# CiteReady — landing site

Static one-page trust site for **CiteReady** (Pancratic Enterprises), the AI-visibility
audit service for personal-injury law firms.

- `index.html` — the landing page.
- `teardown.html` — anonymized sample AI Visibility Teardown, linked from the Proof section.
- `styles.css` — shared styles. No build step, no JavaScript.

## Deploy

Static site — no build step. Hosted on **Cloudflare Pages** (free tier), project `citeready`,
live at **https://citeready.pages.dev**. Deployed via **direct upload** with Wrangler (not
Git-connected), so pushing to GitHub does NOT auto-deploy — run the deploy command after changes:

```sh
# one-time auth (opens browser): wrangler login
mkdir -p .deploy && cp index.html teardown.html styles.css .deploy/
CLOUDFLARE_ACCOUNT_ID=e5364e31fb8a612de4177fecd38d657c \
  npx wrangler pages deploy .deploy --project-name citeready --branch main --commit-dirty=true
rm -rf .deploy
```

## Before / after launch — TODOs

- **Contact email:** set to `boris.pan+citeready@gmail.com` (CTAs use a `mailto:` fallback). ✅
- **Stripe Payment Links:** every `$300 Teardown` button is marked with a `<!-- TODO:stripe -->`
  comment. Replace the `mailto:` href with the Stripe Payment Link URL once it exists.
