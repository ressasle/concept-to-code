---
title: "Quirk: resend-webhook deployed on prod only, missing from repo"
type: quirk
status: open
updated: 2026-07-16
sources: [session finding 2026-07-16 (get_edge_function on prod)]
---

# Quirk: `resend-webhook` missing from repo

Prod runs an edge function **`resend-webhook`** (v11; receives Resend delivery events with Svix signature verification and writes status upgrades to `immoalpha_newsletter_sends`) — **it does not exist in `supabase/functions/`**. The repo only has `resend-inbound-forward`.

Consequences:
- `grep` in the repo won't find the mail-tracking code → fetch it from prod via MCP `get_edge_function` when needed.
- Any repo-based "deploy all functions" run would NOT include it; it can silently go stale.
- **TODO:** mirror the code back into the repo (then set this article to resolved).

The status table `immoalpha_newsletter_sends` tracks only `provider_id` + a status hierarchy (sent < delivered < opened < clicked; bounced/complained as terminal) — **no recipient**.

## Related
[[flow-signup-email-verification]] · [[quirk-newsletter-prod-only]]
