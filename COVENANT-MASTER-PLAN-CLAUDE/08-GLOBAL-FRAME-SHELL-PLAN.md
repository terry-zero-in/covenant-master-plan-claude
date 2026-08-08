# 08 — GLOBAL FRAME AND SHELL PLAN

Current state: the shell chassis (`CovenantShell.tsx`, mounted by `/review-room`) is the strongest built asset — drag-resizable/collapsible left rail, 48px header with view switcher + breadcrumb altitude gate, floating canvas panel; it survived the 12-unit adversarial benchmark with zero REBUILD verdicts. Verdict here: **frame PASS-with-ADJUST** (adjustments below); no rebuild. Data behind the frame is fixture; the frame's architecture is real.

## 1. Shell chassis law (kept, and made addressable)

**Keep:** one view-switching chassis; every screen mounts through it. The rail, header, and canvas panel are the three frame regions; the work area is one big pane with view-switching (ruled pane model), split into at most **two work windows** when a task justifies it; the canvas slot is separate and does not count against the split (frame-mirror spec, merged #528). The reconcile room's three-column layout is ruled KEPT as that surface's interior arrangement.

**Adjust — addressability.** Today the shell state (which view, which loan, which period) lives in component state under one route. The plan makes every material state addressable without changing the chassis:

- `(covenant)` route group grows deep routes that all mount `CovenantShell` with an initial view: `/home`, `/inbox`, `/intake`, `/loans`, `/loans/[loanId]`, `/calendar`, `/reports`, `/documents`, `/documents/[docId]`, `/settings/*`, `/rent-roll`, and the per-period spine `/covenant/[loanId]/[period]/{review|composer|certificate|actuals}`. The certificate route already proves the pattern (own route + resolver + tests); `/review-room` remains as the shell's home alias for continuity.
- Rule: **URL carries loan + period for every spine surface** (the send-vertical pattern demands it; deep links, notifications, and the quiet log all address these routes).

## 2. Breadcrumb altitude gate (kept, completed)

Two altitudes, ruled and already built: book-scoped crumb (`Covenant / Loans`) versus deal-scoped crumbs (`Covenant / {Loan} / {Period} / Review`). Adjustments:

1. The period crumb is a **period selector** (click → period switcher popover listing this loan's periods with status dots), not dead text.
2. The loan crumb is a loan switcher scoped to the current surface (switch loan, stay on Review).
3. Altitude never mixes: book floors show no loan/period crumbs; spine surfaces always show both. The gate is the test: a surface that can't say which loan+period it serves is a book floor by definition.

## 3. Rail — the flagged-open IA rework (PROPOSAL for Terry decision D-1; existing law preserved as the alternative)

**The diagnosed defect (snapshot, flagged-open):** the rail mixes portfolio navigation (Overview, Portfolio, Calendar, Reports, Documents, Inbox, Pipeline) with a surface-switcher role, plus starred items and folders — two axes in one strip.

**Proposal — one axis: places, not modes.** The rail lists the book-level floors only, in workflow order, each a place with a stable address:

```text
[Org switcher / identity]
Home                    (your move concentrates here)
Inbox                   (arrivals)          ← your-move count badge (shared CountBadge)
Loans                   (the book)
Calendar                (obligations/deadlines)
Reports                 (sent-package register)
Documents               (the filed record)
Rent Roll               (the free v1 teaser — sited per its brief)
────────────────────────
Starred                 (user-pinned loans/views — one group, collapsible)
────────────────────────
[Settings · profile]    (bottom cluster)
```

- The per-period spine surfaces (Review, Composer, Certificate, Actuals) are **not rail items**: they are reached through a loan+period (from Home your-move, Inbox, Calendar, Loans, or the palette) and identified by the breadcrumb. This removes the surface-switcher role from the rail entirely; the header view switcher covers intra-surface views (e.g., Loans: table/trends).
- "Pipeline" leaves the rail: pipeline-vs-live is a **filter/segment of Loans**, not a place (a loan's lifecycle stage is loan state, honoring one-owner).
- Folders collapse into Starred (one pinning mechanism, not two).
- Rail remains drag-resizable/collapsible; collapsed state shows icons + badges (Lucide-only, no emoji, per locked law).

**Existing-law option (if Terry declines):** keep current contents; still remove the two defects that are independent of the restructure — the duplicated pinning mechanisms and the Pipeline/live axis mix — as ADJUST items.

## 4. Pane topology law (frame-wide)

| Slot | Persistence | Rules |
|---|---|---|
| Work window 1 (primary) | persistent | The surface itself; minimum 720px |
| Work window 2 (split) | summoned | Only when a decision requires side-by-side (compare, source-beside-draft); max split = 2 work windows; each ≥ 560px or the split refuses (no silent compression — the second window becomes a stacked tab below 1280px total) |
| Canvas panel | floating, summonable | The ruled Linear-style floating slot; carries transient tools (Ask, quick preview, palette results detail); never load-bearing for a decision; Esc dismisses |
| Evidence/inspector pane | pinned within a surface | Surface-interior (e.g., review room's ruled 3-column: canvas + 2 side panes); governed by each brief's co-visibility matrix |
| Modals | last resort | Only for typed acts with legal weight (certify, send, void acknowledgment) and destructive confirmations; never for reading evidence (proof opens beside, per the lit-row law) |

## 5. Command palette and search (from navigation-only to compute-and-return)

Grammar (three result classes, one input):

1. **Places** — routes/views ("Review — {Loan} Q2" with status dot). Exists today; kept.
2. **Objects** — loans, documents, periods, requirement records, sends ("{Loan} loan agreement v2"). Requires the persisted object store (roadmap F2); results carry loan/period identity chips.
3. **Computed answers** — typed queries the engine can answer deterministically ("dscr {loan}" → the latest TestResult with verdict + headroom + a lit-row citation link; "occupancy {loan}"; "next deadline"). Answer rows are provenance-carrying objects: click opens the owning surface with the figure's evidence lit. The palette **never** free-generates: computed answers come from engine reads; anything else routes to the Ask surface explicitly ("Ask the analyst: …"), keeping the deterministic/agent boundary visible in the very grammar of the palette (engine rows get the mono figure treatment; Ask rows get the agent affordance).

Keyboard: `⌘K` palette · `/` focuses search in registers · G-chords for places (`G H` Home, `G I` Inbox, `G L` Loans, `G C` Calendar, `G R` Reports, `G D` Documents) · `G V`/`G P`/`G F` reserved for the spine within a loan context (Review, Composer, Certificate) — final chord map in `cross-cutting/search-command-keyboard.md`.

## 6. Ask panel and highlight-anything-to-ask (frame mechanics only; behavior in the agent brief)

- Highlight any text/figure → a quiet affordance ("Ask about this") → Ask opens in the canvas panel with the selection + its provenance as grounding context. The selection's source refs travel with the question — the answer cites the same lit-row chain.
- Ask never overlays the evidence a question is about (canvas panel positions away from the lit region; the panel is movable).
- Rotating capability prompts: domain-real, engine-completable only (locked law: never advertise an ask that can't complete).

## 7. Provenance mechanic (frame contract)

The lit-row trace is a **frame-level contract**, not a review-room feature: any figure rendered by any surface may carry a provenance ref; clicking it opens/focuses the Evidence context for its chain and lights the exact source line, which stays lit until dismissed. Component contract, wrong-row defect repair (U1-F1), and per-renderer adoption order: `cross-cutting/provenance-lit-row-trace.md`.

## 8. Notifications and your-move in the frame

- One computed your-move query (definition: 03 §4) renders as: the rail badge on Inbox/Home, the Home section, and nothing else (C-8 fix: unread is not a badge concept anywhere).
- Push notifications: only deadline-escalation per org policy (04 §2.3). Everything else is quiet log, discoverable in place.
- A slim "your move" indicator in the header (count chip) is permissible at deal altitude when the current loan+period itself has blocking items; it links to the blocking item, not to a feed.

## 9. Responsive and minimum-size rules (frame level)

| Viewport | Rail | Work area | Canvas panel |
|---|---|---|---|
| ≥1728px | expanded (240–280px) | 1 or 2 work windows; evidence panes per brief | floats free |
| 1440–1727px | expanded or user-collapsed | split allowed if both windows ≥560px; else stacked tabs | floats; may dock right |
| 1280–1439px | auto-collapses to icon rail (48px) | single work window + summonable panes | docks |
| <1280px | icon rail | single window; evidence panes become overlay sheets; spine surfaces remain fully operable (certify ceremony has a defined narrow layout in its brief) | full-height dock |

Minimum viable frame: 1152×720 (below this, a "larger window required for review work" state on spine surfaces only; registers remain usable). No silent compression anywhere: a pane that can't hold its minimum collapses to a labeled tab, never squeezes.

## 10. Frame acceptance tests

1. Every rail item deep-links (`/home` … `/rent-roll`) and restores shell state; back/forward traverse views.
2. Breadcrumb altitude: spine routes always render loan+period crumbs; book routes never do; crumbs are switchers (2 keyboard-reachable).
3. Split honors the max-2 law and per-window minimums; verify no-silent-compression at 1280/1440/1728/2048 fixtures.
4. `⌘K` returns places/objects/computed answers per grammar; engine-answer rows carry working lit-row links; zero free-generation in the palette.
5. G-chords navigate with rail collapsed and with focus inside a table.
6. Your-move badge equals the computed query everywhere it renders (one number, three places).
7. Frame renders on the ten-rung gray ladder tokens with zero local grays (readback check, not eyeball, per the oklch caution).
