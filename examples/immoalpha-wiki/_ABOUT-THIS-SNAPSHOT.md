# About this snapshot — read first

> ⚠️ **This is a frozen snapshot (2026-07-21) of the ImmoAlpha pilot wiki, in its test/raw phase.**
> It is here to show the knowledge-base pattern **in practice** — it is NOT the live wiki, not a template to copy verbatim, and its contents will drift out of date.
> The canonical, living version is `wiki/` in the ImmoAlpha repo; the pattern and conventions it follows are described in [`KNOWLEDGE-BASE.md`](../../KNOWLEDGE-BASE.md).

What to look at (the pattern, not the ImmoAlpha specifics):

- **`index.md`** — the sacred index: every article, one line each. This is what an agent loads first.
- **Hubs vs. atoms** — 4 hubs (domain maps, e.g. `architecture-overview.md`) linking 19 atoms, each one unit of knowledge with a type: `decision-*` · `quirk-*` · `issue-*` · `flow-*` · `idea-*` · `reference-*`.
- **`decision-*` atoms** — decision + WHY + date. Example: `decision-bh-flip-split.md` even warns future readers that the "obvious refactor" was already considered and rejected — exactly the guesswork-killer the pattern exists for.
- **Pointers, not copies** — articles point to files/tables (`Tools/hot-zone-map/...`), never duplicate code.
- **Every line survives the test:** *"Would an agent guess wrong without this?"*

Status of the pilot at snapshot time: seeded 2026-07-16 from existing material (~1 day of consolidation), in daily agent use since, read side wired via CLAUDE.md routing. Write-back and curator pass were still manual — the deferred team decisions are listed in `KNOWLEDGE-BASE.md` §7.
