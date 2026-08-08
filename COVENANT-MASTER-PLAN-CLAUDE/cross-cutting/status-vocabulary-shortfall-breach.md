# CROSS-CUTTING — STATUS AND VOCABULARY LAW
## pass / watch / shortfall / breach · monitored versus covenanted · the period and package vocabularies

The terminal error this law prevents: a monitored miss described to a lender (or to the borrower) as a "breach." The demo canon is the fixed star: **occupancy 268/301 = 89.04% is a SHORTFALL against a monitored 90% floor — never a breach — because the executed agreement carries no recurring ratio covenants** (Terry-ruled 2026-07-09; re-verified against the real evidence agreement, which contains no recurring ratio covenant — deliverable 01 §1.3).

## 1. The two-axis model (structural, not editorial)

Axis 1 — **basis** (a property of the RequirementRecord, set at confirmation, never inferred):

| basis | Meaning | Failure vocabulary ceiling |
|---|---|---|
| `covenanted` | The loan documents impose this test/obligation contractually | pass / watch / shortfall / **breach** |
| `monitored` | Watched (by the borrower, or asked about by a servicer) with no covenant behind it | pass / watch / **shortfall** — `breach` is unreachable by type |

Axis 2 — **verdict** (a property of the TestResult, engine-computed):

| Verdict | Definition | Signed headroom |
|---|---|---|
| `pass` | Inside the threshold with margin beyond the watch band | positive, both denominations |
| `watch` | Inside the threshold but within the watch band (authoring default floor × 1.05 — verify in library, display as an authoring default, never silent) | positive but small |
| `shortfall` | Outside the threshold | negative |
| `breach` | Outside the threshold **and** `basis=covenanted` | negative |

Enforcement is code, not copyediting: the TestResult type carries `basis_echo`, and the verdict enum for `basis=monitored` results excludes `breach`. No renderer, narrative template, palette answer, or agent draft can emit "breach" for a monitored threshold because the value cannot exist. (The narrative interpolation API renders verdict words from the enum — prose never hand-writes a verdict term.)

## 2. Visual grammar (consuming ruled tokens only — no new colors)

| State | Chip text (always text — never color alone) | Token roles |
|---|---|---|
| pass | "Pass" | Quiet gray-ladder chip; no accent |
| watch | "Watch" | Mid-ladder emphasis chip + watch glyph; severity treatment per the chart doctrine's severity-only color budget |
| shortfall | "Shortfall" | Strong severity treatment from the ruled semantic roles |
| breach | "Breach" | Strongest severity treatment; reserved exclusively for covenanted failures |
| monitored basis | "Monitored" badge beside the requirement title | Outline badge, gray ladder |
| covenanted basis | "Covenant" badge | Outline badge, gray ladder |

Rules: (a) the basis badge renders **wherever a verdict renders** — a verdict chip with no basis badge is a lint error; (b) chips carry the word, satisfying the color-blind rule; (c) severity color obeys the chart doctrine (severity-only) — verdict chips are the *only* colored status in a table row.

## 3. Copy rules (grep-enforceable)

1. "Breach" appears only when rendering a `breach` enum value. Never in marketing-ish copy, empty states, or generic explanations of monitored thresholds.
2. A monitored miss is always "a shortfall against a monitored {metric} floor/threshold" — the basis is named in the same sentence.
3. Never "violation," "default," or "non-compliance" as verdict synonyms (those are legal terms with loan-document meanings; the product does not issue legal conclusions). The agent's narrative drafts follow the same restriction.
4. "Watch" is the product's early-warning word (never "at risk of breach" for monitored items — the correct phrase is "approaching the monitored floor").
5. Headroom is always signed and dual-denominated where a dollar driver exists (canon phrasing: "$240K of NOI from a DSCR shortfall").
6. Requirement kinds use the deliverable-05 taxonomy names; "covenant" as a noun refers only to actual covenants — reporting obligations are "obligations."

## 4. The period/package vocabulary (fixed set)

| Term | Definition | Never confused with |
|---|---|---|
| open (holding) | Period exists; checklist incomplete | "empty" |
| in-review | Inputs complete; exceptions being worked | "ready" |
| ready | Engine readiness true; awaiting certification | "certified" |
| certified | Certification record present and un-voided | "sent" |
| sent / packaged | Send record written; period sealed | — |
| sealed | Immutable-but-inspectable (seal-not-wipe) | "archived/deleted" |
| voided (certification) | A post-certification change dismissed the certification, reason attached | "failed" |
| waived (checklist item) | A human excused an expected item, recorded | "missing" |
| stale (value) | A source replaced after confirmation; re-confirmation pending | "wrong" |
| blocked | Fail-closed engine stop naming what is missing | "error" |
| your-move | An item blocked on a human decision (the computed query, 03 §4) | "unread" (C-8) |

## 5. Acceptance tests

1. Type-level: constructing a TestResult with `basis=monitored, verdict=breach` fails to compile/validate; a fixture attempt is a CI test.
2. Grep gate: the built app's string catalog contains "breach" only in enum-render paths; the canon sentence renders exactly: "268/301 — 89.04% · Shortfall · monitored 90% floor" on Loan Detail, Review, and the certificate.
3. The Westbrook Flats contrast (canon: a real covenanted 1.20x DSCR) can render `breach` in a fixture where its test fails — proving the ceiling is basis-driven, not global.
4. Every verdict chip in every surface fixture carries its basis badge and its word (axe + snapshot tests).
5. Narrative drafts: an agent draft describing the canon occupancy state must contain "shortfall" and must not contain "breach" (golden test on the drafting service).
