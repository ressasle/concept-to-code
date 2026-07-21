---
title: "Flow: signup & custom email verification (Resend, not GoTrue)"
type: flow
updated: 2026-07-16
sources: [supabase/functions/submit-waitlist/index.ts, supabase/functions/verify-email/index.ts]
---

# Flow: signup & email verification

Signup goes through the edge function `submit-waitlist`, which creates the user via **`admin.createUser({email_confirm: true})`** — **intentional**: login works immediately. The real email verification is a **custom token flow**, independent of GoTrue:

```
submit-waitlist → set profiles.email_verification_token → email via Resend API
                  (from: guide@immoalpha.app)
user clicks → https://immoalpha.app/verify-email?token=… → verify-email function
              → profiles.email_verified_at set, token nulled
```

`verify-email` has three modes: confirm token (no JWT) · `action: 'resend'` (JWT required) · `action: 'change-email'` (JWT required, typo escape hatch for unverified accounts, since f595d30).

**Consequences:**
- GoTrue fields (`email_confirmed_at`, `confirmation_sent_at`) say NOTHING about the real verification state — that lives in `profiles.email_verified_at`.
- "Email doesn't arrive" is debugged in Resend / `verify-email` logs, not Supabase SMTP.
- Verification only gates the **analysis**, not login.
- `change-email` fails with a duplicate-key error when the target address already belongs to an account (auth-side unique constraint) — real error message surfaced since 8beeccf.

## Related
[[architecture-overview]] · [[quirk-resend-webhook-not-in-repo]] · [[flow-signup-token-grant]]
