---
name: "grundriss-renovation-map"
type: "feature"
status: "draft"
created: "2026-06-24"
readiness: "🟡"
target_project: "/Users/cedi/dev/ImmoAlpha"
---

# Feature: Floorplan Renovation Map — 2026-07-21 (v2)

Status: Draft — reference example for the C2C v2 template (Kasona-Blueprint core skeleton + C2C process layer)

## Readiness: 🟡

| Criterion | Status |
|---|---|
| Problem described | ✅ |
| Goal stated (one sentence, user outcome) | ✅ |
| KB read + gap scan done | ✅ |
| Codebase analyzed | 🟡 (stack captured; `reno_*` tables not yet traced in detail) |
| Current state traced | n.a. (new feature; bridge points traced under Cross-feature integration) |
| Input → Processing → Output traced (target) | ✅ |
| Approach formulated | ✅ |
| Components identified | 🟡 (rough; no file mapping yet) |
| Edge cases defined | ✅ |
| Out of scope named | ✅ |
| Open questions resolved | 🟡 (core decisions made, 5 open) |
| Acceptance criteria formulated | ✅ |

## Goal

An investor can turn a floorplan image into real square meters per room and click renovation options onto floors, walls and windows — so renovation costs in the fix&flip calculation come from actual areas instead of flat estimates.

## Context from the KB

**Read:** [[architecture-overview]], [[decision-bh-flip-split]], [[supabase-dev-prod]]

**Constraints inherited:**
- Frontend lives in `Tools/hot-zone-map/` — there is NO root `src/` ([[architecture-overview]]).
- `reno-*` edge functions (audio/vision/timeline) already exist — extend, don't duplicate.
- PropertyDetails is deliberately split into fix&flip and buy&hold views via `investment_mode`; **do not re-merge** ([[decision-bh-flip-split]]). This feature docks onto the fix&flip view only.
- Everything ships straight to main + prod ([[decision-direct-to-main-prod]]); migrations on prod via MCP `apply_migration`, never `db push`.

**Gap (→ interview/analysis items, the KB does not answer these):**
- No wiki article on the renovation domain yet ("renovation analysis" hub is planned, not written) — `reno_trades`/`reno_items` schema must be read from migrations/code.
- No documented convention for where large JSONB geometry blobs live (table vs. storage).

## Problem

The Renovation Intelligence module (mobile walkthrough: photos + audio → Whisper + Claude Vision → `reno_trades`/`reno_items` with costs and a Gantt timeline) and the Dynamic Room Analysis (C2C, finalized) both lack the **spatial layer**:

1. **Costs are estimates** — without real m² per room, `cost/m²` stays a flat rate instead of `cost/m² × actual area`.
2. **Defects/trades float in the air** — a detected defect is not tied to a place in the property.
3. **No visualization** — nothing to play through floor/wall/window renovations on.
4. **Rooms are just a photo list** — no layout, no adjacency, no areas.

The map is the missing bridge between walkthrough capture (Renovation Intelligence) and calculation (fix&flip).

## Input → Processing → Output (target)

| # | Input | Processing | Output |
|---|---|---|---|
| 1 | Floorplan image (user upload → Supabase Storage) | LLM vision call: geometry-extraction prompt on the existing vision pipeline. ⚠ Context risk: without scale hints the LLM returns proportions, not measurements — output is explicitly *rough* geometry | Rough `{ rooms[], walls[], openings[] }` JSON, pre-filling the editor (never shown as final truth) |
| 2 | Rough geometry + user corrections + **scale calibration** (draw a known distance / enter scale) | Deterministic: polygon area calc, snapping, closed-polygon validation | Editable 2D plan; **m² per room** — blocked entirely until calibration is set |
| 3 | m² per room + chosen renovation option per surface | Deterministic: `cost/m² × actual area`, summed per room/object | Cost per room and object, flowing into `reno_items` and the fix&flip calculation — replaces today's flat estimate ⚠ (currently a hardcoded rate) |
| 4 | Geometry (JSONB) | Deterministic: 2D→3D extrusion (`@react-three/fiber`) | Interactive 3D view; materials on floor/wall, windows as cutouts (P2) |
| 5 | `reno_items` from the walkthrough | User pins items to room/wall | Spatially anchored defects → renovation heatmap (P3) |

## User's notes

- Calculation precision AND appealing visualization are **equal goals** — neither is secondary.
- Capture is **hybrid**: AI extracts rough geometry, the user corrects in the editor. The manual editor is the foundation (= the correction layer), AI is the accelerator.
- Scope: start with **one floor/apartment**, but design the data model **floor-capable from day one** (multi-floor retrofittable without schema break).

## Assessment

| Dimension | Rating | Reasoning |
|---|---|---|
| Workload | 🔴 High | New editor stack (Konva), new 3D stack (r3f), vision prompt, schema, calc coupling |
| Scope | 🟡 Moderate | Touches reno pipeline + fix&flip calculation, but additive |
| Success probability | 🟢 High | Vision pipeline exists; extruding 2D polygons is well-trodden r3f ground |

### Risks
- ⚠️ **Scale/accuracy**: wrong calibration corrupts every downstream number. Must be prominent and validated.
- ⚠️ **Vision geometry quality**: LLM delivers rough polygons, not pixel-perfect walls — the manual editor is therefore not optional.
- ⚠️ **3D complexity/performance**: WebGL on weak devices; LOD/scene limits needed.
- ⚠️ **Data-model lock-in**: missing floor level = painful rebuild for multi-floor support.

## Affected components

> Rough identification — file mapping follows in the agent-spec.

1. **Image upload & storage** — floorplan image in Supabase Storage, linked to the property.
2. **Vision geometry extraction (hybrid, stage 1)** — extend the existing vision pipeline with a prompt returning rough `{ rooms[], walls[], openings[] }`.
3. **2D editor (correction layer)** — `react-konva`: image as backdrop, rooms as polygons, walls as segments, snapping, scale calibration.
4. **Geometry data model (Supabase, JSONB)** — `Project → Floor → Room (polygon, area_m²) → Wall (segment, length, height) → Opening (type, position, size)`. Floor level present from day one.
5. **3D view** — `@react-three/fiber` + `@react-three/drei`: wall extrusion, materials on floor & wall, windows as cutouts.
6. **Renovation options per surface** — selectable renovation type with `cost/m²` per surface → sum per room/object.
7. **Coupling to `reno_*` & fix&flip** — m² values and surface costs flow into `reno_items` and the fix&flip calculation.
8. **Defect anchoring (P3)** — walkthrough `reno_items` pinned to room/wall → 3D renovation heatmap.

## Approach

Phased so the calculation value ships before any AI or 3D exists:

- **P0 — foundation & calculation value (hours→days):** image upload + scale calibration + manual room drawing (2D) → m² → coupling to `reno_items`. No 3D, no AI. Immediately useful.
- **P1 — hybrid goes live (days):** vision pre-fill of polygons, wall & window/door editor.
- **P2 — visualization (1–2 wks):** 3D extrusion, material/floor preview, interactive surface selection.
- **P3 — anchoring (expansion):** walkthrough defects pinned to rooms/walls → renovation heatmap.

```
floorplan image ─► vision ─► rough geometry (JSON)
                                │
                    2D editor (Konva) ◄─ user corrects + calibrates scale
                                │
          ┌─────────────────────┼─────────────────────┐
          ▼                     ▼                     ▼
     m² per room          3D view (r3f)        anchors for defects
          │              material/windows      (reno_items → surface)
          ▼                                            │
   cost/m² × area ─────────────────────────────────────┘
          ▼
    fix&flip calculation
```

## Cross-feature integration

- **Renovation Intelligence** (`reno-*` functions, `reno_trades`/`reno_items`): consumes the map's m² for real per-area costs; delivers the defects that get pinned in P3.
- **Fix&flip calculation**: receives `cost/m² × actual area` instead of flat estimates — the P0 payoff.
- **Dynamic Room Analysis** (C2C, finalized): recognized rooms can pre-label the drawn polygons.
- **PropertyDetails**: docks onto the fix&flip view only ([[decision-bh-flip-split]]).

## Edge cases & failure behavior

| What happens when… | Expected behavior |
|---|---|
| No/unreadable floorplan image | Vision step skippable → draw manually from scratch |
| Scale missing/implausible | Block all m²/cost output until calibration is set; plausibility check (room < 1 m² or > 200 m² warns) |
| Vision returns garbage (overlapping/open polygons) | Editor allows delete/redraw; validation enforces closed polygons |
| Slanted/curved walls, bay windows | P0/P1 orthogonal polygons only; freeform later |
| Multiple floors | Data model carries floor level; UI shows one floor for now |
| Weak device in 3D | Fall back to 2D; LOD/scene limits |

## Out of scope / what NOT to do

- ❌ No photogrammetry/LiDAR room scanning — input is a floorplan *image*, period.
- ❌ No pixel-perfect automatic wall detection — the LLM pre-fills *rough* geometry; precision comes from the human editor. Do not sink time into vision fine-tuning.
- ❌ No multi-floor UI in P0–P2 (data model yes, UI no).
- ❌ No BIM/DXF/IFC import or export.
- ❌ Do not modify the existing walkthrough capture flow (`reno-*` audio/photo ingestion) — this feature only *consumes* its outputs.
- ❌ Do not re-merge the PropertyDetails BH/flip views while docking the map in (inherited: [[decision-bh-flip-split]]).

## ❓ Open questions

1. ❓ Which renovation surface types + where do `cost/m²` rates come from (statically maintained vs. from walkthrough)? — *Tier-1 candidate: wrong source model is expensive to unwind.*
2. ❓ Where does the feature live in the UI — tab inside fix&flip PropertyDetails or own route? — *Tier 2: reversible, propose default.*
3. ❓ Which vision engine for geometry extraction — Gemini (`@google/generative-ai`) or Claude (as in the reno pipeline)? — *Tier 2: both exist in the stack; log the pick.*
4. ❓ Exact `reno_*` schema and how surface costs hook in (codebase analysis outstanding — KB gap, see above).
5. ❓ Material/texture library for 3D — scope and source? — *P2 concern, defer.*

## ✅ Decisions

| # | Decision | Tier | Why |
|---|---|---|---|
| 1 | Calculation precision and visualization are equal goals | 1 | Sets the P2 investment level; user-decided, not derivable |
| 2 | Hybrid capture: vision pre-fill + manual editor; the editor is the mandatory foundation | 1 | LLM geometry is inherently rough; betting on vision-only would sink the m² accuracy the whole feature exists for |
| 3 | One floor at launch, but floor level in the schema from day one | 1 | Wrong guess = schema break later (expensive, no obvious answer at the time) |
| 4 | P0 ships calculation value without any 3D/AI | 2 | Obvious once stated: de-risks the two new stacks; logged, not gated |
| 5 | Stack: `react-konva` (2D), `@react-three/fiber` + `drei` (3D), geometry as JSONB, reuse existing vision pipeline | 2 | Follows existing patterns ([[architecture-overview]]); logged with reasoning, tunable |

## Acceptance criteria

- [ ] User can upload a floorplan image and attach it to a property.
- [ ] User can calibrate a scale; without calibration no m²/costs are shown anywhere.
- [ ] User can draw/correct rooms as polygons; m² are computed automatically.
- [ ] Renovation options are assignable per surface; costs sum per room and object.
- [ ] The m² and cost values flow traceably into the fix&flip calculation.
- [ ] (P1) Vision pre-fills polygons; the user can correct them.
- [ ] (P2) 3D view extrudes the geometry; floors/walls/windows are interactively adjustable.
- [ ] (P3) Walkthrough defects are located on the floorplan/3D view.

## References

- KB: [[architecture-overview]] · [[decision-bh-flip-split]] · [[supabase-dev-prod]] (in `wiki/`)
- Related C2C docs: `C2C/dynamische-zimmer-analyse/` (finalized), Renovation Intelligence (`renovation_user_journey.md`)
- Code entry points: `supabase/functions/reno-*`, `Tools/hot-zone-map/src/pages/` (PropertyDetails, fix&flip view)
- Templates: `~/dev/concept-to-code/TEMPLATES.md` (v2)
