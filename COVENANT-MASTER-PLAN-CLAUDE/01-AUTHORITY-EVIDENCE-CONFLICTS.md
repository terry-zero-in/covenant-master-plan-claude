# 01 — AUTHORITY, EVIDENCE, AND CONFLICTS REGISTER

## 0. Package identity (recorded as first action, per mandatory process rule 1)

| Field | Value |
|---|---|
| Received package filename | `e1e3d603-_Covenant__Master_Plan_R2.zip` |
| Received package size | 7,852,600 bytes |
| Received package SHA-256 | `e87c3fcf7ab6417545b79d6e3b6df6fa3009628af73d028d2f1806746aa874b6` |
| Received package inner folder | `Covenant - Master Plan` |
| Internal manifest | `MANIFEST-SHA256.txt` present; all 47 listed files verified present in the archive |
| Session origin | Claude session (Claude Code, remote) |
| Returned package inner folder | `COVENANT-MASTER-PLAN-CLAUDE` (this folder) — per the package-identity rule |

**Build-target acknowledgment (mandatory process rule 2, in writing):** the product being planned is the Next.js application in the **basis-v2 repository** — the `(covenant)` route group in `src/app/`, `src/components/covenant/`, `src/lib/covenant/`, the `/review-room` shell route, `/covenant/[loanId]/[period]/certificate`, and `POST /api/covenant/send`. No standalone prototype, walkthrough HTML, or worktree copy is the target. Every surface in this plan states its landing address in that repository, and the plan is written to survive the downstream Claude Code feasibility gate against the live repository.

**Repository-access note:** this session attempted to attach the basis-v2 repository read-only to verify snapshot claims directly against source (`add_repo basisuw/basis-v2`); the platform denied the attach (cross-owner restriction — this session is keyed to `terry-zero-in/covenant-master-plan-claude`). Per the handoff's own provision ("the build itself … a chat planning session cannot run; the snapshot … stands in for it"), the current-state snapshot of 2026-08-07 is used as the audit map of record, every load-bearing claim is cited to the repository address the snapshot names, and claims this session could not independently verify are flagged `[UNVERIFIED-BY-SESSION]` where they are load-bearing.

---

## 1. Authority and evidence register

Classification vocabulary (from the assignment): **LAW** = ratified product law · **IMPL** = current implementation evidence · **CALC** = deterministic calculation/domain evidence · **WORKFLOW** = representative workflow evidence · **CONTEXT** = context-only material · **HIST** = historical/advisory material.

### 1.1 Handoff-package instruction files (all read in full)

| Artifact | Class | Authority notes |
|---|---|---|
| `00-START-HERE.md`, `01-COPY-PASTE-FIRST-MESSAGE.md` | LAW (process) | Assignment definition; two mandatory process rules |
| `02-AUTHORITY-AND-CONFLICT-MANIFEST.md` | **LAW — Tier 1** | Carries the PRODUCT BOUNDARIES law verbatim (Terry, 2026-07-14) + hard-fail list; wins on any conflict |
| `03-ATTACHMENT-CHECKLIST-AND-UPLOAD-ORDER.md` | LAW (process) | Freeze rule; slot definitions |
| `04-CURRENT-AUTHORITY-NOTE.md` | **LAW — Tier 1** | Ruled ambiguity resolutions: build target, Review-9 colors-only, demo canon, known open items, **the parity law** (Terry 2026-08-07: Covenant must have the equivalent-or-better of anything Basis has built/specced in detail) |
| `05-WORKING-METHOD-AND-PHASE-GATES.md` | LAW (process) | Phase gates 0–8 |
| `06-SURFACE-INVENTORY-AND-REQUIRED-BRIEF-DEPTH.md` | LAW (scope) | Minimum surface inventory; may refine, may not omit |
| `07-COVENANT-TRACKING-MODEL-PROTOCOL.md` | LAW (domain-process) | The tracking chain; founder-supplied semantics; scope firewall |
| `08-ENGINE-AGENT-HUMAN-GATE-QUIET-LOG-LAW.md` | **LAW — Tier 1** | Five-lane responsibility law; agent may-not list |
| `09-DOCUMENT-INTAKE-PROVENANCE-LAW.md` | **LAW — Tier 1** | Intake-email exclusivity; four-artifact document model; lit-row law |
| `10-PER-SURFACE-BLUEPRINT-TEMPLATE.md` | LAW (format) | 18-section template used for every brief in `surface-briefs/` |
| `11-REFERENCE-EXEMPLAR-INTAKE-DOCUMENTS.md` | LAW (depth bar) | Required depth demonstration |
| `12-MASTER-DELIVERABLE-OUTLINE.md` | LAW (format) | Package tree this deliverable follows |
| `13-ACCEPTANCE-GATES-AND-QUALITY-RUBRIC.md` | LAW (process) | Automatic-failure list; 90+ target |
| `14-CURRENT-BUILD-ORIENTATION.md` | IMPL orientation | Verified against snapshot; consistent |
| `15-ENGINE-AND-KNOWLEDGE-CORPUS-ORIENTATION.md` | CALC orientation | Module table "generated from the snapshot; verify roles" |
| `16-DO-NOT-ASSUME-AND-CONFLICT-RULES.md` | **LAW — Tier 1** | Do-not-assume list; amendment handling; hard-fail list restated |
| `working-templates/*` (5 files) | LAW (format) | Table skeletons; consumed in deliverables 04/07 and per-brief |

### 1.2 References folder

| Artifact | Class | Authority notes |
|---|---|---|
| `references/COVENANT-CURRENT-STATE-SNAPSHOT-2026-08-07.md` | IMPL — **Tier 3, the audit map of record** | Derived snapshot; the rulings it cites outrank it. Sections used load-bearing: §3 surface inventory, §4 design-system state, §5 rulings register, §6 the 14-gap map, §7 authority notes |
| `references/CLAUDE-CODE-DYNAMIC-SURFACE-BENCHMARK-DOCTRINE-2026-08-03.md` | LAW (research method) | Governs Phase 5; registry is seed-not-whitelist |
| `references/00-REFERENCES-MANIFEST.md` | LAW (process) | **See conflict C-2: items 3–9 it promises are absent from the frozen zip** |

### 1.3 Real evidence documents (`02-REAL-EVIDENCE/`) — all inspected

All slots are filled (none waived). One property across every slot — outward name **Calloway Park (CAL)**, one loan, one servicer (JLL), one reporting relationship. Per the outward-name law, the real property/entity names that appear inside the file contents are never printed in this package; every reference here uses the outward name.

| Slot | Artifact(s) | Class | What this session verified by direct inspection |
|---|---|---|---|
| SLOT-1 | T-12 FYE 12.31.18 (.xlsx + .pdf) · T-12 trailing through 01.31.19 (.xlsx) | WORKFLOW + CALC | One sheet, 14 columns (12 months + total), ~150 rows on a numbered chart of accounts (4010 Gross Potential Rent → 8010 Asset Management Fee). Group structure: Rental Income → Vacancy/Losses/Concessions → Net Rental Income → Other Income (21 coded lines incl. 5 utility-reimbursement codes) → Total Income → 9 expense groups → **Total Operating Expenses $1,686,050** → **NOI $1,218,877** → Debt Service/Interest **$644,017** → Asset Mgmt Fee → **Net Income $460,159**. Accrual book; PMS-generated ("Created on 03/22/2019") |
| SLOT-2 | Rent Roll Detail as of 04/30/2018, resident identifiers removed | WORKFLOW + CALC | 24 columns (Unit, Floorplan, SQFT, Unit/Lease Status, anonymized Name, Move-In/Out, Lease Start/End, Market+Addl., deposits, Balance, Lease Rent, RENT, 8 charge codes, Total Billing). **322 lease rows across 301 units** (dual rows where a future lease exists). Sheet-2 floorplan summary (unit mix, avg SQFT, occupancy % per plan); Sheet-3 unit-status summary: Occupied-no-NTV 259 · Occupied-NTV 18 · Occupied-NTV-Leased 2 · Vacant-Leased 13 · Admin/Down 2 · Vacant-Not-Leased 7 = **301** |
| SLOT-3 | Executed loan agreement, 2017 | **CALC — the requirement-side source** | 136 pages. Fannie Mae **Form 6001.NR** Multifamily Loan and Security Agreement (Non-Recourse), 01-16 edition, plus riders: **Form 6220** (Replacement Reserve Deposits Partially/Fully Waived) and **Form 6241** (Green Financing — adds Article 16: ENERGY STAR tracking + annual Energy Performance Metrics report). Article 8 §8.02(b) carries the full reporting schedule (detail in deliverable 05). **No recurring DSCR / debt-yield / occupancy ratio covenant exists anywhere in the agreement** — DSCR appears only in conditional contexts (casualty-restoration test ≥1.0x on a 30-year-amortizing calculation, §9.03; post-casualty payment-adjustment discretion at <1.25x). Verifies the ruled reporting-only / monitored-not-covenanted canon against a real executed agreement |
| SLOT-4 | FYE-2018 submitted package (12 of 15 files; 3 resident-level files withheld by ruling and named) | **WORKFLOW — the golden exemplar** | Balance sheet, income statement (xlsx+pdf), statement of cash flows, trial balance, vendor aging, ownership structure (57 members with percentages summing to 100.0000%), loan/interest/escrows workbook (UPB **$15,232,500**, 2-yr IO → 30-yr am, 10-yr term, maturity 2027-07-07; monthly interest rows tying to the T-12 debt-service line; escrow/reserve activity incl. six renovation-reserve draws), JLL Property Questionnaire (blank + executed), JLL Annual Certification (fillable + executed). Tie-outs verified by inspection: cash-flow Net Income $460,159 = T-12; interest paid $644,017 = T-12 debt-service total; period-end cash $138,940 = balance-sheet Total Cash |
| SLOT-5 | Two additional executed JLL Property Questionnaires (Q2 + Q3 2018) | WORKFLOW | The recurring quarterly ask. Executed versions are **flattened scans with no text layer** (verified: zero extractable text) — direct evidence for the Original vs. Recreated-searchable artifact model |
| `EVIDENCE-CONTEXT.md` | LAW (evidence map) | Slot table, sanitization record, the RESIDENT-DATA LAW (Terry 2026-08-07, verbatim: resident info **NEVER** travels outward) |

### 1.4 Items the assignment message listed but that did not arrive

| Expected | Status | Mitigation in this plan |
|---|---|---|
| References-manifest items 3–9 (product-truth doc, product spec, spec-vs-built reconciliation, 2026-08-04 benchmark report, dated Terry sitting records, extraction checklist + covenant census, trigger library, product-flow verbatim) | **Absent from the frozen zip** (see C-2) | The snapshot §5 rulings register and §6 gap map carry their operative content; this plan cites through the snapshot and marks any claim that depends on the missing primary text |
| Current certified-sheet render | Absent | The six ruled paper hexes are stated in `04-CURRENT-AUTHORITY-NOTE.md` and the snapshot §4; the certificate brief consumes the ruled values verbatim and specifies *where* each paints — it restyles nothing |
| Current walkthrough artifact (context-only) | Absent | No loss — context-only by its own label |
| 2026-08-04 benchmark report (advisory) | Absent (referenced in snapshot §3) | Findings re-tested from the snapshot's citations rather than repeated; each is marked advisory-until-ruled |

---

## 2. Conflicts ledger

Protocol per `02-AUTHORITY-AND-CONFLICT-MANIFEST.md`: never silently blend. Every conflict found during intake:

| ID | Topic | Source A | Source B | Newer/stronger | Proposed resolution | Requires Terry? |
|---|---|---|---|---|---|---|
| C-1 | Rent-roll period in the tracing spine | `EVIDENCE-CONTEXT.md` slot table + filename: roll is **as of 04/30/2018** | Same file's "coherent tracing spine" note: "the SLOT-2 rent roll (12.31.18)" | The file itself (period printed in the header row, verified by inspection) | Treat SLOT-2 as an April-2018 roll. The as-submitted 12/31/18 roll is one of the three withheld resident-level files. Occupancy lineage chains in deliverable 06 use the 04/30/2018 roll and say so; the FYE-2018 financial spine (T-12 → package) is unaffected | No — factual correction, logged |
| C-2 | Frozen-zip completeness | `references/00-REFERENCES-MANIFEST.md`: items 3–9 "copied in at assembly time" | The actual archive + its own `MANIFEST-SHA256.txt`: only the snapshot and the doctrine are present | The archive is what shipped | Proceed on the snapshot as the stand-in (it was written for exactly this role); flag every load-bearing dependency on a missing primary (they are enumerated in §1.4 and in deliverable 14's risk register). The adjudication session should attach the seven missing primaries before ratification | Yes — attach before ratification (listed in 16-DECISIONS §D-9) |
| C-3 | Unit count wording | `EVIDENCE-CONTEXT.md`: "all 24 columns and 322 units" | The roll's own summary sheet: **301 units**, 322 lease rows (dual-row units carry a current and a future lease) | The roll itself | Use 301 units / 322 lease rows everywhere. This distinction is load-bearing product knowledge: naive row-counting overstates the denominator — the rent-roll reader must deduplicate to units before any occupancy figure | No — logged; feeds the occupancy-resolver design |
| C-4 | Demo-canon rate vs. evidence workbook | Demo canon (snapshot §3): Bexley 4.17% fixed | Interest sheet header displays "0.04" (its monthly interest rows are consistent with an actual/360 note-rate computation; header appears to be a rounded display) | Neither governs the other — canon governs the demo book; the workbook is evidence about the real deal | No action in the demo book. Noted so nobody "corrects" canon from the workbook or vice versa. The observed UPB ($15,232,500), unit count (301), IO-then-am structure and agency form all cohere with canon | No |
| C-5 | Two outward names over one apparent real deal | Demo canon outward name **Bexley** ($15,232,500 · 301 units · 6001.NR reporting-only) | Evidence-kit outward name **Calloway Park** (identical UPB, unit count, form, servicer relationship) | Both are Terry-issued print names for their own contexts | Keep both, never asserted as the same deal in outward copy; this plan uses Calloway Park when citing evidence files and Bexley when citing the demo book. The correspondence is noted here once so lineage examples don't confuse a reviewer | No — noted |
| C-6 | Product-truth doc §5 carries retired framing | `00_PRODUCT_TRUTH.md` §5 ("one product, two lenses" era) — *known via snapshot §1; primary absent* | PRODUCT BOUNDARIES law 2026-07-14 | The law (Tier 1) | Already ruled: §5 is superseded text. Recorded here because the missing primary (C-2) means a future reader could encounter §5 unwarned | No — ruled |
| C-7 | Sign-in surface violates the boundaries law | Built sign-in screen: only live link exits to the Basis marketing funnel (snapshot §3, benchmark U12-F2) | Product-boundaries law: products never cross | The law | The onboarding/sign-in brief makes removing the cross-product link a P0 acceptance test; classed ADJUST-blocking in deliverable 15 | No — implementation fix |
| C-8 | Inbox badge semantics | Built Inbox badge counts unread (snapshot §3, U3-F7) | Ruled your-move semantics (the badge is a review count) | Ruled intent | Inbox brief redefines the badge as a your-move count with an exact computed definition; unread is a separate, unbadged notion | No |
| C-9 | Fixture thresholds displayed as covenants | Built screens show Bexley DSCR ≥1.20 / DY ≥8.0% / occ ≥90% styled as covenant tests | Ruling (snapshot §5, Terry 2026-07-01): those thresholds are **UNSOURCED as covenants**; the executed agreement is reporting-only; they are monitored | The ruling, now re-verified against the real agreement (SLOT-3: no recurring ratio covenants exist) | Every surface that renders a test verdict must carry the monitored-vs-covenanted distinction structurally (separate requirement `basis` field, separate visual grammar, separate vocabulary: shortfall vs breach). Detailed in deliverable 05 and `cross-cutting/status-vocabulary-shortfall-breach.md` | No — ruled; design executes it |
| C-10 | Rail IA | Built rail mixes portfolio navigation with a surface-switcher | Flagged-open ruling: rework pending Terry's study | Neither — genuinely OPEN | This plan **proposes** a restructure (deliverable 08) with the existing-law option preserved; decision routed to Terry (16-DECISIONS §D-1) | **Yes** |
| C-11 | Evidence-kit T-12 chart of accounts vs. engine COA spine | SLOT-1 T-12 uses the PMS's numbered COA | Engine F-series chart-of-accounts spine (snapshot: "the Bexley fixture ties to $309,145.14") `[UNVERIFIED-BY-SESSION — repo not attachable]` | Not a conflict of law — a mapping requirement | The actuals model (deliverable 05 §3) requires a per-source COA mapping layer with human-confirmed mappings, ask-once-remember-forever per property/PMS | No — design consequence |

---

## 3. Historical / advisory materials — separated per Gate 0

| Artifact | Status | Handling |
|---|---|---|
| 2026-08-04 independent benchmark report (12 units: 11 ADJUST, 1 BLOCK, 0 REBUILD) | HIST/advisory — nothing binds until Terry rules the buckets | Findings the snapshot names (U1-F1 lit-row wrong-source-row, U3-F2 feed/ledger contradiction, U3-F7 badge semantics, U3-C1 3-of-7 outstanding docs, U12-F2 Basis-funnel link, the outward-door BLOCK) are **re-tested as design inputs** in Phase 2, not repeated as verdicts |
| Pre-reframe lender-side screens (review queue "ranked by covenant risk", surveillance book) | HIST — superseded framing | Evidence of what exists; never carried forward as intent |
| Voltage-indigo and gold accent eras; "one product, two lenses" | DEAD / RETIRED | Never referenced except as history |
| Walkthrough/demo artifacts, terryturner.ai deploys | CONTEXT-only | Not consulted for product truth |
| Basis materials of any kind | CONTEXT-only | Consulted for zero Covenant product decisions; the parity law is consumed as law via `04-CURRENT-AUTHORITY-NOTE.md`, not via Basis artifacts |

---

## 4. Standing constraints this plan operates under (acknowledged once, applied everywhere)

1. **Product boundaries (Terry law 2026-07-14)** — three independent products; own intake emails that can never cross; certify-and-send is Covenant-only; the sole user-standpoint relationship is Covenant actuals as a Basis benchmark for dual subscribers. Every file in this package was grepped against the hard-fail list before packaging (see 15-PASS-ADJUST-REBUILD-MATRIX §appendix for the grep record).
2. **Design tokens are settled law** — Review-9 periwinkle #7189FF family (colors only), the ten-rung gray ladder (2026-08-07), the whole-sheet blue-violet certified sheet with six ruled hexes, Geist Mono, slashed zeros + tabular numerals. This plan proposes **zero** new colors; where a surface needs a semantic color, it names an existing ruled token.
3. **Domain-content firewall** — no covenant threshold, test definition, metric semantic, lender-form content, cadence, or canonical number is invented anywhere in this package. Where a lineage example needs a value, it uses a value read from the evidence documents (marked as evidence) or from the demo canon (marked as canon).
4. **Trust hierarchy** — the engine computes every shipping number; the agent prepares/proposes/drafts/chases; the borrower reviews, certifies, sends. The AI never certifies, never sends, never mutates a ground-truth number past a human gate. Applied structurally in every five-lane table.
5. **Vocabulary law** — pass / watch / shortfall / breach; a monitored miss is a shortfall, never a breach; monitored and covenanted are distinct states modeled separately end to end.
6. **No proof, no done** — every work order in deliverable 13 names its acceptance evidence.
