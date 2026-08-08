# 05 — COVENANT-TRACKING MODEL MAP
## Requirement extraction → actuals → tests → verdicts → package, with the engine-to-surface interface contract

Scope firewall honored throughout: nothing here invents a threshold, test definition, or metric semantic. Every requirement example below is **read from the evidence loan agreement** (Calloway Park, Fannie Mae Form 6001.NR + Forms 6220/6241 riders, executed 2017) or from the demo canon, and labeled as such. Formula correctness is expressly not certified here (that is a separate arithmetic/code audit).

## 1. Requirement taxonomy (the requirement side)

Grounded in SLOT-3 (the executed agreement), the snapshot's description of the knowledge corpus (extraction checklist, covenant census), and the ruled monitored-vs-covenanted distinction.

### 1.1 The taxonomy

| Kind | Sub-kind | `basis` | Cadence shape | Evidence instance (SLOT-3 unless noted) |
|---|---|---|---|---|
| Reporting obligation | Periodic financial statement | covenanted (it is a covenant to deliver; there is no ratio test) | Recurring, due-rule | §8.02(b)(1): quarterly YTD income & expense statement, **Q+45 days** (Q1–Q3). §8.02(b)(2)(A)/(H): annual statements incl. cash flows, **FYE+120 days** |
| Reporting obligation | Rent schedule | covenanted | Recurring | §8.02(b)(3): quarterly (Q+45d) and annual (FYE+120d) rent schedule with named fields: tenant, space, lease expiration, current rent, paid-through date |
| Reporting obligation | Annual certification bundle | covenanted | Annual | §8.02(b)(2)(E): written certification (single-asset status; building-code notices; rezoning; liens) · (F): security-deposit accounting with institution/account detail · (G): ownership-change confirmation |
| Reporting obligation | Rider-added report | covenanted | Annual | Form 6241 Article 16: ENERGY STAR Energy Performance Metrics report with score, Source EUI, period, property ID |
| Reporting obligation | On-request items | covenanted, conditional | On written request (rate-capped: ≤1 per 6 months absent default) | §8.02(b)(4): monthly statements, leasing report, REO statement, ownership statement |
| Financial covenant | Ratio test (DSCR, debt yield, liquidity, net worth) | **covenanted** where the agreement carries a recurring test | Recurring per the agreement | **None in SLOT-3** (verified: no recurring ratio covenant exists in the executed 6001.NR). Canon contrast: Westbrook Flats carries a real 1.20x DSCR covenant. This asymmetry is the product's core modeling fact |
| Financial covenant | Conditional/event test | covenanted, event-triggered | On event | §9.03: casualty-restoration DSCR ≥1.0x on a 30-year-amortizing calculation; post-casualty <1.25x payment-adjustment discretion. Never rendered as a recurring test |
| Monitored threshold | Lender/servicer-watched or self-watched metric with no covenant behind it | **monitored** | Recurring (watch cadence) | Demo canon: Bexley 90% occupancy floor is monitored; 268/301 = 89.04% is a **SHORTFALL**, never a breach. Servicer evidence: the JLL questionnaire asks physical occupancy monthly-by-quarter — an ask, not a covenant |
| Operational covenant | Insurance, taxes, reserves, single-asset, transfer restrictions | covenanted, mostly event/annual | Mixed | §9.02 insurance; §12.02 imposition deposits; Form 6220 modifies replacement-reserve deposits; §4.02(d) single-asset status (certified annually via (2)(E)) |
| One-time item | Post-closing deliverables, repair schedules | covenanted, one-shot | Once | Repair/replacement completion certifications (§13) |

### 1.2 RequirementRecord — the confirmed shape (consumed by every downstream step)

```text
RequirementRecord {
  reqId, loanId
  kind: financial-covenant | operational-covenant | reporting-obligation | monitored-threshold | one-time | conditional
  basis: covenanted | monitored              ← structural, never inferred (C-9)
  title, definition_text (verbatim from the source), definition_source_region (doc/page/region)
  test?: { metric_ref, comparator, threshold, calculation_notes_source_region }   ← only when the agreement defines one
  watch_band?: { rule }                       ← authoring default floor×1.05 [VERIFY against library; never hard-code]
  cadence: { shape: quarterly|annual|monthly|on-request|event, due_rule: e.g. "Q+45d", "FYE+120d" }
  deliverable?: { doc_type, named_fields[] }  ← e.g. the §8.02(b)(3) rent-schedule field list
  status: proposed → confirmed (who, when) → amended (versioned)
}
```

Extraction proposes; **a human confirms every record once per loan** (Gate 1); periods thereafter reuse the confirmed set untouched (ask-once). Loan modifications re-open only affected records.

## 2. Actuals taxonomy (the actuals side)

Grounded in SLOT-1 (T-12) and SLOT-2 (rent roll), inspected directly.

### 2.1 What arrives, and what the spine does with it

| Source | Native shape (evidence) | Normalization steps | Exception classes | Human confirmation required |
|---|---|---|---|---|
| T-12 / income statement (PMS export, xlsx or pdf) | One sheet; 12 month columns + total; ~150 rows; numbered COA (`4010.000 Gross Potential Rent` … `8010.000 Asset Management Fee`); group subtotal rows; accrual book stamp; as-of date | Grid extraction → row classification (line vs subtotal vs header) → **COA mapping** (source code → canonical spine) → control-total reconciliation (sheet totals vs computed) | Unmapped code; subtotal mismatch; period-column gaps; book-basis mismatch (accrual vs cash) | New/changed COA mappings only (remembered per property/PMS thereafter — ask-once). Control-total breaks always |
| Rent roll (PMS detail export) | 24 columns; 322 lease rows over 301 units; unit-status taxonomy (Occupied-no-NTV / Occupied-NTV / Occupied-NTV-Leased / Vacant-Leased / Admin-Down / Vacant-Not-Leased); summary sheets (floorplan mix, status totals) | Row parse → **unit dedup** (future-lease dual rows must not inflate the denominator) → status normalization → occupancy/economic aggregates; cross-check against the roll's own summary sheet | Row/summary disagreement; unknown status label; missing SQFT/rent; duplicate unit | Status-mapping decisions the first time a PMS's labels appear; disagreements always |
| Balance sheet / trial balance / cash flows (annual package) | Statement PDFs + trial balance; escrow/reserve schedules | Statement extraction → tie-out fields (cash, escrows, NOI-to-net-income bridge) | Cross-statement tie failures | Tie failures always |
| Loan terms (from loan docs at setup; balances per period) | UPB, rate structure, IO→am schedule, maturity (evidence workbook: $15,232,500; 2-yr IO → 30-yr am; maturity 2027-07-07) | Terms → annualized debt-service computation per the loan's own definition; IO-to-amortization transition handled by schedule, not assumption | Definition ambiguity (which debt-service basis a test uses) | Terms confirmed at setup; definition ambiguities escalate (never guessed) |

### 2.2 The three-actor data language, applied

Every value in the chain carries exactly one class at any moment, and the class is legible at the point of use:

- **source** — bytes as they arrived (immutable; Original artifact);
- **inferred** — extracted/normalized/proposed by the machine, awaiting or after confirmation (confidence + source region attached; "extracted-and-confirmed" is `inferred` + a confirmation act, and the badge shows both);
- **certified** — bound into a certified package revision (hash-anchored; void-on-change).

## 3. The verdict and headroom model

For each RequirementRecord with a test (and each monitored threshold):

```text
TestResult {
  loanId, period, reqId
  inputs: { metric refs with lineage, threshold ref, definition ref }
  verdict: pass | watch | shortfall | breach
  signed_headroom: { dollars: +/-$, ratio_or_pp: +/- }     ← "how far from the line", the proactive story's fuel
  watch_band_applied: ref
  basis_echo: covenanted | monitored                        ← re-stated on the result so no renderer ever guesses
}
```

**Vocabulary law, structurally enforced:** `breach` is only emittable when `basis=covenanted` and the test fails. A monitored threshold can produce `pass | watch | shortfall` only. The type system (and the engine's fail-closed dispatcher) makes the ruled sentence — "89.04% is a SHORTFALL against a monitored 90% floor, never a breach" — impossible to violate in any renderer. Signed headroom is always dual-denominated: the ratio/percentage-point distance **and** the dollar distance in the driving metric (canon example: "$240K of NOI from a DSCR shortfall"), because the mitigant conversation happens in dollars.

**Tie-out** rides with the verdicts: this period's package values reconciled against the prior sealed package (same field, prior period). Clean tie-out is a readiness precondition; a failed tie-out is a blocking exception with the diff rendered beside both sources.

## 4. Decision and co-visibility map (what must sit beside what, per chain step)

| Chain step | The human decision | Must be co-visible | Downstream fields that move | Work type |
|---|---|---|---|---|
| Confirm requirement | "Is this extracted covenant/obligation what the agreement says?" | Proposed record ⇄ the exact source page/region (65/35 doc-inspector split) | The period checklist; every future test | Confirmation |
| Confirm COA mapping | "Does PMS code X mean canonical line Y?" | Source row ⇄ proposed mapping ⇄ effect on group subtotal | NOI and every metric consuming the line | Confirmation |
| Resolve normalization exception | "Why doesn't the control total tie?" | Extracted grid ⇄ sheet's own totals ⇄ delta | Blocked metrics | Exception |
| Review a verdict | "Do I accept this test result?" | Verdict + signed headroom ⇄ metric inputs ⇄ the loan's own definition text ⇄ source lines (lit-row) | Package covenant section; findings | Inspection |
| Disposition a shortfall | "What do we tell the lender?" | Verdict ⇄ drafted mitigant narrative ⇄ the evidence the narrative cites (e.g. pre-leased ~94%, canon) | Narrative section | Exception/judgment |
| Disposition failed tie-out | "Which period is right?" | This period's value ⇄ prior package's value ⇄ both source chains (50/50 peer compare) | The affected package field | Exception |
| Review the composed package | "Is this the package I certify?" | Package draft ⇄ evidence (the ruled three-column reconcile layout, KEPT) | — | Review |
| Certify | "I certify these contents" | The certified sheet ⇄ what changed since prior period ⇄ readiness proof | CertificationRecord | Certification (gate) |
| Send | "Send to this lender now" | Recipient/transport ⇄ certified-bytes identity (hash) ⇄ deadline | SendRecord; seal | Delivery (gate) |

## 5. Engine-to-surface interface contract (field level)

Categories verified against the snapshot's library description (`[UNVERIFIED-BY-SESSION]` on exact symbol names; the contract is written against capabilities the snapshot attests as built).

| Category | Field/output | Grain | Period | Definition owner | Source document | Consumer surfaces | Tie-out/reconciliation rule |
|---|---|---|---|---|---|---|---|
| Loan terms | UPB, rate structure, IO/am schedule, maturity, program+form+riders | loan | slowly changing | The loan documents (per loan) | SLOT-3-class docs | Loan Detail (owner), Actuals (debt service), Certificate | Balance vs servicer statement when present |
| Requirements | RequirementRecord[] (schema §1.2) | loan | versioned | The loan documents; human-confirmed | Loan agreement + riders | Extraction & Confirmation (owner), Loan Detail, Holding checklist, Review | Amendment re-opens affected records only |
| Checklist | ChecklistItem[] with states | loan+period | per period | Derived from requirements | — | Intake/Holding (owner), Home, Calendar | Completeness = readiness input |
| Normalized financials | NormalizedFinancialLine[] on the COA spine | property+period | monthly/T-12 | COA spine + confirmed mappings | T-12, statements | Actuals (owner), Review evidence pane, RR/Docs links | Control totals must tie to the source sheet's own totals |
| Occupancy | Unit-status aggregates; physical/economic occupancy | property+date | per roll | The roll's status taxonomy + confirmed mappings | Rent roll | Actuals, Loan Detail trends, questionnaire fields | Row-derived aggregates must tie to the roll's own summary sheet; unit dedup mandatory (301 ≠ 322) |
| Metrics | NOI (per definition), annualizations, DSCR, debt yield, liquidity | loan+period | per period | **The loan's own definitions** | Derived | Actuals (owner), Review, Loan Detail, Composer | Inputs carry lineage refs; recompute-on-view |
| Tests | TestResult[] (§3) | loan+period+req | per period | The loan's own documents | Derived | Review (owner of disposition), Loan Detail, Home summary | basis_echo mandatory; fail-closed on missing definition |
| Tie-out | Field-level deltas vs prior package | loan+period | per period | — | Prior sealed package | Review Room | Clean = readiness input |
| Package | Sections, field map, readiness (boolean + reasons[]) | loan+period+rev | per revision | Lender form (per servicer) | Golden-exemplar-class forms | Composer (owner), Certificate | Readiness reasons each link to a blocking item |
| Certification | who/when/hash/void-state | package rev | — | — | — | Certificate (owner), Send, Reports | Any content change → void event |
| Send | SendRecord[] append-only | package rev | — | — | — | Send (owner), Reports, Loan Detail | Bytes hash must equal certified hash |
| Trends | Metric/headroom time series | loan | rolling | — | Sealed periods | Loan Detail (the one existing runtime wire), Home | Series points link to their sealed periods |
| Findings | Finding[] with computed evidence | loan (+period) | rolling | Trigger library | Engine outputs | Home your-move, Loan Detail, Review | Disposition is a typed act |

## 6. Gap-map coverage (snapshot §6 — every numbered gap accounted for)

| # | Gap | This plan's answer (blueprint + roadmap slot) |
|---|---|---|
| 1 | Intake pipeline ABSENT | `surface-briefs/intake-holding.md` + roadmap Wave F1/V2 (intake email provisioning, classifier wiring, two-lane routing, intake security) |
| 2 | Document processing chain ABSENT | `surface-briefs/documents-docview.md` + `extraction-confirmation.md`; DocView gets its importer in V2; four-artifact model in `cross-cutting/documents-artifacts-recreated.md` |
| 3 | Data model / tenancy ABSENT in structure | Deliverable 02 model + roadmap F2 (persistence: org/client/property/pledge/loan/period tables; supplemental linkage; PMC FKs) |
| 4 | Certify→send machinery partial | `certificate-certify-gate.md` + `send-record.md`; PDF/XLSX rendering F4; certification record + void-on-change V3; roles unfixed from server-admin in F2 |
| 5 | Orchestration spine ABSENT end-to-end | Roadmap V2–V5 wire DOCUMENT IN → RECORD through the existing safety core; the send vertical is the pattern |
| 6 | Computed numbers ABSENT on screens | Every wave item states its fixture-to-engine migration; V1 (first computed period on Review) is the seam |
| 7 | Occupancy from real rolls BLOCKED | Rent-roll canonical reader F3 (unit dedup + status taxonomy per §2.1); unblocks the occupancy resolver and the RR teaser |
| 8 | Agent scaffold only | `agent-ask-watch-memory.md` (grounding, refusal, memory store, proactive watch surface, outcome learning) + roadmap W4 |
| 9 | Notifications/calendar machinery ABSENT | `calendar-obligations.md` + `cross-cutting/notifications-deadlines-escalation.md`; due-rule engine F5 |
| 10 | Onboarding + auth ABSENT; door BLOCKED | `onboarding-signin-import.md`; the Basis-funnel link removal is P0 (C-7); CSV import surface W2 |
| 11 | Multi-loan handling fixture-only | Portfolio aggregation with property-dedup rule (deliverable 02 §1) in `portfolio-loans-loan-detail.md`; W3 |
| 12 | Provenance trace PARTIAL/buggy | `cross-cutting/provenance-lit-row-trace.md`: fix the wrong-row defect first (U1-F1 re-test), then extend to every figure |
| 13 | RR-teaser placement NOT STARTED | `rent-roll-teaser-placement.md` — sited, with identity-protection rules |
| 14 | Evidence layer for the master plan | Delivered — the slots arrived filled; C-1/C-3 corrections logged; remaining withheld files named |

## 7. Semantic-risk register (flagged for the separate formula/code audit — not resolved here)

1. Debt-service basis per test: actual-paid vs scheduled vs 30-year-amortizing pro-forma (the evidence agreement itself uses a 30-yr-am basis in its one conditional DSCR context) — per-loan definition capture is mandatory; the engine must refuse a DSCR test with an unspecified basis.
2. Annualization rules for partial periods and trailing windows (T-12 vs YTD vs quarter-annualized) — per the loan's definition, never a global default.
3. Occupancy denominator (total units vs available units; Admin/Down treatment) and physical vs economic occupancy — the roll taxonomy supports several answers; the requirement record must pin one per loan.
4. Watch-band default (floor × 1.05) — verify the library value; surface it as an authoring default the human can see, never silent.
5. NOI boundary (asset-management fee above/below the line; the evidence T-12 places it below NOI as non-operating) — mapping decisions must be visible and remembered.
