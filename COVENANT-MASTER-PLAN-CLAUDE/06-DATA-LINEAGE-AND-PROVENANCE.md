# 06 — DATA LINEAGE AND PROVENANCE
## Representative source-to-package chains from the real evidence, and the lit-row law applied app-wide

All values below are read directly from the evidence documents (SLOT-1/2/3/4) and labeled; canon values are labeled canon. Real property/entity names never print — the outward name Calloway Park (CAL) is used per the print map.

## 1. The provenance chain shape (the law, restated as a data structure)

Every material figure or claim in the app must be able to open, in place (proof beside the claim, never a context-destroying modal):

```text
Rendered figure/claim
→ formula or extraction logic (which engine function / which extractor version)
→ inputs (each a ref, recursively traceable)
→ normalized row/cell/field (COA line, roll aggregate, form field)
→ exact source region (page + region box, or sheet + cell range, or email + part)
→ source document identity (docId + version + artifact: original/recreated/structured)
→ loan / period / package identity
→ processing/model version
→ confirmation/correction history (who, when, what changed, downstream impact)
```

The interaction is the ruled lit-row mechanic: **click a figure → its exact source line lights in the Evidence panel and stays lit** (Anthropic-citation quality). Current state: PARTIAL — the mechanic renders but lights the wrong source row in 2 of 3 renderers (U1-F1). Fix-first, then extend to every figure: certificate values, covenant strips, agent-quoted numbers, trend points (`cross-cutting/provenance-lit-row-trace.md` carries the component contract).

## 2. Chain A — NOI into the annual package (financial spine, FYE 2018, evidence)

```text
SLOT-1 T-12 (accrual book, as-of 12/31/2018, PMS-generated)
  rows 4010.000 Gross Potential Rent … 6640.000 Other Contract Services   [source]
→ grid extraction: 14 columns × ~150 rows; row-class = line|subtotal|header  [inferred]
→ COA mapping per property/PMS (e.g. "4135.000 RAF" → Other Income line)    [inferred → confirmed once; remembered]
→ normalized lines on the spine; control-total check:
    Σ expense lines = sheet's own "Total Operating Expenses" $1,686,050 ✓    [engine]
→ NOI per the sheet's own grouping: $1,218,877                               [engine; deterministic]
    ── note: Asset Management Fee $114,700 sits BELOW NOI as non-operating in this book;
       whether a given loan's NOI definition includes/excludes it is a per-loan
       definition question (semantic-risk register §7.5 in deliverable 05) — never a global default
→ package: annual statement of income & expenses (§8.02(b)(2) deliverable)
    the submitted-package income statement carries the same statement           [WORKFLOW evidence: SLOT-4]
→ cross-statement tie-out (engine): cash-flow statement Net Income $460,159 = T-12 Net Income ✓
    interest paid $644,017 = T-12 debt-service line total ✓
    period-end cash $138,940 = balance-sheet Total Cash ✓
→ reviewed → certified (who/when/hash) → sent → append-only record, sealed period
```

Every row of the certified statement can light its T-12 source row; every T-12 row can open the original bytes.

## 3. Chain B — a covenant-schedule requirement (requirement spine, evidence)

```text
SLOT-3 loan agreement, §8.02(b)(3) (page 44 of the executed PDF)                [source]
→ extracted requirement proposal:
    kind=reporting-obligation · deliverable=rent schedule
    named fields: tenant, space occupied, lease expiration, current-month rent, paid-through date
    cadence: Q+45d (Q1–Q3) and FYE+120d · basis=covenanted (an obligation to deliver)
    definition_source_region: {doc: SLOT-3, page: 44, region: §8.02(b)(3)}       [inferred]
→ human confirmation (typed act, first-time loan setup)                          [confirmed]
→ materialized checklist items each period: "rent schedule — due {period end}+45d"
→ the Composer's rent-schedule section maps roll columns onto the named fields
→ certified package includes it; the servicer's own checklist (JLL questionnaire item:
   "Rent Roll provided is true and correct") is answered by this section          [WORKFLOW evidence: SLOT-4/5]
```

Rider evidence for per-loan extraction: Form 6241 adds an annual ENERGY STAR report requirement that exists **only because this loan carries the Green rider** — the extraction checklist is per-loan, never templated across loans.

## 4. Chain C — occupancy (the taxonomy-sensitive chain, evidence + canon)

```text
SLOT-2 rent roll, as-of 04/30/2018 (C-1: an April roll; the as-submitted 12/31 roll is withheld)  [source]
  322 lease rows · 24 columns · unit-status per row
→ unit dedup: 322 rows → 301 units (future-lease dual rows collapse)             [engine; the 301≠322 rule]
→ status aggregation (per the roll's own taxonomy):
    Occupied-no-NTV 259 + Occupied-NTV 18 + Occupied-NTV-Leased 2 = 279 physically occupied
    Vacant-Leased 13 · Admin/Down 2 · Vacant-Not-Leased 7
→ cross-check against the roll's own summary sheet totals (301 ✓)                [engine tie]
→ physical occupancy = 279/301 = 92.69% as of 04/30/2018                         [engine output, evidence period]
→ requirement join: which occupancy definition does this loan pin?
    (denominator, Admin/Down treatment, physical vs economic — per-loan, §7.3)
→ canon demo instance of the same chain: 268/301 = 89.04% vs monitored 90% floor
    → verdict SHORTFALL (basis=monitored; breach is unreachable)                  [canon]
→ package: the servicer questionnaire's occupancy fields
    ("No. Units Occupied", "% of Total Units Occupied", three month-end dates per quarter)  [WORKFLOW: SLOT-4/5]
```

This chain is currently BLOCKED in the engine (gap 7) pending the rent-roll canonical reader; the reader's acceptance tests come straight from this evidence file (deliverable 14).

## 5. Chain D — a DSCR-class test (requirement + actuals join, stated without inventing semantics)

```text
Loan terms (confirmed at setup): UPB $15,232,500 · 2-yr IO → 30-yr am · maturity 2027-07-07   [evidence workbook]
→ annualized debt service per THE LOAN'S OWN definition
    (basis question — actual-paid vs scheduled vs 30-yr-am pro-forma — is captured on the
     requirement record; the engine refuses to test without it; §7.1)
→ NOI per the loan's own definition (Chain A output + definition adjustments)
→ DSCR = per-definition ratio                                                     [engine]
→ TestResult: verdict + signed headroom in both denominations
    canon phrasing target: "you are $240K of NOI from a DSCR shortfall"
→ where basis=covenanted (canon: Westbrook Flats 1.20x) verdicts may reach breach;
  where basis=monitored, shortfall is the floor of failure vocabulary
→ package covenant section + proactive finding when the watch band is entered
```

## 6. Chain E — the certification itself (trust spine)

```text
Composer assembles package rev N → engine readiness = true (every reason discharged)
→ certified sheet renders (ruled paper treatment) with every figure lit-row-traceable
→ CERTIFY (typed human act) → CertificationRecord { who, when, content_hash }
→ any subsequent change to any input that feeds any package field
   → VOID event (visible, reasoned, quiet-logged) → period returns to in-review
→ SEND (second typed act) → send gate re-verifies hash = certified hash (approved bytes)
→ append-only SendRecord → period sealed (seal-not-wipe)
→ forever after: the sealed period answers lender follow-ups with exact provenance
   (Lender Q&A drafts ground here — the agent quotes sealed figures with their chains)
```

## 7. The three-actor language at the point of use

Every figure renders with its class legible without a click (type treatment + badge grammar defined in deliverable 10, consuming ruled tokens only):

| Class | At-a-glance grammar | On-click |
|---|---|---|
| source | Document-native rendering (DocView/Evidence pane) | Opens original bytes; hash visible |
| inferred (proposed) | Confidence + dotted provenance underline; never bold-final | Lights source region; confirm/correct actions |
| inferred (confirmed) | Provenance underline + confirmer stamp on hover | Lights source region; correction history |
| certified | The certified-sheet paper treatment (ruled hexes); hash chip on the sheet header | Opens the certification record + full chain |

## 8. Derivative-artifact honesty (the four-artifact model, applied)

Evidence instance: the executed JLL questionnaires are flattened scans with zero text layer (verified). The pipeline therefore produces, per document: Original (immutable scan + hash) → Recreated-searchable (page-true render + OCR text/coordinates/confidence — the "Recreated · searchable" gasp artifact, always labeled a derivative) → Structured reading (fields/tables with regions) → Extracted records. Certification attaches to reviewed values only — never silently to a derivative. Compare mode (Original ⇄ Recreated) is a toggle/overlay, not a permanent split, because visible pixels are materially identical (per the document-modes law).
