# CROSS-CUTTING — PERIOD LIFECYCLE: THE STATE MACHINE, VOID-ON-CHANGE, AND SEAL-NOT-WIPE, APP-WIDE

Ruling: the period state machine is `open → in-review → ready → certified → packaged`, with three human gates and agent immutability, and the send vertical extends it to `sent` (03 §2; snapshot §3 — the orchestration safety core exists as a **built** period ledger + deterministic state machine with **no end-to-end wiring**; `[UNVERIFIED-BY-SESSION]` on exact enum spelling). Completion **seals, never wipes** (memory `covenant-pane-model-and-hierarchy.md`, Terry-ruled 2026-07-01).

This file is the contract that wires every surface to that one core — it re-implements nothing — and expands 03 §2 to implementation grade: every transition's trigger/actor/prerequisites/reversibility/audit event, the void mechanics, the seal mechanics, cadence materialization, and the two shared components (period header, period selector). Companion files: `cross-cutting/loan-property-pledge-identity.md` (the identity spine the lifecycle runs on) and `cross-cutting/certify-send-audit-safety.md` (the act-level safety this state machine's gates execute — typed-act recording, the approved-bytes hash chain, append-only store semantics, the extended send gate). Division of labor: **that file owns the acts; this file owns the states the acts move.**

## 1. One state machine, one implementation (binding rules)

1. The safety core in `src/lib/covenant/` is the **sole** authority for period status. Surfaces never mutate status; they call the core's typed transition acts and render its output (02 §3: "no surface mutates status directly; all transitions go through typed acts + gates").
2. **No local enums.** No component, route, or fixture declares its own period-status union. Display labels map from the core's enum through one dictionary (`src/lib/covenant/period-labels.ts`, NEW); the spec binds to the core's spelling, whatever it is — the dictionary adapts copy, never forks state. The `[UNVERIFIED-BY-SESSION]` spelling risk is thereby contained to exactly one file.
3. **Agent immutability is structural.** The core's transition API authenticates actor class; agent-actor calls to any gated transition are refused by type and by runtime check — the agent may advance nothing past a human gate (04 §2; trust hierarchy LOCKED: the agent never certifies, never sends).
4. **The one REAL vertical is the wiring pattern.** `POST /api/covenant/send` already runs the gate against persisted state, appends `package_sends`, flips `reporting_periods.status → sent`, and fails honestly (503 without env, 404 on missing/foreign-org deal, 403 on gate deny) (snapshot §3). Every other transition wires the same way: server-side gate → persisted flip → append-only record → honest failure. No transition ships as client-side state.
5. **Period identity in transport** is always the `(loanId, period)` pair (02 §2; identity file §6.1); the period slug format is the core's existing spelling — surfaces render display labels through the dictionary, never re-derive them.

### 1.1 The transition API surface (contract; symbol names resolve to the core's at wiring time)

```ts
// src/lib/covenant/ — the safety core owns these; surfaces only call them
transition(loanId, period, act: TypedAct, actor: AuthedActor): Result
  // TypedAct ∈ { certify{hash, attestation}, send{transport, recipient}, waive{itemId, reason},
  //              disposition{exceptionId, reason}, void{reason} … } — each act carries its payload
  // Result: ok{newStatus, auditEventId} | refused{reason, blockingRefs[]}   — never a silent no-op
readiness(loanId, period): { ready: boolean; reasons: BlockingReason[] }
  // BlockingReason: { kind: checklist-incomplete | extraction-exception | normalization-exception |
  //                   failed-tie-out | undispositioned-finding | missing-definition | stale-input,
  //                   ref, deepLink }        — every reason links to its blocking item (04 §2.2)
periodLedger(loanId, period): AuditEvent[]   // append-only; feeds the quiet log and the timeline
```

## 2. The transition table (normative)

```text
(cadence) ─▶ OPEN ─▶ IN-REVIEW ─▶ READY ─▶ CERTIFIED ─▶ PACKAGED/SENT ─▶ (sealed)
                         ▲            ▲  │        │
                         └── VOID ────┴──┘        └── void-on-change returns here (§3.3)
```

| # | Transition | Trigger | Actor | Prerequisites | Reversibility | Audit event |
|---|---|---|---|---|---|---|
| T0 | (none) → `open` | Cadence occurrence materializes (§5) | Engine (scheduler) | Confirmed RequirementRecords exist for the loan | n/a — a period, once materialized, is never deleted; a mistaken materialization is end-dated with a typed reason (rare) | PeriodMaterialized {loanId, period, due-rule refs} |
| T1 | `open` → `in-review` | Checklist complete + extraction/normalization/computation done | Engine advances; the agent's preparation feeds it | Every ChecklistItem arrived/matched/waived; no blocking extraction exception | The main line never returns to `open`; a regressed checklist item (revoked waiver, invalidated arrival) renders as an awaiting-documents exception riding alongside (03 §2 exception table), not a state regression | PeriodAdvanced {from, to, checklist snapshot ref} |
| T2 | `in-review` → `ready` | All exceptions dispositioned + tie-out clean + readiness true | Engine (computed; Gate 1's human confirmations are inputs to it) | `readiness().reasons` empty | Yes — automatic: any new exception, stale flag, or failed tie-out returns the period to `in-review` | ReadinessReached {readiness proof ref} / ReadinessLost {new blocking ref} |
| T3 | `ready` → `certified` | **Gate 2 — the certify act** | **Human only, ever** | Readiness true; identity-bound authenticated session; role holds the certify right (unfixes the server-admin-only sender role, gap 4); typed confirmation in the ceremony modal (modal permitted: typed act with legal weight); content hash computed over the package revision | Only via void: automatic void-on-change (§3) or a deliberate human void with a mandatory typed reason (the GitHub review-dismissal pattern, §11) | CertificationRecord {who, when, content_hash} + quiet-log entry (loud in history, quiet in notifications, 04 §1) |
| T4 | `certified` → `in-review` \| `ready` (**VOID**) | Any change that touches certified content (§3.1) | Engine (automatic, never silent) | — | The void itself is irreversible; the voided CertificationRecord remains in the timeline forever, labeled VOIDED with reason | VoidEvent (shape in §3.2) |
| T5 | `certified` → `packaged/sent` | **Gate 3 — the send act**, executed against `POST /api/covenant/send` (REAL) | **Human only, ever** | Certification present and un-voided; sent-bytes hash = certified hash (approved bytes, 02 §6); org/role scope passes; recipient + transport confirmed in the send ceremony | **Irreversible — a send can never be undone.** Corrections are subsequent records (§4.3), never edits | SendRecord (append-only) {transport, recipient, bytes hash, gate result, actor} |
| T6 | `packaged/sent` → sealed | Post-condition of T5 (same act) | Engine | — | Irreversible by design | PeriodSealed {timestamp} — quiet-logged (03 §1 step 13) |

### 2.0 Current build → target wiring (what changes on each surface; nothing re-implements)

| Surface today (snapshot §3) | Target wiring |
|---|---|
| Review room renders fixture status strings | Status + exception order from core reads; disposition acts call `transition()` |
| Composer readiness is decorative (C5) | Readiness block renders `readiness().reasons` verbatim; zero surface-local readiness logic |
| Certificate route (REAL resolver + tests) renders a sheet that cannot certify | The ceremony calls the core's certify act; the sheet renders CertificationRecord state (hash chip, void banner) |
| Send is REAL but has no UI wired as the user's moment | The send surface fronts the existing route; gate refusals render exactly as returned (honest 503/404/403) |
| Home/Inbox/Calendar fixture counts disagree (U3-F2/U3-F7 class) | All period-status summaries project `periodLedger`/status reads — one source, contradictions structurally impossible (07 §2) |

### 2.1 The three human gates, expanded (gate law: every gate is a decision, not an event — 04 §2.2)

Each gate names: the exact ask · why blocked · evidence beside the decision · affected fields · recommendation · consequence of delay · audit record.

- **Gate 1 — into review (confirmation gate).** Ask: "are these extracted requirements/mappings/values what the documents say?" First period per loan: full confirmation pass; every period after: exception-only (ask-once — the agent never re-asks a remembered answer, 03 §3). Evidence: source region beside each proposal (65/35 doc-inspector split, 05 §4). Consequence of delay: the period cannot reach `in-review`; the deadline clock runs. Audit: per-record confirmation acts.
- **Gate 2 — certify.** Ask: "I certify these contents" over the restated loan, period, package revision, and content hash. Blocked-with-reasons until `readiness().ready` — the Certify button renders each `BlockingReason` as its own row deep-linking to the fix (required-checks pattern, §11), never a bare disabled button. Affected fields: the whole package revision (hash-bound). Consequence of delay: deadline escalation ladder (§5). Audit: CertificationRecord.
- **Gate 3 — send.** Ask: "send this certified package to this recipient now" with the certified-bytes hash and transport restated. Deliberately **non-identical** to the certify ceremony (recipient-and-hash review, not typed attestation) so neither trains reflex for the other (NN/g ceremony budget, §11). Consequence of delay: a late package is a compliance failure — the one push-eligible escalation class (04 §2.3). Audit: SendRecord + seal.

These are the product's only two heavy ceremonies (certify, send) plus one confirmation pass; nothing else in Covenant gets a confirmation dialog (§11).

## 3. Void-on-change mechanics

### 3.1 What voids (change classes)

A certification pins a content hash over the package revision's field map and attached bytes (03 §2). The engine derives "feeds certified content" from the **same provenance chains the lit-row trace uses** (`cross-cutting/provenance-lit-row-trace.md` §2) — one dependency graph, not a second bookkeeping system. A write voids iff its `value_id` (or its document/version) appears in the certified revision's chain set:

| Class | Example |
|---|---|
| Corrected ExtractedValue feeding any package field | A confirmed rent-schedule field is corrected after certify |
| Replaced document upstream of any package field | A revised T-12 supersedes the one the statement section reads |
| COA mapping change that moves a normalized line the package consumes | Remapping a PMS code shifts NOI inputs (05 §2.1) |
| Requirement amendment touching this period (§7) | An amendment changes a deliverable already in the certified checklist |
| Identity/pledge change feeding collateral or identity fields | A pledge end-date changes what the package states about collateral (`cross-cutting/loan-property-pledge-identity.md` §8) |
| Memory-entry correction that re-fills a package answer | The remembered management-fee answer is corrected and re-fills the questionnaire field |
| Any package composition change | Section added/removed; narrative edited — even narrative edits void: lender packages are stricter than release notes (GitHub Releases adapt, §11) |

Non-voiding (never invalidate a certification): reads and traces; saved views, stars, filters; chase messages and quiet-log growth; Q&A thread activity; a newly filed document that no certified field consumes; corrections scoped to other loans or periods. The dependency graph decides — never surface guesswork, never a heuristic.

### 3.2 The void event and how it renders

```ts
VoidEvent {
  voided_cert_id, when
  cause: { actor, act_ref }             // who made the change and what the change was
  affected_fields: PackageFieldRef[]    // every certified field whose value moved
  diff_ref                              // field-level before/after, both sides lit-row traceable
  return_state: 'in-review' | 'ready'   // per §3.3
}
```

- **Never silent** (03 §2). A banner in the period header (§6) on every spine surface: `Certification voided {when} — {actor}: {change summary} → {n} fields moved · View diff`, persistent until re-certified.
- The voided CertificationRecord stays in the period timeline, labeled VOIDED with its reason and diff link — the stale-review pattern: dismissed, kept, explained (§11).
- The diff view opens **beside** evidence (never a context-destroying modal): field-level before/after, each side traceable to its source chain.
- The your-move query picks it up: a voided period awaiting re-certify is blocked on a human (03 §4), so it surfaces on Home and the Inbox badge — one computed query, everywhere (08 §8).

### 3.3 Return-to-state rule (deterministic)

On void, the engine recomputes the pipeline for the affected values, then places the period: readiness still true (change absorbed; all exceptions clear; tie-out clean) → `ready`, one re-certify away; otherwise → `in-review` with the new exceptions listed. Nothing re-certifies silently; re-certifying is a fresh T3 producing a **new** CertificationRecord — records are append-only corrections, never edits (Modern Treasury pattern, §11).

## 4. Seal-not-wipe implementation

### 4.1 What sealing freezes

Every row addressed by `(loanId, period)`: checklist items and their chase history, extracted-value period linkage, normalized lines, metrics, test results and verdicts, tie-outs, package revisions and their bytes, the certification record(s) including voided ones, send records, and the period's activity events. Documents' original bytes were already immutable (four-artifact law); the seal freezes the period's **linkage set** — which document versions, which values, which verdicts shipped.

### 4.2 What remains readable — everything, forever

- Reports opens the sealed record read-only: the review/certificate render with the sealed banner, every figure still lit-row traceable (provenance file §5, "Sealed period" row).
- Lender Q&A drafts ground in sealed figures with their chains (06 chain E) — the sealed period is the answering record for follow-ups.
- Trend marks on Loan Detail click through to their sealed periods (chart doctrine: every mark an instrument).
- **Nothing inside a sealed period is deletable** — not a document link, not a chase record, not a voided certification. Retirement anywhere in the object graph is end-dating, never deletion (02 §5).
- Sealed render mode: mutation affordances are absent (not disabled-mystery); confirm/correct/compose actions do not render; the trace, export, and Q&A-reference affordances do.

### 4.3 What is still writable — append-only records *about* the sealed thing

Reads, exports, download events; new SendRecord rows (the send register is append-only by construction, 02 §2); Q&A threads referencing sealed figures; activity events. The sealed content itself never changes. A late-discovered error is handled **forward**: in correspondence, in the next period, or — if a corrected resubmission to the lender is ever required — as a new package revision whose certify→send re-runs T3/T5 with append-only records while every prior record stands (PROPOSED, §9).

### 4.4 The sealed banner

Rendered by the period header (§6) on every surface showing a sealed period: `Sealed — sent {date} via {transport} · read-only forever, fully inspectable`, with the hash chip (click = full hash + verify). It explains what sealing means rather than just stating it (send-record brief direction). Visual: gray-ladder rungs + mono hash treatment (Geist Mono, `tabular-nums slashed-zero`); no new colors — sealed is a neutral archival state, not a severity.

## 5. Cadence materialization (RequirementRecord due-rules → periods with checklists; gap 9 / roadmap F5)

- Each confirmed RequirementRecord carries `cadence: {shape, due_rule}` — evidence: 6001.NR §8.02(b): quarterly YTD income statement **Q+45d** (Q1–Q3); annual statements incl. cash flows **FYE+120d**; rent schedule at both cadences with named fields; on-request items rate-capped at ≤1 per 6 months absent default (SLOT-3; 05 §1).
- At each cadence occurrence the engine materializes: a **ReportingPeriod** (T0) keyed `(loanId, period)` — the address every deep link uses (08 §1) — plus its **ChecklistItems** (one per expected deliverable, each carrying its source requirement ref, 02 §2) plus its **Deadline** rows (due date computed from the due-rule; escalation ladder remind → chase → your-move → push per org policy, 03 §5 — deadline escalation is the ONE push-eligible class).
- **Period grain = cadence occurrence.** A loan with quarterly and annual obligations gets a quarterly period per quarter and an annual period at fiscal year-end, each with its own checklist. Whether year-end items fold into one combined submission is per the loan's own confirmed schedule — never a product-side merge (domain firewall).
- **On-request items** (§8.02(b)(4)) do not pre-materialize: a lender's written request arrives through intake, matches the loan, and adds a one-shot checklist item to the current open period, with the rate-cap rendered beside it (an evidence fact, not a product invention).
- **Timing.** Materialization runs at the cadence boundary ("Month start: E schedules cadence occurrences → new OPEN periods appear with checklists," 03 §4). A newly confirmed loan materializes its current period immediately and never backfills history it wasn't tracking. Amendments re-materialize forward only (§7).
- **Fiscal calendars are per-loan facts.** `FYE+120d` computes from the loan's own confirmed fiscal year-end, not an assumed calendar year; quarter boundaries likewise follow the confirmed schedule. The due-rule engine consumes confirmed records only — an unconfirmed schedule materializes nothing; it is an extraction exception, never a guessed calendar (fail-closed; `cross-cutting/notifications-deadlines-escalation.md` §2.1).
- **Clustering is structural:** every Q+45d rule lands the book's deadlines in the same fortnight. The agenda-first Calendar carries that load; a month grid would collapse exactly when it matters (the "+N more" failure — anti-pattern, §11).

## 6. The period header component (NEW `PeriodHeader`, `src/components/covenant/period/PeriodHeader.tsx`)

Rendered on **every spine surface** — Review, Composer, Certificate, Actuals — directly under the 48px shell header. It is the surface-level identity band the breadcrumb altitude gate promises: a spine surface must always say which loan+period it serves (08 §2.3).

```ts
PeriodHeader { loanId, period }   // everything else derives from core reads:
  // status ← safety core · deadline ← Deadline row (Calendar owns; this is a computed summary, 02 §3)
  // blocking ← readiness().reasons · banners ← VoidEvent / seal state
```

Anatomy (left → right, one row, uniform height, open-not-boxed):

1. **Identity cluster** — loan display name + period label (echoes the crumbs; names via the identity accessor, `loan-property-pledge-identity.md` §3–§4.0).
2. **Status chip** — the core enum through the one label dictionary (§1.2), with status dot; color is severity-only (chart doctrine): verdicts color, ordinary lifecycle states sit on gray-ladder rungs.
3. **Deadline** — due date + days remaining; overdue escalates per policy (§5); the deadline deep-links to Calendar's owning row.
4. **Gate/blocking summary** — when blocked: `{n} items block readiness` via the shared CountBadge (ruled 2026-08-07), popover listing each `BlockingReason` as a deep link (the blocked state always explains itself, §11).
5. **Banner slots** — void banner (§3.2) and sealed banner (§4.4); mutually exclusive by construction (a sealed period cannot void).

Reuse: CountBadge (shared component); the shell breadcrumb (existing) carries the crumbs above it — the header never duplicates the crumb switchers, it echoes identity. The header renders state and mutates nothing; all acts live on their owning surfaces (no double-homing, 02 §3).

Responsive behavior (no silent compression, 08 §9): at full width (≥1440px) all five elements render in one row; 1280–1439px the gate summary collapses to the CountBadge only (popover unchanged); below 1280px the header wraps to two rows — identity + status first, deadline + gates second — and banner slots always span full width. Minimum viable: the header never drops identity or status at any width; a spine surface below the frame minimum shows the frame's "larger window required" state, not a truncated header.

## 7. Amendment handling (loan modification mid-period)

1. The amendment document arrives through intake like any arrival, routes to the loan, and re-opens extraction **only for the affected RequirementRecords** (02 §5); confirmation versions each record with its new source region; prior versions stay in history.
2. Effects by period state: `open` / `in-review` — the checklist re-materializes visibly (items added/removed, each change provenance-linked to the amendment document; quiet-logged); `ready` — readiness recomputes; if affected, the period returns to `in-review` (T2 reverse); `certified` — void-on-change fires (§3.1, requirement-amendment class); **sealed — never retroactive**: amendments apply from their effective date forward, and every sealed period forever renders the requirement version in force when it sealed.
3. Definition changes (e.g., a modified debt-service basis) pin their effective boundary on the requirement record; the engine refuses a test spanning two definitions in one period without an explicit rule from the loan's own documents (fail-closed; 05 §7.1). Rider evidence for per-loan extraction: Form 6241 exists only because this loan carries the Green rider (06 §3) — amendments are per-loan facts, never templated across loans.

## 8. Period selector behavior (breadcrumb popover, per 08 §2) + states

- The period crumb on every spine surface is a **switcher, not dead text**: click (or `Enter` when focused) opens a popover listing this loan's periods, newest first — status dot + period label + deadline per row; sealed periods marked with the seal glyph (Lucide-only); the current period highlighted with ruled accent-family tokens (#7189FF family roles; no new colors).
- Grouped by cadence (quarterly / annual) when the loan carries both; type-to-filter for long histories; rows are uniform height.
- Switching navigates to the **same surface** for the chosen period (`/covenant/[loanId]/{newPeriod}/review` from Review) — surface persistence is the rule. The loan crumb is the same mechanic scoped to loans (switch loan, stay on Review).
- Keyboard: crumbs focusable; arrows traverse; `Enter` navigates; `Esc` closes without navigating. The deep links produced are the same URLs notifications and the quiet log use (08 §1.2).
- A period the viewer may not see does not render in the list (client scoping is structural; identity file I-7).

States (the lifecycle-relevant set; per-surface briefs carry their full state tables):

| State | Behavior |
|---|---|
| Loading | Header skeleton band; identity renders first; status/deadline follow from the core read |
| Open, awaiting documents | Status chip + checklist-gap summary; chase history discoverable, not pushed |
| Blocked (missing definition, failed tie-out) | Gate summary lists reasons as deep links; no plausible-placeholder numbers anywhere (04 §2.5) |
| Overdue | Deadline element escalates per org policy — the one push class; header links the escalation record |
| Certified | Hash chip renders in the header; any change → §3 |
| Voided | Void banner until re-certified; timeline keeps the VOIDED record |
| Sealed | Sealed banner; read-only render mode (§4.2); selector marks the seal glyph |
| Permission-limited | Status renders; gated actions absent; foreign-scope URLs 404 honestly |
| Recovery / undo | Certification recovers via void + re-certify (§3.3); **send has no undo by design** — the ceremony states its irreversibility plainly, and corrections travel forward (§4.3) |

## 9. Ownership boundaries (what this file does NOT own) and build target

### 9.1 Boundaries — one owner per system, summaries elsewhere (02 §3)

| Adjacent system | Owner | This file's relationship |
|---|---|---|
| Typed-act inventory, approved-bytes hash chain, append-only store semantics, the extended send-gate check order | `cross-cutting/certify-send-audit-safety.md` | The gates in §2 execute those acts; this file never restates the hash chain, only requires it as a T3/T5 prerequisite |
| Verdict/basis vocabulary and the period-vocabulary word list | `cross-cutting/status-vocabulary-shortfall-breach.md` (§4 there) | The label dictionary (§1.2) renders that fixed vocabulary; copy rules live there |
| Due-rule engine detail, escalation ladder, your-move query, notification channels | `cross-cutting/notifications-deadlines-escalation.md` | §5 here defines *what materializes* (periods + checklists + deadline rows); the ladder that fires on those rows is owned there |
| Identity graph, `(loanId, period)` addressing, effective-dated pledges | `cross-cutting/loan-property-pledge-identity.md` | Consumed; sealed renders use its `asOf` reads |
| Provenance chains (the void closure's dependency graph) | `cross-cutting/provenance-lit-row-trace.md` | §3.1 consumes the chain set; no second dependency map exists |
| Certify/send ceremony surface design | `surface-briefs/certificate-certify-gate.md`, `surface-briefs/send-record.md` | The ceremonies render there; the state machine they drive is here |

### 9.2 Build target (basis-v2; kit law 12)

| Piece | Landing place |
|---|---|
| The state machine + period ledger | The existing orchestration safety core in `src/lib/covenant/` (snapshot §3 — built; this plan wires it, never re-implements) |
| Label dictionary (§1.2) | `src/lib/covenant/period-labels.ts` (NEW; the one enum-spelling adapter) |
| `PeriodHeader` (§6) | `src/components/covenant/period/PeriodHeader.tsx` (NEW) |
| Period selector popover (§8) | Extends the shell breadcrumb (`CovenantShell.tsx` header) — the crumb becomes a switcher per 08 §2; no new frame region |
| Cadence materializer | The F5 due-rule engine (`src/lib/covenant/` — with `cross-cutting/notifications-deadlines-escalation.md` §2) writing `reporting_periods` + checklist + deadline rows |
| The send transition | The existing `POST /api/covenant/send` (REAL) — extended per the safety file, same route |
| Spine routes rendering the header | `/covenant/[loanId]/[period]/{review\|composer\|certificate\|actuals}` (08 §1); the certificate route exists with resolver + tests |

## 10. Decisions this file makes vs routes

| Item | Status |
|---|---|
| Void return-state rule (§3.3), non-voiding classes (§3.1), one dependency graph via provenance chains | Design consequence of ruled law — no ruling needed |
| Manual human void with mandatory typed reason (T3 reversibility) | Design expansion consistent with void-on-change + the audit posture; flagged for ratification with the certificate brief |
| Post-send correction path (§4.3): new revision re-runs T3/T5, append-only, prior records stand | **PROPOSED** — consistent with `SendRecord[]` plurality (02 §2); routed for ratification, not assumed; default until then: corrections travel via correspondence and the next period |
| Escalation ladder defaults (remind → chase → your-move → push) | Org-configurable per 03 §5; defaults live in the Settings brief |
| Period display-label copy (the §1.2 dictionary's words) | Fixed by the status-vocabulary law's period vocabulary (`cross-cutting/status-vocabulary-shortfall-breach.md` §4); copy changes route through that file, never per-surface |

## 11. Benchmarks consumed (limited roles; official URLs from R4/R5 research)

- **GitHub branch protection / required checks** — readiness as named, linked requirements; the blocked state always explains itself. Take. https://docs.github.com/en/pull-requests/reference/status-checks
- **GitHub stale-review dismissal** — approval bound to a content state; dismissed visibly, kept in the timeline, with a mandatory reason. Take (= void-on-change). Reject: bypass lists and admin exemptions — Covenant's gates bind everyone, including admins. https://docs.github.com/en/pull-requests/how-tos/review-pull-requests/dismissing-a-pull-request-review
- **GitHub Releases (immutable releases)** — draft → publish → frozen content + verifiable attestation; adapt: even narrative edits void after certify (lender packages are stricter than release notes). https://docs.github.com/en/code-security/concepts/supply-chain-security/immutable-releases
- **DocuSign** — identity-bound ceremony + the Certificate-of-Completion-style audit record; adapt: typed attestation instead of signature adoption. https://support.docusign.com/guides/ndse-user-guide-history-coc
- **Modern Treasury** — append-only records; corrections are subsequent records, never edits; approved content is locked. Take. https://moderntreasury.com/learn/data-immutability
- **Avalara Managed Returns** — the reporting-cadence rhythm (reconcile → review → approve within a deadline window). **The one hard rejection: no auto-approve at any deadline, ever** — a missed deadline escalates loudly; it never auto-certifies (Terry law: the agent never certifies, never sends). https://help.avalara.com/Returns/Monthly_filing_timeline_for_Managed_Returns
- **NN/g confirmation-dialog research** — the ceremony budget: exactly two heavy, mutually distinct ceremonies (certify, send); zero "Are you sure?" dialogs anywhere else. https://www.nngroup.com/articles/confirmation-dialog/
- **Google Calendar month grid** — anti-pattern only: "+N more" collapse hides clustered deadlines exactly when heaviest; agenda-first stands. https://support.google.com/calendar/answer/34580

## 12. Acceptance tests (ticket-ready; all bind to the existing safety core — none re-implement it)

1. **No-fork proof.** CI greps `src/components/covenant/` and `src/app/(covenant)/` for period-status literals/unions outside the core and the one label dictionary — zero hits. All surface transitions call the core's typed API (§1.1); symbol names resolve to the core's spelling at wiring time.
2. **Bexley canon walk (fixture: seeded demo book).** Materialize → open → in-review → ready → certify (ceremony writes CertificationRecord {who, when, hash}) → send through the real `POST /api/covenant/send` → `package_sends` row appended, `reporting_periods.status` = sent, seal event logged. Assert every §2 audit event exists, in order, in `periodLedger`.
3. **Agent immutability.** An agent-actor invocation of T3 and T5 refuses structurally (type + runtime), leaves state untouched, and logs the refused attempt; the gates accept only authenticated human sessions; no API path exists around either ceremony (the Avalara rejection, §11).
4. **Void-on-change (fixture: Calloway Park FYE-2018 spine).** Certify a period whose statement section reads the T-12; correct one confirmed extracted value feeding it. Assert: VoidEvent names actor, change, and affected fields; the period lands per §3.3; the voided record renders VOIDED with reason; re-certify writes a new record; the old hash never re-validates.
5. **Void scope negative.** File a new document the certified package does not consume; star the loan; send a chase. Assert: the certification stands — zero false voids (the dependency graph decides, §3.1).
6. **Seal immutability.** Against a sealed Bexley period: attempt value edit, document re-file, checklist mutation, and record deletion via API — every attempt refuses with an honest error; the lit-row trace still resolves every certified figure to its exact source region (provenance file §6.2 chain); sealed render mode hides mutation affordances entirely.
7. **Cadence materialization (fixture: Calloway Park confirmed schedule).** Confirming the SLOT-3 requirement set materializes a quarterly period with a Q+45d deadline and an annual period with FYE+120d; the rent-schedule checklist item appears at both cadences with the §8.02(b)(3) named fields; the Form 6241 ENERGY STAR item appears on annual periods only (rider-specific, per-loan).
8. **On-request handling.** An intake-arrived lender request adds a one-shot checklist item to the current open period with the rate-cap note rendered; no new period materializes.
9. **Amendment forward-only.** Apply an amendment fixture adding an annual deliverable mid-year: future periods carry the new item; the open period re-materializes visibly with provenance to the amendment document; sealed prior periods render the pre-amendment requirement version unchanged.
10. **Header/selector consistency.** All four spine surfaces render PeriodHeader from one core read (one number, everywhere — the U3-F2 class of contradiction is structurally impossible); the selector lists exactly the loan's periods with correct status dots; switching preserves surface; keyboard traversal per §8; a foreign-client period URL 404s.
11. **Readiness contract.** With a failed tie-out fixture, `readiness()` returns `ready:false` with a `failed-tie-out` reason whose deep link opens the Review tie-out module; Certify renders that reason as a linked row; disposition flips readiness without any surface-local state.
12. **Ceremony budget.** The app contains exactly two heavy ceremonies (certify, send), verified by an inventory test over registered modals — any third confirmation dialog fails the build (NN/g, §11).
13. **Label dictionary totality.** `period-labels.ts` maps every enum member the core exports (exhaustiveness-checked at compile time); adding a core state without a label fails the build; the dictionary contains the fixed vocabulary words from the status-vocabulary law (§9.1) and nothing else.
14. **Header responsiveness.** At 1728/1440/1280 fixtures the PeriodHeader renders per §6's responsive rules with zero silent compression (snapshot tests); banner slots span full width at every breakpoint; identity and status render at all widths.
