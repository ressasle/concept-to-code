---
title: Architecture Overview (Hub)
type: hub
updated: 2026-07-16
sources: [CLAUDE.md, repo structure]
---

# Architecture Overview

**Core:** ImmoAlpha is a React/Vite SPA for real-estate investment analysis (exposé analysis, fix&flip vs. buy&hold, hot-zone map). **The frontend lives in `Tools/hot-zone-map/` — there is NO root `src/`.** Backend = Supabase (Postgres + ~40 edge functions in root `supabase/functions/`). Production runs on a Hostinger VPS, domain immoalpha.app.

## Function groups (pointers)
`pipeline-*` exposé analysis (→ [[pipeline-expose-analysis]]) · `calculate-*` financial-metric calculators · `reno-*` renovation analysis (audio/vision/timeline) · `newsletter-*` (→ [[quirk-newsletter-prod-only]]) · billing trio (→ [[billing-stripe]]) · `submit-waitlist`/`verify-email` signup + verification (→ [[flow-signup-email-verification]])

## Atoms in this domain
- [[decision-direct-to-main-prod]] — team convention: everything straight to main + prod
- [[quirk-gh-actions-deploy-broken]] — deploy ONLY via `deploy/deploy-vps.sh`, Actions dead
- [[flow-signup-email-verification]] — signup via admin.createUser + own Resend token flow
- [[quirk-resend-webhook-not-in-repo]] — mail-tracking function exists only on prod
- [[flow-signup-token-grant]] — free quota hangs off signup, not the webhook

## Debugging entry points
Wrong environment → [[supabase-dev-prod]]. "Email doesn't arrive" → [[flow-signup-email-verification]]. Nothing deploys → [[quirk-gh-actions-deploy-broken]].

## Related
[[supabase-dev-prod]] · [[billing-stripe]] · [[pipeline-expose-analysis]]
