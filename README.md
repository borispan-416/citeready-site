# CiteReady — landing site

Static one-page trust site for **CiteReady** (Pancratic Enterprises), the AI-visibility
audit service for personal-injury law firms.

- `index.html` — the landing page.
- `teardown.html` — anonymized sample AI Visibility Teardown, linked from the Proof section.
- `styles.css` — shared styles. No build step, no JavaScript.

## Deploy

Static site — no build command, output directory is the repo root. Hosted on Cloudflare Pages
(free tier). Push to `main` to deploy.

## Before / after launch — TODOs

- **Contact email:** set to `boris.pan+citeready@gmail.com` (CTAs use a `mailto:` fallback). ✅
- **Stripe Payment Links:** every `$300 Teardown` button is marked with a `<!-- TODO:stripe -->`
  comment. Replace the `mailto:` href with the Stripe Payment Link URL once it exists.
