# 11 — RESPONSIVE / CO-VISIBILITY / PANE PLAN AT 1440 · 1728 · 2048

The frame-level law is 08 §4 and §9 (pane slots, max-2 work windows, canvas separate, no silent compression). This file is the per-surface application — the cross-surface reconciliation of every brief's §9/§10. Widths in px are defaults at the named viewport; every pane also declares its minimum, and the rule below a minimum is always **collapse to a labeled tab/overlay, never squeeze**.

## 1. The co-visibility principle (restated once)

A pane exists only where a decision requires simultaneous sight of two things (05 §4 is the decision-to-adjacency map). Everything else is summonable. The three recurring adjacency classes:

1. **Proposal ⇄ source** (confirmations): asymmetric ~65/35 document-dominant split.
2. **Draft ⇄ evidence** (review): the ruled three-column reconcile room.
3. **This ⇄ prior** (compare): synchronized 50/50 peer split, summoned, never permanent.

## 2. Per-surface topology table

Rail states: E = expanded 240–280px · I = icon 48px. All layouts assume the 48px header. "Canvas" = the floating canvas slot (separate from the split count).

### 1440px (rail auto-collapses to I at <1440 per 08 §9; at exactly 1440 user choice, default E)

| Surface | Topology at 1440 | Notes |
|---|---|---|
| Home | Single column, max-width 1040 centered | Sections stack; no panes |
| Inbox | List 100% + preview overlay (summoned, right 480) | Preview never permanent at this width |
| Loans | Table full-width | Saved-view bar above |
| Loan Detail | Content 100%; tabs | Covenant strip full-width rows |
| Calendar | Agenda 100%; cycle rail collapses to top summary strip | |
| Reports | Register 100% | |
| Documents | Register 100%; DocView is a route, not a pane | |
| DocView | Viewer ~880 + metadata rail 320 (collapsible) | Extracted mode: viewer 65 / fields 35 |
| Intake | Queue 280 + preview flex(~700) + inspector 380 → **at 1440 the checklist tab stacks inside the inspector** | The ruled 3-region precedent; queue collapsible to 48 icon strip |
| Extraction | Doc 65% (~890) + inspector 35% (~480) | Below 1360 usable: inspector becomes overlay sheet |
| Actuals | Exceptions table 100%; metrics block below; COA grid = full-width overlay tab | Source-beside-decision rows expand inline (row → 50/50 within the row) |
| Review Room | Outline 260 + canvas flex(~640) + evidence 460 | The ruled 3-column at its minimum comfortable width; below 1360 the outline collapses to icon stops |
| Composer | Sections 260 + workspace flex + readiness 320 | Readiness collapsible to a status chip row |
| Certificate | Sheet centered (~880) + rail 400 as overlay toggle | The ceremony modal is fixed 560 |
| Send | Panel 480 within certificate right rail / overlay | |
| Correspondence | Threads 300 + thread flex + context rail collapses to tabs | |
| Rent Roll | Summary strip + unit table 100% | |
| Settings | Nav 240 + content max 720 | |

### 1728px (the reference desktop)

| Surface | Topology | Deltas from 1440 |
|---|---|---|
| Intake | Queue 300 + preview flex(~880) + inspector 400 with checklist as persistent lower half | All four regions visible — the brief's target state |
| Extraction | Doc ~1060 + inspector 520 | Comfortable verbatim-quote cards |
| Actuals | Exceptions + metrics side-by-side becomes possible: work region 60/40 | COA grid overlay unchanged |
| Review Room | Outline 280 + canvas ~860 + evidence 500 | The demo-quality reconcile layout |
| Composer | Sections 280 + workspace ~880 + readiness 360 persistent | |
| Certificate | Sheet 880 + persistent right rail 440 (readiness/what-changed/history; send panel post-certify) | |
| Loan Detail | Content 1100 + persistent context rail 420 (current period card + your-move) | Rail is summary-only (no ownership) |
| Inbox | List 62% + persistent preview 38% | Preview earns persistence at ≥1600 |
| DocView | Viewer + metadata rail both persistent; Compare = 50/50 within viewer area | |
| Calendar | Agenda 65% + cycle summary rail 35% | |

### 2048px (wide desktop)

| Surface | Topology | Deltas |
|---|---|---|
| Review Room | Outline 300 + canvas ~1040 + evidence 600; compare mode: canvas splits 50/50 (this vs prior) with evidence overlaying right | The only surface using all its width budget routinely |
| Intake | Queue 320 + preview ~1080 + inspector 440; checklist fully expanded | |
| Actuals | Exceptions 55% + metrics/tests 45%; COA grid can open as split window 2 instead of overlay (counts as the second work window) | |
| Extraction | Doc ~1300 + inspector 560; "show all source regions" toggle comfortable | |
| Composer / Certificate | As 1728 with wider workspace; sheet stays 880 (paper width is fixed — extra ground stays quiet) | No stretching of the paper artifact |
| Registers (Loans/Reports/Documents/Calendar) | Content max-width ~1600 centered; tables gain optional columns, never stretch to full 2048 | Line-length discipline |

## 3. Narrow/compact fallback (<1280 down to the 1152×720 floor)

- Rail: icon mode, summonable overlay when expanded.
- All splits collapse: source/evidence panes become full-height overlay sheets summoned per row/stop (slide from right, Esc closes, focus-trapped).
- The review stop walk works serially: stop content full-width, evidence sheet summoned per stop (the 60-second review survives narrow — one extra keystroke per stop).
- The certify ceremony has a defined narrow layout (sheet scrolls; ceremony modal full-screen sheet) — certification is never blocked by viewport, only review comfort degrades.
- Below 1152×720: spine surfaces show the "larger window required for review work" state; registers, Home, Inbox, Settings remain fully usable.

## 4. No-silent-compression enforcement

Every pane declares `min-width` in its component config; the layout engine (a) collapses the pane to its labeled tab/overlay when the allocation would fall below min, (b) logs the collapse event in dev, and (c) CI runs the four viewport fixtures (1152/1440/1728/2048) against every surface fixture asserting: no pane rendered under its min, no horizontal scroll on the page body, split count ≤ 2 work windows, canvas never counted as a work window.

## 5. Focus/compare/proof behavior (uniform)

- **Focus mode:** any work window can zoom to 100% (keyboard `Z`); panes collapse to tabs; Esc restores.
- **Compare mode:** explicit entry (from a changed-field stop, a version row, or the period selector); always synchronized 50/50 of the primary work window; exit restores prior topology.
- **Proof (lit-row):** the Evidence context opens in the surface's designated evidence slot; on surfaces without one, as the overlay sheet — beside, never a modal; never covering the clicked figure (position away from the trigger).
