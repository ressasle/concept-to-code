---
title: Exposé Analysis Pipeline (Hub)
type: hub
updated: 2026-07-16
sources: [supabase/functions/pipeline-*, devlog 2026-07-15]
---

# Exposé Analysis Pipeline

**Core:** The heart of the product: an exposé (PDF or URL) flows through `pipeline-ingest` → `pipeline-parse` → `pipeline-standort` / `pipeline-substanz` / `pipeline-optik` (location / substance / optics) → `pipeline-finalize`. Entry points: `analyze-pdf`, `analyze-url`, `process-onsite-file` (on-site files). AI calls go to Gemini; PDFs are currently sent whole as base64 `inlineData`. Prod state cleaned up since 2026-07-15 (PDF leak closed).

## Atoms in this domain
- [[decision-bh-flip-split]] — result views BH/flip kept separate, do not re-merge
- [[issue-e2e-pipeline-fresh-run]] ⚠ open — fresh E2E run on prod still outstanding
- [[idea-markitdown-pdf-to-text]] — backlog: local PDF→text instead of full PDF to Gemini
- [[quirk-prod-jwt-hs256]] — 401 on all AI functions, prod only → JWT quirk

## Debugging entry points
401 before any analysis → [[quirk-prod-jwt-hs256]]. Quota errors → `check-quota` + [[billing-stripe]]. Scanned PDF yields empty analysis → check text extraction (no OCR guard, see [[idea-markitdown-pdf-to-text]]).

## Related
[[architecture-overview]] · [[billing-stripe]] · [[supabase-dev-prod]]
