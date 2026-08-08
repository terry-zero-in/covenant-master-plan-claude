# CROSS-CUTTING — PERIOD LIFECYCLE: THE STATE MACHINE, VOID-ON-CHANGE, AND SEAL-NOT-WIPE, APP-WIDE

Ruling: the period state machine is `open → in-review → ready → certified → packaged`, with three human gates and agent immutability, and the send vertical extends it to `sent` (03 §2; snapshot §3 — the orchestration safety core exists as a **built** period ledger + deterministic state machine with **no end-to-end wiring**; `[UNVERIFIED-BY-SESSION]` on exact enum spelling). Completion **seals, never wipes** (memory `covenant-pane-model-and-hierarchy.md`, Terry-ruled 2026-07-01). This file is the contract that wires every surface to that one core — it re-implements nothing — and expands 03 §2 to implementation grade: every transition's trigger/actor/prerequisites/reversibility/audit event, the void mechanics, the seal mechanics, cadence materialization, and the two shared components (period header, period selector).

## 1. One state machine, one implementation (binding rules)

1. The safety core in `src/lib/covenant/` is the **sole** authority for period status. Surfaces never mutate status; they call the core's typed transition acts and render its output (02 §3: "no surface mutates status directly").
2. **No local enums.** No component, route, or fixture declares its own period-status union. Display labels map from the core's enum through one dictionary (`src/lib/covenant/period-labels.ts`, NEW); the spec binds to the core's spelling, whatever it is — the dictionary adapts copy, never forks state (`[UNVERIFIED-BY-SESSION]` on spelling is thereby contained to one file).
3. **Agent immutability is structural.** The core's transition API authenticates actor class; agent-actor calls to any gated transition are refused by type and by runtime check — the agent may advance nothing past a human gate (04 §2; trust hierarchy LOCKED).
4. The one REAL vertical is the pattern: `POST /api/covenant/send` already runs the gate against persisted state, appends `package_sends`, flips `reporting_periods.status → sent`, and fails honestly (503/404/403) (snapshot §3). Every other transition wires the same way — server-side gate, persisted flip, append-only record, honest failure.

## 2. The transition table (normative)

```text
(cadence) ─▶ OPEN ─▶ IN-REVIEW ─▶ READY ─▶ CERTIFIED ─▶ PACKAGED/SENT ─▶ (sealed)
                         ▲            ▲  │        │
                         └── VOID ────┴──┘        └── void-on-change returns here
```

| # | Transition | Trigger | Actor | Prerequisites | Reversibility | Audit event |
|---|---|---|---|---|---|---|
| T0 | (none) → `open` | Cadence occurrence materializes (§5) | Engine (scheduler) | Confirmed RequirementRecords exist for the loan | n/a — a period, once materialized, is never deleted; a mistaken materialization is end-dated with reason (typed act, rare) | PeriodMaterialized {loanId, period, due-rule refs} |
| T1 | `open` → `in-review` | Checklist complete + extraction/normalization/computation done | Engine advances; agent's preparation feeds it | Every ChecklistItem arrived/matched/waived; no blocking extraction exception | The main line never returns to `open`; a regressed checklist item (revoked waiver, invalidated arrival) renders as an awaiting-documents exception riding alongside (03 §2 exception table), not a state regression | PeriodAdvanced {from, to, checklist snapshot ref} |
| T2 | `in-review` → `ready` | All exceptions dispositioned + tie-out clean + readiness true | Engine (computed; Gate 1's human confirmations are inputs to it) | Readiness reasons list empty (04 §2.2: each reason links to its blocking item) | Yes — automatic: any new exception, stale flag, or failed tie-out returns the period to `in-review` | ReadinessReached {readiness proof ref} / ReadinessLost {new blocking item} |
| T3 | `ready` → `certified` | **Gate 2 — the certify act** | Human only, ever | Readiness true; identity-bound authenticated session; role holds certify right (unfixes the server-admin-only role, gap 4); typed confirmation in the ceremony modal (modal permitted: typed act with legal weight); content hash computed over the package revision | Only via void: automatic void-on-change (§3) or a deliberate human void with a typed reason (rare; the reason is mandatory — the GitHub review-dismissal pattern, R4) | CertificationRecord {who, when, content_hash} + quiet-log entry (loud in history, quiet in notifications, 04 §1) |
| T4 | `certified` → `in-review` \| `ready` (**VOID**) | Any change that touches certified content (§3.1) | Engine (automatic, never silent) | — | The void itself is not reversible; the voided CertificationRecord remains in the timeline forever, labeled VOIDED with reason | VoidEvent {what changed, who changed it, affected fields[], voided certId, return state} |
| T5 | `certified` → `packaged/sent` | **Gate 3 — the send act**, executed against `POST /api/covenant/send` (REAL) | Human only, ever | Certification present and un-voided; sent-bytes hash = certified hash (approved bytes, 02 §6); org/role scope passes; recipient/transport confirmed in the send ceremony (distinct from the certify ceremony so neither trains reflex for the other — NN/g budget, R4) | **Irreversible — a send can never be undone.** Corrections are subsequent records (§4.4), never edits | SendRecord (append-only) {transport, recipient, bytes hash, gate result, actor} |
| T6 | `packaged/sent` → sealed | Post-condition of T5 (same act) | Engine | — | Irreversible by design | PeriodSealed {seal timestamp} — quiet-logged (03 §1 step 13) |

Gate anatomy (all three gates): exact ask, why blocked, evidence beside the decision, affected fields, recommendation, consequence of delay, audit record (gate law, 04 §2.2). Certify and send are the product's only two heavy ceremonies; nothing else in Covenant gets a confirmation dialog (R4: NN/g, https://www.nngroup.com/articles/confirmation-dialog/ — reject "Are you sure?" everywhere else).

## 3. Void-on-change mechanics

### 3.1 What voids (change classes)

A certification pins a content hash over the package revision's field map and attached bytes (03 §2). The engine derives "feeds certified content" from the **same provenance chains the lit-row trace uses** (`cross-cutting/provenance-lit-row-trace.md` §2) — one dependency graph, not a second bookkeeping system. A write voids iff its `value_id` (or its document/version) appears in the certified revision's chain set:

| Class | Example |
|---|---|
| Corrected ExtractedValue feeding any package field | A confirmed rent-schedule field is corrected after certify |
| Replaced document upstream of any package field | A revised T-12 supersedes the one the statement section reads |
| COA mapping change that moves a normalized line consumed by the package | Remapping a PMS code shifts NOI inputs (05 §2.1) |
| Requirement amendment touching this period (§7) | A rider adds/changes a deliverable in the certified checklist |
| Identity/pledge change feeding collateral or identity fields | A pledge end-date changes what the package states about collateral (`cross-cutting/loan-property-pledge-identity.md` §8) |
| Memory-entry correction that re-fills a package answer | The remembered management-fee answer is corrected and re-fills the questionnaire |
| Any package composition change | Section added/removed; narrative edited — even narrative edits void; lender packages are stricter than release notes (R4: GitHub Releases adapt, https://docs.github.com/en/code-security/concepts/supply-chain-security/immutable-releases) |

Non-voiding (never invalidate a certification): reads and traces; saved views, stars, filters; chase messages and quiet-log growth; Q&A thread activity; a newly filed document that no certified field consumes; corrections scoped to other loans/periods. The dependency graph decides — not surface guesswork.

### 3.2 How the void renders

- **Never silent** (03 §2). A banner in the period header (§6) on every spine surface: `Certification voided {when} — {actor}: {change summary} → {n} fields moved · View diff`, until re-certified.
- The voided CertificationRecord stays in the period timeline, labeled VOIDED with its reason and the diff link — the GitHub stale-review pattern: dismissed, kept, explained (R4, https://docs.github.com/en/pull-requests/how-tos/review-pull-requests/dismissing-a-pull-request-review).
- The diff view opens beside evidence (never a modal): field-level before/after, each side lit-row traceable.
- The your-move query picks it up (a voided period awaiting re-certify is blocked on a human, 03 §4).

### 3.3 Return-to-state rule (deterministic)

On void, the engine recomputes the pipeline for the affected values, then places the period: readiness still true (change absorbed, all exceptions clear, tie-out clean) → `ready`, one re-certify away; otherwise → `in-review` with the new exceptions listed. Nothing re-certifies silently; the re-certify act is a fresh T3 producing a **new** CertificationRecord — records are append-only corrections, never edits (R4: Modern Treasury append-only, https://moderntreasury.com/learn/data-immutability).

## 4. Seal-not-wipe implementation

### 4.1 What sealing freezes

Every row addressed by `(loanId, period)`: checklist items and their chase history, extracted-value period linkage, normalized lines, metrics, test results and verdicts, tie-outs, package revisions and their bytes, the certification record(s) including voided ones, send records, and the period's activity events. Documents' original bytes were already immutable (four-artifact law); the seal freezes the period's **linkage set** — which document versions, which values, which verdicts shipped.

### 4.2 What remains readable — everything, forever

The sealed period is fully inspectable: Reports opens the sealed record read-only (the review/certificate render with the sealed banner); the lit-row trace works forever inside it (provenance file §5 "Sealed period" row); Lender Q&A drafts ground in sealed figures with their chains (06 chain E); trend marks click through to their sealed periods (Loan Detail). Nothing inside a sealed period is deletable — not a document link, not a chase record, not a voided certification. Retirement anywhere in the object graph is end-dating, never deletion (02 §5).

### 4.3 What is still writable — append-only records *about* the sealed thing

Reads, exports, and download events; new SendRecord rows (the send register is append-only by construction); Q&A threads referencing sealed figures; activity events. The sealed content itself never changes. A late-discovered error is handled forward: in correspondence, in the next period, or — if a corrected resubmission to the lender is ever required — as a **new package revision whose certify→send re-runs T3/T5 with append-only records while every prior record stands** (PROPOSED path, consistent with `SendRecord[]` plurality in 02 §2; routed for ratification, not assumed).

### 4.4 The sealed banner

Rendered by the period header (§6) on every surface showing a sealed period: `Sealed — sent {date} via {transport} · read-only forever, fully inspectable` with the hash chip (click = full hash + verify). It explains what sealing means rather than just stating it (send-record direction). Visual: gray-ladder rungs + the mono hash treatment (`tabular-nums slashed-zero`); no new colors — sealed is a neutral archival state, not a severity.

## 5. Cadence materialization (RequirementRecord due-rules → periods with checklists; gap 9 / roadmap F5)

- Each confirmed RequirementRecord carries `cadence: {shape, due_rule}` — evidence: 6001.NR §8.02(b): quarterly YTD income statement **Q+45d**; annual statements **FYE+120d**; rent schedule at both cadences; on-request items rate-capped at ≤1 per 6 months absent default (SLOT-3; 05 §1).
- At each cadence occurrence the engine materializes: a ReportingPeriod (T0) keyed `(loanId, period)` — the address every deep link uses (08 §1) — plus its ChecklistItems (one per expected deliverable, each carrying its source requirement ref, 02 §2) plus its Deadline rows (due date computed from the due-rule; escalation ladder remind → chase → your-move → push per org policy, 03 §5).
- **Period grain = cadence occurrence.** A loan with quarterly and annual obligations gets a quarterly period per quarter and an annual period at fiscal year-end, each with its own checklist; whether year-end items fold into one combined submission is per the loan's own schedule as confirmed — never a product-side merge (domain firewall).
- On-request items (§8.02(b)(4)) do not pre-materialize: a lender's written request arrives through intake, matches the loan, and adds a one-shot checklist item to the current open period, with the rate-cap rendered beside it (evidence fact, not product invention).
- Timing: materialization runs at the cadence boundary ("Month start: new OPEN periods appear with checklists," 03 §4). A newly confirmed loan materializes its current period immediately and never backfills history it wasn't tracking.
- Amendments re-materialize forward only (§7). Deadlines cluster by construction (every Q+45d lands the same fortnight across the book) — the calendar's agenda-first design carries that load (R5: Google Calendar month-grid "+N more" anti-pattern, https://support.google.com/calendar/answer/34580 — rejected as primary).

## 6. The period header component (NEW `PeriodHeader`, `src/components/covenant/period/PeriodHeader.tsx`)

Rendered on **every spine surface** — Review, Composer, Certificate, Actuals — directly under the 48px shell header; it is the surface-level identity band the breadcrumb altitude gate promises (a spine surface must always say which loan+period it serves, 08 §2.3).

Anatomy (left → right, one row, uniform height):

1. **Identity cluster** — loan display name + period label (echoes the crumbs; names via the identity accessor, `loan-property-pledge-identity.md` §3–§4.0).
2. **Status chip** — the core enum through the one label dictionary (§1.2), with status dot; severity color only where the period carries a blocking verdict (chart doctrine: severity-only color).
3. **Deadline** — due date + days remaining from the Deadline row (Calendar owns deadlines; this is a computed summary of the same row, 02 §3); overdue state escalates per policy, the one push-eligible class (04 §2.3).
4. **Gate/blocking summary** — when the period is blocked: `{n} items block readiness` as a CountBadge (shared component), popover listing each reason as a deep link (GitHub required-checks pattern: the blocked state always explains itself, R4, https://docs.github.com/en/pull-requests/reference/status-checks).
5. **Banner slots** — void banner (§3.2) and sealed banner (§4.4); mutually exclusive with each other.

States: loading (skeleton band, identity renders first); certified (hash chip); voided (banner); sealed (banner, read-only affordances downstream); permission-limited (status renders, gated actions absent). The header renders state; it mutates nothing — all acts live on their owning surfaces.

## 7. Amendment handling (loan modification mid-period)

1. The amendment document arrives through intake like any arrival, routes to the loan, and re-opens extraction **only for the affected RequirementRecords** (02 §5); confirmation versions each record with its new source region — prior versions stay in history.
2. Effects by period state: `open`/`in-review` — the checklist re-materializes visibly (items added/removed, each change provenance-linked to the amendment document; quiet-logged); `ready` — readiness recomputes; if affected, the period returns to `in-review` (T2 reverse); `certified` — void-on-change fires (§3.1, requirement-amendment class); sealed — **never retroactive**: amendments apply from their effective date forward, and every sealed period forever renders the requirement version that was in force when it sealed.
3. Definition changes (e.g., a modified debt-service basis) pin their effective boundary on the requirement record; the engine refuses a test spanning two definitions in one period without an explicit rule from the loan's own documents (fail-closed; 05 §7.1).

## 8. Period selector behavior (breadcrumb popover, per 08 §2)

- The period crumb on every spine surface is a **switcher, not dead text**: click (or `Enter` when focused) opens a popover listing this loan's periods, newest first — status dot + period label + deadline; sealed periods marked with the seal glyph (Lucide-only); the current period highlighted with ruled accent-family tokens.
- Grouped by cadence (quarterly / annual) when the loan carries both; type-to-filter for long histories.
- Switching navigates to the **same surface** for the chosen period (`/covenant/[loanId]/{newPeriod}/review` from Review) — surface persistence is the rule; the loan crumb is the same mechanic scoped to loans (switch loan, stay on Review).
- Keyboard: crumbs are focusable; arrow keys traverse the popover; `Esc` closes without navigation. Deep links produced here are the same URLs notifications and the quiet log use (08 §1.2).
- A period the viewer may not see does not render in the list (client scoping is structural, identity file I-7).

## 9. Acceptance tests (ticket-ready; all bind to the existing safety core — none re-implement it)

1. **No-fork proof:** CI greps `src/components/covenant/` and `src/app/(covenant)/` for period-status literals/unions outside the core and the one label dictionary — zero hits. All surface transitions call the core's typed API (the snapshot-attested safety core; `[UNVERIFIED-BY-SESSION]` symbol names are resolved at wiring time, in the core's spelling).
2. **Bexley canon walk (fixture: seeded demo book):** materialize → open → in-review → ready → certify (typed ceremony writes CertificationRecord {who, when, hash}) → send through the real `POST /api/covenant/send` → `package_sends` row appended, `reporting_periods.status` = sent, seal event logged. Assert every transition's audit event from §2 exists, in order.
3. **Agent immutability:** an agent-actor invocation of T3 and T5 refuses structurally (type + runtime), leaving state untouched and logging the refused attempt; the three gates accept only authenticated human sessions — no API path around either ceremony (R4 synthesis: reject Avalara's deadline auto-approve entirely — a missed deadline escalates loudly, it never auto-certifies; https://help.avalara.com/Returns/Monthly_filing_timeline_for_Managed_Returns).
4. **Void-on-change (fixture: Calloway Park FYE-2018 spine):** certify a period whose statement section reads the T-12; then correct one confirmed extracted value feeding it. Assert: VoidEvent names the actor, the change, and the affected fields; the period lands per §3.3; the voided record remains rendered VOIDED with reason; re-certify writes a new record; the old hash never re-validates.
5. **Void scope negative:** file a new document the certified package does not consume; star the loan; send a chase. Assert: certification stands — zero false voids (the dependency graph, not heuristics, decides).
6. **Seal immutability:** against a sealed Bexley period, attempt value edit, document re-file, checklist mutation, and record deletion via API — every attempt refuses with an honest error; the lit-row trace still resolves every certified figure to its exact source region (provenance file §6.2 chain).
7. **Cadence materialization (fixture: Calloway Park confirmed schedule):** confirming the SLOT-3 requirement set materializes a quarterly period with a Q+45d deadline and an annual period with FYE+120d; the rent-schedule checklist item appears at both cadences with the §8.02(b)(3) named fields; the Form 6241 ENERGY STAR item appears on annual periods only (rider-specific — per-loan extraction, 06 §3).
8. **Amendment forward-only:** apply an amendment fixture adding an annual deliverable mid-year. Assert: future periods carry the new item; the open period re-materializes visibly with provenance; sealed prior periods render the pre-amendment requirement version unchanged.
9. **Header/selector consistency:** all four spine surfaces render PeriodHeader from one core read (one number, everywhere); the period selector lists exactly the loan's periods with correct status dots; switching preserves surface; keyboard traversal per §8; a foreign-client period URL 404s.
10. **Ceremony budget:** the app contains exactly two heavy ceremonies (certify, send), verified by an inventory test over modal registrations — any third confirmation dialog fails the build (R4: NN/g).
