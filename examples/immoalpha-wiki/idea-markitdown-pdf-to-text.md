---
title: "Idea: local PDF→markdown (MarkItDown) instead of full PDF to Gemini"
type: idea
status: backlog
updated: 2026-07-16
sources: [Notion KNOWLEDGE "Saving Token Costs" §9 (2026-06-15)]
---

# Idea: MarkItDown as a shared service (backlog)

Both document paths currently send the **entire PDF base64** to Gemini (exposé parse + `ingest-document`); Gemini bills ~258 text tokens + image tokens per page. MarkItDown (Microsoft, Python) extracts text **locally for free** → only cheap text tokens remain.

Design points captured from the Notion analysis:
- **As a shared microservice** (Python container, HTTP), not inline — edge functions are Deno, MarkItDown is Python. Reusable cross-project (ImmoAlpha first, then Kasona).
- **Vision path untouched** — images/room classification need real vision; `pdfImageExtractor` stays.
- **Scanned PDFs**: pdfminer does no OCR → needs an OCR fallback + a "did we actually get text?" guard.
- **Before building:** token audit to quantify the saving.

## Related
[[pipeline-expose-analysis]]
