---
title: "Flow: signup grant (idempotent)"
type: flow
updated: 2026-07-16
sources: [supabase/functions/submit-waitlist/index.ts]
---

# Flow: signup grant

New accounts receive their free quota in `submit-waitlist` via `grantSignupTokens()`. Idempotency via the **`free_grant_claimed`** flag — the "already registered" path also calls the grant; nothing is handed out twice.

If you touch the signup path: the grant hangs off submit-waitlist, **not** the Stripe webhook.

Pointer: `supabase/functions/submit-waitlist/index.ts`

## Related
[[billing-stripe]] · [[architecture-overview]]
