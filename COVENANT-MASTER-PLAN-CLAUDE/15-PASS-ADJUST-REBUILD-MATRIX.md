# 15 — FINAL PASS / ADJUST / REBUILD MATRIX

Grading rules (Phase 2 law): classify FIXTURE/REAL/PARTIAL/ABSENT first; grade the architecture, not the data; fixture strings are not product truth; existing work gets no exemption; prior benchmark findings re-tested, not repeated. **UNBUILT** marks surfaces graded on plan only. The evidence column cites the snapshot (surface table §3, gap map §6), the 2026-08-04 benchmark findings re-tested as design inputs, and this plan's briefs.

## 1. The frame

| Unit | State | Verdict | Why / what changes |
|---|---|---|---|
| Shell chassis (rail+header+canvas) | FIXTURE (architecture real) | **PASS with ADJUST riders** | Survived adversarial benchmarking, zero REBUILD; riders: route-addressability (08 §1), crumb switchers (08 §2) |
| Rail contents | FIXTURE; IA flagged-open | **ADJUST** (proposal pending D-1) | Two-axis mix diagnosed; one-axis proposal in 08 §3; two defects (dual pinning, Pipeline axis) fix regardless |
| Breadcrumb altitude gate | FIXTURE | **PASS with ADJUST** | Concept ruled and correct; crumbs become switchers |
| Command palette | FIXTURE (navigates only) | **ADJUST** | Grammar extension to objects + computed answers (08 §5); no rebuild — the input/registry architecture stands |
| Canvas panel | FIXTURE | **PASS** | Slot law honored; hosts Ask per brief |
| Provenance lit-row | PARTIAL — wrong row 2/3 renderers (U1-F1 re-confirmed as open by snapshot) | **ADJUST (P0)** | One-resolver contract (WO-002); the mechanic's concept is ruled and right |
| Keyboard/G-chords | FIXTURE | **ADJUST** | Complete the map + focus order per cross-cutting spec |

## 2. Portfolio floors

| Surface | State | Verdict | Why |
|---|---|---|---|
| Home/Overview | FIXTURE (U3-F2 feed contradiction re-confirmed possible by architecture) | **ADJUST** | IA right (landing + your-move); rebuild the data layer onto the one ActivityEvent store + your-move query; anatomy per brief |
| Inbox | FIXTURE (U3-F7 badge; U3-C1 truncation) | **ADJUST** | Semantic fix (your-move, not unread) + readiness gating; register architecture stands |
| Portfolio/Loans/LoanDetail | FIXTURE + one real wire | **ADJUST** | The strongest floor; keep the trend wire, extend; record page per brief; covenant strip rebuilt on TestResult rows |
| Calendar | FIXTURE | **ADJUST** | Task-first agenda kept (liked); due-rule engine is NEW behind it; month-grid = D-3 |
| Reports | FIXTURE | **PASS with wiring** | The ruled gold-standard pattern; wire to real send/certification records; no design change |
| Documents + DocView | FIXTURE; DocView orphaned | **ADJUST** | Register modes + version law added; DocView kept (porcelain quality) and given its importer (V2) — an orphaned viewer was a prop, not a surface |
| Settings | FIXTURE | **ADJUST** | Full IA per brief (intake mgmt, roles, agent memory, escalation policy) |
| Sign-in / outward door | FIXTURE; **BLOCK** (guest reaches nothing) + C-7 boundary violation | **REBUILD (the door), P0 (the link)** | The one REBUILD-class verdict: the outward door as an experience is rebuilt (onboarding brief + D-6); WO-001 ships the link fix immediately |
| Onboarding / Import | ABSENT | **UNBUILT → build per brief** | Requirements-computed checklist; CSV import surface over the existing library function |
| Rent Roll teaser | NOT STARTED (ruled to exist) | **UNBUILT → build per brief (W5)** | Sited per D-4 recommendation |

## 3. Reporting spine

| Surface | State | Verdict | Why |
|---|---|---|---|
| Intake/Holding | FIXTURE (no machinery) | **ADJUST (surface) + BUILD (machinery)** | The screens render the ruled vision's idea; the decision grammar, checklist states, and chase mechanics are new; F1/V2 build the pipeline |
| Extraction & Confirmation | ABSENT (substrate only) | **UNBUILT → build per brief** | The requirement side's home; 65/35 split; fail-closed rendering |
| Actuals & Computation | ABSENT (engine built) | **UNBUILT → build per brief** | Exception-first; the engine's F-series finally gets its surface |
| Review Room | FIXTURE; 3-column ruled KEPT | **ADJUST** | Layout kept; rebuilt on engine rows with the exception walk; lit-row fixed first |
| Composer | FIXTURE; readiness decorative | **ADJUST** | Anatomy kept in spirit; readiness becomes computed (the decorative readiness was the defect) |
| Certificate | Route REAL; sheet paints 0/8 ruled paper tokens | **ADJUST (paint + ceremony)** | Route/resolver stand; the ruled whole-sheet blue-violet finally painted; ceremony + record + void added |
| Send & Record | API REAL; no human moment | **PASS (gate) + BUILD (surface)** | The gate is the pattern-proof — untouched; the send panel/receipt/seal UI is new |
| Lender Q&A | ABSENT | **UNBUILT → build per brief (W4)** | Draft-first, grounded, human-approved |

## 4. Agent layer

| Unit | State | Verdict | Why |
|---|---|---|---|
| AskPanel + HighlightToAsk | FIXTURE (one scripted answer) | **ADJUST** | Interaction scaffold kept (the "gasp" mechanics); grounded-or-refuse engine behind it is new |
| Proactive watch | ABSENT (engine emits, nothing frames) | **UNBUILT → build (V5/W4)** | Findings service + surfacing per brief |
| Ask-once memory | ABSENT | **UNBUILT → build (W4)** | One store; inline chips; Settings inspector |
| Outcome learning | ABSENT | **UNBUILT → build (W4)** | Edit-event capture; repeat-error metric |
| Quiet log | PARTIAL (fixture feeds) | **ADJUST** | One append-only store; feeds become projections |
| Capability prompts | FIXTURE | **ADJUST** | Rotating, engine-completable only (locked law) |

## 5. Engine and data (graded as wiring, not formulas)

| Unit | State | Verdict |
|---|---|---|
| Engine library (requirements, metrics, tests, tie-out, COA spine, safety core) | Built, tested, green, unwired | **PASS as architecture** — the wiring order is the roadmap; formula correctness expressly deferred to the separate audit |
| Persistence | Contract-only (1 of 5 tables wired; 2 migrations unconfirmed) | **ADJUST** — confirm/apply, then extend (F2) |
| Demo book | Canon-clean fixture | **PASS as seed source** — becomes seeds (WO-011), then retires as a direct import (B9) |
| Send gate | REAL | **PASS** — the pattern the rest follows |

## 6. Prior benchmark findings — re-test disposition (advisory findings, re-tested per Phase 2 law)

| Finding | Re-test result | Disposition in this plan |
|---|---|---|
| U1-F1 lit-row wrong row | Still open (snapshot) | WO-002 P0; regression fixtures |
| U3-F2 feed contradiction | Still possible (architecture) | Killed structurally (one store) |
| U3-F7 badge semantics | Still open | WO-003 P0 |
| U3-C1 3-of-7 outstanding docs | Still open | Register-completeness rule in Inbox brief |
| U12-F2 Basis-funnel link | **Un-fixed as of the sweep** | WO-001 immediate hotfix |
| The outward-door BLOCK | Still blocked | REBUILD verdict above + D-6 |
| 11 ADJUST verdicts / 70-entry polish register | Not re-repeated item-by-item (advisory until Terry rules buckets) | Subsumed: every unit's ADJUST work is specified by its brief; the polish register should be re-graded against the briefs at wave start |

## 7. Summary counts

- **PASS:** shell chassis, canvas panel, breadcrumb concept, Reports pattern, send gate, engine architecture, demo book (as seeds) — 7
- **ADJUST:** rail, palette, lit-row (P0), keyboard, Home, Inbox, Loans/LoanDetail, Calendar, Documents+DocView, Settings, Intake surface, Review Room, Composer, Certificate, Ask scaffold, quiet log, capability prompts, persistence — 18
- **REBUILD:** the outward door (sign-in/guest experience) — 1
- **UNBUILT → build to brief:** Extraction & Confirmation, Actuals & Computation, Send surface, Lender Q&A, proactive watch, memory, outcome learning, onboarding/import, Rent Roll teaser — 9

The build's spine survives; the product's absence was never the screens — it is the machinery this plan's F/V phases construct and the ownership/semantic corrections the briefs specify.

## Appendix — packaging grep record

Before packaging, every file in this deliverable was grepped for the hard-fail list ("one product, two lenses" · cross-product "one agent" · certify/send attached to Basis · offering-memorandum/OM/broker/kill-gate language · shared inbox/intake across products · "engine for Basis" · Investor-Reporting-vs-Juniper positioning) and for vocabulary violations ("breach" outside covenanted contexts). Result recorded in the final commit message of the package branch. Real property names verified absent (outward names only).
