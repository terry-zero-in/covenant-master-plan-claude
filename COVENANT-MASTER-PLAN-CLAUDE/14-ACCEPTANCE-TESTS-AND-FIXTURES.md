# 14 — ACCEPTANCE TESTS AND FIXTURES

Per-surface acceptance tests live in each brief's §17. This file defines the **shared fixtures** every test suite draws from, and the **whole-product acceptance tests** that no single brief owns. No proof, no done: every roadmap item (12) and work order (13) names tests from here or from a brief §17.

## 1. The canonical fixtures

### FX-CAL — the real-evidence period (from the Calloway Park kit; contents used as test data, outward names only)

| Fixture | Source | What it exercises |
|---|---|---|
| FX-CAL-T12 | SLOT-1 T-12 FYE 12.31.18 (xlsx + pdf twin) | Grid extraction; COA mapping; control totals (Total OpEx $1,686,050; NOI $1,218,877; Net Income $460,159); xlsx and pdf paths converge to identical normalized lines |
| FX-CAL-RR | SLOT-2 roll 04/30/2018 | Unit dedup 322→301; six-status taxonomy; roll-summary tie-out; occupancy aggregates (279/301 physical) |
| FX-CAL-AGMT | SLOT-3 agreement (6001.NR + 6220 + 6241 riders) | Requirement extraction: §8.02(b) obligations with due-rules; rider-added ENERGY STAR requirement; ZERO recurring ratio covenants (the reporting-only shape); conditional DSCR contexts NOT extracted as recurring tests |
| FX-CAL-PKG | SLOT-4 submitted package (12 files) | Package composition target; cross-statement tie-outs (cash-flow NI = T-12 NI; interest = T-12 debt service; cash = balance-sheet cash); the questionnaire + certification transcription targets |
| FX-CAL-SCAN | SLOT-4/5 executed questionnaires (flattened scans) | The four-artifact pipeline: original → recreated-searchable → structured → extracted records |

### FX-CANON — the demo book (seeded from `book.ts` at F2, preserved verbatim)

| Fixture | Contents | What it exercises |
|---|---|---|
| FX-CANON-BEX | Bexley: $15,232,500 · 4.17% fixed · 301 units · 6001.NR reporting-only · 268/301 = 89.04% vs monitored 90% floor | The SHORTFALL canon; monitored basis; reporting-only loan first-class |
| FX-CANON-WBF | Westbrook Flats: floating SARM with covenanted 1.20x DSCR | The covenanted contrast; `breach` reachable in a failing fixture |
| FX-CANON-BOOK | The seven-loan book | Registers, aggregation-dedupe, saved views, multi-loan floors |

### FX-SYN — synthetic edge fixtures (authored; no real data)

| Fixture | Contents |
|---|---|
| FX-SYN-POOL | One loan ⇄ two properties (cross-collateral); one property ⇄ two loans (senior + supplemental with `supplemental_of`) |
| FX-SYN-PMC | One org, two clients, per-client scoping; preparer/owner role split |
| FX-SYN-SHUFFLE | The provenance shuffled-fixture (rows reordered vs extraction order) — the U1-F1 killer |
| FX-SYN-DRIFT | A certified period whose source doc is then replaced — void-on-change chains |
| FX-SYN-CLOCK | Deadline clock fixtures (approach, hit, pass) for escalation-ladder transitions |
| FX-SYN-UNKNOWN | An unrecognized loan form → fail-closed blocked extraction |

## 2. Whole-product acceptance tests

### A. The full-period E2E (the product's definition of working)

**A1 — Arrival to sealed record (FX-CAL + FX-CANON-BEX):** email arrival (T-12 + roll + questionnaire) → recognition → holding completes → extraction confirmed (first-time) → normalization (mappings confirmed once) → computation (metrics + monitored-threshold results with correct SHORTFALL vocabulary) → tie-out vs prior seeded package → composition (readiness reasons discharge one by one, each linked) → review (exception walk; every stop's evidence lights correctly) → CERTIFY (typed act; record with who/when/hash) → SEND (gate passes; approved-bytes hash equality; append-only record) → period sealed; every figure in the sealed package traces to source bytes. **Run keyboard-only as a second pass.**

**A2 — Second period (ask-once proof):** the next period for the same loan runs A1 with **zero re-asked questions** (no requirement re-confirmation, no COA re-mapping, remembered questionnaire answers pre-filled with provenance chips) and only genuinely new exceptions surfacing.

**A3 — The void path (FX-SYN-DRIFT):** post-certification source replacement → certification voids visibly with reason → period returns → re-review shows the change as stop #1 → re-certify → send verifies the new hash.

### B. Law-enforcement tests (structural)

| ID | Test |
|---|---|
| B1 | Vocabulary: constructing `basis=monitored, verdict=breach` fails; the canon sentence renders as "Shortfall · monitored 90% floor" on Loan Detail, Review, and the certificate; FX-CANON-WBF failing fixture CAN render Breach |
| B2 | Agent immutability: no agent-reachable path invokes certify/send writers; typed-act writers require an interactive human session (unit + route tests) |
| B3 | Shipping numbers: narrative interpolation API rejects numerals without `value_id`; CI counts bare numerals in agent-draft templates = 0 |
| B4 | Approved bytes: sha256(sent bundle) == certification hash (E2E on A1); bit-flip → gate refusal (409-class) |
| B5 | Append-only: UPDATE/DELETE on certification/send/activity rows fails at the persistence layer |
| B6 | Product boundaries: the built app's user-facing string catalog greps clean against the hard-fail list; sign-in contains zero cross-product links |
| B7 | Tokens: canvas-readback of rendered surfaces matches the pinned hex values (the oklch trap check); zero non-token color literals in covenant components (lint) |
| B8 | Quiet log: a full A1 run generates zero push notifications; every autonomous step appears in the activity register with actor + evidence; the one push class fires only on FX-SYN-CLOCK overdue |
| B9 | Fixture burndown: components importing `book.ts` directly = 0 at W5 exit; bare-numeral JSX count = 0 |
| B10 | Resident data: no outbound package bundle contains resident-level records (FX-CAL-RR names are anonymized in any rendered rent schedule; the withheld-file classes are structurally excluded from bundles) |

### C. Frame and interaction tests

| ID | Test |
|---|---|
| C1 | Deep links: every rail floor and spine route restores shell state; back/forward traverse correctly |
| C2 | Altitude gate: spine routes always render loan+period crumbs (as switchers); book floors never do |
| C3 | Viewports: the four CI fixtures (1152/1440/1728/2048) on every surface fixture — no pane under its min, no body horizontal scroll, split ≤ 2 work windows |
| C4 | Palette: "dscr {loan}" returns the engine figure with working lit-row link; zero free-generation rows; certify/send verbs navigate-only |
| C5 | G-chords work with rail collapsed and focus in a grid; "?" renders the full map; Esc closes summoned panes before navigating |
| C6 | Provenance: FX-SYN-SHUFFLE passes on all renderers; sealed-period traces work (seal-not-wipe read paths) |
| C7 | Accessibility: axe-clean on every surface fixture; verdict chips carry words; keyboard-only A1 (the second pass) completes; ceremony modals focus-trap |
| C8 | Your-move: the badge count equals the computed query in all three render locations simultaneously (property test across state mutations) |

### D. Engine-boundary tests (wiring, not formula certification)

| ID | Test |
|---|---|
| D1 | Fail-closed: FX-SYN-UNKNOWN renders the blocked state naming the missing definition; no plausible placeholder anywhere |
| D2 | Definition-pinning: a DSCR-class test without a debt-service basis on its requirement record refuses to compute (renders blocked, not guessed) |
| D3 | Recompute-on-view: a stale render is impossible — mutating an input and re-opening Review shows recomputed values (no cached figure without a stale badge) |
| D4 | Occupancy: FX-CAL-RR through the F3 reader ties to the roll's own summary; FX-CANON-BEX renders the canon 89.04% through the same code path |

## 3. Test-to-roadmap binding

| Roadmap item | Exit tests |
|---|---|
| F1 | Intake substrate tests (brief §17) + B8 arrival logging |
| F2 | Seed-parity render test (every surface renders unchanged from seeds) + B5 |
| F3 | D4 + FX-CAL-RR suite |
| F4 | B4 hash stability |
| F5 | FX-SYN-CLOCK ladder + B8 |
| F6 | C6 (FX-SYN-SHUFFLE) |
| V1 | A1 subset (compute→review) + D1–D3 |
| V2 | A1 subset (arrival→confirm) + FX-CAL-SCAN four-artifact round-trip |
| V3 | A3 + B2/B4 + ceremony-budget check |
| V4 | Full A1 |
| V5 | Findings + grounded-reply tests (briefs §17) |
| W1–W5 | Each brief's §17 + the wave's slice of B/C suites + challenger audits |
