---
title: "Quirk: newsletter content exists only on prod"
type: quirk
updated: 2026-07-16
sources: [memory newsletter-content-from-prod]
---

# Quirk: newsletter content prod-only

Newsletters (archive content) live **exclusively in the prod DB**. The frontend reads them **cross-project** via the `newsletterService` — even when the app otherwise runs against dev. The archive page is DB-driven; the guides are static.

Sending works without local keys via edge functions: `newsletter-test-send` ({to, subject, html}) and `newsletter-broadcast` (header `x-broadcast-secret`, supports `dry_run`).

Symptom: "newsletters missing on dev" is not a bug — they were never there.

## Related
[[architecture-overview]] · [[supabase-dev-prod]]
