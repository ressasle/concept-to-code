# ImmoAlpha Wiki — Index

> Living knowledge base (Karpathy pattern). **Atoms** (one unit of knowledge = one file) + **hubs** (map of content per domain). Knowledge ABOUT the system + pointers — never code copies. Rule for every line: *"Would an agent guess wrong without this?"* Types: `hub` · `decision` · `quirk` · `issue` · `flow` · `idea` · `reference` (later: `fn` · `table`).

## Hubs
| Article | One line |
|---|---|
| [[architecture-overview]] | Frontend in `Tools/hot-zone-map`, ~40 edge functions, VPS deploy |
| [[supabase-dev-prod]] | dev `sampdq…` vs. prod `adygxb…` — environment bugs & rules |
| [[billing-stripe]] | Subscription + token quotas; Stripe trio on prod, mock on dev |
| [[pipeline-expose-analysis]] | Exposé pipeline ingest→parse→location/substance/optics→finalize |

## Atoms
| Article | Type | One line |
|---|---|---|
| [[decision-direct-to-main-prod]] | decision | Everything straight to main + prod, no asking (team convention 2026-07-16) |
| [[decision-migrations-via-mcp]] | decision | Prod migrations via MCP `apply_migration`, never `db push` (May drift) |
| [[decision-env-development-versioned]] | decision | `.env.development` committed — npm run dev hits dev everywhere |
| [[decision-customer-update-auto]] | decision | Stripe Tax: `customer_update: auto` in checkout is mandatory |
| [[decision-billing-catalog-mirror]] | decision | `billingCatalog.ts` mirrors `pricing.ts` — always change both |
| [[decision-bh-flip-split]] | decision | Keep PropertyDetails BH/flip views separate, do not re-merge |
| [[quirk-cli-env-targets]] | quirk | Which tool points at which Supabase project |
| [[quirk-prod-jwt-hs256]] | quirk | Prod still HS256 → getUser fallback in verify.ts is mandatory |
| [[quirk-newsletter-prod-only]] | quirk | Newsletter content prod-only; frontend reads cross-project |
| [[quirk-billing-mock-dev]] | quirk | Dev mocks Stripe entirely (`BILLING_MOCK=true`) |
| [[quirk-gh-actions-deploy-broken]] | quirk ⚠ | Deploy only via `deploy-vps.sh`; GH Actions dead since May |
| [[quirk-resend-webhook-not-in-repo]] | quirk ⚠ | Mail-tracking function deployed on prod only, missing from repo |
| [[flow-signup-email-verification]] | flow | Signup = admin.createUser + custom Resend token flow (not GoTrue) |
| [[flow-signup-token-grant]] | flow | Free grant idempotent via `free_grant_claimed` in submit-waitlist |
| [[issue-token-grant-dedup]] | issue ⚠ | Dedup fix (objective-kilby) waiting for prod deploy |
| [[issue-billing-cron-missing]] | issue ⚠ | No periodic grant/reset cron |
| [[issue-e2e-pipeline-fresh-run]] | issue ⚠ | Fresh E2E pipeline run on prod outstanding |
| [[idea-markitdown-pdf-to-text]] | idea | Backlog: local PDF→text (MarkItDown service) instead of full PDF to Gemini |
| [[reference-preview-test-account]] | reference | Dev test login `claude-preview-test@test.de` + BH test deal |

*As of 2026-07-16 · 4 hubs + 19 atoms · language: English (team convention). Planned hubs: auth/accounts · inspection protocol (Besichtigungsprotokoll) · RAG ingest · security/monitoring · renovation analysis · frontend structure*
