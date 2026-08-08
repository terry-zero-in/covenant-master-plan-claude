# 02 — CANONICAL OBJECT AND RELATIONSHIP MODEL

Sources: the ruled graph (snapshot §5 — `covenant-pane-model-and-hierarchy.md`, Terry-ruled 2026-07-01), the tenancy hierarchy required by `05-WORKING-METHOD` Phase 1, the engine library's object surfaces as the snapshot describes them, and the real evidence documents. Where the current implementation differs (the library treats "deal" as atomic; no tenancy FK — gap 3), that is recorded as migration work, not as a competing model.

## 1. The object graph

```text
Organization (the account boundary; every intake email, permission, and send record keys here)
└── Client                     [PMC mode only — a fee property-management company's owner client;
                                in owner mode Organization has exactly one implicit Client: itself]
    └── Sponsor                [the ownership/guarantor entity grouping; optional layer — 1..n]
        └── Portfolio          [a named grouping of properties for humans; saved-view-like, non-exclusive]
            └── Property ⇄ Pledge ⇄ Loan        [MANY-TO-MANY through the Pledge join — RULED]
                    │                │
                    │                ├── ReportingPeriod (per loan, per cadence occurrence)
                    │                │     └── Package (the deliverable for that loan+period)
                    │                │           ├── PackageSection[]
                    │                │           ├── CertificationRecord (0..1, void-on-change)
                    │                │           └── SendRecord[] (append-only)
                    │                └── RequirementRecord[] (the confirmed covenant/reporting schedule)
                    └── Document[] (filed per loan and/or property, versioned)
```

### Why each ruled edge is the way it is

- **Loan ⇄ Property via Pledge (many-to-many)** — RULED. One loan can be secured by several properties (cross-collateralized pools); one property can secure several loans (senior + supplemental). The Pledge join is the only place the collateral relationship lives. A supplemental agency loan is **a separate Loan row** with its own reporting obligations, joined to the same Property through its own Pledge; a `combined-debt-service` linkage between senior and supplemental loans is a Loan↔Loan relation (`supplemental_of`), never a merged loan (gap 3 names the missing flag).
- **Reporting-only loans are first-class** — a Loan whose RequirementRecords contain zero ratio tests still owns ReportingPeriods, Packages, and the full lifecycle. The demo loan (Bexley) and the evidence loan (Calloway Park, Form 6001.NR) are both exactly this shape.
- **Portfolio is a human grouping, not a security boundary.** Permissions bind at Organization and Client; Portfolio exists for navigation, aggregation, and saved views. A Property may appear in more than one Portfolio; aggregation surfaces must therefore de-duplicate by Property, never sum across Portfolios.
- **Client is the PMC tenancy layer.** A fee property-management company (one Organization) prepares packages for many owner Clients. Every object below Client carries its Client FK; read-only projections for the owner are Client-scoped. In owner mode the layer is invisible (one implicit Client) — surfaces never show a "Client" column to a single-owner org.

## 2. Object definitions, identity, and ownership grain

| Object | Identity / address | Owned by (surface) | Key fields (grain) | Notes |
|---|---|---|---|---|
| Organization | `orgId` | Settings | name, designated intake address, plan, roles | The intake email is minted per organization (product-boundaries law: Covenant's own address, never shared with any other product) |
| Client | `clientId` | Settings (PMC mode) | name, owner contacts, branding-for-exports | Invisible in owner mode |
| Sponsor | `sponsorId` | Portfolio floor (Loans) | legal name, guarantor links | Optional; used for guarantor-level reporting obligations (6001.NR §8.02(b)(2) guarantor statements) |
| Portfolio | `portfolioId` | Portfolio floor (Loans) | name, member properties | Non-exclusive grouping |
| Property | `propertyId` | Loan Detail (property tab) | outward name, address, units, PMS identity, COA mapping ref | Real names never print outward; outward name is a first-class field |
| Pledge | `(loanId, propertyId)` | Loan Detail | collateral role (primary/pool member), dates | The only home of the collateral relation |
| Loan | `loanId` | **Loan Detail** | lender/servicer, program+form (e.g. 6001.NR + riders), UPB, rate structure, IO/am schedule, maturity, `supplemental_of?`, reporting-only flag | The loan master record; gap 11 names missing provenance on these fields — every term field carries a source region ref |
| RequirementRecord | `reqId` (per loan) | **Extraction & Confirmation** (authoring/confirmation) · summarized on Loan Detail | kind (financial-covenant / operational-covenant / reporting-obligation / one-time / conditional), **basis: covenanted \| monitored**, definition ref (source region), threshold?, watch band?, cadence, due-rule (e.g. Q+45d, FYE+120d), form/template ref | The requirement side of the tracking model. `basis` is structural — C-9 |
| ReportingPeriod | `(loanId, period)` — the address that appears in every deep link | **The reporting spine** (state machine) | period label, cadence occurrence, status: open → in-review → ready → certified → packaged (+ sent), checklist ref, seal | Seal-not-wipe: completion freezes, never deletes |
| ChecklistItem | `(loanId, period, itemId)` | **Intake/Holding** | expected doc type, source requirement ref, state: expected/arrived/matched/waived/chased, chase history | Derived from RequirementRecords for the period |
| Document | `docId` (+ version) | **Documents** | loan/period/client links, type, artifact set (below), versions, replaces? | Four artifacts per §09 law: Original (immutable bytes+hash) / Recreated-searchable / Structured reading / Extracted records |
| ExtractedValue | `valId` | Extraction & Confirmation | field, value, confidence, source region (page/x-y or sheet/cell), state: proposed/confirmed/corrected, corrector, history | Confirmation is a typed human act |
| NormalizedFinancialLine | `(propertyId, period, coaCode)` | Actuals & Computation | source cell refs, mapping ref, value | The chart-of-accounts spine row |
| Metric | `(loanId, period, metricId)` | Actuals & Computation | value, formula ref, input refs, definition owner (the loan's own documents) | Deterministic engine output only |
| TestResult | `(loanId, period, reqId)` | Actuals & Computation (computed) · verdict surfaced on Review Room + Loan Detail | verdict: pass/watch/shortfall/breach, signed headroom ($ and ratio), watch-band ref, inputs | Breach is only reachable when `basis=covenanted` |
| TieOut | `(loanId, period)` | Review Room | prior-package deltas, status: clean/failed with diffs | Fail-closed: a failed tie-out blocks readiness |
| Package | `pkgId` = `(loanId, period, rev)` | **Composer** | sections, lender form refs, attachments, readiness (computed), draft narrative refs | Readiness is engine-computed, never decorative |
| PackageSection | `sectionId` | Composer | kind (statement/rent-schedule/questionnaire/certification/narrative/attachment), field map to lender form | Field map carries per-field lineage |
| CertificationRecord | `certId` | **Certificate surface** | who, when, content hash, void-on-change status | Voided automatically by any post-certification change; the void event is logged |
| SendRecord | `sendId` (append-only) | **Send & Record** | transport (download/email), recipient, bytes hash, gate result, actor | Already REAL at `POST /api/covenant/send` |
| CorrespondenceThread / Message | `threadId` | Lender Q&A | loan/period links, direction, draft/approved state | Draft-first; human approves outbound |
| MemoryEntry (ask-once) | `memId` | Agent layer (inspected in Settings > Agent memory and inline at point of use) | scope: borrower/loan/lender, question, answer, provenance (who answered, when, in which context), reuse log | The agent may not re-ask; corrections version the entry |
| Finding (proactive watch) | `findingId` | Agent layer (Home surfaces your-move; Loan Detail shows per-loan) | trigger ref, computed evidence (headroom), drafted narrative ref, state: raised/acknowledged/dispositioned | Dispositions are typed human acts |
| ActivityEvent (quiet log) | `eventId` (append-only) | Cross-cutting; rendered per loan/period | actor (engine/agent/human), what, evidence refs, period/package identity, outcome | The quiet log is the notification-noise firewall |
| SavedView | `viewId` | The surface that owns the underlying register | filter/sort/column state, owner, shared? | One saved-view mechanism app-wide (no per-surface reinvention) |
| Notification / Deadline | `noteId` | Calendar/Obligations (deadlines) · frame (delivery) | due date computed from due-rule, escalation policy, state | Gap 9 machinery |

## 3. No-double-homing map (summaries allowed, ownership exclusive)

| Object/action | Sole owner | Who may summarize/link (and only that) |
|---|---|---|
| Arrival routing decisions, holding checklist | Intake/Holding | Documents shows "arrived via intake on {date}" + link; Home counts your-move items; Calendar shows due-date pressure |
| Canonical filed documents, versions, artifacts | Documents | Intake links to the filed doc after routing; Review Room's Evidence pane renders (not re-files) documents; Loan Detail lists per-loan docs as links |
| Requirement records (create/confirm/correct) | Extraction & Confirmation | Loan Detail renders the confirmed schedule read-only; Review Room cites requirement defs beside tests |
| COA mappings + normalized lines | Actuals & Computation | Review Room renders results; Documents links from source cells |
| Metrics, test results, headroom | Actuals & Computation (engine) | Loan Detail trends them; Home/Portfolio summarize verdict counts; agent quotes them with provenance |
| Package composition, readiness | Composer | Review Room links "open in Composer"; Home shows readiness state per period |
| Certification act + record | Certificate surface | Composer shows certified state; Send requires it; Reports lists it |
| Send act + record | Send & Record | Reports lists send history; Loan Detail summarizes last-sent |
| Period state machine transitions | The orchestration spine (engine) — surfaced on the period header everywhere | No surface mutates status directly; all transitions go through typed acts + gates |
| Ask-once memory entries | Agent layer (one store) | Rendered inline beside the field they fill, with "learned {when} from {whom}" |
| Correspondence threads | Lender Q&A | Loan Detail summarizes open threads; Home counts your-move drafts |
| Obligation due dates + escalation | Calendar/Obligations | Home shows next-due strip; period header shows this period's deadline |
| Saved views | Each register surface owns its own instances | One shared mechanism/component |
| Intake email address management | Settings | Intake/Holding displays it read-only ("send documents to …") |

Anything not listed inherits the rule: **the surface that owns the state machine owns the object; every other appearance is a summary with a link.**

## 4. Actor model

| Actor | Description | May do | May never do |
|---|---|---|---|
| Owner (borrower principal) | 2–50 loans; monthly/quarterly/annual rhythm | Everything incl. certify + send; manage org | — |
| PMC preparer | Fee property-management staff preparing for owner clients | Prepare, confirm, compose, draft; per-client scope | Certify or send where the engagement reserves those to the owner (role-configurable; default: preparer prepares, owner certifies) |
| Reviewer (read-only) | Lender-relations counsel, partners, auditors | View, comment, export where permitted | Mutate anything; see other Clients |
| The engine | Deterministic library (`src/lib/covenant/`) | Compute metrics/tests/tie-outs/readiness; enforce gates; fail closed | Guess; hide a failed check behind a plausible number |
| The agent | The preparing analyst persona | Classify, recognize, extract-as-proposal, match, chase, draft, watch, remember, learn, explain | **Certify — ever; send — ever;** compute or alter shipping numbers; bless a threshold interpretation; re-ask a remembered answer; fabricate evidence; push-notify completed routine work |
| The lender/servicer | Audience, not user | Receives the package; asks follow-ups | Appears as a user in v1 (lender-side is a later mode) |

## 5. Version and approval model

- **Documents** version by replacement (`replaces` chain); originals immutable; derivatives labeled.
- **ExtractedValues** version by correction; the confirmation act names the confirmer; corrections keep the prior value in history with downstream-impact notes (which package fields moved).
- **RequirementRecords** version by amendment (a loan modification re-opens extraction for the affected records only); each version carries its source region.
- **Packages** version by revision within a period (`rev`); certifying binds the CertificationRecord to a content hash of a specific revision; **any change voids** (the void event is visible, never silent).
- **Periods** seal, not wipe: after packaged/sent, every object remains readable with full history; nothing is deletable inside a sealed period.
- **MemoryEntries** version by correction; the agent always uses the newest and can show the chain.

## 6. External-send model

Send is a second typed act, separate from certify, executed against the real send gate (`POST /api/covenant/send`): gate checks (org scope, role, certification present and un-voided, readiness true) → append-only SendRecord with bytes hash → period → sent. Download-first today; email transport is an additive transport on the same gate (roadmap), never a bypass. Approved bytes: what is sent is byte-identical to what was certified — the hash proves it.
