# ACTUALS & COMPUTATION

The per-period surface where source financials become normalized lines on the chart-of-accounts spine, metrics compute per the loan's own definitions, covenant tests verdict with signed headroom, and this period ties out against the prior sealed package. This surface is the home of the tracking model's actuals side (05 §2) and the computation chapter of the workflow (03 §1 steps 6–8).

Its law is **exception-first**: a clean line completes itself and consumes no permanent screen space; only exceptions persist as work (R3 research: Numeric autosubmit-when-matched — "one-click reconciliations autosubmit where the connected workpaper and GL balance match; the clean case consumes zero human attention").

## 1. Identity and verdict

- Product layer: reporting spine (per loan, per period).
- Route/address in the basis-v2 repository: `/covenant/[loanId]/[period]/actuals` — planned deep route in the `(covenant)` group (08-GLOBAL-FRAME-SHELL-PLAN §1), mounting `CovenantShell` with deal-scoped breadcrumbs.
  - Page: `src/app/(covenant)/covenant/[loanId]/[period]/actuals/page.tsx` (own resolver + tests, following the certificate route's proven pattern).
  - Components: `src/components/covenant/actuals/`.
  - Engine: reads from `src/lib/covenant/` — the built F1–F11 financials spine (grid extraction → COA normalization → reconcile → review gate; the Bexley fixture ties to $309,145.14 — snapshot §3).
  - Tokens: consumed from `src/styles/covenant-tokens.css`; zero local values.
- Current build state: `ABSENT` as a surface. The engine substrate is built, test-covered, and green (snapshot §3: metric engine with annualization, covenant test with watch band, prior-package tie-out, COA spine). Gap 6 ("computed numbers absent on screens") is exactly this missing surface; gap 7 (occupancy resolver BLOCKED) lands here visibly.
- Existing-surface verdict: `UNBUILT` (DIRECTIONS: actuals-computation).
- Research tier: C (R3 — close, evidence & tie-out).
- Primary users/roles: PMC preparer and owner (resolve exceptions, confirm mappings); reviewer read-only (inspect chains, never confirm).
- Frequency and session duration: monthly/quarterly per loan.
  - First period per property: 20–40 minutes of mapping confirmations (the ask-once investment).
  - Steady state: under two minutes — a scan of chips and a usually-empty exception queue, because remembered mappings auto-apply.
  - Deadline pressure: this surface sits upstream of Q+45d / FYE+120d due-rules (evidence: SLOT-3 §8.02(b)); an unresolved exception here is what makes a deadline dangerous.
- Error cost: terminal-adjacent. A wrong COA mapping flows into NOI → DSCR → the certified package → the lender. Every confirmation here is upstream of the certify gate; that is why confirmations are typed acts with downstream-impact display, and why the engine fails closed rather than guessing.
- Success criterion: a zero-exception period consumes zero attention here; no unmapped T-12/rent-roll line survives to certify (R3 research: FloQast completeness check, adapted); every computed figure opens its full chain (06 §1); a monitored miss can never render "breach" anywhere on this surface.

## 2. User job and decisions

- Primary job-to-be-done: "turn what the property manager sent into the numbers I can certify — and show me only what actually needs me."
- Decisions made here (each a typed act with downstream impact shown before commit):
  1. Confirm/correct a proposed COA mapping — remembered per property/PMS thereafter (ask-once); moves the group subtotal, NOI, and every consuming metric/test.
  2. Resolve a control-total break — decide which side is right (extraction error, mapping error, source anomaly) and apply the owning fix.
  3. Resolve a roll/summary disagreement — row-derived aggregate vs the roll's own summary sheet.
  4. Acknowledge/resolve a book-basis flag — the statement's book stamp (accrual/cash) vs the loan's required basis.
  5. Escalate a definition ambiguity to authoring in Setup — e.g. which debt-service basis a DSCR test uses (05 §7.1); never decided ad hoc here.
- Questions the surface must answer in scan order:
  1. How far has this period's pipeline run — Extracted → Normalized → Reconciled → Computed?
  2. Do the control totals tie?
  3. What needs me, and how much of it?
  4. What did the engine compute — metrics, verdicts, signed headroom?
  5. Does this period tie out against the prior sealed package?
  6. Can I see the whole normalized statement and roll if I want? (Yes — summonable, never forced.)
- What the user should not have to decide here: any shipping number (engine-only); verdict vocabulary or basis (structural); re-confirmation of remembered mappings (the agent never re-asks); tie-out dispositions (owned by Review Room); anything about composition, certification, or sending.
- Entry paths:
  - Home your-move exception rows ("2 normalization exceptions — {Loan} {Period}") deep-link here with the offending group focused.
  - Inbox items; Loan Detail current-period card; Calendar deadline rows (via the period).
  - Review Room "fix upstream" routes — a corrected figure lands here with the offending line focused, with a return path.
  - The palette (`⌘K` → "actuals {loan} {period}"); the breadcrumb period/loan switchers.
- Exit paths: Review Room (`/covenant/[loanId]/[period]/review`) once exceptions clear; Extraction & Confirmation / Setup for definition authoring; Documents/DocView for source artifacts; back to Home/Loans via breadcrumb altitude.
- Completion/advancement conditions: all normalization exceptions resolved + control totals tie + tie-out computed → this surface's contributions to period readiness go green; the period advances toward `in-review`/`ready` per the ruled state machine (03 §2). This surface never flips period status itself — the orchestration spine owns transitions (02 §3).

## 3. Object and ownership model

| Object/action/status | Owned here? | Summary/link elsewhere | Address/identity | Notes |
|---|---:|---|---|---|
| NormalizedFinancialLine | YES | Review evidence pane renders; Documents links from source cells | `(propertyId, period, coaCode)` | The COA-spine row (02 §2) |
| COA mapping confirm/correct (specialized MemoryEntry) | YES | Settings → Data renders the per-property/PMS library for inspection | mapping per `(propertyId, pms, sourceCode)` | Ask-once: confirmed once, remembered forever; corrections version (03 §3) |
| Control-total / cross-statement reconciliation state | YES | Pipeline chip summaries on Home/Loan Detail | `(loanId, period)` per check | Sheet's own totals vs computed; cash-flow/balance-sheet ties (evidence: SLOT-4 tie-outs) |
| Occupancy aggregates + unit-status normalization | YES | Loan Detail trends; questionnaire fields consume | `(propertyId, roll-date)` | BLOCKED today pending the canonical reader (gap 7) — rendered honestly |
| Metric (NOI, annualized debt service, DSCR, debt yield, occupancy, liquidity) | YES | Loan Detail, Review, Composer consume | `(loanId, period, metricId)` | Deterministic engine output only; per the loan's own definitions |
| TestResult (verdict + signed headroom) | computed YES / disposition NO | Review Room owns disposition; Loan Detail + Home summarize | `(loanId, period, reqId)` | `basis_echo` mandatory (05 §3); breach unreachable when monitored |
| TieOut (prior-package deltas) | rendered + inspected here | **Review Room owns the disposition act** (02 §2) | `(loanId, period)` | Actuals shows enum state + the 50/50 compare; "disposition with reason" is a Review stop |
| RequirementRecord definitions | NO | cited read-only beside tests/metrics | `reqId` | Owned by Extraction & Confirmation; "Resolve in Setup" routes there |
| Documents/artifacts | NO | source regions render in Evidence; link to DocView | `docId` | Documents owns the filed canon |
| Period status | NO | status chip in header | `(loanId, period)` | Orchestration spine owns transitions |
| ActivityEvent (this period's normalization/computation trail) | writes YES / store NO | one app-wide store (02 §2); `QuietLogFoot` renders the period-scoped projection | `eventId` append-only | The quiet log is the notification-noise firewall |
| Anomaly flags (band-gated variance vs prior) | raised here | consumed as Review exception-walk stops; Home findings summarize | `(loanId, period, coaCode)` | Non-blocking watch items; agent lane, never a gate |

No-double-homing boundary: Actuals owns normalization, mapping memory, reconciliation state, metrics, and computed test results. Review Room owns dispositions (tie-out failures, shortfall/breach findings) and renders this surface's outputs as evidence. Composer/Certificate consume metrics and verdicts by reference. Settings renders the mapping library as an inspection view, never a second confirmation path. Intake/Holding owns arrivals; this surface links to the holding checklist when documents are missing, never renders its own copy.

## 4. Data and semantic model

- Source facts (immutable, class `source` in the three-actor language — 05 §2.2):
  - T-12 grid cells (evidence: SLOT-1 — one sheet, 12 month columns + total, ~150 rows, numbered COA `4010.000 Gross Potential Rent` … `8010.000 Asset Management Fee`, group subtotal rows, accrual book stamp, as-of date).
  - The sheet's own subtotal/total rows (the control-total reference side).
  - Rent-roll rows (evidence: SLOT-2 — 24 columns, 322 lease rows over 301 units) and the roll's own summary sheets (floorplan mix, status totals).
  - Balance-sheet / cash-flow statement values for the annual package's cross-statement ties.
  - Loan terms confirmed at setup (evidence workbook: UPB $15,232,500 · 2-yr IO → 30-yr am · maturity 2027-07-07).
- Extracted values awaiting confirmation (class `inferred`, proposed):
  - Proposed COA mappings for new/changed source codes — confidence + source region attached.
  - Proposed unit-status label mappings the first time a PMS's taxonomy appears.
  - Row-class proposals (line vs subtotal vs header) only where ambiguous.
- Confirmed values (class `inferred` + confirmation act, badge shows both):
  - Mappings with confirmer identity and date ("learned {date} from {who}" — MemoryEntry grammar, 03 §3).
  - Pinned per-loan definitions consumed read-only here: debt-service basis, annualization rule, occupancy denominator and Admin/Down treatment, NOI boundary decisions (semantic-risk register 05 §7.1–7.5).
- Deterministic outputs (ENGINE lane only):
  - NormalizedFinancialLine[] on the spine.
  - Control-total checks: Σ mapped expense lines = the sheet's own "Total Operating Expenses" (evidence: $1,686,050 ✓); NOI per the sheet's grouping ($1,218,877 — 06 Chain A).
  - Cross-statement ties (evidence: cash-flow Net Income $460,159 = T-12 Net Income ✓; interest paid $644,017 = T-12 debt-service total ✓; period-end cash $138,940 = balance-sheet cash ✓).
  - Unit dedup 322 → 301 and status aggregates cross-checked against the roll's own summary (301 ✓ — 06 Chain C).
  - Metric[] per the loan's own definitions; TestResult[] with verdict, `basis_echo`, and signed dual-denomination headroom; tie-out enum states per field.
- Agent proposals/drafts: mapping proposals; anomaly flags vs prior period gated by materiality bands (R3 research: Numeric flux thresholds, adapted — under-band variance passes silently; over-band variance raises a non-blocking watch item that feeds Review's exception walk). The agent never produces a figure that lands in a package (04 §2.1).
- Human decisions: confirm/correct mapping; resolve break; resolve roll/summary disagreement; acknowledge book-basis flag; escalate definition gap. Each is identity-stamped and versioned.
- Certified values: none minted here. If the period is certified, every input on this surface is under void-on-change: a confirming/correcting act warns that it will void the certification (03 §2) and proceeds only through that explicit confirm.
- Versions/periods/packages: mappings version by correction (prior value kept, downstream-impact noted — 02 §5); normalized lines carry processing/extractor version; the tie-out target is the prior **sealed** package revision, addressed by hash.
- Evidence/provenance: every normalized line carries source cell refs (sheet + cell range); every metric carries formula ref + input refs; the full chain shape is 06 §1 and every material figure here opens it in place, beside the claim.
- Permissions/read-only projections: reviewer role sees everything, confirms nothing (actions disabled with reason); PMC preparers scoped per client (02 §1); sealed periods render this whole surface read-only.
- Grain: organization → (client) → property (mappings, normalized lines, occupancy) → loan (terms, definitions) → reporting period (this route's address) → document (sources) → requirement (definitions, tests) → metric/test result → package (downstream consumer) → certification/send records (downstream, never here).

## 5. State machine and exceptions

Pipeline stages render as the header chips **Extracted → Normalized → Reconciled → Computed**. Chip state grammar (each chip):

- `pending` — stage not yet reached (quiet gray rung).
- `running` — stage executing now; label names the step ("normalizing 148 lines").
- `done` — clean completion; quiet.
- `exceptions(n)` — stage complete but n items need a human; count via shared `CountBadge`; click filters the queue.
- `blocked` — fail-closed stop; the chip names exactly what is missing and links the owning fix path. **Never a plausible placeholder** (04 §2.5); a blocked downstream chip explains its upstream cause ("Computed: blocked — Reconciled has 2 open breaks").

Stage prerequisites and outputs (the dependency spine the chips render):

| Stage | Consumes | Produces | Blocks on |
|---|---|---|---|
| Extracted | arrived source documents (T-12, roll, statements) | grid cells + row classes with regions | unreadable/unparseable sheet; missing expected document (partial) |
| Normalized | grid cells + mapping memory | NormalizedFinancialLine[]; roll unit/status rows | unmapped codes (exceptions, not a hard block); unknown status labels |
| Reconciled | normalized lines + the sheet's own totals + roll summary | control-total checks; cross-statement ties; roll/summary ties | any open break (downstream honesty block) |
| Computed | reconciled lines + confirmed loan terms + pinned definitions | Metric[]; TestResult[]; tie-out enum states | unpinned definition (per-metric block); occupancy reader absent (gap 7) |

| State | Trigger | Actor to resolve | Render | Reversible? | Audit |
|---|---|---|---|---|---|
| empty / awaiting documents | period open, no financials arrived | agent chases (Holding owns) | "Waiting on {items} — see holding checklist" + link to `/intake`; chips all `pending` | n/a | arrival events |
| loading/processing | extraction/normalization running | engine | chip `running` with stage label; known-shape skeletons only | n/a | processing version logged |
| partial | T-12 arrived, roll missing (or reverse) | agent/human upstream | computable stages proceed; dependent stages `blocked` naming the missing input | yes (on arrival) | quiet log |
| extracted-unconfirmed | new/changed source codes | human confirms mapping | exception rows in Region 1; consuming metrics show "pending confirmations (n)" | confirm is versionable | confirmation act |
| low confidence | proposal under org threshold | human | exception row even when a match exists; confidence shown beside the proposal | yes | act + confidence |
| conflict — control-total break | Σ mapped lines ≠ sheet's own total | human | Reconciled chip `exceptions(n)`; break row shows computed vs sheet's-own vs signed delta | yes | resolution act |
| conflict — roll/summary disagreement | row-derived aggregate ≠ roll's own summary | human | exception row with both values + delta | yes | resolution act |
| book-basis flag | statement book ≠ loan's required basis | human acknowledges/resolves | exception row citing the sheet's book stamp | yes | act |
| blocked — definition gap | e.g. DSCR test with unspecified debt-service basis | human authors in Setup | metric/test card renders BLOCKED naming the unpinned definition (05 §7.1); "Resolve in Setup" | yes | gate event |
| blocked — occupancy resolver | canonical rent-roll reader absent (gap 7) | build dependency (F3) | honest block card: "Occupancy cannot compute: the rent-roll canonical reader is not yet built. Unit dedup (322→301), status taxonomy, and summary ties land with it." No number renders anywhere | n/a | — |
| stale/superseded | a source doc replaced after values confirmed | human re-confirms affected values | stale badge on affected lines/metrics with diff link; per-line re-confirm path | yes | supersede + re-confirm events |
| failed tie-out | prior-package delta outside tolerance | Review Room dispositions | tie-out row `unreconciled` with delta; 50/50 compare summonable here; "Disposition in Review" link | via disposition | disposition (in Review) |
| provisional tie-out | fresh statement before management review | time / final export | `provisional` chip (R3 research: Modern Treasury `tentatively_reconciled`, adapted); computed and shown, never satisfies the clean-tie-out readiness input | auto-clears on final | flagged in log |
| watch / shortfall / breach | test verdicts | Review dispositions shortfall/breach; watch is informational | verdict chips; **monitored basis can emit pass/watch/shortfall only — breach is type-unreachable** (05 §3) | via disposition | TestResult history |
| permission denied | reviewer/foreign scope | — | read-only with disabled-with-reason actions; foreign scope → honest 403/404 per the send-vertical pattern | n/a | access log |
| read-only (sealed) | period sent + sealed | — | full surface inspectable forever, zero mutations (seal-not-wipe) | no — by design | seal event |
| certified (void-on-change) | any confirm/correct touching certified content | human, explicit | pre-act warning modal: "this period is certified; proceeding voids the certification"; on proceed → visible VOID event, period returns to in-review | the void is never silent | void event |
| recovery/undo | re-open a confirmed mapping | human | corrections version the mapping (never edit history); downstream recompute + impact strip | yes, always by new version | correction act |
| undo impossible | sealed periods; quiet-log entries; void events | — | stated in UI ("sealed — read-only forever") | no — by design | append-only |

Gate anatomy (the gate law applied — every gate is a decision, not an event; 04 §2.2). This surface owns two gate objects:

- **Control-total break gate.**
  - Exact ask: "The mapped expense lines sum to {computed}; the sheet's own Total Operating Expenses reads {sheet value}. Which is right, and what fixes it?"
  - Why blocked: a broken control total means the normalized statement no longer represents the source — every downstream metric would be unfounded.
  - Evidence shown: the contributing normalized lines (filtered), the sheet's own total cell (lit), the signed delta.
  - Affected fields: the group subtotal, NOI, every metric/test consuming the group (named in the impact strip).
  - Typed options: re-map a line (routes to its exception row) · mark an extraction error (re-runs extraction on the region) · flag a source anomaly (escalates to the document owner; the break stays open).
  - Recommendation: the agent proposes the likeliest cause ("line 6220 mapped to the wrong group drives 94% of the delta") — proposal only, never auto-applied.
  - Consequence of delay: the Computed stage stays blocked; the period cannot reach `ready`; deadline pressure surfaces via Calendar/Home escalation.
  - Audit record: the resolving act with actor, choice, and recompute results.
- **Definition-gap gate (per metric/test).**
  - Exact ask: "This loan's DSCR test has no pinned debt-service basis (actual-paid vs scheduled vs 30-yr-am pro-forma). Author it in Setup."
  - Why blocked: the engine refuses to test without the definition (05 §7.1) — a guessed basis would be an invented covenant semantic.
  - Evidence shown: the requirement record's definition text and source region; the candidate bases named, none preselected.
  - Affected fields: the metric card, its TestResult, the package covenant section.
  - Typed options: Resolve in Setup (authoring flow) · leave blocked (explicit, logged).
  - Consequence of delay: the test never verdicts; readiness holds false with this reason linked.
  - Audit record: the authoring act in Extraction & Confirmation, referenced back here.

## 6. Engine / Agent / Human / Gate / Quiet Log

| Step | ENGINE (deterministic) | AGENT (prepares/proposes) | HUMAN (typed decisions) | GATE (interrupts) | QUIET LOG |
|---|---|---|---|---|---|
| Grid extraction & row classing | extraction validation; row-class rules | propose classes where ambiguous | resolve ambiguous rows | unreadable/unparseable sheet | clean extraction runs |
| COA normalization | apply remembered mappings; spine placement | propose mappings for new codes with confidence + source region | confirm/correct new mappings (remembered per property/PMS) | — | remembered mappings auto-applied ("mapped from memory, learned {date}") |
| Control-total & cross-statement reconciliation | Σ checks vs the sheet's own totals; statement ties | explain the break (which lines drive the delta) | resolve breaks | control total broken → downstream stages block honestly | ties that pass |
| Rent-roll normalization | unit dedup (322→301); status aggregation; summary cross-check | propose status-label mappings first time per PMS | confirm status mappings; resolve row/summary disagreements | reader absent → occupancy stage blocked (gap 7) | clean roll runs |
| Metrics & annualization | **all of it** — per the loan's own definitions; annualized debt service from terms | explain results with lit-row evidence; anomaly flags vs prior (band-gated) | none — metrics are never edited by anyone | unpinned definition → the metric refuses to compute (05 §7) | metric recomputes |
| Covenant tests & verdicts | tests + watch bands + signed dual headroom; `basis_echo`; breach type-gated | frame findings; draft mitigant narratives (surfaced in Review/Home, not here) | disposition happens in Review | a breach on a covenanted test is never auto-dispositioned | pass/watch verdicts; headroom history |
| Prior-package tie-out | field deltas vs prior sealed package; enum state incl. provisional | explain deltas; propose dispositions for known-benign classes | disposition in Review Room | failed tie-out blocks readiness | clean tie-outs |
| Mapping memory (ask-once) | storage integrity; provenance stamps on reuse | reuse remembered mappings; never re-ask a confirmed one | correct a remembered mapping (versioned) | — (memory never gates) | every reuse ("pre-filled from memory {ref}") |

Lane invariants checked (04 §2): no shipping number outside ENGINE; agent proposals never auto-commit (R3 research: Modern Treasury — "the end-user retains full control"); every gate names its typed options; the quiet log is append-only and identity-stamped.

## 7. Information hierarchy

1. Frame header (shell, 48px): deal-scoped breadcrumb `Covenant / {Loan} / {Period} / Actuals` with loan and period switchers (08 §2).
2. Surface status band: pipeline chips Extracted → Normalized → Reconciled → Computed + control-total chip + tie-out chip + this-surface readiness line ("2 items block this period here").
3. Primary work: the exception queue (Region 1) — only what needs a human.
4. Secondary context: computed results (Region 2) — metrics block, tests block, tie-out strip.
5. Evidence/proof: the Evidence panel (summonable right, lit-row trace) and the summonable full grids (Region 3 overlay tabs: Statement grid · Rent roll).
6. Actions: per-row Confirm / Correct / Resolve / Escalate-to-Setup; "Open in Review" for dispositions.
7. Activity/history: this period's normalization/computation quiet log, collapsed at the foot ("what ran, what auto-applied, when").

Absent by design: charts (grids, chips, and signed numbers out-encode any chart candidate here; trends live on Loan Detail under the doctrine); portfolio context; narrative drafting; certify/send controls (Certificate owns the ceremony); any generic chat box (Ask lives in the canvas panel with selection grounding only — agent presence is contextual, not chrome).

## 8. Page anatomy and regions

**Status band**
- Purpose: pipeline truth at a glance — the honesty layer.
- Content: four `StageChip`s (state + exception count via shared `CountBadge`), `ControlTotalChip` ("Totals tie ✓" / "2 breaks"), `TieOutChip` (enum state), readiness contribution line.
- Persistence: persistent, pinned directly under the shell header.
- Interaction: every chip is an instrument — click filters/scrolls to its owning rows (the chart-doctrine instrument rule applied to chips).
- Minimum: 56px height, full width; never collapses and never hides a blocked state.
- Why this form: status must be readable before any scroll; chips carry state + count + link in one 56px band — a dashboard pane would duplicate what the queue already is.

**Region 1 — Exception queue (primary work)**
- Purpose: the only mandatory work on the surface.
- Content: uniform-row table grouped by class, fixed order: control-total breaks → unmapped COA codes → roll/summary disagreements → book-basis flags → definition gaps. Group headers carry counts. Groups with zero exceptions render as one quiet summary row ("Income lines — 41 mapped from memory · totals tie ✓") per the exception-first law (R3 research: Numeric).
- Row anatomy: class icon (Lucide) · source code + source label · proposed target (or the break's two values + signed delta) · confidence · status.
- Expansion — **source-beside-decision**: the row opens in place as a two-cell split. LEFT: the exact source cell/row rendered from the sheet with the region lit (DocView sheet renderer). RIGHT: the decision form — proposed canonical line, live effect on the group subtotal, `DownstreamImpactStrip` naming every metric/test that moves, memory note ("will be remembered for {property}/{PMS}"). Actions: Confirm (typed) / Correct / Escalate to Setup.
- Persistence: persistent; collapses to a single all-clear line when empty ("No exceptions — all mappings remembered, totals tie ✓").
- Minimum: 720px width (primary work window), 240px height before internal scroll.
- Resize/collapse: groups individually collapsible; resolved rows animate to their group summary (reduced-motion: instant).
- Why inline expansion, not a pane: the mapping decision needs source ⇄ proposal ⇄ subtotal effect simultaneously (05 §4, "Confirm COA mapping" row) at row grain; a persistent second pane would outlive the decision and violate pane discipline.

**Region 2 — Computed results**
- Purpose: what the engine holds true right now — inspection, not editing.
- Content, three stacked blocks:
  - *Metrics block*: cards for NOI, annualized debt service, DSCR, debt yield, occupancy, liquidity — each **per the loan's own definitions, never a global formula** (05 §5). Card anatomy: label · value (Geist Mono, `tabular-nums slashed-zero`) · three-actor class badge · definition summon (verbatim `definition_text` + source region, popover beside the card) · lit-row click into Evidence. A metric with an unpinned definition renders its BLOCKED card — the engine refuses a DSCR test with an unspecified debt-service basis (semantic-risk register 05 §7.1) — never a guessed number.
  - *Tests block*: `TestResultRow` per requirement with a test or monitored threshold: requirement title · basis badge (`covenanted`/`monitored` — structural, never inferred) · verdict chip (pass/watch/shortfall/breach) · signed headroom in BOTH denominations (ratio/pp + the driving metric's native units — dollars of NOI for DSCR-class tests, units for occupancy; canon phrasing target: "$240K of NOI from a DSCR shortfall") · watch-band note ("watch band: floor × 1.05 authoring default [VERIFY against library; never hard-coded], visible, never silent" — 05 §7.4). Reporting-only loans render the honest empty: "No recurring ratio covenants in this agreement (reporting-only — evidence: SLOT-3). Monitored thresholds below."
  - *Tie-out strip*: prior-package field deltas as enum'd rows — `reconciled / partially_reconciled / unreconciled / provisional` (R3 research: Modern Treasury expected-vs-actual enum incl. `tentatively_reconciled` for thin early data). Clean rows collapse to one summary ("14 fields tie ✓"). Failed rows persist and summon the 50/50 compare; disposition links to Review.
- Persistence: persistent below Region 1; promotes to the top when the queue is empty.
- Minimum: shares the 720px primary work window with Region 1 (same pane, vertical scroll).
- Why one pane: exceptions and results are one narrative ("what needs me, then what came out"); splitting them into siblings would force a co-visibility neither needs permanently.

**Region 3 — Normalized grids (summonable overlay tabs)**
- Purpose: the whole cloth, on demand — never forced on a clean period.
- Content, two full-width tabs (view-switch of the big pane per the ruled pane model — not a third pane):
  - *Statement grid*: full COA spine rows × 12 month columns + total; group subtotal rows; per-line tie marks; control-total footer rendering the sheet's own totals beside computed — evidence fixture: Total OpEx $1,686,050 ✓ · NOI $1,218,877 ✓. Spine cells show source code + canonical label; hover reveals the mapping stamp.
  - *Rent roll*: dedup banner (322 lease rows → 301 units — future-lease dual rows collapse; the 301 ≠ 322 rule); unit-status taxonomy table (the roll's own six statuses with counts); aggregates block tied to the roll's own summary (301 ✓); occupancy block honest-BLOCKED until the canonical reader lands (gap 7).
- Persistence: summonable; Esc returns to the exception/results view exactly as left (drill state is view state, never mutation — R3 research: Sigma view-underlying-data).
- Minimum: full pane width; internal horizontal scroll only (the page never scrolls sideways).
- Why overlay tabs: the grids are reference, not decision surfaces; view-switching honors "one big pane + view-switching" without spending the split.

**Evidence panel** — the reused review-room Evidence panel (lit-row trace; the U1-F1 wrong-row fix lands first per `cross-cutting/provenance-lit-row-trace.md`). Summonable, pinned right, 400–420px. Renders the source artifact with the exact line/cell lit and **staying lit**. Proof beside the claim, never a context-destroying modal (kit law 6).

**Canvas panel** — floating, separate slot (does not count against the split): `AskPanel` with `HighlightToAsk`; the selection's provenance refs travel with the question; never load-bearing for a decision; Esc dismisses.

**Modal** — exactly one: the void-on-change warning before a confirming act on a certified period. A typed act with legal weight, so a modal is permitted (kit law 5). No other modals exist on this surface.

## 9. Co-visibility matrix

| Datum/surface A | Datum/surface B | Must be simultaneous? | User decision enabled | Default topology | Fallback |
|---|---|---:|---|---|---|
| Source T-12 cell (lit) | Proposed mapping + subtotal effect | YES | confirm/correct mapping | inline row expansion, two cells | source stacks above decision (<1280px) |
| Extracted grid slice | Sheet's own total + signed delta | YES | resolve control-total break | break-row expansion with pinned totals line | same, stacked |
| Metric value | Its definition text (verbatim) | on demand | trust/inspect | popover beside the card | overlay sheet |
| Any figure | Its full source chain | on demand | verify | Evidence panel right, lit row persists | overlay sheet (narrow) |
| This period's field | Prior sealed package's field + both chains | YES during tie-out inspection | judge which period is right | 50/50 split — the one two-work-window use here (max-2 law) | labeled stacked tabs below 1280px; no silent compression |
| Roll-derived aggregates | The roll's own summary totals | YES | resolve disagreement | one aggregates block, both columns side by side | same |
| Blocked metric card | The requirement's definition text + candidate bases | YES at the gate | escalate to authoring | gate content inline on the card | same |
| Remembered mapping | Its memory provenance ("learned {date} from {who}") | on demand | inspect/correct | hover stamp → click opens the entry | inline row |
| Exception queue | Statement grid | NO | — | overlay tab replaces the view | chips link back |

No pane exists merely because information exists: the grids are summonable, the evidence is summonable, and the only persistent split lives inside an expanded exception row for the duration of that decision.

## 10. Layouts and viewport behavior

Default pane topology and ratios (rail expanded; Evidence summoned where noted):

| Viewport | Rail | Primary work window | Evidence panel | Resolver split | Tie-out compare |
|---|---|---|---|---|---|
| 1440px | 240px | ~1150px (Evidence closed) / ~750px (open) | 400px summoned | 55/45 (~610/~500px) | 2×~575px split |
| 1728px | 240px | ~1440px / ~1020px | 420px pinned | 55/45 | 2×~720px split |
| 2048px | 260px | ~1740px / ~1320px | 420px pinned | 55/45 | 2×~880px split |
| <1280px | 48px icon rail | full width | overlay sheet | stacked | labeled stacked tabs |

- **1440px**
  - Rail expanded (240px) or user-collapsed; primary work window ~1150px.
  - Status band full-width; Region 1 rows full-width.
  - Expanded resolver splits ~55/45 (source left ~610px / decision right ~500px).
  - Metrics block: 3 cards per row. Evidence summon (400px) narrows primary to ~750px; metrics reflow to 2 per row.
  - Tie-out 50/50 compare: 2×~575px — both ≥560px, split permitted (08 §9).
- **1728px**
  - Primary ~1440px with rail expanded; Evidence pins at 420px leaving ~1020px primary; resolver split holds 55/45.
  - Statement grid overlay shows the spine (240px) + ~10 of 14 month columns; the remainder scrolls horizontally inside the grid container, never the page.
- **2048px**
  - Primary + pinned Evidence + floating canvas coexist without contention.
  - Statement grid shows all 14 columns (240px spine + 14×~92px) with no internal scroll.
  - Metrics 3 across with widened headroom readouts. No chart appears at any width (doctrine test fails for every candidate here).
- **Narrow/compact fallback (<1280px)**
  - Single work window; Evidence becomes a labeled overlay sheet.
  - The tie-out compare becomes two labeled stacked tabs ("This period" / "Prior sealed") — no silent compression.
  - Resolver rows stack source above decision; chips wrap to two lines before truncating never.
- Minimum viable dimensions: 1152×720 (frame law 08 §9); below it the spine surface shows the "larger window required" state while the status band remains readable.
- Focus behavior: expanding a resolver row scrolls it to the top third and dims sibling rows one gray rung; Esc collapses.
- Compare behavior: the 50/50 is scroll-synchronized by field; selecting a field on either side lights both chains.
- Proof/source behavior: lit rows stay lit until dismissed (frame contract 08 §7); summoning Evidence never reflows the figure that was clicked out of view.
- Tab stacking/replacement: Region 3 tabs replace the big pane's view; Esc unwinds summons in reverse order (Evidence → overlay tab → resolver row) before ever navigating.

## 11. Components and exact anatomy

Reused (named from the snapshot/repo):
- `CovenantShell` — chassis, rail, 48px header, breadcrumb altitude gate.
- `CommandPalette` / `SearchPalette` — places/objects/computed answers per 08 §5.
- `AskPanel` + `HighlightToAsk` — canvas slot, selection-grounded.
- Shared `CountBadge` — the ruled single badge component (snapshot §4: consolidation ruling).
- The review-room **Evidence panel** with the lit-row trace — adopted here after the U1-F1 wrong-row fix.
- DocView's sheet renderer — renders source cells inside resolvers and the Evidence panel.

NEW components (all in `src/components/covenant/actuals/`):
- `ActualsStatusBand` — parts: `StageChip` ×4 (label, state icon (Lucide), `CountBadge`), `ControlTotalChip`, `TieOutChip`, readiness line. Every chip an instrument.
- `ExceptionQueue` — uniform-row table. Parts:
  - `ExceptionGroupHeader` — class label + count + collapsed-summary variant.
  - `ExceptionRow` — icon · source code/label · proposal or break values · confidence · status.
  - `ExceptionResolver` — the in-row split: `SourceCellPane` (DocView sheet render, region lit) + `DecisionForm` (canonical-line picker seeded with the proposal · live subtotal-effect line · `DownstreamImpactStrip` naming moved metrics/tests · `MemoryStamp` "will be remembered for {property}/{PMS}" or "learned {date} from {who}") · actions Confirm / Correct / Escalate to Setup.
- `MetricsBlock` / `MetricCard` — parts: label · `DefinitionPopover` summon (verbatim definition text + source-region link) · mono value · three-actor class badge · provenance underline (lit-row ref) · `BlockedMetricCard` variant (names the unpinned definition; "Resolve in Setup" route).
- `TestResultTable` / `TestResultRow` — parts: requirement title · `BasisBadge` (covenanted | monitored) · `VerdictChip` (pass/watch/shortfall/breach; the prop type makes `breach` unrepresentable when basis=monitored — the vocabulary law in the type system) · `HeadroomReadout` (signed, dual denomination, mono) · watch-band note. These three atoms serve Loan Detail's covenant strip and Review too — build once here, share.
- `TieOutStrip` / `TieOutRow` — parts: field name · this-period value · prior sealed value · signed delta · enum state chip (`reconciled / partially_reconciled / unreconciled / provisional`) · compare summon.
- `TieOutCompare` — the 50/50 work-window split; both sides render value + chain via the Evidence renderers; scroll-synced; "Disposition in Review" footer link.
- `StatementGrid` — virtualized (~150 rows × 14 columns). Parts: COA spine cell (code + canonical label + mapping stamp on hover) · month cells (mono, right-aligned) · `SubtotalRow` (one gray rung darker ground) · `TieMark` per line — the DataSnipper adaptation: a small agreed-mark (Lucide check) or exception-mark (Lucide x) in the ruled severity tokens, click → the line's tie detail (R3 research: DataSnipper Validation-V/Exception-X tickmarks) · `ControlTotalFooter` (sheet's own total beside computed, tie state).
- `RentRollNormalizedView` — parts: `DedupBanner` ("322 lease rows → 301 units; future-lease dual rows collapsed") · status-taxonomy table (six evidence statuses with counts) · `AggregatesBlock` (row-derived vs the roll's own summary, tie state per line) · `BlockedOccupancyCard` (the honest gap-7 block).
- `DefinitionPopover` — parts: verbatim `definition_text` (quoted, never paraphrased) · source-region link (lights the agreement page/region in Evidence) · the pinned semantic choices for this loan (debt-service basis, annualization rule, occupancy denominator) with their confirmation stamps · "Amend in Setup" link.
- `VoidWarningModal` — the surface's one modal. Parts: restatement of loan, period, certification (who/when/hash short-form) · exactly what act is about to void it · typed proceed/cancel. Appears only when the period is certified.
- `QuietLogFoot` — collapsed period-scoped ActivityEvent projection ("42 mappings applied from memory · totals tied · computed in {engine version}").
- `AllClearRow` — the zero-exception state object ("No exceptions — all mappings remembered, totals tie ✓"), calm, not celebratory.
- `BlockedStageCard` — the shared fail-closed render for gate objects: what is missing · why it blocks · evidence · typed options · owning fix route. Consumed by chips, metric cards, and the occupancy block so every honest block reads the same.

## 12. Interaction specification

- Selection: single-row focus model in the queue; selecting any figure sets the provenance context (highlight-to-ask picks it up with its refs).
- Hover: provenance underline on every material figure; mapping stamps ("learned {date} from {who}") on spine cells; confirmer stamp on confirmed values (06 §7).
- Focus: visible focus ring (ruled accent family) on rows, chips, cards; focus order = the §7 scan order.
- Keyboard:

| Key | Action |
|---|---|
| `J` / `K` | next/previous queue row |
| `Enter` | expand/collapse the resolver |
| `C` | confirm the proposal (typed act) |
| `X` | correct — focus the canonical-line picker |
| `A` | escalate to Setup (definition authoring) |
| `T` | summon the tie-out compare for the focused row |
| `S` / `R` | summon Statement grid / Rent-roll overlay tab |
| `V` | toggle the Evidence panel |
| `Esc` | close topmost summon → collapse row → never navigates first |
| `⌘K` | palette; G-chords per frame (`G L` Loans, `G H` Home, …) |
| `⌘Z` | within-session: re-open the last confirmation as a correction draft (versioned, never a silent revert) |

- Editing and validation: the decision form validates target-line existence and shows the subtotal effect live before commit; confirm is an explicit button press, never implicit blur-save.
- Bulk action: multi-select is allowed only within one proposal group (same proposed canonical target); bulk-confirm re-states the shared target and the combined downstream impact. No bulk across heterogeneous mappings — per-decision provenance would blur.
- Undo/recovery: corrections version mappings; the impact strip shows what recomputes; on certified periods the void warning interposes before anything commits.
- Sorting/filtering: queue groups in fixed class order; chips filter; the grids filter by group/month. Saved views are not needed here — the queue is computed, not curated.
- Drill-down and return: metric card → Evidence chain → DocView (deeper) → back restores scroll + lit state; overlay tab → Esc returns exactly where you were.
- Source-linked selection: click any figure → its exact source line/cell lights in Evidence and **stays lit** (the ruled lit-row mechanic). The one-gesture law governs creation too: confirming a mapping mints the value and its source-region reference as one object, never separately (R3 research: DataSnipper — "the extracted value lands in the cell AND makes a cross-reference back to the original area in one gesture").
- Save/persistence: confirmations persist immediately with actor identity; all view state (expanded rows, summoned panes, tabs) is ephemeral and restorable via URL params for deep links.
- Collaboration/commenting: none here beyond identity stamps — annotation and review notes belong to the Review Room.

## 13. Visual craft direction

- Typography roles: surface title + group headers in the app sans (13px/600 caps-tracked for group headers); queue rows 13px; metric labels 12px; metric values 20px Geist Mono; test rows 13px with 15px headroom readouts; chip labels 12px.
- Financial-number treatment: Geist Mono with `tabular-nums slashed-zero` (ruled), right-aligned columns; signed values always carry an explicit `+`/`−` glyph — sign is never conveyed by color alone; no accounting parentheses.
- Spacing rhythm: 8px base grid; 12px row padding; uniform row heights — queue 40px, grid rows 32px, test rows 44px (ruled uniform-row law).
- Density: a working surface for long sessions — compact by default, no card decoration around tabular content, no empty-space theater.
- Open ground vs earned boundaries: open, not boxed. Group separation is whitespace plus a hairline only where groups must read apart; the resolver's two cells separate by one hairline, not frames.
- Dividers/elevation: gray-ladder rungs only (the ten-rung ramp, adopted 2026-08-07); overlay tabs and the Evidence panel take one rung of elevation + hairline; rows never carry shadows.
- Semantic color: verdict/severity strictly from the ruled status tokens. Vanta's orange-attention/red-overdue grading maps onto the **watch vocabulary** — watch = attention-not-overdue; shortfall/breach = the failure grades — rendered with ruled tokens only, zero new values proposed (R3 research: Vanta test priorities). Accent #7189FF family exclusively for interactive/selected states (chip focus, lit-row ring, links); the accent is never a severity signal.
- Certified-sheet paper: not painted here. This surface never renders the certified sheet; certified-state figures reference the record with a hash chip in the standard surface treatment (the six ruled hexes belong to the Certificate surface alone).
- Focus/selected/hover: hover = one gray rung shift; selected = accent-tinted rail edge (ruled tint `#A9B5FF` at token opacity) + focus ring; lit source rows use the existing Evidence lit treatment unchanged.
- Chart style: no charts on this surface by design — the doctrine's out-encode test fails for every candidate (chips, signed numbers, and tie marks carry more per pixel).
- Motion: lit-row pulses once then holds steady; chip state changes cross-fade 120ms; resolved rows collapse 150ms ease-out. Reduced-motion: no pulse, instant states.
- Long-session ergonomics: keyboard-complete queue processing; the all-clear line is calm; nothing blinks, bounces, or celebrates.

## 14. Benchmark research and synthesis

| Product | Limited role | Official evidence | Exact pattern to take | Covenant adaptation | What to reject | Why this reference beats alternatives |
|---|---|---|---|---|---|---|
| Numeric | close/recon management | https://www.numeric.io/product/reconcile · https://www.numeric.io/product/close | autosubmit-when-matched: clean recons complete themselves; a single exception-view workspace | clean lines collapse to group summary rows; only exceptions persist; materiality bands gate anomaly flags | AI-drafted explanations as default package content | it names the law this whole surface obeys |
| Modern Treasury | expected-vs-actual matching | https://docs.moderntreasury.com/reconciliation/docs/overview · https://docs.moderntreasury.com/v1.0/docs/processing-tentative-reconciliation | enum'd match states (`unreconciled/partially_reconciled/reconciled`) + provisional `tentatively_reconciled`; humans see only the residue | the tie-out strip's enum incl. `provisional` for pre-review statements; manual matches recorded like automatic ones | payment-grade continuous streaming — Covenant matches at ingest, periodically | typed states beat free-text recon notes |
| DataSnipper | figure↔source provenance | https://knowledge.datasnipper.com/how-to-extract-data-from-a-document · https://knowledge.datasnipper.com/how-to-validate-data-on-a-document | one-gesture value+provenance objects; Validation-V/Exception-X tickmarks; per-document snip index; saved matching templates | per-line `TieMark`s on the statement grid; confirmation mints value+source-ref as one object; saved "templates" = the per-property/PMS mapping memory | Excel as host; free-form snip targets (Covenant constrains provenance to normalized lines) | its snip IS the lit-row trace, proven in audit practice |
| FloQast | the tie-out gate | https://www.floqast.com/blog/strict-tie-out-mode | sign-off only when tied out (explicit tolerance); data drift voids signatures; trial-balance completeness check | "no unmapped line at certify" readiness input; corroborates the ruled void-on-change | cloud-folder workbooks as the evidence substrate | strongest precedent for tie-out-as-gate |
| Puzzle | statement→record drilldown | https://help.puzzle.io/en/articles/11385709-financial-statements-in-puzzle | every statement figure is a live door to its filtered underlying transactions | metric → contributing normalized lines → source cells; read-only during inspection (edits go upstream to mapping) | single-entity COA assumptions — Covenant normalizes heterogeneous PMS charts | cleanest fixed-spine drill model |
| Sigma | aggregate+underlying on demand | https://help.sigmacomputing.com/docs/drill-into-data · https://help.sigmacomputing.com/docs/view-underlying-data | maximize-to-see-underlying at the exact granularity that fed the aggregate; drill is view state | Region 3 overlay grids; metric cards expand to contributing lines; Esc restores untouched | ad-hoc re-slicing beside the gates | proves drill without mutation |
| Vanta | graded urgency/freshness | https://help.vanta.com/en/articles/11345547-test-priorities-and-slas | orange attention-not-overdue vs red overdue; evidence freshness driving test state | the watch band before failure; stale-source badges; grading rendered in ruled tokens only | control/framework indirection | the watch-vocabulary precedent |
| Finley | borrower-side covenant objects | https://docs.finleycms.com/ | computed covenants vs dated deliverables as separate objects; gates read computed state | tests block and holding checklist stay separate objects; readiness reads TestResults | lender-facing system-of-record framing | closest domain object model |
| Setpoint | certify-by-recompute | https://www.setpoint.io/calculation-agent/ | independent recomputation from source as the trust mechanism; computed-vs-reported diff as the core artifact | recompute-on-view; computed-vs-stated diff as a blocking check downstream | agent-in-the-middle settlement role — Covenant reports, never settles | grounds "Covenant calculates" in market practice |
| BankStride / nCino | anti-pattern | https://www.bankstride.com/tickler-tracking-management | — (covenant-as-tickler: due dates + an upload chute, zero computation) | nothing except reminder cadence, owned by Calendar | the entire model: no headroom, no provenance, opaque numbers inside uploaded PDFs | the void this surface exists to fill |

Synthesis: the surface becomes original because no benchmark combines a **contract-defined** spine with exception-first normalization. Numeric and Modern Treasury supply the law that agreement is silent and only residue reaches a human; DataSnipper supplies the one-gesture provenance object, generalized from free rectangles to structured normalized lines (Puzzle and Sigma supply the drill grammar that makes every aggregate a door); Vanta supplies graded urgency mapped onto the ruled verdict vocabulary; FloQast and Setpoint supply the gate semantics the downstream surfaces consume. What none of them have — and what makes this borrower-side lender reporting rather than a close tool: metrics computed per each loan's own definitions with fail-closed refusal on unpinned semantics, a structurally enforced monitored/covenanted vocabulary with basis echoed on every result, and a reporting period that seals into an addressable record.

## 15. Domain references

Terminology, expected data, and workflow semantics only:
- Finley (covenant/deliverable object split; borrowing-base validate-then-calculate pipeline) and Setpoint (calculation-agent recomputation language) — borrower-side debt-compliance vocabulary.
- FloQast / Numeric — close vocabulary: tie-out, flux, completeness, sign-off.
- The JLL servicer questionnaire in evidence (SLOT-4/5) — the occupancy-by-month-end, capital-improvement, and management-fee-band fields this surface's outputs ultimately feed via the Composer.
- BankStride / nCino — the named anti-pattern (covenant-as-tickler).

**Domain authority does not equal visual authority** — none of these products' screens govern a pixel here. And covenant semantics come from the loan documents and Terry, never from any referenced product (kit law 8: no invented thresholds, formulas, cadences, or definitions — "per the loan's own definition" is the only permitted default).

## 16. Accessibility, performance, and safety

- WCAG contrast and focus: AA on all ruled-token pairings, verified by canvas readback per the oklch caution (snapshot §4), never by eyeball; visible focus ring on every interactive element.
- Verdict and stage chips always carry text labels — meaning is never color-only; signed headroom carries an explicit sign glyph.
- Keyboard completeness: every §12 action reachable without a pointer; resolver forms fully tabbable; the 50/50 compare navigable by field with synced focus.
- Screen-reader semantics: the queue as `grid` with row-level `aria-expanded`; stage chips as a `status` group with a polite live region announcing transitions ("Reconciled: 2 exceptions"); lit-row activation announces "source line highlighted: {label}"; blocked cards read their reason text verbatim.
- Table virtualization/large data: `StatementGrid` (~150×14) and the roll view (322 rows) windowed; uniform row heights make virtualization exact; wide content scrolls inside its own container — the page never scrolls sideways.
- Loading and latency feedback: stage chips report real pipeline state (no fake progress); skeletons only for known-shape blocks; recompute-on-view guarantees no stale render (04, Review lane), with honest "computing…" text past 300ms.
- Destructive action confirmation: nothing here destroys. The one interposing modal is the void-on-change warning on a certified period, restating exactly what will void and why.
- Certify and external-send safety: this surface holds no certify or send control; its contribution is honest readiness inputs (no unmapped lines, totals tie, tie-out clean and final). It can block certification; it can never perform it.
- Source immutability: originals are immutable bytes+hash; normalization writes new objects; corrections version, never overwrite (02 §5); sealed periods are read-only forever, fully inspectable.
- Auditability: every confirm/correct/resolve is an identity-stamped ActivityEvent with evidence refs and downstream impact; the quiet-log foot renders the period's own trail; the append-only log is the record of what the analyst did while the borrower was away.

## 17. Acceptance tests and fixtures

Fixtures: **CAL-FYE2018** (the Calloway Park evidence spine: SLOT-1 T-12, SLOT-2 roll, SLOT-3 agreement) and **BEXLEY-CANON** (demo book). All tests ticket-ready.

1. **Control totals tie (CAL-FYE2018).** Given the ingested T-12 with all mappings confirmed — when the pipeline completes — then the Reconciled chip is `done`, the statement grid footer shows Total OpEx **$1,686,050 ✓** and NOI **$1,218,877 ✓** beside the sheet's own totals, and the cross-statement ties render clean (Net Income $460,159; interest $644,017; cash $138,940).
2. **Exception-first collapse.** Given zero exceptions in the income group — then the group renders exactly one summary row and its full lines appear only in the Region 3 grid. Given an empty queue — then the `AllClearRow` renders and Region 2 promotes to the top.
3. **Unmapped code, ask-once (CAL-FYE2018).** Given a T-12 carrying one unmapped code (a new `4135.000 RAF`-class line) — when the preparer expands the row — then the exact source cell lights in the left cell; when they confirm — then the mapping persists with confirmer identity, the impact strip names the moved subtotal and NOI, and next period the same code auto-applies with a "learned {date} from {who}" stamp and is **never re-asked**.
4. **NOI-boundary visibility (CAL-FYE2018).** Given the Asset Management Fee line ($114,700, below NOI as non-operating in this book) — then its mapping decision and stamp are visible on the spine cell; when corrected — then NOI recomputes and the impact strip lists every affected metric/test (05 §7.5).
5. **Fail-closed DSCR.** Given a loan whose debt-service basis is unpinned — then the DSCR metric card and test row render BLOCKED naming the missing definition with "Resolve in Setup," and no DSCR number renders anywhere on the surface (05 §7.1: the engine refuses the test).
6. **Vocabulary law (BEXLEY-CANON).** Given occupancy 268/301 = **89.04%** against the **monitored** 90% floor — then the verdict renders **shortfall** with basis badge `monitored` and signed headroom "−0.96pp · 3 units"; and the `VerdictChip` type test proves `breach` is unrepresentable with basis=monitored. Contrast fixture: Westbrook Flats' covenanted 1.20x DSCR can reach breach.
7. **Dual-denomination headroom.** Given a DSCR-class watch verdict — then the headroom readout renders both denominations (ratio + dollars of the driving metric; canon phrasing target "$240K of NOI from a DSCR shortfall") in mono with explicit signs.
8. **Tie-out enum + provisional.** Given prior-sealed-package deltas — then each field row carries an enum state; given a pre-review statement — then the state is `provisional` and the clean-tie-out readiness input stays unsatisfied; given a failed row — then the 50/50 compare opens with both chains scroll-synced, and disposition is offered only as "Open in Review."
9. **Rent-roll normalization (CAL-FYE2018).** Given the April 2018 roll — then 322 rows dedup to 301 units (banner states it), the six status counts match evidence (259 / 18 / 2 / 13 / 2 / 7), aggregates tie to the roll's own summary (301 ✓), and physical occupancy 279/301 = **92.69%** renders **only when the canonical reader exists**. Until F3 lands, the `BlockedOccupancyCard` renders the honest gap-7 block and no occupancy number appears anywhere on the surface.
10. **Stale source.** Given a replaced T-12 after confirmations — then affected lines and downstream metrics flag stale with diff links and per-line re-confirm; given the period is certified — then the void warning interposes and proceeding voids visibly (never silently).
11. **Lit-row correctness (U1-F1 class).** Clicking NOI lights exactly the T-12 rows that compose it, and the lit state persists through scroll; clicking a grid cell lights its source sheet cell — verified against the evidence sheet's coordinates.
12. **Keyboard fixture.** Process three exceptions end-to-end with keyboard only (J/K/Enter/C/X/Esc); `S`/`R`/`T`/`V` summon and Esc unwinds strictly in reverse order without navigating.
13. **Viewport fixtures.** At 1440/1728/2048/1152×720: split minimums honored (both compare windows ≥560px or stacked tabs); resolver stacks below 1280px; grids scroll internally only; the "larger window required" state appears only below 1152×720.
14. **Permission fixture.** Reviewer role: all actions disabled-with-reason; a foreign-org loanId returns an honest 404 (the send-vertical pattern); a sealed period renders fully read-only with the seal explained.
15. **Data-integrity fixture.** A mapping correction creates a new version (prior kept, history intact); the quiet log carries both acts; no path exists that edits a normalized line in place.
16. **Accessibility checks.** Chip live-region announcements fire on state change; queue grid semantics verified; AA contrast readback passes on every verdict chip in every severity state.
17. **Control-total break gate (CAL-FYE2018, seeded).** Given one expense line mapped to the wrong group so Σ lines ≠ $1,686,050 — then the Reconciled chip shows `exceptions(1)`, the break row shows computed vs sheet's-own vs signed delta, the agent's likeliest-cause proposal renders as a proposal (not applied), and resolving via re-map recomputes and clears the chip.
18. **Bulk confirm within one proposal group.** Given three new source codes all proposed to the same canonical line — then multi-select + bulk-confirm restates the shared target and combined impact and writes three distinct mapping entries; given proposals to different targets — then bulk action is unavailable.
19. **Deep-link state.** `/covenant/{loan}/{period}/actuals?focus={exceptionId}` opens with that row expanded and its source lit; the URL from a Home your-move row reproduces exactly this state; back returns to Home.
20. **Quiet-log integrity.** After a session of two confirms and one correction, the `QuietLogFoot` projection lists all three acts with actor and downstream impact, in order, and the app-wide ActivityEvent store contains the identical rows (one store, no divergence — U3-F2 class check).
21. **Benchmark challenger review.** A reviewer armed with the §14 table confirms: clean lines consume no permanent space (Numeric); tie-outs are enum'd with a provisional state (Modern Treasury); tie marks and one-gesture provenance behave per DataSnipper; and nothing anywhere renders a tickler-style due-date-only covenant row (BankStride rejection).

## 18. Build plan

- Dependencies (named):
  - F2 persistence — normalized-line, mapping/memory, metric/test tables with tenancy FKs (gap 3).
  - F3 rent-roll canonical reader — unblocks the occupancy resolver and fixture 9 (gap 7); this surface ships before it, with the block rendered honestly.
  - The orchestration spine wiring DOCUMENT IN → normalize → compute (V2 pattern, gap 5).
  - The lit-row component contract with the U1-F1 wrong-row fix (`cross-cutting/provenance-lit-row-trace.md`) — fix-first, then adopt here.
  - The engine F1–F11 spine — built, green; reuse, never rebuild.
- Foundation work: the route + resolver under `(covenant)` following the certificate route's proven pattern (own route, own resolver, own tests); TestResult/verdict type definitions with the basis-gated verdict union (the vocabulary law enforced in the type system before any pixel renders).
- Components to build first, in order: `ActualsStatusBand` → `ExceptionQueue` + `ExceptionResolver` → `MetricsBlock`/`MetricCard` with `BlockedMetricCard` → the shared verdict atoms (`BasisBadge`, `VerdictChip`, `HeadroomReadout` — consumed later by Loan Detail and Review; build once here) → `TieOutStrip`/`TieOutCompare` → `StatementGrid` → `RentRollNormalizedView` → `QuietLogFoot`.
- Vertical slice (the send-vertical pattern): one route, one loan+period — ingest the CAL-FYE2018 T-12 through the real F-series, render the queue with one seeded unmapped code, confirm it, and watch NOI compute and the control totals tie on screen. Engine data end to end; zero fixture strings.
- Migration from fixture data: `book.ts` demo values never render here — this surface launches engine-only. Bexley canon values enter as engine fixtures, not display strings, retiring the gap-6 habit for this surface from day one.
- Rollout/feature flag: `covenant-actuals-surface`; ship behind the flag until fixtures 1–8 and 11 pass; the occupancy block ships in its honest BLOCKED state until F3 lands (launch is never gated on it).
- Proof artifacts required: screen recordings of fixture 3 (exception resolve with lit source), fixture 1 (ties rendering), fixture 6 (shortfall vocabulary), and the blocked occupancy card; a canvas-readback token report proving zero non-ruled values.
- Final gate: `PASS` when all §17 fixtures pass and the duplication audit confirms no second home for mappings, metrics, or verdicts; otherwise `ADJUST` — no `REBUILD` path exists for an unbuilt surface.
