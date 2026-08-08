# CROSS-CUTTING — IDENTITY: THE LOAN ⇄ PLEDGE ⇄ PROPERTY GRAPH, APP-WIDE

Ruling: the schema is a GRAPH — Loan ⇄ Property many-to-many through a Pledge join; supplemental agency loans are separate loans; reporting-only loans are first-class (memory `covenant-pane-model-and-hierarchy.md`, Terry-ruled 2026-07-01; snapshot §5). Current state: the graph exists only in fixture types — the engine library treats "deal" as atomic, no tenancy FK exists, and no supplemental linkage is modeled (snapshot §6, gaps 3 and 11). Every surface reads one 7-loan fixture, `src/components/covenant/book.ts` (574 lines) (snapshot §3).

This file is the identity contract that expands 02-OBJECT-RELATIONSHIP-MODEL §1–§2 to implementation grade: one graph, one persistence shape, one naming law, one read accessor — every surface a projection of the graph, none an owner of a private copy. Companion: `cross-cutting/period-lifecycle-seal.md` (the lifecycle that runs on top of this identity spine).

## 1. The ruled graph, restated as invariants

```text
Organization → Client (PMC mode; owner mode = one implicit Client) → Sponsor (optional)
  → Portfolio (non-exclusive human grouping)
      Property ⇄ Pledge ⇄ Loan          ← the only home of the collateral relation
                     Loan.supplemental_of → Loan   ← Loan↔Loan link, never a merged loan
                     Loan → ReportingPeriod → Package → Certification/SendRecord (period file)
```

| # | Invariant | Consequence for every surface and every table |
|---|---|---|
| I-1 | **One collateral home.** The Loan↔Property relationship lives only in Pledge rows (02 §2) | No `loan.property_id` column ever; no surface stores a private loan→property mapping; every render of "which property secures this loan" reads the pledge set through the accessor (§4.0) |
| I-2 | **Loans never merge.** A supplemental loan is a separate Loan row with its own RequirementRecords, ReportingPeriods, and Packages, joined to the same Property through its own Pledge; `supplemental_of` is a Loan↔Loan link (02 §1) | Two loans on one property = two rows in the loan book, two period spines, two independent lifecycles; the linkage renders as a chip on both rows, never as a combined record |
| I-3 | **Combined debt service is a per-loan definition question, never a global formula.** Whether a test on a senior or supplemental loan uses that loan's own debt service or a combined senior+supplemental figure is captured on that loan's RequirementRecord from its own documents (semantic-risk register, 05 §7.1); the engine refuses a test whose debt-service basis is unspecified (fail-closed) | No engine default, no settings toggle, no product-wide "combined DSCR"; a missing definition renders as a blocked test naming exactly what is missing (04 §2.5) — never a plausible number |
| I-4 | **Reporting-only loans are first-class.** Zero ratio tests ≠ diminished lifecycle: full periods, checklists, packages, certify, send (02 §1). Canon: Bexley; evidence: Calloway Park, Fannie Form 6001.NR with no recurring ratio covenant anywhere in the executed agreement (SLOT-3) | No surface branches on "has covenants" for structure — only the tests block is empty; monitored thresholds still render with `basis=monitored` grammar (pass/watch/shortfall vocabulary; breach unreachable, C-9) |
| I-5 | **Portfolio is a human grouping, not a security boundary.** Non-exclusive membership; permissions bind at Organization and Client (02 §1) | Aggregation surfaces de-duplicate by Property, never sum across Portfolios (§4.2); no permission check ever consults Portfolio |
| I-6 | **Outward name is the only name surfaces render** (§3) | One field, one render path, zero real-name code paths |
| I-7 | **Tenancy chain is total.** Every row below Organization carries `org_id`; every row below Client carries `client_id` (the gap-3 FKs) | Client-scoped reads are structural (joins on the FK, not filter conventions); owner mode renders no Client column anywhere (02 §1) |

## 2. Persistence contract against basis-v2 (roadmap F2)

Today: five migrations authored; only `covenant_test_state` has a wired writer; the send vertical persists `package_sends` and flips `reporting_periods.status` (snapshot §3 — the one REAL vertical). The identity tables below are the gap-3 additions; existing tables gain the tenancy FKs. Contract level: columns are the interface, not the DDL. `[UNVERIFIED-BY-SESSION]` applies to exact existing table/column spellings (repo not attachable, 01 §0) — the contract binds to roles; the migration binds to live names at wiring time.

```text
organizations   { id, name, intake_address, plan }
                  -- the account boundary; the intake address is Covenant's own,
                  -- minted per org (09-law; never shared with any other product)

clients         { id, org_id, name, owner_contacts, export_branding }
                  -- PMC tenancy layer; owner mode = exactly one implicit row;
                  -- invisible in owner-mode UI (02 §1)

sponsors        { id, org_id, client_id, legal_name, guarantor_links }
                  -- optional layer; serves guarantor-level obligations
                  -- (evidence: 6001.NR §8.02(b)(2) guarantor statements)

portfolios      { id, org_id, client_id, name }
portfolio_properties { portfolio_id, property_id }        -- PK pair; membership join;
                  -- a property may appear in n portfolios (I-5)

properties      { id, org_id, client_id, outward_name, address, unit_count,
                  pms_identity, coa_mapping_ref }
                  -- outward_name: the single render-name field (§3)
                  -- unit_count: the occupancy denominator authority
                  --   (canon: Bexley 301; the 322-rows/301-units dedup rule, C-3)

pledges         { loan_id, property_id,                    -- PK pair (02 §2)
                  role: 'primary' | 'pool_member',
                  effective_from, effective_to?,           -- carries mid-period changes (§8)
                  source_region_ref }                      -- provenance to the loan docs (gap 11)

loans           { id, org_id, client_id, sponsor_id?,
                  lender, servicer, program_form,          -- e.g. "6001.NR + 6220 + 6241"
                  upb, rate_structure, io_am_schedule, maturity,
                  reporting_only: bool,
                  supplemental_of?: loan_id,               -- the gap-3 linkage (I-2)
                  stage: 'live' | 'pipeline' }             -- Pipeline is a filter, not a place (08 §3)

reporting_periods (exists) + loan_id FK into this graph    -- keyed (loanId, period);
                  -- lifecycle contract in cross-cutting/period-lifecycle-seal.md
```

Constraint rules (contract level):

- **Uniqueness:** at any effective moment a loan has exactly one `role='primary'` pledge; `(loan_id, property_id)` is unique per effective window.
- **No supplemental chains:** `supplemental_of` may not point at a loan that itself carries `supplemental_of` — a deeper structure enters only when a loan's own documents establish it, as a modeled amendment, never as a silent self-reference.
- **No deletes below Organization once any period has sealed** (seal-not-wipe, 02 §5): loans and properties retire by end-dating (`pledges.effective_to`, `loans.stage`), never by row deletion.
- **Provenance on terms (gap 11):** every loan term column pairs with a source-region ref held in the ExtractedValue/provenance store (not twin columns here); the Loan Detail terms block renders each field with its ref (02 §2), traceable via the lit-row contract.

## 3. The outward-name law, implemented

Law: real property names never print; outward name is a first-class field (02 §2; kit law 9). Implementation rules:

1. **One field.** `properties.outward_name` is the only name field in the schema. No `legal_name` / `real_name` column exists in the v1 contract; the demo's outward↔real rename map deliberately never enters the repository (snapshot §3 — it stays in the fleet's internal memory, outside this package and outside basis-v2).
2. **One render path.** All name rendering goes through the identity accessor (§4.0): `propertyName(propertyId)` and `loanDisplayName(loanId)`. No component string-formats an identity from raw extraction output.
3. **Loan display-name rule.** A loan renders as its primary pledge's `outward_name`. When two loans share a primary property (senior + supplemental), the accessor disambiguates with the loan's own program/lien qualifier read from its confirmed terms — a label from the loan's own documents, never an invented one (domain firewall).
4. **Documents are exempt as bytes, not as chrome.** Original artifacts are immutable and may contain any name inside their pixels (four-artifact law). The outward-name law governs what Covenant renders as identity — list rows, crumbs, chips, headers, package cover identity fields — all of which bind to `outward_name` or to confirmed, provenance-carrying package fields.
5. **Setup flow.** At loan setup, extraction proposes the property identity from the loan documents with a source region; the human confirms it into `outward_name` at Gate 1 (Extraction & Confirmation owns the act, 02 §3). Ask-once: it is never re-asked; corrections version the confirmed value.
6. **Canon guard (CI).** The demo fixture asserts exactly the canonical outward names — Bexley, Westbrook Flats, and the five fictional pipeline peers — and the canon values byte-for-byte: Bexley $15,232,500 UPB · 4.17% fixed · 301 units · 6001.NR reporting-only · occupancy 268/301 = 89.04% shortfall vs monitored 90% floor (canon). Any diff fails the build; nobody "corrects" canon from the evidence workbook or vice versa (01 §C-4).
7. **Resident firewall (restated once).** Resident-level data never travels outward — rent rolls anonymize; deposit audits and delinquency lists are withheld from outbound packages (RESIDENT-DATA LAW, 01 §1.3). The enforcement point is Composer's section contract; recorded here because it is an identity-adjacent boundary, owned there (no double-homing).

## 4. How each surface renders the graph (projections, never copies)

### 4.0 The one read accessor — `src/lib/covenant/identity.ts` (NEW; pure)

```ts
// The only door to identity data. Surfaces import this, never tables or book.ts.
getLoan(loanId): Loan                       // terms + provenance refs + stage + supplemental_of
getProperty(propertyId): Property
pledgesForLoan(loanId, asOf?): Pledge[]     // effective-dated; asOf defaults to now;
                                            // sealed-period renders pass the period's seal date
pledgesForProperty(propertyId, asOf?): Pledge[]   // the fan-out (which loans this property secures)
loanDisplayName(loanId): string             // §3.3 rule
propertyName(propertyId): string            // outward_name, always
aggregate(scope, facts[]): AggregateResult  // enforces the §4.2 grain rules; refuses cross-grain ratios
```

The accessor is scope-aware: every call resolves inside the caller's org/client scope (I-7); an out-of-scope id behaves as nonexistent (the send route's honest-404 pattern, snapshot §3).

### 4.1 Loans register (`/loans`)

- The Property/ies column is pledge-aware: primary property outward name, then a `+n pool` chip via the shared CountBadge component (ruled 2026-08-07) when the pledge set exceeds one; expanding lists pool members with roles.
- A supplemental loan's row carries a quiet linkage chip — `supplemental of {senior display name}` — deep-linking to the senior loan; the senior row carries the inverse chip.
- Live/Pipeline renders as a segment filter on `loans.stage`, not a rail place (08 §3); verdict chips stay basis-aware (covenanted tests and monitored thresholds rendered distinctly, C-9).

### 4.2 Portfolio aggregation — the dedupe-by-property rule (normative)

Aggregates have two grains, and the grain decides the dedupe key:

- **Property-grain facts** — unit counts, occupancy, NOI, property-level actuals: aggregate over the **distinct Property set**, counted once regardless of how many Portfolios include the property or how many Loans pledge it (I-5). A property securing a senior and a supplemental loan contributes its units once, never twice.
- **Debt-grain facts** — UPB, debt service, loan counts: aggregate over the **distinct Loan set**. Senior + supplemental on one property are two loans; both count.
- **Cross-grain ratios at portfolio level** (a portfolio-wide DSCR or debt yield): not computed in v1. Each such ratio depends on per-loan definitions (I-3; 05 §7.1–7.2); `aggregate()` refuses cross-loan ratio roll-ups without a definition to own them, and the surface renders per-loan rows instead of a fabricated summary (fail-closed renders honestly, 04 §2.5).
- Header grammar: every aggregate names its grain ("across {n} properties" / "across {m} loans") so the denominator is legible at the point of use.

### 4.3 Loan Detail — the PROPERTIES tab (pledge graph)

Per the portfolio brief's direction (property cards + pledge roles + supplemental linkage rendered):

- Card anatomy (open-not-boxed, uniform heights): `outward_name` · pledge role (primary / pool member) · effective dates · unit count · address · the property's full pledge fan-out ("also secures: {other loan chips}") so the senior⇄supplemental relationship is visible from either end of the join.
- A Loan↔Loan strip above the cards renders `supplemental_of` linkage with both loans' display names and each loan's own reporting cadence — two spines, visibly separate (I-2).
- Every fact on a card carries its provenance ref (gap 11) and traces via `traceToEvidence` (provenance file §2) to the loan-document region that establishes it; the pledge role itself is a traceable claim.

### 4.4 Cross-collateral pool rendering (one loan, n properties)

- The loan's spine surfaces (Review, Actuals, Composer) render a pool strip under the period header: property chips in pledge order, primary first; every chip an instrument opening its property card (chart-doctrine instrument rule applied to chips).
- Per-property contribution tables render **only** where the loan's own documents define an allocation; otherwise the pool renders whole-loan metrics only — no invented per-property allocation of debt service or NOI (domain firewall).
- Checklist items materialize per the requirement's own deliverable scope: a pool loan's statement obligation may span properties, and the requirement record says so — or the item blocks with the gap named.

### 4.5 Everything else

- Home/Inbox/Calendar rows carry loan+period identity chips with property secondary (07 §3 entry/exit spine).
- Spine surfaces derive identity from the URL (`/covenant/[loanId]/[period]/…`, 08 §1) and render names via the accessor; the breadcrumb loan crumb is a loan switcher (08 §2).
- Certificate and Send print identity from confirmed package fields (which carry lineage), never from ad-hoc strings; the certified sheet's identity block is lit-row traceable like every other figure.
- Palette object results carry loan/period identity chips (08 §5); Ask answers name loans by display name via the same accessor.

## 5. Identity acts — typed decisions, owners, and audit events

Identity is confirmed from documents, not configured (Attio-reject, §10). Every mutation of the graph is a typed act with an owner surface and an append-only audit event; the agent proposes, the human confirms (trust hierarchy). No act below is a modal except where noted — confirmation happens source-beside-decision (05 §4).

| Act | Actor | Owner surface | Co-visible at the decision | Audit event | Downstream |
|---|---|---|---|---|---|
| Create loan (import or doc-drop) | Agent proposes from documents; human confirms | Onboarding/Import, then Extraction & Confirmation | Proposed record ⇄ source region (65/35 doc-inspector split, 05 §4) | LoanCreated {source refs} | Setup flow begins; no periods yet |
| Confirm property identity (`outward_name`, units, address) | Human (Gate 1, once) | Extraction & Confirmation | Proposed fields ⇄ the exact document region | PropertyConfirmed {who, when, source region} | Name renders everywhere via accessor; unit_count becomes the occupancy denominator authority |
| Confirm pledge (role, effective_from) | Human (Gate 1, once per pledge) | Extraction & Confirmation | Proposed pledge ⇄ the collateral/security clause region | PledgeConfirmed {who, when, source region} | Pool strip and PROPERTIES tab render it; aggregation includes it |
| Link supplemental (`supplemental_of`) | Agent proposes from the supplemental loan's docs; human confirms | Extraction & Confirmation | Both loans' identity ⇄ the supplemental instrument region | LoanLinked {senior, supplemental, source region} | Linkage chips render on both rows; debt-service basis question surfaces on the requirement records (I-3) |
| Amend a pledge (end-date, add, change role) | Human, via the amendment flow | Extraction & Confirmation (re-opened records only, 02 §5) | Current pledge ⇄ amendment document region | PledgeAmended {before, after, source region} | Open periods recompute; certified content → void-on-change (period file §3); sealed periods unaffected (asOf reads) |
| Retire loan / property (payoff, sale) | Human (typed act; destructive-confirm modal permitted) | Loan Detail | The loan's open obligations + sealed history summary | LoanRetired {reason, effective date} | End-dating only; register moves to closed segment; nothing deletes |
| Correct a confirmed identity field | Human | The field's point of use (inline) or Extraction & Confirmation | Current value ⇄ new source | FieldCorrected {before, after, who} | Versioned (02 §5); downstream-impact note lists moved package fields; certified content → void |

Rules: no identity act is agent-completable end to end; every act's event carries the source region that justifies it (gap 11 closed at the act, not retrofitted); corrections keep prior values in history with downstream-impact notes (02 §5).

## 6. Addressing, components, and reuse

### 6.1 Addressing law (identity in URLs)

- URLs carry **ids, never names**: `/loans/[loanId]`, `/covenant/[loanId]/[period]/{review|composer|certificate|actuals}` (08 §1). `outward_name` is display-only — renames never break links, and no name (outward or otherwise) ever appears in a URL, log line, or notification payload; identity in transport is always the id pair.
- `(loanId, period)` is the address of every spine deep link (02 §2 — "the address that appears in every deep link"); the identity accessor resolves display names at render time.
- Palette object results, quiet-log entries, and notifications all carry the id pair + display name resolved at render (08 §5): a stale name is structurally impossible.

### 6.2 Component inventory (reuse first, NEW named with parts)

| Component | Status | Parts / notes |
|---|---|---|
| `CountBadge` | REUSE (ruled shared component, snapshot §4) | The `+n pool` chip and all identity-adjacent counts |
| `CovenantShell` breadcrumb | REUSE (08 §2) | Loan crumb = loan switcher; renders `loanDisplayName` |
| Evidence panel + `<Figure>`/`traceToEvidence` | REUSE (provenance file §2) | Pledge/term provenance traces |
| `PropertyCard` | NEW — `src/components/covenant/identity/PropertyCard.tsx` | Name row (outward_name) · role badge · effective dates · unit count · fan-out chip row ("also secures…") · provenance affordances per fact |
| `PledgePoolStrip` | NEW — `identity/PledgePoolStrip.tsx` | Ordered property chips (primary first) · overflow behavior at narrow widths (chips collapse to `+n` with popover, no silent compression) |
| `LoanLinkageChip` | NEW — `identity/LoanLinkageChip.tsx` | "supplemental of {name}" / "senior to {name}" · deep link · renders on register rows and Loan Detail |
| `identity.ts` accessor | NEW — `src/lib/covenant/identity.ts` (§4.0) | Pure; scope-aware; fixture adapter for the migration window |

All NEW components consume ruled tokens only (periwinkle accent family, ten-rung gray ladder); Lucide icons only; figures in Geist Mono with `tabular-nums slashed-zero`.

## 7. Migration from the atomic-deal fixture (book.ts → persisted graph, canon preserved)

Current: `src/components/covenant/book.ts` is the single demo spine — 7 loans, atomic deal shape, every surface imports it, "so surfaces can never disagree" (snapshot §3). That virtue — one source — survives the migration by moving the single source from a fixture file to the graph store, with the fixture demoted to seed data. Steps, each PR-sized, no visual change until M4:

| Step | Work | Proof |
|---|---|---|
| M1 | Split the fixture types in place: introduce `Property[]` and `Pledge[]` arrays inside book.ts; keep the existing exported shape via selectors so zero consumers change | Type-check green; screenshot diff = zero on all surfaces |
| M2 | Land the F2 identity migrations (§2); write a seed script that materializes the 7-loan book as graph rows — 7 loans, 7 properties, 7 pledges (all `role='primary'`, 1:1 today; the graph shape is ready for the first real pool or supplemental without schema change) | Seed run idempotent; row counts and canon values asserted |
| M3 | Introduce `src/lib/covenant/identity.ts` (§4.0) with a fixture adapter so it can serve either store; surfaces migrate imports book.ts → accessor, one surface per PR | Lint rule lands: no `import … from '…/book'` outside the seed script and the accessor's fixture adapter |
| M4 | Re-point the one existing runtime wire — `assembleTrendSeries` on Loans (snapshot §3) — to read through the accessor | Trend tab renders identically against seeded rows |
| M5 | Canon guard becomes a store test (`canon-identity-guard`): the §3.6 values verbatim, plus Westbrook Flats as the covenanted contrast (floating-rate, real 1.20x DSCR covenant — canon) | CI green; guard fails on any canon drift |

Rules: fixture-era demo strings never gain provenance retroactively — a value without a ref renders the "no source" affordance and counts toward the migration burndown (provenance file §5). M-steps that would alter a canon value are malformed by definition. The five fictional pipeline peers migrate as `stage='pipeline'` rows so the Live/Pipeline segment works from day one.

## 8. States

| State | Behavior |
|---|---|
| Loan with no property yet | Legal during setup (extraction proposed, Gate 1 pending): the loan renders with a "collateral pending confirmation" chip; property-grain figures (occupancy, unit counts) render blocked-with-reason, never a placeholder number; periods do not materialize until the requirement schedule confirms (period file §5) |
| Property with no loan | Legal (created via CSV import or the Rent Roll teaser's property context): renders in property contexts and portfolios; absent from the loan book; no periods, no checklists materialize; no covenant verdicts render anywhere near it (RR identity guard — verdicts live on covenant surfaces only) |
| Pledge change mid-period | Effective-dated (§2): open/in-review periods recompute affected checklist items and metrics, quiet-logged; if the change touches certified content, void-on-change fires (period file §3); sealed periods forever render the pledge set as of their seal — `pledgesForLoan(loanId, sealDate)` — the sealed record never restates history |
| Senior + supplemental pair | Two rows everywhere (I-2); linkage chips on both; each loan's tests use its own defined debt-service basis (I-3) — a missing combined-basis definition blocks that one test, with the gate naming the missing definition; the sibling loan's lifecycle is unaffected |
| Loan payoff / property sale | End-dated, never deleted (seal-not-wipe); sealed periods remain addressable forever; the register moves the loan to a closed segment; trend marks still click through to sealed periods |
| Extracted-unconfirmed identity | Proposed outward name / terms render with inferred grammar (confidence + dotted provenance underline, 06 §7); never bold-final until confirmed |
| Low-confidence / conflict | Two documents disagree on a term → extraction conflict state: the field renders the conflict badge, dependent computation blocks, and resolution is a typed confirmation with both sources co-visible (05 §4) |
| Stale identity input | A replaced loan document upstream of confirmed terms flags the affected fields stale with a diff link; re-confirmation clears (03 §2 exception table) |
| Permission-denied (PMC client scope) | Client-scoped reads exclude other clients' subtrees structurally (I-7); a direct URL to a foreign loan or property 404s honestly (send-route pattern) |
| Read-only reviewer | The graph renders fully; every mutation affordance (confirm, amend, end-date) absent, not disabled-mystery |
| Empty book (new org) | Loans register renders the import CTA (onboarding brief); zero fixture leakage — seeded demo rows exist only in demo orgs |

## 9. Acceptance tests (ticket-ready)

1. **Graph shape (fixture: Calloway Park kit).** Seed one org → implicit client → one property (301 units) → one loan (6001.NR + 6220 + 6241, `reporting_only=true`) → one primary pledge. Assert: loan book renders one row; PROPERTIES tab renders one primary card; zero ratio-test rows render; the monitored occupancy threshold renders `basis=monitored` grammar and its verdict domain is pass/watch/shortfall only (evidence: SLOT-3; C-9).
2. **Dedupe-by-property (fixture: property P in portfolios A and B; loans L1 senior, L2 `supplemental_of=L1`, both pledging P).** Assert: portfolio A's unit total counts P once; a combined A+B view counts P once; the UPB total counts L1+L2; no portfolio-level DSCR renders — per-loan rows render instead, and the header names its grain (§4.2).
3. **Supplemental separation (same fixture).** L2 owns its own periods and packages; certifying L1's period leaves L2's state untouched; both register rows carry linkage chips that deep-link to each other; L2's DSCR-class test with no combined-basis definition renders blocked, naming the missing definition (I-3) — never a computed number.
4. **Pledge provenance.** Click the pledge role on a property card → the loan-document source region lights via `traceToEvidence` and stays lit; the shuffled-fixture variant (rows reordered vs extraction order) still lights the exact region (provenance file §2.4 invariant).
5. **Outward-name guard.** `canon-identity-guard` passes with the §3.6 canon values verbatim; lint proves no component imports book.ts (M3); grep proves no `legal_name` / `real_name` identifier exists under `src/components/covenant/` or `src/lib/covenant/`.
6. **Mid-period pledge change (fixture: Bexley canon period in `certified`).** End-date the primary pledge and add a replacement pledge → certification voids with a VoidEvent naming the pledge change and the affected package fields; the prior quarter's sealed period still renders the original pledge set via `asOf` (period file §§3–4).
7. **Tenancy scope (fixture: two-client PMC org).** Client-A preparer's loan register, palette results, and deep links exclude client B entirely; a forged URL to a client-B loan, property, or period returns the honest 404; the owner-mode org renders no Client column anywhere (I-7).
8. **Display-name disambiguation.** With L1/L2 sharing primary property P, `loanDisplayName` yields two distinct names using each loan's own confirmed program/lien qualifier; with a single loan, the name equals `outward_name` exactly.
9. **Aggregation performance.** The 7-loan seeded book renders `/loans` with pledge-aware columns in one accessor round-trip (no N+1 per row); the pool strip on a 5-property pool fixture renders under uniform row height with no silent compression.
10. **Reporting-only parity.** Bexley (reporting-only) and Westbrook Flats (covenanted 1.20x DSCR) traverse identical lifecycle structure — same period spine, same gates; only the tests block differs (I-4).

## 10. Benchmarks consumed (limited roles)

- **Attio — object → record → view projection.** One record page per object; every table a projection of the same store. Take: the projection discipline (§4 — surfaces project, never copy). Reject: user-editable schema — Covenant's identity is confirmed from documents, not configured. Official: https://attio.com/help/reference/attio-101/attios-data-model/define-your-data-model-objects-lists-and-views (R5 research).
- **Juniper Square — one connected graph, every workflow a projection.** Domain lesson only, never a positioning comparison (kit law 1). Take: the single-graph shape mirrored borrower-side. Official: https://www.junipersquare.com/platform (R5 research).
- **LoanBoss — lender-adjusted metric math per loan.** Thresholds stored with the lender's own definition beside the value. Adapt: reinforces I-3 (per-loan definitions, no global formulas). Reject: rates/hedging/valuation breadth. Official: https://www.loanboss.com/blog/automated-lender-adjusted-dscr-dy (R5 research).
- Rejected across all three: any lender-shared workspace as a v1 assumption — Covenant is borrower-side; the lender is the audience of the output, never the user.
