# CALENDAR / OBLIGATIONS

Surface brief per the 18-section template (10-PER-SURFACE-BLUEPRINT-TEMPLATE). Direction of record: DIRECTIONS `calendar-obligations.md` (Tier B, verdict ADJUST — agenda liked, month-grid OPEN as decision D-3). Research spine: R5. Model spine: 03 §5 (cadence/deadline engine), 05 §1 (due-rules on RequirementRecord), 02 §2/§3 (Deadline ownership), 04 §1 (Calendar & deadlines lanes), 08 (frame law).

## 1. Identity and verdict

- Product layer: portfolio floor (inventory B4, 07 §1).
- Route/address: `/calendar` in the `(covenant)` route group — `src/app/(covenant)/calendar/page.tsx`, mounting `CovenantShell` with the Calendar view (08 §1). Today the Calendar view mounts only through `/review-room` (snapshot §3). Components land in `src/components/covenant/calendar/`; engine machinery in `src/lib/covenant/deadlines/`; tokens consumed from `src/styles/covenant-tokens.css` (law 12).
- Current build state: `FIXTURE` — the task-first agenda renders from `book.ts` demo strings; no recurring-obligation engine, no deadline watching, no escalation exists (snapshot §6, gap 9).
- Existing-surface verdict: `ADJUST`. The agenda IA is liked by Terry and is KEPT task-first (snapshot §3: "task-first list — Terry likes it"); what changes is machinery (due-rule engine F5), row anatomy (dual dates, due-rule provenance, urgency-in-the-row), and the escalation ladder behind it. The month-grid question is genuinely **OPEN — decision D-3**; this brief presents a recommendation (§8.5), it does not decide.
- Research tier: B (DIRECTIONS).
- Primary users/roles: owner (all rights), PMC preparer (client-scoped; sees only their clients' loans), reviewer (read-only projection). Escalation-policy editing is owner/admin and lives in Settings, not here (02 §3).
- Frequency and session duration: a 1–3 minute scan, several times a week in calm weeks; daily during the deadline fortnight. The R5 clustering math makes this surface's load intensely seasonal by construction: 2–50 loans × 1–4 obligations/quarter ≈ 2–200 items per quarter, and `Q+45d`-class rules stack most of them into the same 2–3 weeks (R5 synthesis (b)).
- Error cost: a missed lender deadline is a real compliance failure (03 §5 — "deadline consequence is real: a late package is a compliance failure"). No shipping number is computed or displayed for certification here, so the terminal error (a wrong number certified and sent to a lender) is not directly reachable from this surface. The failure mode this surface owns is *silence* — an obligation that never surfaced, never promoted, never escalated. The design consequence: fail-closed materialization (no confirmed rule → visible block, never a guessed date) and an escalation ladder that cannot be configured to zero.
- Success criterion: zero missed deadlines across the book; every obligation row states its due date, its rule, and its rule's source; a borrower who opens `/calendar` cold answers "what is overdue, what is due this week, what is blocked" in under ten seconds without opening a single row.

## 2. User job and decisions

- Primary job-to-be-done: know everything the book owes every lender, by when, under which contractual rule, and what state each item is in — then jump to the surface that advances it.
- Decisions made here:
  1. What to work next (prioritization by bucket order + urgency class + at-risk reasons).
  2. Acknowledge an overdue item (typed acknowledgment per 04 §1 — stops repeat push at the current stage; never completes the obligation).
  3. Which owning surface to open for a row (period → Review; missing inputs → Intake; rule question → Evidence).
  4. Save/share a filtered view of the agenda (the shared SavedView act).
  - Nothing else. Calendar decides the ordering of attention, never the content of work.
- Questions the surface must answer in scan order:
  1. Is anything overdue?
  2. What is due this week, and is each item on-track?
  3. Is anything blocked or at-risk (missing inputs against a near date)?
  4. What lands in the next 30 days?
  5. How does the cycle stand (met / due / overdue)?
  6. Why is this date the date (the due-rule and its source, on demand per row)?
- What the user should not have to decide here:
  - Chase approval, chase cadence, and waivers — Intake/Holding owns those decisions (02 §3).
  - Escalation-policy content — Settings → Notifications owns editing; the rail summarizes read-only.
  - Whether a monitored threshold or covenanted test passed — verdicts render on covenant surfaces, never here (§4 vocabulary firewall).
  - Period state transitions — the orchestration spine owns them; Calendar only projects.
  - Which occurrence exists — materialization is engine-automatic from confirmed rules, never hand-scheduled.
- Entry paths: rail item Calendar (`G C` chord, 08 §5); Home §2 next-deadlines mini-agenda rows link here (DIRECTIONS home-overview); the period header's deadline chip links here filtered to that loan; palette computed answer "next deadline" (08 §5); a deadline-escalation push deep-links to the overdue row.
- Exit paths: row → the period it belongs to (period header opens Review — 07 §3 spine); at-risk row's missing-items link → `/intake` holding checklist selection; escalation summary → `/settings/notifications`; loan cell → `/loans/[loanId]`; complete row → the sealed record via `/reports`.
- Completion/advancement conditions: none originate here — Calendar is a projection surface. Rows flip to `complete` when the owning spine act happens elsewhere (package sent → SendRecord seals the period, 02 §6); the row then leaves the active buckets and joins the cycle's met count and history. Acknowledging overdue advances nothing except the notification state.

## 3. Object and ownership model

| Object/action/status | Owned here? | Summary/link elsewhere | Address/identity | Notes |
|---|---:|---|---|---|
| Deadline (materialized cadence occurrence) | YES | Home next-deadlines strip; period header deadline chip (both computed summaries of these rows — 07 §2) | `(loanId, reqId, period)` | The due-rule engine (F5) materializes these from confirmed RequirementRecords (03 §5) |
| Escalation state per deadline (remind → chase → your-move → overdue) | YES (state machine render + acknowledge act) | Inbox/Home render your-move-stage items as rows of the one computed query (03 §4) | rides the Deadline | Policy machinery shared with `cross-cutting/notifications-deadlines-escalation.md` (05 §6 gap 9) |
| Overdue acknowledgment | YES (typed act) | Quiet log entry | `(deadline, who, when, note?)` | Stops repeat push; never completes the obligation |
| Escalation policy | NO — Settings → Notifications | Right rail renders a read-only policy summary with an edit link | org | 02 §3; DIRECTIONS settings |
| RequirementRecord (cadence, due-rule, definition) | NO — Extraction & Confirmation | Every row cites its rule + source region; click opens evidence | `reqId` | 05 §1.2; amendments re-materialize affected future deadlines only (§5) |
| ReportingPeriod status | NO — orchestration spine | Row status chip reflects it | `(loanId, period)` | 02 §2 |
| Holding checklist / chases / waivers | NO — Intake/Holding | At-risk rows summarize "2 of 5 items arrived" + last/next chase; acts happen in `/intake` | `(loanId, period, itemId)` | Projection only; no chase button here beyond the link |
| Saved views over the agenda | YES (instances) | — | `viewId` | One shared mechanism app-wide (02 §2 SavedView) |
| Push notification (deadline escalation) | Delivery: frame/notification service (A7) | Calendar row shows the escalation stage that caused it | — | The ONE push-eligible class (04 §2.3) |

No-double-homing boundary: **Calendar/Obligations owns obligation due dates and escalation state; Home and period headers render computed summaries of the same rows** (07 §2 duplication resolution). Calendar never owns checklist decisions, period transitions, or policy editing — it links. Conversely, no other surface may compute a due date: Home's mini-agenda and the period header's deadline chip both read this engine's rows.

## 4. Data and semantic model

- Source facts: RequirementRecord cadences and due-rules with their definition source regions. Evidence instances (SLOT-3; 05 §1.1): §8.02(b)(1) quarterly YTD income statement **Q+45d**; §8.02(b)(2) annual statements **FYE+120d** incl. cash flows; §8.02(b)(3) rent schedule at both cadences; §8.02(b)(2)(E)/(F)/(G) annual certification items; Form 6241 annual ENERGY STAR report; §8.02(b)(4) on-request items rate-capped at once per 6 months. Loan terms contribute date-bearing facts (maturity; notice windows **where the loan's own documents define them** — never invented, per law 8; LoanBoss's critical-date taxonomy is the domain cue, R5).
- Extracted values awaiting confirmation: none render as dates. A *proposed* (unconfirmed) requirement materializes **no deadline** — fail-closed. It renders as one blocked line per loan ("N proposed obligations awaiting confirmation — no dates scheduled") linking to `/loans/[loanId]/setup`.
- Confirmed values: the confirmed RequirementRecord set is the sole materialization input (ask-once: confirmation happened at loan setup, gate 1 — 03 §2).
- Deterministic outputs (engine, F5): Deadline rows `{loanId, reqId, period, lender_due_date, internal_prep_date, bucket, urgency_class, escalation_state, status}`. **Dual dates** (the Karbon pair, adopted — R5): `lender_due_date` computed from the due-rule (external, contractual); `internal_prep_date` = lender date minus the org's send-day margin (a borrower-scope MemoryEntry — "preferred send-day margin before deadlines", 03 §3), overridable per loan.
- Agent proposals/drafts: chase drafts (owned by Holding; summarized in row expansion); deadline-risk summaries the agent may draft for an at-risk cluster (04 §1). The agent never sets, moves, or waives a date.
- Human decisions: acknowledge overdue (typed, with optional note); view saving/sharing; everything else routes out.
- Certified values: none originate here. `complete` rows link the sealed record via Reports.
- Versions/periods/packages: deadlines version with their rule — a loan amendment that changes a due-rule re-materializes **future** occurrences only; in-flight deadlines keep the rule version active when they were materialized, with a "rule amended for future periods" note (Vanta's versioned-SLA mechanic, adopted — R5).
- Evidence/provenance: every row carries a **due-rule provenance chip** — e.g. "Q+45d per §8.02(b)(1)" (evidence-cited) — whose click opens the Evidence context beside the agenda and lights the requirement's definition source region (lit-row law, 06 §1). Chip format: `{cadence.due_rule} per {definition_source_region.section}` from the RequirementRecord (05 §1.2).
- Permissions/read-only projections: PMC preparers see client-scoped rows only; reviewers get the full read-only agenda without acknowledge affordances or workspace-scope view saving.

Field groups and grain (template checklist applied):

| Grain | What exists at this grain on this surface |
|---|---|
| Organization | Escalation policy (summarized); send-day margin memory; the agenda's whole scope |
| Client (PMC mode) | Row scoping + client filter chip; owner-mode hides the layer entirely (02 §1) |
| Sponsor / Portfolio | Filter dimensions only (portfolio filter dedupes by property per 02 §1); no calendar objects at this grain |
| Property | Indirect — at-risk reasons name property-level documents (T-12, roll) via the checklist projection |
| Loan / Pledge | Loan cell on every row; pledge not rendered (collateral is not a deadline concern) |
| Reporting period | The period chip; deadline identity includes period; completion is a period event |
| Document | Only as at-risk reasons ("T-12 not arrived") linking to Intake |
| Requirement | The row's parent: title, due-rule, cite, deliverable name all read from it |
| Test / Metric | Absent by design (vocabulary firewall) |
| Package section | Absent |
| Certification record | Only as the void-reversal trigger (§5 certified state) |
| Send record | Only as the completion trigger + sealed-record link |

- Vocabulary firewall: Calendar renders **deadline states** (on-track / at-risk / blocked / complete / overdue) and never verdict words. Pass/watch/shortfall/breach belong to TestResults on covenant surfaces (law 3); a row may *link* to a period carrying a shortfall, but the word never prints here.

## 5. State machine and exceptions

Per-deadline lifecycle (engine-owned; all transitions quiet-logged with actor + trigger):

| State | Trigger in | Actor | Out | Reversible | Failure behavior |
|---|---|---|---|---|---|
| scheduled-future | Materialization (rolling 12-month horizon, idempotent recompute) | Engine | time passes → upcoming | n/a | Materialization is re-runnable; duplicates impossible by identity `(loanId, reqId, period)` |
| upcoming (Later this cycle / Next 30 days) | `internal_prep_date` enters window | Engine (time trigger — Karbon global-automator pattern, R5) | → this-week | auto | — |
| this-week | prep date in current week | Engine | send happens → complete; lender date passes → overdue | auto | — |
| at-risk (modifier on any active state) | Checklist incomplete with prep date near/past, or prep date passed unsent | Engine | inputs arrive / package advances → clears | auto | Renders the reason ("T-12 not arrived") + link to `/intake` |
| blocked (modifier) | Requirement unconfirmed, due-rule unparseable, or definition missing | Engine (fail-closed) | human confirms/authors at owning surface | via fix | Never a guessed date — the block names what is missing (04 §2.5) |
| overdue | `lender_due_date` passed, period not sent | Engine | send → complete (recorded late) | no (history keeps the overdue interval) | Enters the push-eligible escalation stage per policy |
| complete (met) | SendRecord seals the period | Engine (projection of the spine) | — | no | Moves to cycle history with a met-late flag if applicable |
| waived | ChecklistItem waived in Holding (by whom, when) | Projection of Holding's act | Holding may un-waive | via Holding | Rendered with waiver provenance |
| conditional-dormant | On-request/event requirement with no triggering event | Engine | request/event recorded → materializes with its window | auto | Renders in Scheduled future as "on request — rate-capped 1 per 6 months" (evidence: §8.02(b)(4)) without a date |

Escalation ladder (rides the deadline; policy from Settings; machinery shared with `cross-cutting/notifications-deadlines-escalation.md`): `none → reminded (quiet) → chased (agent, approved cadence, quiet-logged) → your-move (enters the computed your-move query → Home/Inbox) → overdue-push (the ONE push-eligible class, org policy)` (03 §5, 04 §2.3). Each stage stamps who/what/when; acknowledge stops repeat push at the current stage; the ladder can be tuned in Settings but never configured to zero stages.

Template state coverage, explicitly:

- **empty** — no confirmed loans: "No obligations scheduled yet" + link to loan setup. A confirmed book in a genuinely clear week renders every bucket with honest zero counts — calm is displayed, never faked or hidden.
- **awaiting documents (holding)** — the at-risk modifier: row shows "waiting on {item}" with last/next chase projected from Holding.
- **loading/processing** — skeleton buckets; the header always carries the last-materialized timestamp; if the promotion pass is older than 24h a `MaterializationStaleBanner` renders (honest clock).
- **partial/incomplete** — some loans confirmed, some not: confirmed rows render normally; each unconfirmed loan contributes one `BlockedRequirementLine`.
- **extracted/unconfirmed** — no dates materialize (fail-closed); the blocked line names the count and links to setup.
- **stale/superseded** — an amended rule: future occurrences re-materialize; in-flight rows annotate "rule amended for future periods" (§4).
- **low confidence** — n/a by construction: only human-confirmed records materialize; confidence never reaches this surface.
- **conflict** — two confirmed requirements materializing the same deliverable+period collapse to one row listing both cites; contradictory dates for the same deliverable render a conflict row whose only action is "resolve at Extraction & Confirmation".
- **failed tie-out** — never rendered here; it blocks readiness on Review and surfaces as the period's at-risk reason ("period blocked in review").
- **watch/shortfall/breach** — never rendered here (vocabulary firewall, §4); the period chip carries the user to where verdicts live.
- **permission denied** — client-scoped rows are absent entirely for PMC preparers (not grayed, not counted in headers).
- **read-only** — reviewer projection: no acknowledge affordance, no workspace-scope view saving, no policy edit link.
- **blocked/gated** — the blocked modifier (fail-closed materialization) and the escalation gate (§6).
- **certified (void-on-change)** — a voided certification reverts `complete`-pending rows to their date-appropriate bucket with the void reason linked (03 §2 void-on-change); never silent.
- **sent/sealed** — complete; the row's history is permanent and the sealed record link never breaks (seal-not-wipe).
- **recovery/undo** — acknowledgment is revertible (un-acknowledge re-arms push); materialization recompute is idempotent; **a push notification already delivered cannot be undone by design** — the quiet log records exactly what fired and when.

Audit events (append-only, named; every transition writes one):

| Transition | ActivityEvent | Payload beyond identity |
|---|---|---|
| Materialization creates/updates occurrences | `deadline.materialized` | rule version, horizon window, count |
| Bucket promotion | `deadline.promoted` | from-bucket, to-bucket, trigger date |
| At-risk raised/cleared | `deadline.at_risk` / `deadline.at_risk_cleared` | reason (missing item ref / period block) |
| Escalation stage change | `deadline.escalated` | stage, policy version, channel (quiet/your-move/push) |
| Acknowledgment / reversal | `deadline.acknowledged` / `deadline.unacknowledged` | who, note |
| Completion | `deadline.met` | on-time/late flag, sealed-record ref |
| Rule amendment applied | `deadline.rule_amended` | old/new rule versions, affected future periods |

## 6. Engine / Agent / Human / Gate / Quiet Log

Five-lane table (expands 04 §1 "Calendar & deadlines"):

| Lane | Responsibilities on this surface |
|---|---|
| ENGINE | Materialize deadlines from confirmed RequirementRecords (due-rule parse → dates; rolling horizon; idempotent); compute dual dates (lender date per rule; prep date per margin memory); bucket assignment + auto-promotion on time triggers; urgency classification (due-soon vs overdue); at-risk/blocked detection (checklist state × time); the escalation state machine per org policy; business-day math **where the loan's own rule text requires it** (Vanta's business-day SLA cue, R5) — never assumed |
| AGENT | Execute chases on the approved cadence (act owned by Holding, projected here); draft deadline-risk summaries for at-risk clusters ("three Q2 packages at risk behind one missing T-12"); answer Ask questions about dates with rule + source citations; never sets, moves, or waives a date |
| HUMAN | Acknowledge overdue (typed); navigate to owning surfaces; save/share views; edit policy (in Settings); waive items (in Holding) |
| GATE | Overdue-with-no-path-to-complete is the escalation gate — the one class allowed to push (04 §1); an unconfirmed requirement is a materialization gate (no date until confirmed — fail-closed); gate renders name the exact ask, the evidence, and the consequence of delay (04 §2.2) |
| QUIET LOG | Every materialization run, promotion, reminder, chase, escalation-stage change, acknowledgment, and met deadline writes an ActivityEvent (actor, trigger, deadline identity, outcome, next scheduled action). Deadlines met on time are quiet-log-only — no celebration chrome, no push |

Lane invariants honored: no shipping number exists on this surface; every date is deterministic engine output from a confirmed rule (04 §2.1). Agent presence is contextual — the chase projection beside the at-risk row, the risk summary beside its cluster — never a chat box (law 10).

Worked five-lane walk — one CAL-SPINE quarterly deadline's life (evidence rule: Q+45d per §8.02(b)(1)):

1. ENGINE materializes the Q2 occurrence at loan setup: lender date = quarter end + 45d; prep date = lender date − the remembered margin. Quiet log: `deadline.materialized`.
2. ENGINE promotes the row Later → Next 30 days → This week as the prep date approaches; no human touches it. Quiet log per promotion.
3. The T-12 hasn't arrived at prep−7d: ENGINE flags at-risk; AGENT's chase (Holding-owned, approved cadence) projects into the row expansion; ESC-POL-A's remind stage fires quietly.
4. The document arrives; recognition files it; the checklist advances; ENGINE clears at-risk. The row is on-track again — no notification was ever pushed.
5. The package seals via the send gate on day 40: ENGINE flips the row `complete` (met, on time); the cycle summary's met count increments. Quiet log: `deadline.met`.
6. Counterfactual: had the lender date passed unsent, GATE escalation would have entered your-move at deadline−3d and the overdue-push class at deadline+1d — the only push in the whole story — until HUMAN acknowledged or the package sealed.

## 7. Information hierarchy

Scan order, top to bottom:

1. Page/frame header: shell header, book-scoped breadcrumb (`Covenant / Calendar` — altitude gate: no loan/period crumbs, 08 §2), count header "{n} obligations · {o} overdue" using the shared `CountBadge`.
2. Decision/status summary: the **Overdue bucket** — always first, even when empty ("0 overdue" is the calm state, earned and displayed, never hidden).
3. Primary work region: the task-first agenda — This week / Next 30 days / Later this cycle / Scheduled future, auto-promoting (§8.2).
4. Secondary context: right rail — cycle summary (met / due / overdue for the current cycle) + escalation policy summary (read-only, edit → Settings).
5. Evidence/proof: due-rule provenance chips per row; the Evidence context opens beside the agenda on chip click (lit-row).
6. Actions: per-row (open period, open checklist gap, acknowledge overdue); header (filter cluster, saved views, heat-strip toggle if D-3 adopts).
7. Activity/history: cycle history (met deadlines with on-time/late flags) collapsed at the bottom; the full trail lives in the quiet log.

Absent by design: a month grid as the default framing (D-3, §8.5); verdict chips (vocabulary firewall); decorative charts and KPI tiles; any "unread" concept (C-8 — the only counts are obligations and your-move); duration bars (deadlines have no duration — TaxDome's own due-date-only lesson, R5).

## 8. Page anatomy and regions

### 8.1 Header strip

- Purpose: identity, totals, controls.
- Content: count header ("{n} obligations · {o} overdue"), filter cluster (loan, lender, client [PMC only], cadence shape, status), saved-views control, heat-strip toggle (only if D-3 adopts).
- Persistence: persistent; full width; 48px shell header + one 40px control row.
- Interaction: filter chips toggle; `F` focuses the cluster; view save/share.
- Minimum: full width always; chips wrap to a second row below 1280px.
- Why this form: standard register header — no pane semantics needed.

### 8.2 The agenda (primary work window)

- Purpose: the ordered attention queue — the surface's entire reason to exist.
- Content: buckets in fixed order Overdue → This week → Next 30 days → Later this cycle → Scheduled future (DIRECTIONS; Karbon My-Week auto-promotion adapted, R5). Each bucket: sticky header with `CountBadge`, uniform-height rows (open-not-boxed, law 11).
- Row anatomy (one line, fixed columns): obligation title (deliverable name from the requirement, e.g. "Quarterly YTD income statement") · loan (outward name) · lender/servicer · **dual-date cell** (lender deadline primary, Geist Mono; internal prep date secondary, "prep by {date}") · **due-rule provenance chip** ("Q+45d per §8.02(b)(1)") · period chip · status (on-track/at-risk/blocked/complete) · escalation glyph.
- Persistence: persistent — the one big pane (law 5).
- Interaction: J/K navigation, Enter opens period, E expands, chip clicks per §12.
- Minimum width: 720px; below that, columns shed per the §10 priority order.
- Resize/collapse: buckets individually collapsible (collapsed state shows header + count); Scheduled future collapsed by default.
- Why a single pane: the decision here is ordering and jumping — nothing must be co-visible beside the full agenda except summoned evidence (§9). A permanent split would tax the scan without enabling any decision.

### 8.3 Row expansion (inline, transient)

- Purpose: per-row detail without leaving the queue.
- Content: escalation history (each stage, timestamp, actor); checklist summary projection ("2 of 5 arrived; last chase {date}, next {date}" — links to `/intake`); rule version note if amended; dual-date derivation ("prep date = deadline − {margin}; margin learned {date} from {who}" — the remembered answer rendered beside the field it fills, law 10, with inline inspect/correct link to the memory entry).
- Persistence: transient — one row expanded at a time; Esc collapses; expansion state not persisted.
- Interaction: E/Enter toggles; links route out with return path.
- Minimum height: content-fit, max 240px with internal scroll.
- Why inline expansion, not an inspector pane: the content is strictly per-row and momentary; a pinned inspector would spend permanent width on transient detail (law 5's co-visibility justification fails).

### 8.4 Right rail (pinned pane, 320px)

- Purpose: cycle context that must stay visible while scanning — the "how does the cycle stand" question is answered *while* scanning rows, not instead of it (co-visibility justification).
- Content: cycle summary (obligations met n · due n · overdue n, counts recomputing against active filters — Mercury's stats-bound-to-filters mechanic adapted to plain counts, no graphs, R5); materialization horizon note; escalation policy summary (ladder stages + cadence, read-only, "Edit in Settings → Notifications" link).
- Persistence: pinned; user-collapsible.
- Interaction: counts are instruments — clicking "overdue 3" applies the overdue filter.
- Minimum width: 320px; below 1440px it collapses to a labeled tab above the agenda (no silent compression, law 5).
- Why a pinned pane: glanceable state co-visible with the queue; it never carries work.

### 8.5 Month heat-strip — the D-3 recommendation (PROPOSE; Terry decides)

Recommendation from the R5 evidence: **the agenda stays the default and only landing view; no month grid ships as a workspace.** Optionally ship a compact **due-date-only heat-strip**:

- Form: a 7×5 mini-grid (weekday columns × 5 week rows, current month), collapsed by default above the agenda, toggled from the header; ~120px tall when open.
- Cell content: **density dots only** (1–4 dots on gray rungs; ≥5 renders a numeral count) — never item titles, never lists. A cell containing any overdue item takes the ruled overdue-class token role (§13).
- Interaction: click a day → the agenda scrolls to that day's rows and flash-highlights them. The strip is a lens over the list, never a workspace (TaxDome and Financial Cents both treat their grids exactly this way — R5).
- The named anti-pattern: **the Google Calendar month grid** — ~2–3 items per day cell before "+N more" truncation; cell size fixed by month geometry, so workload is invisible exactly when it is heaviest; no urgency dimension (R5: Google Calendar). Covenant's deadlines cluster **by construction** — every loan's `Q+45d` lands the same fortnight — so a Google-class grid would overflow precisely during crunch. The strip is structurally immune: cells never list items, so truncation is impossible.
- Chart-doctrine check (law 11): each cell is an instrument (click → day); color is severity-only (the overdue class); the strip must out-encode the agenda's own date column to earn its toggle — in calm months it does not, which is exactly why it is optional and off by default.
- Existing-law alternative if Terry declines: pure agenda, no grid artifact at all — the surface is complete without it.

### 8.6 Canvas panel and modals

- Canvas panel: the shell's floating slot (Ask entry; highlight-to-ask on any row travels with the row's provenance); never load-bearing for a decision (08 §4).
- Modals: none on this surface — acknowledge is an inline typed act, not a legal-weight ceremony; evidence never opens in a modal (law 6).

## 9. Co-visibility matrix

| Datum/surface A | Datum/surface B | Must be simultaneous? | User decision enabled | Default topology | Fallback |
|---|---|---:|---|---|---|
| Deadline row | Its due-rule + rule source cite | YES | Trust the date ("why is this the date?") | Chip in the row itself | — (never separated) |
| Due-rule chip | Definition source region (loan agreement page) | On demand | Verify the rule against the document | Evidence context opens beside the agenda (summoned, right); row stays lit | Below 1280px: evidence as overlay sheet (08 §9) |
| Agenda | Cycle summary + policy summary | YES while scanning | Prioritize with cycle context | Pinned right rail 320px | Labeled tab <1440px |
| Row | Escalation history + checklist projection | On demand | Judge at-risk severity | Inline expansion | Same at all widths |
| Heat-strip (if adopted) | Agenda | YES when open | Locate a day's load, then work the list | Strip above agenda; agenda scrolls on cell click | Strip hidden <1440px (the agenda is the fallback, by design) |
| Calendar | Review / Composer / Intake | NO | — | Navigation with return path, never split | — |

No second work window exists on this surface: no decision here requires side-by-side documents (law 5 justification). Panes are not created merely because information exists — the rail and the evidence context are the only two, each with a stated decision.

## 10. Layouts and viewport behavior

- **2048px**: nav rail expanded (240–280px) · agenda ~1280px max, centered · right rail 320px · heat-strip (if open) spans agenda width. All columns at full labels; gutters grow, row width caps (long lines hurt scanning).
- **1728px**: same topology; agenda ~1040–1120px; all columns render; evidence context opens at 420px beside the agenda, agenda compresses to its 720px minimum before the right rail auto-collapses.
- **1440px**: nav rail expanded or user-collapsed; agenda ≥720px; right rail 320px pinned. Opening Evidence auto-collapses the right rail to its labeled tab — evidence and rail never stack side-by-side (one summoned context at a time).
- **1280–1439px**: shell auto-collapses the nav rail to icons (08 §9); right rail becomes a labeled tab above the agenda header; Evidence opens as a docked pane.
- **Narrow/compact (<1280px)**: single column; buckets stack full-width; the dual-date cell drops the prep date into the expansion; the provenance chip collapses to the rule only ("Q+45d" — cite moves to expansion); Evidence as overlay sheet. The register remains fully usable (08 §9 keeps registers operable below the spine minimum).
- Default pane topology and ratios: agenda : right rail ≈ 72 : 28 at 1440px; evidence (when open) takes the rail's slot at ~420px with the agenda holding ≥720px.
- Focus behavior: bucket headers sticky within the scroll; keyboard focus follows J/K with the focused row elevated one gray rung; focus survives promotion re-seats (the focused row keeps focus even as it moves buckets).
- Compare behavior: none — no peer-compare case exists on this surface.
- Proof/source behavior: Evidence beside, per §9; never a modal (law 6); the lit region stays lit until dismissed.
- Minimum viable dimensions: 1152×720 frame minimum (08 §9); agenda functional at 720px with shed columns.
- Tab stacking/replacement rules: right rail and Evidence become labeled tabs, never squeezed panes; column sheds in priority order — escalation glyph (folds into status chip) → lender (folds into expansion) → prep date (folds into expansion) — each shed reflected in the column picker. No silent compression anywhere.

## 11. Components and exact anatomy

- `ObligationAgenda` — NEW (`src/components/covenant/calendar/ObligationAgenda.tsx`).
  - Parts: `BucketSection[]` (sticky header + shared `CountBadge` — the consolidated atom per the 2026-08-07 ruling, snapshot §4; collapse control), virtualized uniform-row list, cycle-history collapse, `AgendaEmptyState`.
- `ObligationRow` — NEW.
  - Parts: title cell; loan cell (outward name, link); lender/servicer cell; `DualDateCell` (lender date in Geist Mono `tabular-nums slashed-zero`, prep date secondary on a mid gray rung); `DueRuleChip`; period chip (status dot + period label, links to the period); `DeadlineStatusChip` (on-track/at-risk/blocked/complete — text + form, never color-only); `EscalationGlyph` (Lucide `bell` reminded / `bell-ring` chased / `hand` your-move / `alert-triangle` overdue — icons only, no emoji, law 11); overflow menu (acknowledge, when overdue; open checklist; copy link).
  - Row expansion parts per §8.3.
- `DueRuleChip` — NEW.
  - Parts: rule text ("Q+45d"), cite ("per §8.02(b)(1)"), provenance affordance (dotted underline per the 06 §7 inferred-confirmed grammar).
  - Behavior: click → Evidence context opens with the definition source region lit; consumes the lit-row contract from `cross-cutting/provenance-lit-row-trace.md`; keyboard-activatable.
- `CycleSummaryRail` — NEW.
  - Parts: three count instruments (met/due/overdue, recomputed against filters; click applies the filter); horizon note ("scheduled through {date}"); `EscalationPolicySummary` (read-only ladder render, per-stage cadence, Settings link).
- `MonthHeatStrip` — NEW, built only on a D-3 adopt ruling.
  - Parts: 7×5 cell grid; per-cell density dots (gray rungs) or numeral ≥5; overdue-class cell state; day-click scroll wiring; month label + prev/next month pager (pager renders density only, never items).
- Filter cluster — reuse the Reports gold-standard filter-cluster pattern (snapshot §3: Reports is the Terry-named list-page gold standard); dimensions: loan, lender, client (PMC), cadence shape, status.
- Saved views — reuse the one shared SavedView mechanism (02 §2; Linear/Attio scoping per R5 synthesis (d)): personal → workspace scope, star-to-rail into the Starred group, URL-shareable; workspace views share filter/sort state (Attio semantics) with ephemeral local filtering for exploration.
- Evidence panel — reuse the existing review-room Evidence panel component (snapshot §3), summoned beside the agenda; renders the requirement's definition text + source region.
- Command palette — reuse `CommandPalette`; this surface contributes computed answers ("next deadline", "deadlines for {loan}") per the 08 §5 grammar — engine rows with mono figures, never generation.
- `DeadlineStatusChip` — NEW.
  - Parts: state text (on-track / at-risk / blocked / complete), leading Lucide glyph (`circle-check` complete, `circle-alert` at-risk, `octagon-x` blocked, none for on-track), at-risk reason on hover.
  - Never color-only; on-track renders quiet (gray rung), reserving emphasis for exceptions.
- Empty/error/recovery objects — NEW:
  - `AgendaEmptyState` — two variants: no-loans ("No obligations scheduled yet" + loan-setup link) and clear-week (buckets render with zero counts; the state is the calm itself, no illustration chrome).
  - `MaterializationStaleBanner` — last-run age + retry affordance; renders only past the 24h honesty threshold.
  - `BlockedRequirementLine` — per-loan fail-closed block: count of unconfirmed records, what is missing, link to `/loans/[loanId]/setup`; never a guessed date.

## 12. Interaction specification

- Selection: single-row focus model; click or J/K moves focus; no multi-select in v1 — no bulk acts exist here (acknowledge is deliberate and singular).
- Hover: row elevates one gray rung; chips reveal full text (prep-date derivation, full cite); hover never triggers evidence — click-only, deliberate.
- Focus: visible focus ring (ruled accent-family focus token); bucket headers focusable (Enter collapses/expands).
- Keyboard:

| Key | Act |
|---|---|
| `J` / `K` | Next / previous row (crosses bucket boundaries) |
| `Enter` | Open the focused row's period |
| `E` | Expand/collapse the focused row |
| `A` | Acknowledge (overdue rows only; inline typed confirm) |
| `V` | Open evidence for the focused row's due-rule |
| `F` | Focus the filter cluster |
| `/` | Search within the register |
| `Esc` | Collapse expansion → dismiss evidence → clear filters focus (in that order) |
| `G C` | Arrive here from anywhere (08 §5); `⌘K` palette throughout |

- Editing and validation: nothing editable here except the acknowledgment note; prep-date margin edits route to the memory entry (inline inspect/correct in the expansion) or Settings.
- Bulk action: none by design.
- Undo/recovery: un-acknowledge from the row expansion (re-arms push); no other mutating acts exist on this surface.
- Sorting/filtering: buckets are fixed-order — time sorts, the user does not; within a bucket, sort by lender date (default) or loan name; filters per §11; filter state savable as views.
- Palette contribution: this surface registers computed-answer handlers with `CommandPalette` — "next deadline" returns the soonest active row (mono date + loan + rule cite, click = deep link with the row focused); "deadlines {loan}" returns that loan's active rows; answers are engine reads with provenance links, never generation (08 §5 grammar).
- Drill-down and return path: row → period surface carries the loan+period breadcrumb (altitude gate); Esc/back returns to the agenda with scroll + focus restored (07 §3 return-path law).
- Source-linked selection (lit-row): `DueRuleChip` click lights the exact definition region in Evidence and it **stays lit** until dismissed (06 §1); the owning row stays highlighted while its evidence is open.
- Save/persistence: view state (filters, sort, collapsed buckets, heat-strip toggle) persists per user; workspace-scope saved views share state per Attio semantics (R5).
- Collaboration/commenting: none in v1; the escalation history is the shared record of who was told what, when.

## 13. Visual craft direction

- Typography roles and sizes: row title 13px/500 sans; loan/lender 13px/400 on gray rung 7; dates Geist Mono 13px `tabular-nums slashed-zero` (the ruled figure setting extends to dates for columnar alignment); bucket headers 11px/600 uppercase tracked on gray rung 6; counts in `CountBadge` mono; expansion detail 12px.
- Financial-number treatment: no financial figures render here (dates and counts only); mono treatment as above keeps the date column machine-straight.
- Spacing rhythm: 40px uniform row height (law 11); 24px between buckets; 16px cell gutters; 8px chip padding; 12px expansion inset.
- Density: comfortable default; the app-wide compact setting (Settings → Appearance) drops rows to 32px.
- Open ground versus earned boundaries: open-not-boxed — buckets separated by whitespace plus one hairline under each sticky header; rows borderless, zebra-free; the only boxed elements are chips.
- Dividers/elevation: hairlines from the ten-rung gray ladder (adopted 2026-08-07, snapshot §4); the sticky header earns its hairline only on scroll; the summoned Evidence pane carries the ruled elevation treatment; nothing else floats.
- Semantic color — **the urgency semantics, in ruled tokens only** (law 4): two urgency classes, adopted from Vanta's deadline-in-the-row evidence (orange-class "needs attention, not overdue" vs red-class "overdue" — R5). Covenant binds them to **ruled token roles, never new values**: the soft class (due-soon, at-risk) consumes the same ruled watch-class semantic role that renders `watch` verdicts app-wide; the hard class (overdue) consumes the ruled negative/danger-class role that `breach` verdicts consume on covenant surfaces — one severity language across the product, severity-only color per the chart doctrine. `[VERIFY]` the Review-9 43-token set's named roles for these two semantics in `src/styles/covenant-tokens.css`; if a role is not defined there, that is a token-authority question for Terry (COLOR LAW D-50 — no session invents a value); the interim rendering is weight + `EscalationGlyph` + gray-rung contrast, never a locally authored color. Text labels stay deadline vocabulary (due-soon/overdue), never verdict words: a deadline can be overdue while every test passes.
- The certified sheet's ruled paper treatment: not touched here — no paper surface exists on this floor; `complete` rows link to sealed records, they do not imitate the sheet (the six ruled hexes paint only the light document surface, snapshot §4).
- Focus/selected/hover states: consume the ruled accent-family (#7189FF family) selection tokens per Ruling-J's Covenant-side token layer (snapshot §4); hover is gray-rung elevation only — accent is reserved for selection and interactive affordances.
- Chart style under the chart doctrine: the heat-strip per §8.5's doctrine check; no other marks render on this surface.
- Motion and reduced-motion: bucket promotion animates as a 150ms position-only re-seat; heat-strip day-click flash is a two-pulse background on the target rows; `prefers-reduced-motion` replaces both with instant state plus a persistent highlight until the next interaction.
- Long-session ergonomics: this is a scan surface — sessions are short by design; sticky headers, fixed bucket order, and the stable column grid make the tenth visit of the week zero-relearning; nothing blinks or auto-refreshes under the user's eyes (promotion re-seats apply on load or explicit refresh, with a quiet "agenda updated" affordance if the engine promoted rows mid-session).

## 14. Benchmark research and synthesis

| Product | Limited role | Official evidence | Exact pattern to take | Covenant adaptation | What to reject | Why this reference beats alternatives |
|---|---|---|---|---|---|---|
| Karbon | Recurring-work instantiation donor | https://help.karbonhq.com/en/s/articles/6096234-setup-repeating-work-on-a-schedule · https://help.karbonhq.com/tasks/faqs/how-do-task-due-dates-work · https://help.karbonhq.com/en/articles/5724366-best-practices-for-managing-your-work-in-my-week (R5) | Repeating schedules auto-create work; **deadline (external/statutory) vs due date (internal)** as two first-class fields; My-Week buckets with automatic time-triggered promotion | Due-rule engine materializes obligations; dual dates = lender deadline + internal prep date; buckets Overdue/This week/Next 30 days/Later auto-promote on time triggers, not human memory | Email-client triage ambitions | The closest structural analog to "each loan owes each lender a package every period" — and its dual-date framing is documented product law, not folklore |
| Vanta | Deadline-annotated status list donor | https://help.vanta.com/en/articles/11345374-the-tests-page · https://help.vanta.com/en/articles/11345547-test-priorities-and-slas · https://help.vanta.com/en/articles/11346006-editing-slas-in-vanta (R5) | Deadlines inline with two-class urgency **in the row** (soft attention vs overdue); SLA rules versioned against in-flight items; business-day deadline math | Urgency classes bound to ruled token roles (§13); amended due-rules apply to future occurrences only; the escalation surface is a filtered list, never a grid | Burying deadline rules in per-control settings pages — Covenant's rules are readable on every row | Proves compliance deadlines need **no calendar at all** — the strongest possible evidence for agenda-first |
| TaxDome | Evidence of how a compliance calendar actually renders | https://help.taxdome.com/article/785-workflow-basic-calendar-view · https://taxdome.com/blog/calendar-manage-your-day-workload-and-due-dates-in-a-handy-timeline-format (R5) | "Show due date only" toggle; saveable filter templates on the calendar; grid as secondary lens | If any grid ships (D-3), it is due-date-only, density-only, toggled off the agenda — never the landing view | Day/Week/Month as the primary workflow surface; duration bars on a deadline view | The vendor's own concessions (due-date-only toggle, crowded-day sidebar) document the grid's failure mode honestly |
| Financial Cents | Comparator for grid-as-lens | https://help.financial-cents.com/en/articles/4610145-calendar-view (R5) | Plots work on its due day from the start; recurrence generated on a forward horizon | Rolling 12-month materialization horizon; any grid strictly subordinate to the list | The grid as a tracking surface for managing work | Independent confirmation of the same lesson from a second compliance-work vendor |
| Ramp | Readiness gating for attention queues | https://support.ramp.com/hc/en-us/articles/4417421399699-Transaction-Reviews (R5) | Items enter the action queue only when actionable; blocked items live elsewhere with a Remind action that pushes the ball to whoever owes the input | The your-move escalation stage fires only when human action unblocks; at-risk rows carry the chase projection instead of queue noise | Approval-hierarchy routing (manager chains) — borrower teams are 1–5 people | Cleanest documented separation of "ready for you" vs "waiting on inputs" |
| Linear | Saved views + rhythm framing | https://linear.app/docs/custom-views · https://linear.app/docs/conceptual-model (R5) | View scoping personal→workspace, star-to-sidebar, URL-shareable views; cycles as a first-class rhythm page | The shared SavedView mechanism applied to the agenda; the reporting cycle as the rail's rhythm framing (cycle summary) | Triage accept/decline verbs — lender obligations cannot be declined | Its view mechanism is the app-wide standard this surface consumes rather than reinventing |
| LoanBoss | Domain-native critical-date taxonomy | https://www.loanboss.com/notifications (R5) | Critical dates include covenant test dates, extension notice windows, maturity events — automatic reminders around all of them | Obligation types extend beyond packages to notice windows and maturity events **where the loan's own documents define them** | Rates/hedging/valuation breadth — Covenant stays on the reporting slice | The only multifamily-borrower-native evidence of what "critical dates" must include |
| Finley | Agreement→schedule decomposition | https://www.finleycms.com/blog/product-deep-dive-credit-facility-deliverable-tracking (R5) | Credit agreements decomposed into a schedule of due dates + deliverable templates; early warnings before violation | The RequirementRecord→Deadline materialization is the same decomposition for multifamily; at-risk raises before the date, not on it | The lender-shared workspace as a v1 assumption — Covenant is borrower-side first | Proof the exact obligation model already exists in adjacent form — validating the shape, never styling it |
| Google Calendar month grid | Named anti-pattern | https://support.google.com/calendar/thread/1706707 · https://support.google.com/calendar/answer/34580 (R5) | Nothing | Nothing — boundary marker only | Month-grid-as-primary: "+N more" truncation at ~2–3 items per cell; workload invisible exactly when heaviest; no urgency dimension — fatal for deadlines that cluster by construction (`Q+45d` stacks every loan onto the same fortnight) | Its documented failure is the reason decision D-3 exists |

Synthesis: the agenda becomes original by fusing Karbon's recurrence-with-dual-dates into Covenant's evidence-grounded due-rules — no benchmark carries **contractual provenance on the date itself** ("Q+45d per §8.02(b)(1)", click-to-source), because no benchmark's dates come from executed loan agreements. Vanta contributes urgency-in-the-row, Ramp the ready-vs-blocked split, TaxDome/Financial Cents the grid-as-lens ceiling, and the R5 clustering math makes the recommendation domain-correct rather than aesthetic: a borrower-side reporting book is a deadline register with a rhythm, not a wall calendar. The escalation ladder (remind → chase → your-move → the one push-eligible overdue class) is Covenant's own — it exists because the agent chases and the quiet log absorbs routine, leaving push for the single class where lateness has contractual consequence.

## 15. Domain references

Terminology and expected-data references only: **Finley** (deliverable schedules; compliance-deadline vocabulary for credit facilities), **LoanBoss** (multifamily critical-date taxonomy: covenant test dates, extension notice windows, maturity events), **Karbon/TaxDome-class** practice management (recurring compliance-work vocabulary: work instantiation, deadlines vs internal dates), and servicer practice as evidenced in the Calloway Park kit (the JLL quarterly questionnaire cadence and the §8.02(b) delivery windows — evidence: SLOT-3/4/5). Domain authority does not equal visual authority: none of these products' renderings govern a pixel here. **Covenant semantics — what is owed, when, and under which rule — come from the loan documents and from Terry, not from any referenced product** (law 8; 05 preamble).

## 16. Accessibility, performance, and safety

- WCAG contrast and focus: all text on the gray ladder meets AA at the specified rungs; urgency is never encoded by color alone — the status chip text and `EscalationGlyph` icon carry the same information redundantly (§13); visible focus ring on every interactive element.
- Keyboard completeness: every act in §12 is keyboard-reachable, including acknowledge and evidence summon; heat-strip cells (if built) are buttons with accessible names ("August 14, 7 obligations, contains overdue").
- Screen-reader semantics: buckets as labeled regions with counts; rows as list items announcing "obligation, loan, lender, due {date}, prep by {date}, {status}, escalation {stage}"; the provenance chip announces "due rule {rule}, per section {cite}, opens evidence"; promotion re-seats announced politely via live region on explicit refresh only.
- Table virtualization/large data: a 50-loan book at full horizon ≈ low hundreds of rows — virtualize above 200; collapsed buckets render lazily.
- Loading and latency feedback: skeleton buckets; last-materialized timestamp always visible; `MaterializationStaleBanner` beyond 24h; promotion recompute on view so buckets are never stale at render (04 §1 recompute-on-view spirit).
- Destructive action confirmation: no destructive acts exist here; acknowledge is typed but non-destructive and revertible.
- Certify and external-send safety: not applicable — this surface can never reach either gate; it links to them and renders their outcomes.
- Source immutability: evidence opened from chips is the read-only Evidence context over the filed document (Documents owns the canon); the agenda can never mutate a document or a rule.
- Auditability: every escalation stage, push, chase, acknowledgment, and met deadline is an append-only ActivityEvent with actor + timestamp + deadline identity; the cycle history is reconstructable entirely from the log; push delivery records are permanent (§5 recovery).

## 17. Acceptance tests and fixtures

Fixtures:

- **CAL-SPINE** — Calloway Park with the confirmed SLOT-3 §8.02(b) requirement set (quarterly YTD income statement Q+45d; annual statements FYE+120d incl. cash flows; rent schedule at both cadences; annual certification bundle §8.02(b)(2)(E)/(F)/(G); Form 6241 ENERGY STAR annual; §8.02(b)(4) on-request items rate-capped once per 6 months).
- **BEXLEY-CANON** — Bexley reporting-only loan with the monitored 90% floor and the 268/301 = 89.04% shortfall period (canon).
- **CLUSTER-7** — the 7-loan demo book, all on quarterly Q+45-class rules (clustering fixture).
- **ESC-POL-A** — escalation policy fixture: remind at prep−7d, chase on missing inputs, your-move at deadline−3d, push at deadline+1d (fixture values, explicitly not product defaults).

Ticket-ready tests:

1. Materialization: given CAL-SPINE confirmed, the engine materializes exactly the §8.02(b) occurrences for the horizon — each `lender_due_date` = period end + the rule's offset; **no deadline exists for any proposed/unconfirmed record** (fail-closed assertion); the on-request requirement renders as conditional-dormant with its rate-cap note and no date.
2. Idempotency/data-integrity: run materialization three times over CAL-SPINE — row set identical by `(loanId, reqId, period)`; no duplicates; deleting nothing (append-only history of promotion events preserved).
3. Dual dates: with the borrower margin memory set, every row renders lender date + "prep by" = lender date − margin; the expansion shows the derivation with the memory's "learned {date} from {who}" stamp and an inspect/correct link.
4. Bucket promotion: freeze the clock at prep−31d → row in Later this cycle; advance to prep−29d → Next 30 days; into the prep week → This week; past the lender date unsent → Overdue. Zero human action involved (Karbon-automator behavior); focus survives a promotion re-seat.
5. Provenance (lit-row): click the `DueRuleChip` on CAL-SPINE's quarterly-statement row → Evidence opens beside the agenda and lights the §8.02(b)(1) definition region; the light persists until dismissed; the row stays highlighted. Regression assertion in the U1-F1 class: the **correct** region lights, verified against the fixture's region ref, not by eyeball.
6. Escalation ladder: under ESC-POL-A, stages fire in order remind → chase → your-move → push, each writing an ActivityEvent with actor and trigger; **only** the overdue stage produces a push (assert zero push events from any other state across the whole simulated quarter); acknowledge stops repeat push; un-acknowledge re-arms it.
7. Clustering / D-3 evidence test: with CLUSTER-7, all seven Q2 packages land in the same fortnight; the agenda renders all seven rows with no truncation; if the heat-strip is built, the peak day cell shows a numeral (≥5) and clicking it scrolls the agenda to all of that day's rows — assert **no "+N more"-class truncation affordance exists anywhere in the DOM**.
8. Rule amendment: amend CAL-SPINE's quarterly rule (fixture amendment) → future occurrences re-materialize under the new rule; the in-flight occurrence keeps its original date and renders the "rule amended for future periods" note (Vanta versioned-SLA behavior).
9. Vocabulary firewall: seed BEXLEY-CANON's shortfall period → assert the strings "pass", "watch", "shortfall", "breach" appear nowhere on `/calendar`; the period chip links to the period where the verdict renders.
10. Completion and void: seal CAL-SPINE's annual package (SendRecord) → row flips complete, leaves active buckets, joins cycle history with its on-time/late flag; void the certification before send → the row returns to its date-appropriate bucket with the void reason linked (never silent).
11. Viewport fixtures: 2048 / 1728 / 1440 / 1280 / 1152×720 — assert nav-rail, right-rail, and evidence behavior per §10; assert every column shed appears in the column picker (no silent compression).
12. Keyboard fixture: complete a full pass — arrive via `G C`, J/K through buckets, E expand, V evidence, A acknowledge an overdue row, Esc chain — pointer never touched; with the nav rail collapsed.
13. Permission fixtures: PMC preparer sees only their client's rows (absent, not grayed; header counts exclude them); reviewer has no acknowledge affordance and cannot save workspace-scope views.
14. Accessibility checks: axe-clean at all viewport fixtures; forced-colors emulation — urgency information fully present with color removed; screen-reader row announcement matches §16.
15. Saved-view sharing: user A saves "Q3 — Fannie loans" as a workspace view; user B opens the same URL and sees the identical filter/sort state; user B's ephemeral filter tweak does not mutate the shared view (Attio semantics, R5 synthesis (d)).
16. Stale-clock honesty: freeze the promotion scheduler for 25 simulated hours → `MaterializationStaleBanner` renders with the real last-run age; resuming the scheduler clears it without a reload.
17. Benchmark challenger review: a reviewer armed with the R5 Karbon/Vanta/TaxDome citations attempts to name a documented mechanic this surface lacks a reasoned position on; every unanswered mechanic becomes a backlog item or an explicit rejection in this brief.

## 18. Build plan

- Dependencies: **F5 due-rule engine** (this brief's machinery — 03 §5, 05 §6 gap 9); F2 persistence (RequirementRecords, ReportingPeriods, a deadlines table); confirmed requirements from Extraction & Confirmation (fixture-confirmed records until that surface ships); the notification service (A7) for the push stage; escalation-policy storage (Settings brief); the shared SavedView mechanism; the lit-row contract (`cross-cutting/provenance-lit-row-trace.md`).
- Foundation work: `src/lib/covenant/deadlines/` — due-rule parser covering the 05 §1.2 cadence shapes (`Q+45d`, `FYE+120d`, monthly, on-request, event); materializer (idempotent, horizon-bounded); promotion pass; escalation state machine. Pure functions, test-covered like the F-series; a scheduler wire runs promotion at least hourly.
- Components to build first: `ObligationRow` + `DueRuleChip` (the row is the surface), then `ObligationAgenda` bucketing, then `CycleSummaryRail`; `MonthHeatStrip` only after a D-3 adopt ruling.
- Vertical slice (the send-vertical pattern): one loan (CAL-SPINE), end to end — confirmed requirement fixtures → materializer → the real `/calendar` route rendering engine rows → simulated-clock promotion → ESC-POL-A escalation writing real quiet-log events → completion driven by the real `POST /api/covenant/send` period flip. One route, one loan and period set, engine data throughout.
- Migration from fixture data: the fixture agenda's demo rows retire bucket-by-bucket behind the flag; no demo string may survive as a date (04 §2.5 — fail-closed renders honestly, fixture habits retired).
- Rollout/feature flag: `covenant.calendar.engine` (agenda from engine rows) and `covenant.calendar.heatstrip` (D-3-gated, default off; never ships without the ruling).
- Proof artifacts required: materialization test suite green; a recorded promotion run across a simulated quarter with CLUSTER-7; the lit-row screenshot from test 5 with the region-ref assertion; the push-singularity log assertion from test 6.
- Final gate: `ADJUST` confirmed — agenda kept and wired; dual dates, provenance, urgency, and the escalation ladder added; the month grid decided by Terry at D-3 with this brief's recommendation (agenda default; optional due-date-only density strip; grid-as-workspace rejected) on the table.
