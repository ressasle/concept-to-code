# The Knowledge Base Layer — Living Wiki (Karpathy Pattern)

> **Status:** raw / informational — v0, 2026-07-21.
> **Purpose of this file:** get everyone on the same page about the third layer of the C2C system (the memory layer). Nothing in here requires a team decision — every project can start this **today** with a `wiki/` folder in its own repo. The shared cross-project setup comes later (see §7).
> **Origin:** Karpathy's LLM-wiki idea (Tech Meeting Week #25) + our Context Engine sessions. A working pilot exists (ImmoAlpha: `wiki/`, 4 hubs + 19 atoms) and has been in daily agent use since 2026-07-16.

---

## 1. The idea in five lines

AI agents guess when they lack context — and the context they lack usually already exists in our heads, devlogs, meeting notes, or analytics. The knowledge base is **shared memory**: small, curated, interlinked markdown articles that any agent loads *before* the first prompt. It is a **traversable graph** (`[[backlinks]]`), not similarity-RAG: the agent reads `index.md`, follows links, and gets a connected story — metric → code → constraint → decision — instead of top-k fragments.

Karpathy's key insight: while the curated layer stays under ~100k tokens, the whole thing fits in a context window — **no vector DB needed**. RAG stores can keep existing underneath as raw storage; the wiki is the distilled working memory on top.

## 2. The three-layer rule (most important boundary)

| Layer | What | Where |
|---|---|---|
| 1 — Raw sources | code, logs, full transcripts, PDFs | GitHub / DB / Notion — stay where they are |
| 2 — RAG index (optional) | embeddings over big raw material | untouched, if it exists |
| 3 — **The wiki** | curated, linked articles + pointers | `wiki/` in the project repo — small enough to read whole |

**The wiki never copies code or raw content.** It holds knowledge *about* them plus pointers (file paths, table names, doc links). If the info lives elsewhere, link it — same rule as the Blueprint's "references, not duplication."

## 3. Article format & conventions

One file = one unit of knowledge. Two shapes: **atoms** (one fact/decision/quirk per file) and **hubs** (map of content per domain, linking the atoms). Useful atom types: `decision` · `quirk` · `issue` · `flow` · `idea` · `reference`.

```markdown
---
title: <topic>
type: hub | decision | quirk | issue | flow | idea | reference
updated: YYYY-MM-DD
sources: [<devlog / commit / meeting / notion page>]
---

# <Topic>

**Core:** <2–5 sentences: how it works, what the source of truth is.>

## Constraints & decisions
- <decision + WHY + date/commit — this is what kills agent guesswork>

## Where the typical bugs live
- <symptom → file/table> (pointer: `path/to/real/file.ts`)

## Related
[[article-a]] · [[article-b]]
```

Rules (each one exists because its absence produced garbage somewhere):

1. **Link liberally** — `[[backlinks]]` are the graph edges. A link to a not-yet-existing article is fine; it marks something worth writing.
2. **Pointers, not copies.** Never duplicate content that lives elsewhere.
3. **Date contradictions instead of overwriting** ("until 11.07: X; since commit abc: Y") — trends stay visible.
4. **`index.md` is sacred:** every article gets one line there (title + one-line hook), updated in the same commit. The index is what an agent loads first.
5. Every line must survive the test: *"Would an agent guess wrong without this?"* If not → cut it. This is what keeps the layer under 100k tokens.
6. **English everywhere** — agents parse it best, the whole team reads along.

## 4. How agents consume it (read side)

1. **Routing in the project's agent config** (CLAUDE.md or equivalent): *"Before working on topic X, read `wiki/<x>.md`."* Works immediately, no behavior change needed.
2. **C2C integration:** every concept starts with the mandatory **"Context from the KB"** block (see `TEMPLATES.md` §3) — articles read, constraints inherited, gaps named. The interview then asks **only the gaps**. This is the KB-read → gap-scan → grill-only-the-delta mechanism: the interview gets shorter every week.

## 5. How knowledge gets in (write side)

Two channels:

1. **C2C write-back** (already wired, see `SKILL-FINALIZE.md`): when a feature is finalized, its Tier-1 decisions flow back into the affected articles as proposals — decision, reasoning, trade-off, dated.
2. **Curator pass** (manual for now): periodically run the 5-step loop over new sources (git log, devlogs, meeting notes):

   ```
   READ → EXTRACT → MATCH (against index.md) → WRITE (+ dedupe & contradiction check) → LINK
   ```

   The dedupe/contradiction check is the actual value step — it prevents the garbage heap. Output is always a **proposal/PR, never a silent commit**: the curator proposes, the human owns the merge. (Same tier principle as C2C: the model proposes, the human owns the cut line.)

## 6. Start today, per project (no decisions needed)

1. Create `wiki/` + `index.md` in your project repo.
2. Seed ~10 articles by **consolidating what already exists** (devlogs, agent config, reports, your head) — don't write anything new. Aim at the topics where agents guessed wrong recently; every past wrong guess is a missing article.
3. Add the routing lines to the project's agent config.
4. Definition of done: an agent that reads only `index.md` + linked articles can answer *"what must I know before touching topic X?"* without asking a human.

Reference implementation: the ImmoAlpha pilot (`wiki/` — 4 hubs, 19 atoms, seeded in ~1 day from existing material).

## 7. Deliberately deferred (needs team decisions — NOT required to start)

These come later and don't block anything above:

- **Shared `knowledge/` repo** (per-project wikis move in via `git mv`, structure is designed for it) — needs: repo home, write access.
- **Curator as a scheduled routine** (weekly, automated PRs) — needs: cadence, whose machine/runner.
- **External ingest feeds** (meeting transcripts, YouTube digests, analytics findings) — needs: source list, model mix.
- **Notion mirroring** for non-repo readers — needs: which articles, manual vs. automated.

Success criteria once piloted team-wide (Ship/Kill/Extend after ~4 weeks): C2C interviews get measurably shorter; "DECISION NEEDED" escalations the KB could have answered trend to zero; curator review takes < 15 min/week. If the wiki isn't being read → kill it, no zombie process.
