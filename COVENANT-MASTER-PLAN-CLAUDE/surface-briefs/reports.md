# REPORTS (SENT-PACKAGE AUDIT REGISTER)

Surface brief per the 18-section template (10-PER-SURFACE-BLUEPRINT-TEMPLATE). Direction of record: DIRECTIONS `reports.md` (Tier A/B, verdict ADJUST — the Terry-named gold standard for Covenant list pages; preserve the pattern, wire it). Research spine: R5 (registers, saved views) + R4 (immutable record listing). Model spine: 02 §2/§3/§6 (CertificationRecord, SendRecord, seal-not-wipe), 03 §2 (period lifecycle), 05 §5 (engine-to-surface contract rows: Certification, Send), 06 §6 (Chain E).

## 1. Identity and verdict

- Product layer: portfolio floor (inventory B5, 07 §1).
- Route/address: `/reports` in the `(covenant)` route group — `src/app/(covenant)/reports/page.tsx`, mounting `CovenantShell` with the Reports view (08 §1). Today the view mounts through `/review-room` (snapshot §3). Components in `src/components/covenant/reports/`; register queries in `src/lib/covenant/` read paths; tokens from `src/styles/covenant-tokens.css` (law 12).
- Current build state: `FIXTURE` — the list page renders demo strings; but its backing stores are partly REAL: `POST /api/covenant/send` already writes append-only `package_sends` rows and flips `reporting_periods.status → sent` (snapshot §3 — the one real vertical). Reports is the natural first *reader* of that real store.
- Existing-surface verdict: `ADJUST`. Terry named this page **the gold standard for Covenant list pages** (snapshot §3, §5: count+CTA header, filter cluster, no charts). The pattern is preserved exactly; the work is wiring it to the real certification/send stores and completing the audit-register semantics (hash chips, sealed-record open, audit scopes).
- Research tier: A/B (DIRECTIONS).
- Primary users/roles: owner (full register); PMC preparer (client-scoped register); reviewer/auditor (read-only — this surface is their primary destination); the lender is never a user (audience only).
- Frequency and session duration: bursts after each cycle's sends (confirm the record); episodic audit sessions ("what did we send {lender} in 2026?") that may run 10–20 minutes; lender-question lookups (find the sealed figure fast).
- Error cost: the register **is the borrower's proof of compliance**. A register that misstates who certified, when a package went, or what bytes went would corrupt the audit trail — so rows render exclusively from the append-only CertificationRecord and SendRecord stores, never from derived or cached state (02 §5; 04 §2.3). No new number is computed here; the terminal error (wrong number certified/sent) cannot originate here — but a register that *hides* a send or shows a wrong hash would destroy the trust the gates earned.
- Success criterion: every certified-and-sent package findable in seconds; every row provably tied to its bytes (hash chip verifies); every audit question expressible as a filter scope; the page remains the pattern-donor other Covenant registers copy.

## 2. User job and decisions

- Primary job-to-be-done: answer — with proof — "what did we send, to whom, when, certified by whom, and is the record intact," across the whole book.
- Decisions made here:
  1. Which sealed record to open (read-only inspection).
  2. Which audit scope to apply and optionally save/share ("everything sent to {lender} in 2026" as a named view).
  3. Export the register (CSV of register metadata — an explicit act).
  4. Verify a hash (on-demand integrity check of a row).
  - Nothing mutating beyond view management: the register is read-only by essence.
- Questions the surface must answer in scan order:
  1. How many packages exist, and how many this cycle (count header)?
  2. What went out most recently, and is anything certified but not yet sent?
  3. For any row: who certified, when; when sent, how (transport); which bytes (hash)?
  4. For any scope (lender, loan, period range): the complete, untruncated set.
  5. For any row: the full sealed record, one click away, read-only.
- What the user should not have to decide here: nothing about package content (sealed); nothing about period state (spine owns transitions); no certify/send acts (those ceremonies live on the certificate/send surfaces — Reports only records their outcomes); no interpretation of verdicts (the sealed record itself renders them).
- Entry paths: rail item Reports (`G R`, 08 §5); Loan Detail PERIODS tab's sealed links; the send surface's post-send receipt ("view in Reports"); Home's cycle strip "sent n" count; palette object results ("{loan} Q2 package").
- Exit paths: row → the sealed period record (read-only Review/Certificate render with the sealed banner — 07 §3: "Reports ──sent package──▶ sealed period record"); loan cell → `/loans/[loanId]`; certified-awaiting-send row → `/covenant/[loanId]/[period]/certificate`.
- Completion/advancement conditions: none — nothing advances a period from here. The register grows only by acts performed at the gates.

## 3. Object and ownership model

| Object/action/status | Owned here? | Summary/link elsewhere | Address/identity | Notes |
|---|---:|---|---|---|
| The sent-package register (projection + its filters/scopes) | YES | Loan Detail PERIODS tab shows a per-loan slice as links; Home shows the cycle's sent count | — | Reports is deliberately a projection owner: its owned object is the *register view*, not the records |
| Saved views over the register (audit scopes) | YES (instances) | — | `viewId` | One shared mechanism app-wide (02 §2 SavedView) |
| CSV export act | YES (typed act, logged) | — | export event | Register metadata only — never package contents (§4) |
| Hash verify act | YES (on-demand engine check) | — | verify event | Recompute/compare against stored certified hash |
| CertificationRecord | NO — Certificate surface | Rendered as the Certified chip (who/when) per row | `certId` | 02 §3: "Certification act + record → Certificate surface … Reports lists it" |
| SendRecord (append-only) | NO — Send & Record | Rendered as Sent chip (when/transport) + hash chip; multiple sends listed in row detail | `sendId` | Already REAL at `POST /api/covenant/send` (snapshot §3) |
| Sealed ReportingPeriod + Package revision | NO — orchestration spine / Composer | Row opens the sealed record read-only | `(loanId, period, rev)` | Seal-not-wipe (02 §5) |
| Documents inside packages | NO — Documents | Reachable only inside the opened sealed record | `docId` | No document listing here (no double-homing) |
| Period status transitions | NO — spine | Status column reflects sealed/sent | — | Reports never mutates status |

No-double-homing boundary: Reports owns **the audit register projection, its saved scopes, and its export** — nothing else. The records it lists are owned by the Certificate and Send surfaces; the sealed periods it opens are owned by the spine; it summarizes and links, and no other surface renders a competing send-history register (Loan Detail's PERIODS tab links here for history depth).

## 4. Data and semantic model

- Source facts: none arrive here; the register reads persisted records.
- Extracted values awaiting confirmation: none — by construction, nothing unconfirmed can appear (only certified-and/or-sent packages list).
- Confirmed values: the row fields are all record-backed: period label, loan (outward name), lender/servicer, certification {who, when}, send {when, transport}, bytes hash, seal status.
- Deterministic outputs: the register query itself (filter → row set, count header math); hash verification (recompute/compare — engine); CSV serialization. No metric is computed here.
- Agent proposals/drafts: none render on the register. The agent's only presence is the frame-level Ask entry (highlight a row → ask; answers ground in the sealed record and cite it — agent brief). No chat chrome (law 10).
- Human decisions: open, filter, save/share views, export, verify. All read-side.
- Certified values: the Certified chip renders the CertificationRecord (who/when); the certification's content hash is the row's hash identity. Void state cannot appear on a sent row (the send gate refuses a voided certification — 02 §6); a certified-then-voided *unsent* package never lists in the default register (it is spine work-in-progress, visible via the awaiting-send segment as its current state).
- Versions/periods/packages: a row addresses `(loanId, period, rev)` — the certified revision that was sent. Re-sends of the same revision append SendRecords (row detail lists all, newest summarized in the Sent chip). A corrected package after sending is a **new revision with its own certification and send** — it lists as its own row; the register never edits history (append-only truth, R4: Modern Treasury).
- Evidence/provenance: the row's chips are provenance: Certified chip → opens the certification record (who/when/hash, void history if any); Sent chip → the SendRecord receipt; Hash chip → full hash + verify + copy. Inside the opened sealed record, every figure keeps its full lit-row chain (06 §6 Chain E: "forever after: the sealed period answers lender follow-ups with exact provenance").
- Permissions/read-only projections: PMC preparers see client-scoped rows; reviewers see the register read-only (no export where the org restricts it — role-configurable); foreign-org records are 404-invisible, matching the send API's honest scoping (snapshot §3).

Field groups and grain (template checklist applied):

| Grain | Presence on this surface |
|---|---|
| Organization | The register's scope boundary; export policy |
| Client (PMC) | Row scoping + filter chip; invisible in owner mode (02 §1) |
| Sponsor / Portfolio | Filter dimensions only (portfolio filter dedupes by property, 02 §1) |
| Property | Not a column (loan-grain register); reachable inside the sealed record |
| Loan / Pledge | Loan column (outward name); pledge absent |
| Reporting period | Period column; row identity |
| Document | Absent from the register (inside sealed records only) |
| Requirement / Test / Metric | Absent — no verdict columns on the register (the sealed record renders verdicts); status here means *record* status (sealed), never test outcome |
| Package section | Absent (inside the sealed record) |
| Certification record | Certified chip (who/when) + hash identity |
| Send record | Sent chip (when/transport) + append-only detail list |

- Vocabulary note: the register's Status column speaks record vocabulary (certified · sent · sealed), never verdict vocabulary (law 3). A package containing a shortfall narrative lists identically to a clean one — the record is the record.

## 5. State machine and exceptions

The register itself has no lifecycle; its rows project the spine's terminal states. Row-level states:

| Row state | Meaning | Source of truth | Row rendering |
|---|---|---|---|
| certified-awaiting-send | Gate 2 passed, gate 3 not yet | CertificationRecord present, no SendRecord | Segment-filtered row (not in default view); Certified chip live, Sent column "—", link → certificate route's send panel |
| sent/sealed (default register population) | Gate 3 passed; period sealed | SendRecord + `reporting_periods.status = sent` | Full row; sealed badge |
| re-sent | Additional SendRecords on the same revision | Append-only SendRecords | Sent chip shows latest + "×{n}" count; detail lists all |
| superseded-by-revision | A later revision was certified and sent | Newer row exists for same (loanId, period) | Both rows list; older carries "superseded by rev {n}" note — history never edits |
| verify-passed / verify-failed | On-demand hash check outcome | Engine recompute vs stored hash | Transient confirmation on the chip; a mismatch renders the integrity alarm (below) |

Template state coverage, explicitly:

- **empty** — no packages yet: first-cycle empty state explaining how rows are born ("packages appear here after you certify and send — your first period is {status}" + link to the current period). Never a blank table.
- **awaiting documents / loading/processing** — loading skeleton rows with the count header last; the register never renders a partial set silently: if a page of rows is still loading, the header says "loading {n} of {m}".
- **partial/incomplete** — the certified-awaiting-send segment is the only "incomplete" concept, and it is a deliberate scope, not default noise (Ramp readiness-gating logic inverted: the default register admits only completed records — R5).
- **extracted/unconfirmed / low confidence / conflict** — n/a by construction: nothing pre-gate can appear. Asserted in tests, not merely assumed.
- **stale/superseded** — the superseded-by-revision row state above; append-only, both rows permanent.
- **failed tie-out / watch / shortfall / breach** — never rendered as register columns (vocabulary note, §4); the opened sealed record shows its own verdicts in place.
- **permission denied** — client-scoped rows absent for PMC preparers; a deep link to a foreign-org record resolves 404 (matching the send API's scoping honesty); an org-internal role denial renders 403 with the reason.
- **read-only** — the entire surface, structurally; additionally the opened sealed record renders the sealed banner ("read-only forever, fully inspectable" — DIRECTIONS send-record) with zero mutation affordances.
- **blocked/gated** — n/a for rows; the export act may be role-gated (renders the denial reason, never a dead button).
- **certified (void-on-change)** — a void event on an awaiting-send row drops it from the segment (it returns to spine work); the revision's void history remains visible inside the certification record opened from any listed row — full honesty at the record level, no register row for work-in-progress.
- **sent/sealed** — the register's home state; seal-not-wipe guarantees every row is permanently addressable (02 §5).
- **integrity alarm** (this surface's one exceptional state): a hash verify mismatch — should be impossible; renders a maximum-honesty error naming both hashes, timestamps, and "contact support / audit log" guidance; writes a loud ActivityEvent. Never silently re-verified.
- **recovery/undo** — none exists by design: no register act mutates records; export and verify are repeatable; **a send can never be undone** (R4: the irreversibility asymmetry — certification voids by change, a send never cancels); corrections happen as new revisions, listed alongside.

Audit events this surface writes (append-only, named):

| Act | ActivityEvent | Payload beyond identity |
|---|---|---|
| CSV export | `register.exported` | who, scope (filters restated), row count, column set |
| Hash verify | `register.hash_verified` | who, row identity, outcome (pass/mismatch), both hashes on mismatch |
| Integrity alarm raised | `register.integrity_alarm` | row identity, hashes, timestamps — loud (surfaced, not just logged) |
| Sealed-record open (per org audit policy) | `register.sealed_opened` | who, row identity — policy-gated logging (Settings) |
| Saved view created/shared | `register.view_saved` | scope, sharing scope (personal/workspace) |

## 6. Engine / Agent / Human / Gate / Quiet Log

| Lane | Responsibilities on this surface |
|---|---|
| ENGINE | Register query (filter scopes → exact row sets; count header math); hash verification (recompute vs certified hash); CSV serialization (metadata columns only); 404/403 scoping identical to the send API's honest failure semantics |
| AGENT | Nothing on the register itself. Via the frame's Ask entry: answer questions grounded in sealed records with lit-row citations ("what occupancy did we report to {lender} in Q2?" → the sealed figure + chain); refuses when no sealed record grounds the answer (agent brief) |
| HUMAN | Filter, open sealed records, save/share audit scopes, export (typed act), verify hashes |
| GATE | None — both gates (certify, send) live upstream; this surface renders their outcomes. Export role-denial is a permission check, not a workflow gate |
| QUIET LOG | Every export (who, scope, when, row count) and every verify (who, row, outcome) writes an ActivityEvent; sealed-record opens by reviewers are loggable per org audit policy (Settings) |

Lane invariants: no shipping number is computed here (04 §2.1) — the register displays recorded facts; the agent never summarizes the register into new figures on the page (law 10: contextual presence only).

Worked five-lane walk — one SEALED-CAL-FYE2018 row's life on this surface:

1. Upstream (not here): HUMAN certifies at gate 2 (CertificationRecord {who, when, hash}); HUMAN sends at gate 3; ENGINE's send gate verifies hash equality and appends the SendRecord; the period seals (06 §6 Chain E).
2. ENGINE's register query lists the row the moment the stores hold it — no publishing step, no cache: the register is a read of the truth.
3. Months later a lender asks about the annual package: HUMAN scopes lender + 2018, opens the row, reads the Certified and Sent chips, opens the sealed record, and the NOI figure lights its T-12 source row (Chain A) — the answer carries its proof.
4. HUMAN runs `Verify` on the hash before replying: ENGINE recomputes; pass renders with a timestamp; QUIET LOG records `register.hash_verified`.
5. The auditor season: HUMAN saves "sent to {lender} — 2018" as a workspace view and exports the CSV; QUIET LOG records the export with its restated scope. Nothing in steps 2–5 mutated a record — and nothing on this surface ever can.

## 7. Information hierarchy

Scan order, top to bottom:

1. Page/frame header: shell header, book-scoped breadcrumb (`Covenant / Reports` — altitude gate, 08 §2).
2. Decision/status summary: the **count+CTA header** — "{n} packages · this cycle {m}" left; the CTA cluster right (Export CSV; Save view). This is the Terry-named gold-standard header pattern, preserved verbatim in structure (snapshot §3).
3. Primary work region: the filter cluster (loan, lender, period range, status/segment) directly under the header — filters are the audit-scope instrument, so they sit above the rows they scope.
4. The register itself: uniform rows, newest sent first.
5. Evidence/proof: per-row chips (Certified/Sent/Hash) — proof is in the row; the deep proof (the sealed record) is one click.
6. Actions: per-row (open, verify, copy link); header CTAs.
7. Activity/history: none on the page (each row *is* history); export/verify events live in the quiet log.

Absent by design: **charts — ruled for this page** (snapshot §5: the chart doctrine names Reports the gold-standard list page; DIRECTIONS: "NO charts (ruled)"; R4: Stripe's own operational lists are chartless). Also absent: verdict columns, document listings, any mutation affordance, unread concepts, pagination that hides rows without saying so (the count header always states the full scoped total).

## 8. Page anatomy and regions

### 8.1 Count+CTA header

- Purpose: the page's one-line answer ("how much, and what can I do") — the gold-standard signature.
- Content: count text "{n} packages · this cycle {m}" (both numbers recompute against active filters — Mercury's stats-bound-to-filters, counts only, no graphs, R5); CTA cluster: `Export CSV` (primary), `Save view`.
- Persistence: persistent, full width, one 48px row under the shell header.
- Interaction: "this cycle {m}" is an instrument — click applies the current-cycle filter.
- Why this form: ruled pattern; consumed, not redesigned.

### 8.2 Filter cluster (the audit-scope instrument)

- Purpose: filters double as audit scopes — "everything sent to {lender} in 2026" is a filter state, a shareable URL, and a saved view (DIRECTIONS).
- Content: structured chips — loan, lender/servicer, period range (loose date entry accepted, Stripe's grammar scaled down — R5), status segment (sent [default] · certified-awaiting-send · all), client (PMC mode only). 4–6 curated chips, deliberately not an open query builder (R5: reject Stripe's filter surface area at 2–50 loans).
- Persistence: persistent; one 40px row; chips wrap below 1280px.
- Interaction: chip → popover picker; active chips removable; `F` focuses; the whole state is URL-encoded.
- Why this form: scoping must stay visible while scanning results (co-visibility of question and answer).

### 8.3 The register (primary work window)

- Purpose: the audit rows.
- Content — column anatomy (uniform 40px rows, open-not-boxed, law 11): **Period** (label, Geist Mono) · **Loan** (outward name) · **Lender/Servicer** · **Certified** (chip: certifier initials + date; hover full name + timestamp; click → certification record popover) · **Sent** (chip: date + transport glyph (Lucide `download` today, `mail` when email transport ships) + "×{n}" on re-sends; click → SendRecord receipt popover) · **Hash** (chip: first 8 chars, Geist Mono; click → popover with full hash, `Verify`, `Copy`) · **Status** (sealed badge; "awaiting send" in that segment) · **→** (open sealed record).
- Persistence: persistent — the one big pane (law 5); no second work window exists on this surface.
- Interaction: row click (anywhere non-chip) opens the sealed record; chips open their popovers in place; J/K + Enter.
- Minimum width: 720px; column shed order per §10.
- Why a single pane: the audit decision is "find and open" — nothing needs permanent co-visibility beside the full-width register; proofs are chip-popovers (beside the claim, transient) and the sealed record opens as navigation with a return path.

### 8.4 Row detail popovers (transient proof objects)

- Purpose: proof beside the claim without leaving the register (law 6 — never a context-destroying modal).
- Certification popover: who (full identity), when (timestamp), content hash, void/re-certification history for the revision.
- Send popover: the receipt — timestamp, actor, transport, recipient, bytes hash, gate result; the append-only list when multiple sends exist.
- Hash popover: full hash (mono, selectable), `Verify` (engine recompute; result renders inline with timestamp), `Copy`.
- Persistence: transient; Esc or outside-click dismisses; keyboard-reachable from the chip.

### 8.5 The opened sealed record (navigation, not a pane)

- Row → the sealed period read-only: the Review/Certificate render of that period with the sealed banner ("sealed {date} — read-only forever, fully inspectable"), full lit-row provenance intact on every figure (06 §6), zero mutation affordances. This is a route transition (the period's review/certificate view in sealed mode), never an embedded pane — the sealed record deserves the full work window and its own breadcrumb altitude; Esc/back returns to the register with scroll + filter state restored.

### 8.6 Canvas panel and modals

- Canvas panel: the shell's floating Ask slot; highlight a row → ask travels with the row's record identity (law 10).
- Modals: none — export confirms inline (scope + row count restated); no legal-weight act happens on this surface.

## 9. Co-visibility matrix

| Datum/surface A | Datum/surface B | Must be simultaneous? | User decision enabled | Default topology | Fallback |
|---|---|---:|---|---|---|
| Register rows | Active filter scope + counts | YES | "Am I looking at the complete answer to my audit question?" | Header + cluster pinned above rows | Chips wrap; never hidden |
| Row | Its certification/send/hash proof | On demand | Trust the record ("who, when, which bytes") | Chip popover beside the chip | Same at all widths |
| Row | The full sealed record | NO (sequential) | Deep inspection | Navigation with return path | — |
| Register | Export scope confirmation | On demand | "Exactly what am I exporting?" | Inline confirm restating scope + count | — |
| Register | Charts/analytics | NEVER | — | Ruled absent | — |

No pane is created merely because information exists: the register is the page, proofs are transient popovers, depth is navigation.

## 10. Layouts and viewport behavior

- **2048px**: nav rail expanded · register max ~1400px centered (audit tables read better capped than edge-to-edge); all columns at full width; generous gutters.
- **1728px**: same topology; register ~1200px; all columns render.
- **1440px**: register ≥1040px; all columns render at standard widths; popovers position within the viewport.
- **1280–1439px**: nav rail auto-collapses to icons (08 §9); register full-width; transport glyph folds into the Sent chip's popover.
- **Narrow/compact (<1280px)**: column shed in priority order — Hash (chip becomes a `hash` Lucide icon button, full popover unchanged) → Lender (folds into a two-line loan cell) → Certified date (chip keeps initials, date to popover). Each shed appears in the column picker; no silent compression (law 5). The register remains fully usable at the 1152×720 frame minimum (08 §9 — registers stay operable).
- Default pane topology: single work window, 100% of the work area; no splits exist here.
- Focus behavior: focused row elevated one gray rung; focus + scroll + filter state restored on return from a sealed record.
- Compare behavior: none on the register (comparing two sealed packages is the Review compare mode's job, reached from within a sealed record).
- Proof/source behavior: chip popovers beside their chips; the sealed record opens with full lit-row provenance (law 6).
- Minimum viable dimensions: functional at 720px register width with sheds; below the frame minimum, the shell's global rule applies.
- Tab stacking/replacement: n/a (no side panes); popovers become bottom sheets under 720px width.

## 11. Components and exact anatomy

- Reuse first — the built gold standard: the existing Reports view's count+CTA header and filter-cluster components (snapshot §3 — the Terry-named pattern; these are the components other registers copy). Wiring, not restyling.
- `PackageRegister` — ADJUST of the existing Reports table (`src/components/covenant/reports/PackageRegister.tsx`): uniform 40px rows, virtualization, column picker, sort (sent date default desc; period; loan).
- `CertChip` — NEW. Parts: certifier initials (text avatar, gray rung), date (Geist Mono `tabular-nums slashed-zero`), popover trigger; popover per §8.4.
- `SendChip` — NEW. Parts: date (mono), transport glyph (Lucide `download`/`mail` — icons only, law 11), re-send count "×{n}"; popover = the receipt object (shared with the send surface's receipt render — one receipt component, `SendReceipt`, reused from the send-record brief).
- `HashChip` — NEW. Parts: 8-char prefix (Geist Mono, `slashed-zero` — hash legibility is exactly what slashed zeros exist for), popover with full hash, `Verify` action (engine call; renders pass/mismatch + timestamp), `Copy`.
- `SealedBadge` — NEW. Parts: Lucide `lock` + "Sealed" text; the same component renders the sealed-banner variant inside opened records (one seal vocabulary; R4: GitHub's lock-icon-plus-immutable-badge pattern, adapted).
- `AuditScopeBar` — ADJUST of the existing filter cluster: adds the period-range picker with loose date parsing and the status segment control.
- Saved views — reuse the one shared SavedView mechanism (02 §2): personal → workspace scoping, star-to-rail, URL-shareable; shared views carry shared filter state (Attio semantics, R5 synthesis (d)).
- `RegisterExport` — NEW. Parts: inline confirm (scope restated in words — "23 rows: sent to {lender}, 2026 — metadata only"), column list, download; writes the export ActivityEvent.
- `RegisterEmptyState` — NEW: first-cycle variant (explains how rows are born, links the current period) and no-match variant (states the active scope and offers to clear it — the register is complete or says exactly what is filtered; the U3-C1-class truncation defect is structurally banned here).
- `IntegrityAlarm` — NEW: the verify-mismatch state per §5 (both hashes, timestamps, guidance, loud log write).
- `CountBadge` — reuse the shared atom (snapshot §4) in the header counts.
- Command palette — reuse `CommandPalette`: rows are addressable objects ("{loan} {period} package") per the 08 §5 object grammar.

## 12. Interaction specification

- Selection: single-row focus; J/K move; Enter opens the sealed record; no multi-select (bulk acts don't exist — export acts on the scope, not a selection).
- Hover: row elevates one gray rung; chips show full values (full name, full timestamp, full hash prefix).
- Focus: visible ring (ruled accent-family token); chips individually focusable within a row (roving tabindex).
- Keyboard:

| Key | Act |
|---|---|
| `J` / `K` | Next / previous row |
| `Enter` | Open the focused row's sealed record |
| `C` / `S` / `H` | Open the focused row's Certified / Sent / Hash popover |
| `V` | Verify the focused row's hash |
| `F` | Focus the filter cluster; `/` search |
| `E` | Export (opens the inline confirm) |
| `Esc` | Dismiss popover → clear focus → (from sealed record) return to register |
| `G R` | Arrive here (08 §5); `⌘K` palette throughout |

- Editing and validation: nothing is editable — structurally read-only.
- Bulk action: none; export is scope-level by design (an audit export is a scope, not a hand-picked selection).
- Undo/recovery: none needed — no mutations; export/verify repeatable.
- Sorting/filtering: sort by sent date (default), period, loan; filters per §8.2; the count header always restates the scoped total; clearing filters is one act.
- Drill-down and return path: row → sealed record (route transition, breadcrumb at deal altitude) → Esc/back restores register scroll + filters + focus (07 §3).
- Source-linked selection (lit-row): on the register, chips are the provenance surface; inside the opened sealed record, the full lit-row contract applies to every figure (`cross-cutting/provenance-lit-row-trace.md`).
- Save/persistence: filter/sort/column state per user; saved views shared per the one mechanism; every view state URL-encoded for pasteable audit scopes.
- Collaboration/commenting: none on the register; an auditor shares a view URL, not comments.

## 13. Visual craft direction

- Typography roles and sizes: column headers 11px/600 uppercase tracked, gray rung 6; row cells 13px/400; loan names 13px/500; period labels + dates + hashes Geist Mono 13px `tabular-nums slashed-zero` (the ruled figure setting; hashes demand slashed zeros for transcription safety).
- Financial-number treatment: no financial figures render on the register (record metadata only); the mono treatment covers periods, dates, counts, hashes. Figures appear only inside opened sealed records, which carry their own ruled treatments.
- Spacing rhythm: 40px uniform rows (law 11); 16px cell gutters; 12px chip padding; the header/filter/register stack at 48/40px bands with one hairline between filter band and rows.
- Density: comfortable default; the app-wide compact setting drops rows to 32px — an auditor scanning years of sends benefits most from compact mode.
- Open ground versus earned boundaries: open-not-boxed — no cell borders, no zebra; chips are the only boxed elements; column alignment does the table's work.
- Dividers/elevation: hairlines from the ten-rung gray ladder (snapshot §4); popovers carry the ruled elevation; nothing else floats.
- Semantic color from the ruled ladder and accent family: the accent family (#7189FF) marks interactive affordances (chip hover, focus ring, CTA); the sealed badge sits on gray rungs (a seal is a fact, not an alert); verify-pass renders as quiet confirmation text; the integrity alarm consumes the ruled negative/danger-class role — `[VERIFY]` its named token in `src/styles/covenant-tokens.css`; if absent, that is a Terry token question (COLOR LAW D-50), interim rendering is weight + `octagon-alert` glyph, never an invented value.
- The certified sheet's ruled paper treatment: **not painted on the register.** The six ruled hexes (#E7EBF8 / #DDE4FB / #B3BCDA / #33419D / #5265C5 / #121459) belong to the certified sheet's light document surface only (snapshot §4); the Certified chip is register chrome on the gray ladder. The paper treatment appears exactly when a row opens its sealed certificate render — the sheet inside is the artifact; the register never imitates it.
- Focus/selected/hover states: ruled accent-family selection tokens (Ruling-J's Covenant-side layer, snapshot §4); hover is gray-rung elevation.
- Chart style: none — charts are ruled off this page (snapshot §5); the chart doctrine's gold-standard citation of this page is preserved by keeping it chartless.
- Motion and reduced-motion: popovers 120ms fade/scale; the row return-highlight after navigating back is a single quiet pulse; `prefers-reduced-motion` renders instant states with a persistent return-highlight.
- Long-session ergonomics: audit sessions run long — stable column grid, sticky header band, compact mode, and URL-addressable scopes mean an auditor never loses their place; no auto-refresh reorders rows mid-scan (new sends append on explicit refresh with a quiet "register updated" affordance).

## 14. Benchmark research and synthesis

| Product | Limited role | Official evidence | Exact pattern to take | Covenant adaptation | What to reject | Why this reference beats alternatives |
|---|---|---|---|---|---|---|
| Stripe Dashboard | Register + object-detail donor | https://docs.stripe.com/development/dashboard/events · https://docs.stripe.com/activity-logs · https://support.stripe.com/questions/exporting-payment-data (R4) · https://docs.stripe.com/dashboard/search (R5) | Filter-first chartless operational lists; per-object append-only event history; actor identity on every logged action; explicit CSV export as an act; loose date parsing | The register as a filterable table of record objects; row detail = the record's own append-only history; export with restated scope | Charts/analytics modules on operational lists (Stripe itself ships none there); the full query-builder surface area at 2–50 loans | The strongest evidence that an industrial-scale money register is a chartless, filter-first table — exactly Terry's ruling, independently reached |
| Modern Treasury | Immutable-record semantics donor | https://moderntreasury.com/learn/data-immutability · https://docs.moderntreasury.com/ledgers/docs/verify-prior-ledger-states (R4) | Append-only ledger: "if mistakes are made, they are corrected with a subsequent entry," never overwritten; prior states verifiable | Mis-sends/corrections are new revisions with their own certifications and sends, listed alongside — the register never edits history; hash verify = prior-state verification | Full ledger UI complexity | The canonical statement of append-only correction — the register's core honesty rule, documented as product law |
| GitHub Releases | Draft→publish→immutable listing donor | https://docs.github.com/en/repositories/releasing-projects-on-github/about-releases · the immutable-releases doc (R4) | Published-release immutability shown **on the artifact** (lock icon + badge); cryptographic attestation of exactly-published content; tag names never reusable | The `SealedBadge` on every row and inside every opened record; the hash chip as the attestation the borrower (or a lender) can verify | Editable release notes post-publish — Covenant is stricter: any content change voids (R4 adaptation) | The clearest consumer-grade rendering of "this artifact is frozen and provably itself" |
| Vanta | Audit-register framing | https://help.vanta.com/en/articles/11345374-the-tests-page (R5; R4 audit-package context) | Compliance evidence organized as a filterable status register auditors work from | Filters as audit scopes; the reviewer role's read-only register as the auditor's primary surface | SLA/deadline machinery here (that lives on Calendar) | Closest framing of "a register that answers an auditor," not just an ops team |
| Attio | Shared-view mechanism donor | https://attio.com/help/reference/managing-your-data/views/filter-and-sort-views (R5) | Shared views carry common filter/sort state for all members; ephemeral filtering for exploration; top-ordered view = default landing | "Sent to {lender} — 2026" as a workspace view any teammate opens identically; personal exploration never mutates the shared scope | User-configurable schema — register columns are fixed record facts | The documented shared-state semantics Covenant's one SavedView mechanism adopts |
| Mercury | Calm-register craft donor | https://support.mercury.com/hc/en-us/articles/38790547830036-Viewing-cashflow-and-transactions-data-on-your-Transactions-page (R5) | Quick vs advanced filter tiers; header stats recomputing against active filters; row-scoped actions staying on the row | The count header recomputing against scope; per-row overflow staying on the row | **The two graphs above the register** — ruled off this page (no charts) | Proof a finance register stays legible for a small operator — minus the charts Terry ruled out |
| Ramp | Queue-admission discipline (inverted) | https://support.ramp.com/hc/en-us/articles/4417421399699-Transaction-Reviews (R5) | Only complete items enter the default queue; incomplete items live in a named segment elsewhere | Default register = sent records only; certified-awaiting-send is a deliberate segment, not default noise | Approval routing | Cleanest documented default-set discipline for registers |

Synthesis: the result is original because no benchmark's register carries **certification identity, transport, and content-hash as first-class row anatomy** — Stripe proves the chartless filter-first table, Modern Treasury the append-only correction rule, GitHub the visible immutability badge with verifiable attestation, Attio the shared audit scope — but only a borrower-side lender-reporting product needs all four fused into a page whose rows are *proof of what left the building*. The register is domain-correct because its columns are exactly the facts a lender dispute or an audit demands (who certified, when sent, which bytes), and nothing else.

## 15. Domain references

Terminology and expected-data references only: **Finley** (deliverable-status registers for credit-facility compliance — the "status of all deliverables in one place" framing, R5), loan-servicing practice as evidenced in the Calloway Park kit (the servicer's own submission-tracking vocabulary: quarterly questionnaires, annual certifications — evidence: SLOT-4/5), and financial-close audit-trail conventions (R3's close-management products, for "support schedule"-class vocabulary). Domain authority does not equal visual authority: no referenced product's rendering governs a pixel here, and **covenant semantics — what a package is, what certification means, what the record must prove — come from the loan documents and from Terry, not from any referenced product** (law 8; 05 preamble).

## 16. Accessibility, performance, and safety

- WCAG contrast and focus: gray-ladder text at AA-passing rungs; chips meet contrast on their rungs; visible focus ring everywhere; popovers trap focus while open and restore it on dismiss.
- Keyboard completeness: every act in §12 keyboard-reachable, including per-chip popovers (roving tabindex within rows) and verify.
- Screen-reader semantics: the register is a real table (column headers programmatically associated); rows announce "period {label}, {loan}, {lender}, certified by {name} {date}, sent {date} by {transport}, hash {prefix}, sealed"; popovers are labeled dialogs; the hash chip announces its copy/verify actions.
- Table virtualization/large data: 50 loans × up to 12 periods/year × years — thousands of rows; virtualize above 100; filters execute against the persisted stores; the count header is authoritative regardless of rendered window.
- Loading and latency feedback: skeleton rows + "loading {n} of {m}" honesty; verify shows an in-flight state with a timestamped result.
- Destructive action confirmation: no destructive actions exist; export confirms scope inline (not because it destroys, but because it discloses — the confirm restates exactly what leaves).
- Certify and external-send safety: upstream; this surface enforces their *record* honesty — rows only from append-only stores; the send gate's approved-bytes guarantee (hash equality, 02 §6) is what the hash chip renders and verify re-proves.
- Source immutability: sealed records open read-only; original document bytes remain immutable and hash-addressed (law 7); the register can never reach a mutation path.
- Auditability: the register is itself the audit surface; its own acts (export, verify, reviewer opens per policy) write ActivityEvents; CSV exports carry register metadata only — never package contents, and **never resident-level data** (law 9: resident data never travels outward; the export is metadata-only by schema, asserted in tests).

## 17. Acceptance tests and fixtures

Fixtures:

- **SEALED-CAL-FYE2018** — Calloway Park annual package, sealed: certification {who, when, hash} + one SendRecord (download transport), built on the evidence spine (T-12 NOI $1,218,877; cash-flow Net Income $460,159 = T-12 Net Income tie — evidence, inside the sealed record).
- **BEXLEY-Q-SEALED** — Bexley quarterly package sealed (canon), containing the monitored-floor shortfall narrative (verdict visible only inside the record).
- **RESEND-FIX** — one revision with two SendRecords (a re-send).
- **REV-SUPERSEDED** — same loan+period: rev 1 certified+sent, rev 2 certified+sent after a correction.
- **AWAITING-SEND** — a certified package with no SendRecord.

Ticket-ready tests:

1. Record-backed rows: with SEALED-CAL-FYE2018 persisted via the REAL stores (`package_sends` + certification record), the register row renders every field from the stores — assert zero fixture strings in the rendered output (fail-closed: a missing store field renders an honest gap, never a placeholder).
2. Hash integrity: the row's hash chip prefix = first 8 chars of the certified content hash; `Verify` recomputes and passes; tamper the stored artifact in the test harness → `IntegrityAlarm` renders both hashes and writes the loud ActivityEvent.
3. Audit scope: filter lender = {CAL's servicer}, period range = 2018 → exactly the seeded rows; the count header restates the scoped total; the URL reproduces the scope in a fresh session; saving it as a workspace view gives user B the identical register (Attio shared-state semantics).
4. Sealed open: Enter on the row opens the sealed period read-only — sealed banner present, zero mutation affordances in the DOM, every figure's lit-row trace functional (the NOI figure lights its T-12 source row — 06 §2 Chain A); Esc returns with scroll + filters + focus restored.
5. Append-only rendering: RESEND-FIX shows "×2" on the Sent chip with both receipts in the popover, newest summarized; REV-SUPERSEDED lists both rows, the older carrying the superseded note — assert no record is hidden or edited.
6. Segment discipline: AWAITING-SEND appears only under the certified-awaiting-send segment, never in the default register; its link routes to the certificate route's send panel; voiding it removes it from the segment (and it never appeared as history).
7. No-charts assertion: the DOM contains zero chart/canvas/sparkline elements on `/reports` under every fixture (the ruling, enforced by test).
8. CSV export: export of test 3's scope yields exactly the scoped rows; columns are register metadata only — assert no package-content fields, no document bytes, no resident-level data columns; the export ActivityEvent records who/scope/count.
9. Vocabulary/verdict firewall: with BEXLEY-Q-SEALED listed, the strings pass/watch/shortfall/breach appear nowhere on the register page; opening the row shows the record's own shortfall narrative unchanged (sealed truth — 89.04% vs the monitored 90% floor stays a shortfall, canon).
10. Permission fixtures: PMC preparer sees only their client's rows (counts exclude others); a foreign-org deep link → 404; role-denied export renders the reason; a reviewer can open and verify but not export where policy restricts.
11. Viewport fixtures: 2048 / 1728 / 1440 / 1280 / narrow — column sheds per §10 with column-picker visibility; popovers stay in-viewport; bottom-sheet behavior under 720px register width.
12. Keyboard fixture: arrive via `G R`, scope with `F`, J/K to a row, `C`/`S`/`H` popovers, `V` verify, `Enter` open, `Esc` return — pointer never touched.
13. Empty/first-cycle: an org with zero sends renders the first-cycle empty state naming the current period's status and linking it; a scoped no-match renders the no-match variant stating the active scope (complete-or-says-what's-filtered — the U3-C1-class defect banned).
14. Accessibility checks: axe-clean at all viewports; table semantics verified in a screen-reader pass; forced-colors: sealed/verify/alarm states legible without color.
15. Benchmark challenger review: a reviewer armed with the R4 Stripe/Modern Treasury/GitHub citations and the R5 Attio/Mercury citations attempts to name a documented register mechanic this page lacks a reasoned position on; unanswered mechanics become backlog or explicit rejections.

## 18. Build plan

- Dependencies: the REAL send store (`package_sends`, `reporting_periods.status` — already persisted, snapshot §3); CertificationRecord persistence (V3 machinery per 05 §6 gap 4 — until it lands, the Certified chip renders the honest gap, never a demo string); the sealed-period read-only render (Review/Certificate surfaces); the shared SavedView mechanism; F2 tenancy for client scoping; the lit-row contract inside opened records.
- Foundation work: the register read path in `src/lib/covenant/` (scope query → rows + authoritative counts; the hash-verify function; the CSV serializer with the metadata-only schema).
- Components to build first: `HashChip` + `CertChip` + `SendChip` (the row's proof anatomy is the page), then `PackageRegister` wiring, then `AuditScopeBar` + `RegisterExport`.
- Vertical slice (the send-vertical pattern, literally its second consumer): wire `/reports` to the REAL `package_sends` rows first — one route, real persisted sends end to end — because the send API already writes the truth this page lists. This is the cheapest honest register in the product: the store exists today. Certification chips join when V3 lands.
- Migration from fixture data: the fixture Reports list retires the moment the real read path renders; the gold-standard header/filter components carry over untouched (pattern preserved, data replaced).
- Rollout/feature flag: `covenant.reports.register` (real read path); segment + export behind the same flag; no partial fixture/real mixing — a row is store-backed or the page states the store is empty.
- Proof artifacts required: a screenshot of the register listing a real `package_sends` row created through `POST /api/covenant/send`; the test-2 verify pass/alarm pair; the test-7 no-charts assertion in CI; the test-8 export file.
- Final gate: `ADJUST` confirmed — the Terry-named gold-standard pattern preserved verbatim in structure, wired to the append-only truth it was always meant to list.
