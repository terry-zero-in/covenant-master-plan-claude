# HOME / OVERVIEW

## 1. Identity and verdict

- Product layer: portfolio floor (the landing floor of the book).
- Route/address in the basis-v2 repository: today the Home view inside `/review-room`'s view-switching shell (FIXTURE, inventory B1 — 07 §1); target deep route `/home` in the `(covenant)` route group, mounting `CovenantShell` with initial view Home (08 §1). Components land in `src/components/covenant/home/`; the your-move query in `src/lib/covenant/your-move.ts`; the activity store in `src/lib/covenant/activity.ts`.
- Current build state: `FIXTURE` — the built Home renders an activity feed and your-move items from demo strings; one feed row contradicts the canonical ledger (finding U3-F2), possible only because feeds are separate fixture arrays (snapshot §3; 07 §2).
- Existing-surface verdict: `ADJUST` — the layout instinct (concentrate the borrower's next acts) is right; the machinery (one computed query, one event store) is absent.
- Research tier: B.
- Primary users/roles: owner (certifies/sends), PMC preparer (prepares; sees the same book scoped to their clients), reviewer (read-only projection).
- Frequency and session duration: the most-opened surface — daily glances of 10–60 seconds mid-cycle; several visits per day in deadline week. Home is a dispatch floor, not a work floor: sessions end by leaving it.
- Error cost: indirect but real — if Home under-reports or mis-groups your-move, a period misses its deadline (a compliance failure, 03 §5) or the borrower certifies later than the deadline margin allows. Home never renders a shipping number itself, so the terminal error (a wrong number certified and sent) cannot originate here; the risk is omission and contradiction, which is exactly what the single-query/single-store repair removes.
- Success criterion: within five seconds of landing, the borrower knows (a) how many decisions block the book right now, (b) which act each one is, and (c) where the cycle stands — and every count on screen agrees with every other surface because they are the same query.

## 2. User job and decisions

- Primary job-to-be-done: "Tell me what the book needs from me, in the order it needs it, and prove the rest is handled."
- Decisions made here:
  - which your-move item to take next (dispatch decision — resolved by navigation);
  - whether a proactive finding is acknowledged/dispositioned (a typed act on the Finding object, whose disposition affordance renders here — see §3);
  - nothing else. Every substantive act (certify, send, file, confirm, disposition a tie-out, approve a draft) happens on its owning surface via deep link.
- Questions the surface must answer in scan order:
  1. How many moves are mine, grouped by act?
  2. Which are deadline-pressed?
  3. Where does the cycle stand (open → sent counts)?
  4. What is due in the next 7 days?
  5. What has the analyst noticed early (findings)?
  6. What did the analyst do while I was away (collapsed)?
- What the user should not have to decide here: whether a count is trustworthy (one query, one store); whether an item is actually actionable (readiness gating admits only actionable items — R5 research: Ramp); which surface owns an act (every row deep-links); what to do about blocked-on-inputs work (that lives in Inbox's waiting-on-inputs lane and the agent's chase machinery; Home shows only escalations).
- Entry paths: sign-in default landing; rail item Home; `G H` chord; ⌘K "Home"; the shell logo.
- Exit paths: every your-move row → its owning surface with loan+period in the URL (07 §3 entry/exit spine); book-strip segment → `/loans` filtered to that period status; deadline row → `/calendar` (or the period it belongs to); finding card → its evidence / Loan Detail / drafted narrative; quiet-activity row → the owning object; "all n in Inbox →" → `/inbox`.
- Completion/advancement conditions: Home itself advances nothing. A row leaves the your-move section only when the owning surface's real act completes (done-by-action — no mark-as-done exists anywhere on this surface). The empty your-move state is the success state.

## 3. Object and ownership model

| Object/action/status | Owned here? | Summary/link elsewhere | Address/identity | Notes |
|---|---:|---|---|---|
| Your-move query result (items + count) | No — engine-owned (`your-move.ts`, definition 03 §4) | Home renders the grouped top slice; Inbox renders the full register; rail badge renders the count | item = {class, loanId, period, objectRef, blockedSince, deadlineRef} | ONE computed query; every appearance is the same result (07 §2 duplication fix) |
| ActivityEvent (quiet log) | No — cross-cutting append-only store (02 §2) | Home renders a collapsed filtered projection; Loan Detail and period surfaces render their filters of the same store | `eventId`, actor, evidence refs, loan/period | U3-F2 becomes structurally impossible: there is no second feed to contradict |
| Finding (proactive watch) | No — agent-layer findings service owns state (02 §2) | Home is a ruled render+disposition point ("Home surfaces your-move" — 02 §2); Loan Detail shows per-loan; a blocking finding becomes a review stop | `findingId`, trigger ref, headroom evidence, drafted narrative ref | Acknowledge/disposition invoked from the card writes through the findings service — it is the owning real act, not a local mark-as-done |
| Period status counts (book strip) | No — orchestration spine owns period status (02 §3) | Home renders computed counts; `/loans` renders the rows | per-org aggregate over ReportingPeriods | No surface mutates status directly |
| Deadlines (7-day mini-agenda) | No — Calendar/Obligations owns due dates + escalation (02 §3) | Home shows the next-7-days slice with links | rows keyed to (loanId, period, requirement) | Due-rule provenance stays Calendar-owned; Home reuses its tooltip render |
| Arrival decisions | No — Intake owns (02 §3) | Home's Arrival-decision rows deep-link to `/intake` selection | arrival id | Never resolved from Home |
| Item-level presentation states (new/seen/snoozed) | No — Inbox owns | Home renders none of them (C-8: no unread affordance anywhere on Home) | — | Snooze filtering still applies to Home's rows because it executes inside the shared query |
| Section layout/collapse preferences | Yes | — | per-user, per-org | The only state Home owns: presentation preferences (quiet-activity collapsed state, group collapse) |

No-double-homing boundary: Home owns no domain object. It is a projection floor over four owned stores (your-move query, period ledger, deadline engine, ActivityEvent store) plus the findings service. Everything it shows is a summary with a link to the owner.

## 4. Data and semantic model

- Source facts: none rendered directly (Home never shows a source cell; evidence opens from findings via the lit-row chain).
- Extracted values awaiting confirmation: appear only as counted your-move items ("Exceptions — extraction: n"), never as values.
- Confirmed values: not rendered; the book strip and counts are aggregates.
- Deterministic outputs:
  - the your-move query (03 §4) — items blocked on a human decision: periods awaiting certify, awaiting send, unrecognized arrivals, extraction/normalization exceptions, failed tie-outs, findings awaiting disposition, drafts awaiting approval, deadline escalations;
  - minus actively-snoozed items — snooze filtering executes inside the query, so every render everywhere stays identical;
  - ordered by deadline proximity, then blocked-since age (fixed order; no user sort);
  - period-status aggregates; deadline materializations (03 §5); finding evidence (signed headroom from TestResult rows).
- The item contract (shared with Inbox and the rail badge — one shape, one query):

```text
YourMoveItem {
  class: certify-ready | send-ready | arrival-decision | exception-extraction |
         exception-normalization | exception-tieout | finding-disposition |
         draft-approval | deadline-escalation
  loanId, period                      ← identity chips + deep-link address
  objectRef                           ← the blocking object (periodId / arrivalId / valId / tieOutId / findingId / draftId / deadlineId)
  blockedSince, deadlineRef?          ← ordering inputs (deadline proximity, then age)
  actRoute                            ← the owning surface's deep route (07 §3 spine)
  requiredRole?                       ← certify/send rights holder where restricted
}
```

- Agent proposals/drafts: surfaced as counts + links (drafts awaiting approval; drafted mitigant narrative linked from a finding card). Draft text never renders on Home beyond its title line.
- Human decisions: dispatch (navigation); finding acknowledge/disposition (typed act via the findings service).
- Certified values: never on Home; "certified n" in the book strip is a status count, not a value.
- Versions/periods/packages: every row carries loan+period identity chips; the book strip is grained per ReportingPeriod; package revision identity stays on the spine surfaces.
- Evidence/provenance: finding cards carry lit-row refs into their computed evidence (headroom figures open Evidence beside the claim per the frame contract, 08 §7); quiet-activity rows carry evidence refs per ActivityEvent grain (04 §2.3: actor, evidence, loan/period/package, outcome).
- Permissions/read-only projections: one org-level query — the badge renders the same number for every member (the strongest reading of the one-number law; 08 §10 test 6). Role shapes affordances, not counts: a preparer sees certify-ready rows labeled "awaiting {owner-name}" with a nudge affordance instead of an enabled act; a reviewer sees all sections read-only with disposition controls absent. PMC mode scopes the whole floor per Client (02 §1); the Client column never shows in owner mode.

Field grain: organization (the floor's scope) → client (PMC filter) → loan → reporting period (row identity) → requirement/test/finding/draft (item objectRef). Package, certification record, and send record appear only as status words in row titles ("Q2 package — certify-ready").

## 5. State machine and exceptions

Home is a projection; its states are render states of the queries beneath it. Grain: per section, per row.

How each your-move class enters and leaves the query (entry = engine trigger; exit = the owning surface's real act — the only exit that exists):

| Class | Enters when (trigger, actor) | Leaves when (real act, surface) | Deep-link target |
|---|---|---|---|
| Certify-ready | period → ready ∧ readiness true (engine) | certification recorded (human, Certificate) | `/covenant/[loanId]/[period]/certificate` |
| Send-ready | certification recorded ∧ un-voided ∧ not sent (engine) | SendRecord written (human, send gate) | certificate route, send panel |
| Arrival decision | recognition cannot auto-file (agent → gate) | file/replace/hold/dismiss decided (human, Intake) | `/intake` with arrival selected |
| Exception — extraction | low-confidence/conflict/unreadable proposal (agent) | confirm/correct/mark-unreadable (human, Extraction) | setup or period exception card |
| Exception — normalization | unmapped COA / control-total break (engine) | mapping confirmed / break resolved (human, Actuals) | `/covenant/[loanId]/[period]/actuals` |
| Exception — tie-out | prior-package reconciliation failed (engine) | disposition with reason (human, Review) | `/covenant/[loanId]/[period]/review` |
| Finding disposition | findings service raises shortfall/breach-class finding (agent over engine evidence) | acknowledge/disposition (human, findings service — invocable from the Home card) | evidence pane / Loan Detail |
| Draft approval | narrative/reply/chase-cadence draft ready (agent) | approve/edit (human, Composer or Correspondence) | the draft's owning surface |
| Deadline escalation | escalation ladder reaches your-move rung (engine policy) | underlying blocker resolved or waived (human, owning surface) | the blocked period/item |

| State | Render on Home | Trigger / actor | Prerequisites | Reversibility | Audit event |
|---|---|---|---|---|---|
| empty (no your-move) | "Nothing needs you right now." + book strip + next deadlines; the success state, styled calm — never congratulatory | query returns 0 | stores wired | new item re-enters naturally | — |
| awaiting-documents (holding) | NOT a your-move row (readiness gating). Appears only as book-strip "open n" and — if the escalation ladder fires — a Deadline-escalation row ("Bexley Q2: checklist incomplete, due in 4d, chases exhausted") | escalation policy (03 §5; 04 §1 Holding lane) | chase ladder exhausted within deadline−N | checklist completes or human waives via Intake | escalation logged |
| loading/processing | skeleton rows per section with stable heights; counts render only when computed — no placeholder numbers (04 §2.5) | route mount | — | — | — |
| partial/incomplete | a failed sub-query renders an honest `SectionErrorBlock` naming the failed store; other sections render; the rail badge shows "—", never a stale or partial number | store error | — | retry affordance | error logged |
| extracted-unconfirmed | your-move class Exceptions (extraction): row per loan+period with count | extraction pipeline | proposal exists | confirm/correct on owning surface removes it | via owning surface |
| low confidence / conflict | same Exceptions class; row subtitle names the reason ("2 low-confidence fields · 1 conflict") | extraction pipeline | — | same | same |
| stale/superseded | Exceptions row "re-confirm 3 values — source replaced"; the affected period's strip status does not advance | engine stale flag (03 §2) | replacement filed | re-confirmation | stale flag + resolution logged |
| failed tie-out | Exceptions (tie-out) row → Review Room stop | tie-out engine | tie-out ran | disposition in Review | disposition logged |
| watch | informational only — renders inside finding cards as headroom phrasing; never a your-move row by itself | TestResult verdict | — | — | finding raised logged |
| shortfall / breach | a shortfall/breach finding awaiting disposition is a your-move row (Findings group). Vocabulary law enforced structurally: chips render from TestResult basis — a monitored miss can only ever print "shortfall" (canon: Bexley 268/301 = 89.04% vs monitored 90% floor); "breach" requires `basis=covenanted` (02 §2 TestResult) | TestResult verdict | test computed | disposition (typed) | disposition logged |
| permission denied | a row whose act the viewer lacks renders with the act-holder's name, never disappears (counts stay one number) | role check | — | role change or the holder acts | — |
| read-only (reviewer) | full floor visible; zero action affordances; "read-only" banner | role | — | — | — |
| blocked/gated | fail-closed engine blocks (unknown form, missing definition) surface as Exceptions rows naming exactly what is missing (04 §2.5) | engine fail-closed | — | authoring/confirmation | block + resolution logged |
| certified (void-on-change) | a void event removes the period from "certified n", returns it to in-review in the strip, and raises the your-move row "re-review: certification voided — {what changed, by whom/what}" (03 §2) | void-on-change engine | certification existed | re-certification is a fresh act | void logged, loud in history |
| sent/sealed | leaves your-move; increments "sent n"; the seal event appears in quiet activity with a link to the sealed record in Reports | send gate | send completed | none — seal-not-wipe by design | SendRecord + seal logged |
| recovery/undo | quiet-activity rows for auto-filed arrivals carry Intake's undo-window re-route link (undo executes on Intake, not here). Certify and send have no undo by design — void and fresh acts only | — | — | — | re-route logged |

## 6. Engine / Agent / Human / Gate / Quiet Log

| Function | ENGINE (deterministic) | AGENT (prepares/proposes) | HUMAN (typed decisions) | GATE (interrupts) | QUIET LOG |
|---|---|---|---|---|---|
| Your-move section | Computes the one query (03 §4), incl. readiness gating and snooze filtering; fixed ordering | — (agent work never adds itself to your-move; completed agent work is quiet log — 03 §4) | Dispatch by navigation | Deadline-escalation rows are the gate's landing place on this floor | Row entry/exit events logged |
| Book strip + mini-agenda | Period-status aggregates; due-rule materialization slice (03 §5) | — | — | — | — |
| Findings | Trend series, headroom time-series, threshold-crossing detection (04 §1 proactive watch) | Frames findings ("you are $240K of NOI from a DSCR shortfall" — canon phrasing, 04 §1); drafts mitigant narratives; ranks by consequence | Acknowledge/disposition (typed act via findings service) | A finding blocking an imminent certify renders gate styling and also appears as a review stop | Findings raised + evidence logged |
| Quiet activity | Store integrity (append-only, identity-stamped — 04 §2.3) | Writes its completed autonomous work here | Reads; follows undo links | — | This IS the quiet log's Home projection |

Lane invariants checked: no shipping number renders outside engine output (Home renders counts and engine-computed headroom only); no push from this surface except deadline escalation per org policy (04 §2.3); agent presence is contextual — findings beside their evidence links, no generic chat box (the Ask entry stays in the canvas panel per 08 §6, capability prompts engine-completable only).

## 7. Information hierarchy

1. Frame header (shell, 48px): book-scoped breadcrumb `Covenant / Home` — no loan/period crumbs at this altitude (08 §2).
2. Decision/status summary: YOUR MOVE header with the count ("7 moves") — the same number as the rail badge, by construction.
3. Primary work region: the grouped your-move slice (§8 region 1).
4. Secondary context: THE BOOK THIS CYCLE strip + next-deadlines mini-agenda.
5. Evidence/proof: FINDINGS cards (each carrying lit-row refs into its computed evidence).
6. Actions: per-row deep links; finding acknowledge/disposition; nothing global.
7. Activity/history: QUIET ACTIVITY, collapsed, last.

Absent by design (DIRECTIONS): decorative charts; KPI tiles without decisions attached; news-feed noise; any unread/read affordance; any mark-as-done control; any chart that would not out-encode its adjacent text (the book strip is counts-as-instruments, deliberately not a chart).

## 8. Page anatomy and regions

Single work window (ruled pane model; Home never splits). Content column max-width 1120px, centered.

```text
┌─ shell header (48px) — Covenant / Home ────────────────────────────────────┐
│ YOUR MOVE — 7                                                              │
│   Certify-ready · 2         [row] [row]            all 2 in Inbox →        │
│   Exceptions · 3            [row] [row] [row]                              │
│   Drafts awaiting approval · 1   [row]                                     │
│   Deadline escalations · 1  [row]                                          │
│                                                                            │
│ THE BOOK THIS CYCLE                          NEXT 7 DAYS                   │
│   open 2 · in-review 1 · ready 1 ·           [deliverable · loan · due]    │
│   certified 1 · sent 2                       [deliverable · loan · due]    │
│                                              full agenda → Calendar        │
│ FINDINGS                                                                   │
│   ┌─ FindingCard ──────────────┐  ┌─ FindingCard ──────────────┐           │
│   │ shortfall · headroom line  │  │ watch · headroom line      │           │
│   └────────────────────────────┘  └────────────────────────────┘           │
│                                                                            │
│ ▸ While you were away — 12 actions                    (collapsed feed)     │
└────────────────────────────────────────────────────────────────────────────┘
```

### Region 1 — YOUR MOVE (top)

- Purpose: dispatch — the borrower's queue of blocked decisions.
- Content: group headers in fixed act order — Certify-ready · Send-ready · Arrival decisions · Exceptions (extraction, normalization, tie-out) · Findings awaiting disposition · Drafts awaiting approval · Deadline escalations (DIRECTIONS) — each header with its full count; up to 3 rows per group (the top slice, ordered by deadline proximity then age), then "all {n} in Inbox →". Empty groups do not render.
- Persistence: persistent; never collapses (the section is the page's reason to exist).
- Interaction: J/K row traversal; Enter opens the owning surface; no inline snooze/delegate here (Inbox affordances).
- Minimum height: one row. Why inline list, not pane: dispatch needs no co-visibility — each row's full context lives on its owning surface.

### Region 2 — THE BOOK THIS CYCLE

- Purpose: cycle position at a glance.
- Content: (a) period-status strip `open n · in-review n · ready n · certified n · sent n` — five uniform segments, each an instrument (click → `/loans` filtered to that status), counts in Geist Mono; (b) next-deadlines mini-agenda — 7-day window, max 5 rows (deliverable title · loan · due date, due-rule provenance on hover), footer "full agenda → Calendar".
- Persistence: pinned; sub-blocks side by side ≥1440px, stacked below.
- Resize/collapse: on narrow viewports the strip collapses to one labeled summary line — labeled, never silently compressed.
- Why not a chart: five counts out-encode any bar; chart doctrine applied by omission.

### Region 3 — FINDINGS

- Purpose: the analyst's proactive watch, surfaced before it becomes a deadline problem.
- Content: `FindingCard`s ranked by consequence (agent lane); grid 1-up at 1440, 2-up ≥1728, 3-up at 2048.
- Persistence: persistent when non-empty; absent entirely when no findings (no empty-state card).
- Interaction: card actions Acknowledge · Disposition (typed popover); evidence link; drafted-narrative link.
- Minimum: one card width 480px.

### Region 4 — QUIET ACTIVITY

- Purpose: discoverable record of autonomous work — "what the analyst did while you were away" (DIRECTIONS).
- Content: collapsed header "While you were away — {n} actions" + filter chips (actor: engine/agent/human · type · loan); expanded: uniform `ActivityEventRow`s from the one store, newest first, virtualized past 50.
- Persistence: persistent, collapsed by default (04 §2.3: completed autonomous work is never pushed).
- Interaction: `.` toggles; rows link to owning objects; auto-file rows carry the Intake undo-window re-route link.

### Canvas panel and modals

- Canvas panel (floating, summonable, the separate ruled slot): Ask with rotating engine-completable capability prompts; never load-bearing for a Home decision; Esc dismisses.
- Modals: none on Home — no typed act with legal weight initiates here; finding disposition uses an inline typed confirm popover (not legal-weight; proof stays visible beside it).

## 9. Co-visibility matrix

| Datum/surface A | Datum/surface B | Must be simultaneous? | User decision enabled | Default topology | Fallback |
|---|---|---:|---|---|---|
| Your-move row | Owning surface's full context | No | Dispatch only | Deep link, navigate away | — |
| Your-move group counts | Rail badge count | Yes (same viewport) | Trust ("one number") | Both render the one query | Badge shows "—" on query failure, never a stale number |
| Finding card claim | Its computed evidence (headroom inputs) | On demand | Judge the finding before disposition | Evidence pane summons beside the card (overlay from the right, never covering the card) | Below 1280px: overlay sheet |
| Book strip | Deadline mini-agenda | No (adjacent is enough) | Cycle awareness | Side-by-side ≥1440px | Stacked |
| Quiet-activity row | Undo affordance (auto-file re-route) | No | Follow link to Intake | Inline link | — |

No pane is created merely because information exists; Home stays a single window by rule.

## 10. Layouts and viewport behavior

| Viewport | Rail | Content column | Region 2 | Findings grid | Evidence pane |
|---|---|---|---|---|---|
| 1440 | 240px expanded | 1120px centered | side-by-side 60/40 | 1-up | overlay from right, 420px |
| 1728 | 240–280px | 1120px | side-by-side 60/40 | 2-up | overlay 480px |
| 2048 | 280px | 1120px (findings to 1360px) | side-by-side 60/40 | 3-up | overlay 480px |
| <1280 | 48px icon rail | full-width, stacked | labeled summary line | 1-up | full-height overlay sheet |

- 1440px: rail expanded (240px); content column 1120px centered; Region 2 sub-blocks side by side (strip ~60% / agenda ~40%); findings 1-up.
- 1728px: identical topology; findings 2-up; added width becomes open ground, not new regions (rhythm unchanged).
- 2048px: findings 3-up; regions 1–2 hold at 1120px column (line-length discipline); the findings grid may extend to 1360px.
- Narrow/compact (<1280px): rail auto-collapses to 48px icon rail (08 §9); regions stack single-column; Region 2 collapses to its labeled summary line; quiet activity stays collapsed.
- Default pane topology and ratios: one work window, 100%; no split exists on this surface.
- Focus behavior: J/K moves one focus ring through your-move rows across groups; group headers announce on entry.
- Compare behavior: none (comparisons belong to spine surfaces).
- Proof/source behavior: finding evidence opens beside the card; Esc closes the summoned pane before it ever navigates.
- Minimum viable dimensions: the frame minimum 1152×720 (08 §9); Home remains fully usable at minimum (registers-class surface, no "larger window" state).
- Tab stacking/replacement: not applicable (no split); the summoned evidence pane becomes a full-height overlay sheet below 1280px.
- No silent compression: any region below its minimum becomes a labeled collapsed line with counts.

## 11. Components and exact anatomy

- `CovenantShell` — REUSE (`src/components/covenant/CovenantShell.tsx`): rail, 48px header, canvas panel; `/home` mounts it with initial view Home.
- `CountBadge` — REUSE (the ruled single shared component; snapshot §4: 37 hand-rolled atoms consolidate to one): renders the your-move count on the rail and the YOUR MOVE header. One data source: the query.
- `YourMoveRow` — NEW, shared with Inbox (built once in `src/components/covenant/your-move/`). Parts:
  - act-class icon (Lucide, one glyph per class; no emoji);
  - title ("Certify Bexley — 2026-Q2");
  - loan + period identity chips;
  - deadline chip (relative; severity-tinted only when escalated);
  - blocked-since age; trailing chevron.
  - Home variant: no inline snooze/delegate affordances; Enter/click navigates. Uniform row height 40px.
- `YourMoveGroupHeader` — NEW: act-class label + full-count `CountBadge` + "all {n} in Inbox →" link whenever the slice truncates (the slice is always labeled — no U3-C1-class silent truncation).
- `BookCycleStrip` — NEW: five status segments (label + Geist Mono count), open-not-boxed, hairline separators, each segment a link. Deliberately not a chart.
- `DeadlineMiniAgenda` — NEW: uniform rows (deliverable · loan · due date); due-rule provenance on hover ("Q+45d per §8.02(b)(1)" — evidence-class cite) rendered by Calendar's tooltip component, reused not re-implemented.
- `FindingCard` — NEW. Parts:
  - severity-neutral card frame (hairline + one raised gray rung);
  - trigger title; verdict chip (pass/watch/shortfall/breach per TestResult basis);
  - one headroom sentence with engine values interpolated by reference — values re-render from the engine at view time, never frozen into text (04 §2.1); canon example: "You are $240K of NOI from a DSCR shortfall";
  - evidence link (opens the lit-row chain); "drafted narrative →" link into Composer/Review;
  - footer actions Acknowledge · Disposition → typed confirm popover naming the finding, its evidence, and the consequence (gate-law anatomy: exact ask, why, evidence, recommendation, consequence of delay, audit record).
- `QuietActivityFeed` — NEW over the ActivityEvent store: collapsed header with count; filter chips; `ActivityEventRow` (actor glyph, sentence, loan/period chips, timestamp, evidence/undo links); virtualized.
- Evidence panel — REUSE (the review-room Evidence panel) summoned for finding evidence; the U1-F1 wrong-row repair is a stated dependency (cross-cutting provenance brief).
- `CommandPalette` / `SearchPalette` — REUSE: `G H` lands here; palette carries Home and per-class your-move places.
- `SectionErrorBlock` — NEW: names the failed store, offers retry; used by every section independently.
- Empty state — NEW calm all-clear object (text + book strip remains); no illustration spectacle.

## 12. Interaction specification

- Selection: single-row focus; no multi-select on Home (bulk acts live on owning surfaces).
- Hover: row hover raises background one gray rung; deadline chips reveal the due-rule tooltip; finding sentences reveal per-value provenance affordances.
- Focus: visible 2px accent (#7189FF) focus ring; focus order = DOM order = scan order (§7).
- Keyboard: `J/K` next/prev your-move row (crosses groups); `Enter` open owning surface; `A` on a focused finding card = Acknowledge (opens the typed popover); `E` on a finding = open evidence; `.` toggles quiet activity; `G H/G I/G L/G C/G R/G D` chords; `⌘K` palette. No snooze/delegate keys here (Inbox owns item-level states).
- Editing and validation: none — no editable values exist on Home.
- Bulk action: none by design.
- Undo/recovery: navigation is the only "action"; the disposition popover has an explicit cancel; auto-file undo links route to Intake within its undo window.
- Sorting/filtering: your-move order is fixed (deadline proximity, then age) — no user sort, so the surface always agrees with the badge's implied urgency; quiet activity is filterable by chips but never reorderable.
- Drill-down and return path: every deep link carries loan+period in the URL; browser back returns to `/home` with scroll and focus restored.
- Source-linked selection: any figure inside a finding's evidence lights its exact source line in the Evidence pane and stays lit (frame contract 08 §7).
- Save/persistence: collapse states and activity filters persist per user; nothing else is savable — Home is not a register and has no saved views.
- Collaboration/commenting: none; annotation lives in Review.

## 13. Visual craft direction

- Typography roles: section headers 13px/600, uppercase-tracked, secondary ink rung; row titles 14px/450 primary ink; meta (chips, ages, timestamps) 12px tertiary ink; the finding headroom sentence 15px — the one enlarged text on the page, because it carries the surface's only figures.
- Financial numbers: Geist Mono, `tabular-nums slashed-zero` (ruled), for every count and headroom figure; counts right-align inside `CountBadge`.
- Spacing rhythm: 8px base grid; 24px between regions; 40px uniform row heights; 32px group headers.
- Density: calm-register density — Home is a glance floor; more open ground than any other Covenant surface.
- Open ground vs earned boundaries: no boxed containers except `FindingCard` (which earns its hairline as a discrete disposable object); rows separate by hairline only; the book strip is open with hairline dividers.
- Dividers/elevation: ten-rung gray ladder only (adopted 2026-08-07 — snapshot §4); the raised rung reserved for the summoned evidence pane and the canvas panel.
- Semantic color: accent family #7189FF for interactive/focus/links (hover #8EA1FF, active #6078F4, tint #A9B5FF for selected ground); verdict/severity chips consume the ruled Review-9 verdict tokens — severity-only color, nothing decorative; deadline chips tint only at escalation. Zero new color values proposed.
- Certified-sheet treatment: none — the six certified-sheet hexes are the sheet's paper and never paint Home; "certified n" in the strip is plain ink.
- Focus/selected/hover states: consume the existing Covenant control-layer tokens (Ruling-J measurement: the layer exists with zero consumers — Home becomes a consumer, never an author).
- Chart style: no charts ship on Home (chart doctrine: nothing here would out-encode adjacent text).
- Motion: rows enter with a 120ms fade/slide; count changes swap numerals without pulsing; `prefers-reduced-motion` disables both.
- Long-session ergonomics: optimized for re-visit scanning rather than long sessions — stable region order, zero layout shift between visits, skeletons hold heights.

## 14. Benchmark research and synthesis

| Product | Limited role | Official evidence | Exact pattern to take | Covenant adaptation | What to reject | Why this reference beats alternatives |
|---|---|---|---|---|---|---|
| Ramp | "Needs your attention" queue donor | https://support.ramp.com/hc/en-us/articles/4417421399699-Transaction-Reviews (R5 research) | One inbox across heterogeneous object types with a count badge; readiness gating — items enter only when actionable; blocked items live elsewhere with a Remind action | Your-move admits only ready items (period closed, inputs present, readiness true); blocked work stays in Inbox's waiting-on-inputs lane; Home shows only escalations | Approval-hierarchy routing (borrower teams are 1–5 people) | The only researched product documenting gating + a separate blocked surface as policy — exactly the C-8/U3-F7 repair shape |
| Linear | Concentration + snooze semantics donor | https://linear.app/docs/inbox (R5/R1 research) | One delta surface; snooze hides until a chosen time or new activity, whichever first | Snooze is set in Inbox but applied inside the shared query, so Home and the badge agree; auto-unsnooze on activity resurfaces work without human vigilance | Unread/read as a count concept (C-8: unread is not your-move) | Best-documented single-queue discipline; snooze-until-activity is the only snooze that cannot hide work forever |
| Vanta | Deadline-in-the-row rollup donor | https://help.vanta.com/en/articles/11345374-the-tests-page ; https://help.vanta.com/en/articles/11345547-test-priorities-and-slas (R5 research) | Urgency as a property of the row (soft-warning orange vs overdue red), not a position on a grid; soft and hard states drive different machinery | Deadline chips on your-move rows; escalation (gate) vs watch (informational) are different render classes, mirroring Vanta's Failing-vs-Needs-Attention automation split | The SLA-workforce apparatus | Proves compliance deadlines need no calendar on a status floor |
| Karbon | Time-decayed agenda donor | https://help.karbonhq.com/en/articles/5724366-best-practices-for-managing-your-work-in-my-week (R5 research) | Auto-promoting time buckets (Later → Next Week → This Week) with no human re-filing | The 7-day mini-agenda is the "This Week" slice only; the full buckets live on Calendar | Rendering the full My Week on Home (would double-home Calendar) | Closest analog to recurring obligations promoting themselves on time triggers |
| Stripe | Event-log projection donor | https://docs.stripe.com/payments/payment-records ; https://docs.stripe.com/development/dashboard/events (R5 research) | Append-only event log from which state is derived, never overwritten; detail = facts + event history | The ActivityEvent store; quiet activity and every other feed are filtered projections of it (the U3-F2 structural fix) | Stripe's open-ended filter surface area | The cleanest documented statement of events-as-truth — the exact contradiction-killer Home needs |
| Mercury | Boundary marker (rejected pattern) | https://support.mercury.com/hc/en-us/articles/38790547830036-Viewing-cashflow-and-transactions-data-on-your-Transactions-page (R5 research) | — (its graphs-bound-to-filters trick belongs to register surfaces like Loans/Reports) | — | Real-time graphs above the landing surface — Home's "absent by design" list exists precisely to keep this genre off the dispatch floor | Included to document that the omission of charts is a decision against a strong researched pattern, not an oversight |

Synthesis: Home becomes original by inverting the dashboard genre — it renders zero KPIs and zero charts, and instead concentrates the borrower's constitutional role (review, certify, send — the only acts the human must perform, per the locked trust hierarchy) into one gated queue, with the cycle's state demoted to a glanceable strip and the analyst's autonomous work demoted to a collapsed log. Ramp contributes the gate, Linear the concentration and snooze, Vanta the row-borne urgency, Karbon the self-promoting week, Stripe the single event truth. No researched product combines a human-gate queue with a deterministic provenance chain and an agent whose completed work is deliberately quiet — that combination is Covenant's own, and it is what makes Home a borrower-side lender-reporting floor rather than a generic work dashboard.

## 15. Domain references

Terminology and workflow semantics only: Finley (credit-facility deliverable tracking — the deliverable-register framing and pre-violation early warnings that shape the findings section's domain language) and LoanBoss (multifamily critical-date taxonomy: covenant test dates, extension notice windows, maturity events — informing which deadline classes the mini-agenda may eventually carry) (R5 research: Finley, LoanBoss). Domain authority does not equal visual authority: neither product's rendering governs a pixel here, and covenant semantics — what is covenanted, what is monitored, what a verdict means — come from the loan documents and Terry's rulings (05 §1), never from any referenced product.

## 16. Accessibility, performance, and safety

- WCAG contrast and focus: all ink/ground pairs from the ten-rung ladder meet AA at their assigned roles; severity chips always carry text labels (verdict words print — never color alone); visible accent focus ring on every interactive element; skip-link to YOUR MOVE.
- Keyboard completeness: every action reachable without pointer (§12); J/K traversal announces group context.
- Screen-reader semantics: your-move groups are labeled lists; the count badge is `aria-live="polite"` (count changes announce); quiet activity uses the `feed` role behind an explicit disclosure.
- Table virtualization/large data: the your-move slice is bounded (3/group); quiet activity virtualizes past 50 events; the query is bounded by book size (2–50 loans).
- Loading and latency feedback: skeletons hold layout; counts render only from the resolved query — no optimistic numbers, no fixture strings (04 §2.5).
- Destructive action confirmation: no destructive acts exist on Home; finding disposition uses a typed confirm popover.
- Certify and external-send safety: not performable here — Home links into the certificate/send ceremonies and never hosts them; typed acts stay on their owning surfaces with their legal-weight modals.
- Source immutability: Home reads projections only; its sole mutation paths are finding disposition (through the findings service) and its own presentation preferences.
- Auditability: every finding disposition and escalation render is backed by ActivityEvent rows; the badge's value at any moment is reproducible from persisted state (deterministic query — an auditor can replay it).

## 17. Acceptance tests and fixtures

Fixtures: the Bexley canon loan (occupancy 268/301 = 89.04% shortfall vs monitored 90% floor; UPB $15,232,500) and the Calloway Park FYE-2018 evidence spine (due-rules Q+45d / FYE+120d per §8.02(b); T-12 NOI $1,218,877; tie-out Net Income $460,159 = T-12 Net Income). Seed: a 7-loan book with periods staged across all five statuses.

1. One-number law. Seed state producing 7 your-move items → assert rail badge = YOUR MOVE header count = Inbox register count = 7, all reading `yourMove()`. Certify one period via the certificate route → assert all three decrement atomically to 6 (07 §2; 08 §10 test 6).
2. U3-F7 regression. Mark an item "seen" in Inbox → assert no count anywhere changes; unread/seen is provably absent from the badge query (C-8).
3. U3-F2 regression. Write one ActivityEvent ("filed T-12 to Calloway Park 2018-FY") → assert Home quiet activity and Loan Detail activity render the identical event (same `eventId`); static check: feed components accept only store projections — no local feed arrays compile.
4. Readiness gating. Bexley 2026-Q2 with incomplete checklist → assert NO certify-ready row; the period shows in the strip as "open". Exhaust the chase ladder inside deadline−N → assert exactly one Deadline-escalation row appears (Ramp-gating per R5).
5. Slice labeling. 5 extraction exceptions on one loan → group header "Exceptions — 5"; slice shows 3 rows + "all 5 in Inbox →" (no silent truncation).
6. Done-by-action only. Assert the Home DOM contains no dismiss/done/clear affordance on any your-move row; no client-side row-removal API exists (mutation test) — rows leave only via owning-surface acts.
7. Finding card. Seed the canon shortfall finding → card renders "shortfall" (assert the string "breach" absent), headroom sentence values equal engine TestResult output to the cent, evidence click lights the exact occupancy source row and it stays lit (U1-F1 fix verified on this renderer); Acknowledge writes a findings-service disposition + ActivityEvent.
8. Void-on-change. Replace a source document feeding a certified package field → strip decrements "certified"; your-move gains "re-review: certification voided" naming the change; assert no silent re-certify path.
9. Viewport fixtures. 1440/1728/2048/1280 screenshots → region topology per §10; at 1279px the strip renders its labeled summary line (no squeezed layout).
10. Keyboard fixture. Full J/K/Enter traversal with rail collapsed; `G H`/`G I` chords work with focus inside the list; focus restored on back-navigation.
11. Accessibility. axe-clean; badge announces via aria-live during test 1's mutation; verdict chips pass color-independence.
12. PMC scoping. Seed a PMC org with two Clients → a preparer scoped to Client A sees only Client A's items in every section; the badge equals Client-A's query result; no Client column renders in a single-owner org (02 §1).
13. Deadline provenance. The mini-agenda row for Calloway Park's annual statement shows due date = FYE+120d materialization; hover renders the due-rule cite "FYE+120d per §8.02(b)(2)" from Calendar's tooltip component (evidence: SLOT-3 §8.02(b)(2)); clicking lands on the period it belongs to.
14. Role affordance. As a preparer without certify rights, the Certify-ready row renders "awaiting {owner-name}" with a nudge affordance and no enabled act; the count is unchanged (one org-level number).
15. Benchmark challenger review. A reviewer walks Ramp's gating and Linear's snooze docs against the shipped surface and files any divergence from §14's take/adapt/reject columns as defects.

## 18. Build plan

- Dependencies: persisted period ledger + orchestration spine states (gap 5); the your-move query (`src/lib/covenant/your-move.ts` — new engine function, test-first); ActivityEvent table + append-only writer (migration; persistence gap); findings service (agent brief D2); deadline materialization (F5, Calendar brief); notification service for badge transport (07 A7). Tenancy: org scoping as proven on the send route; Client scoping for PMC mode.
- Foundation work: define the your-move item shape and query semantics as a tested library function BEFORE any UI (the badge law is a data contract, not a component); enumerate ActivityEvent write sites across engine/agent code paths (write coverage is a deliverable).
- Components to build first: `YourMoveRow` + `YourMoveGroupHeader` (shared with Inbox — build once in `your-move/`), `CountBadge` adoption (the ruled shared atom), `BookCycleStrip`, `QuietActivityFeed`.
- Vertical slice (the send-vertical pattern): `/home` route mounting the shell; ONE loan (Bexley) with a persisted certified period → Home renders exactly one real Send-ready row reading real gate state; clicking lands on the certificate route's send panel; completing the REAL send (`POST /api/covenant/send`) removes the row and writes the seal ActivityEvent that quiet activity then renders. One route, one loan+period, engine data end to end.
- Migration from fixture data: the current Home view's fixture feed and your-move arrays (projections of `book.ts`) are deleted, not adapted — no fixture number may survive behind a computed section (04 §2.5); sections whose stores are unbuilt render honest "not yet wired" blocks behind the flag, never fixtures.
- Rollout/feature flag: `covenant-home-live`; the `/review-room` Home view swaps to the same components once green.
- Proof artifacts required: recorded badge-equality run (test 1); U3-F2/U3-F7 regression tests green in CI; 4-viewport screenshot set; ActivityEvent write-coverage report (every autonomous code path names its event).
- Final gate: `ADJUST` confirmed — the built fixture's section instinct survives; the data plane beneath it is replaced wholesale. PASS requires tests 1–8 green against persisted state with zero fixture reads.
