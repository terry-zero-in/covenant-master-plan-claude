# HOME / OVERVIEW

## 1. Identity and verdict

- Product layer: portfolio floor (the landing floor of the book).
- Route/address in the basis-v2 repository: today the Home view inside `/review-room`'s view-switching shell (FIXTURE, inventory B1 — 07 §1); target deep route `/home` in the `(covenant)` route group, mounting `CovenantShell` with initial view Home (08 §1). Components land in `src/components/covenant/home/`; the your-move query in `src/lib/covenant/your-move.ts`; the activity store in `src/lib/covenant/activity.ts`.
- Current build state: `FIXTURE` — the built Home renders an activity feed and your-move items from demo strings; one feed row contradicts the canonical ledger (finding U3-F2), possible only because feeds are separate fixture arrays (snapshot §3; 07 §2).
- Existing-surface verdict: `ADJUST` — the layout instinct (concentrate the borrower's next acts) is right; the machinery (one computed query, one event store) is absent.
- Research tier: B.
- Primary users/roles: owner (certifies/sends), PMC preparer (prepares, sees the same book scoped to their clients), reviewer (read-only projection).
- Frequency and session duration: the most-opened surface — daily glances of 10–60 seconds mid-cycle; several visits per day in deadline week. Home is a dispatch floor, not a work floor: sessions end by leaving it.
- Error cost: indirect but real — if Home under-reports or mis-groups your-move, a period misses its deadline (a compliance failure, 03 §5) or the borrower certifies later than the deadline margin allows. Home never renders a shipping number itself, so the terminal error (wrong number certified and sent) cannot originate here; the risk is omission and contradiction, which is exactly what the single-query/single-store repair removes.
- Success criterion: within five seconds of landing, the borrower knows (a) how many decisions block the book right now, (b) which act each one is, and (c) where the cycle stands — and every count on screen agrees with every other surface because they are the same query.

## 2. User job and decisions

- Primary job-to-be-done: "Tell me what the book needs from me, in the order it needs it, and prove the rest is handled."
- Decisions made here: which your-move item to take next (dispatch decision); whether a proactive finding is acknowledged/dispositioned (a typed act on the Finding object, whose disposition affordance renders here — see §3); nothing else. Every substantive act (certify, send, file, confirm, disposition a tie-out, approve a draft) happens on its owning surface via deep link.
- Questions the surface must answer in scan order: 1) How many moves are mine, grouped by act? 2) Which are deadline-pressed? 3) Where does the cycle stand (open → sent counts)? 4) What is due in the next 7 days? 5) What has the analyst noticed early (findings)? 6) What did the analyst do while I was away (collapsed)?
- What the user should not have to decide here: whether a count is trustworthy (one query, one store); whether an item is actually actionable (readiness gating admits only actionable items — R5 research: Ramp); which surface owns an act (every row deep-links); what to do about blocked-on-inputs work (that lives in Inbox's waiting-on-inputs lane and the agent's chase machinery; Home shows only escalations).
- Entry paths: sign-in default landing; rail item Home; `G H` chord; ⌘K "Home"; the shell logo.
- Exit paths: every your-move row → its owning surface with loan+period in the URL (07 §3 entry/exit spine); book-strip segment → `/loans` filtered to that period status; deadline row → `/calendar` (or the period header); finding card → Loan Detail / Review evidence; quiet-activity row → the owning object; "all n in Inbox →" → `/inbox`.
- Completion/advancement conditions: Home itself advances nothing. A row leaves the your-move section only when the owning surface's real act completes (done-by-action — no mark-as-done exists anywhere on this surface). The empty your-move state is the success state.

## 3. Object and ownership model

| Object/action/status | Owned here? | Summary/link elsewhere | Address/identity | Notes |
|---|---:|---|---|---|
| Your-move query result (items + count) | No — engine-owned (`your-move.ts`, definition 03 §4) | Home renders the grouped top slice; Inbox renders the full register; rail badge renders the count | item = {class, loanId, period, objectRef, blockedSince, deadlineRef} | ONE computed query; every appearance is the same result (07 §2 duplication fix) |
| ActivityEvent (quiet log) | No — cross-cutting append-only store (02 §2) | Home renders a collapsed filtered projection; Loan Detail and period surfaces render their filters of the same store | `eventId`, actor, evidence refs, loan/period | U3-F2 becomes structurally impossible: there is no second feed to contradict |
| Finding (proactive watch) | No — agent-layer findings service owns state (02 §2) | Home is a ruled render+disposition point ("Home surfaces your-move" — 02 §2); Loan Detail shows per-loan; a blocking finding becomes a review stop | `findingId`, trigger ref, headroom evidence, drafted narrative ref | Acknowledge/disposition invoked from the card writes through the findings service — it is the owning real act, not a local mark-as-done |
| Period status counts (book strip) | No — orchestration spine owns period status (02 §3) | Home renders computed counts; `/loans` renders the rows | per-org aggregate over ReportingPeriods | No surface mutates status directly |
| Deadlines (7-day mini-agenda) | No — Calendar/Obligations owns due dates + escalation (02 §3) | Home shows the next-7-days slice with links | Deadline rows keyed to (loanId, period, requirement) | Due-rule provenance stays on Calendar; Home shows date + title only |
| Section layout/collapse preferences | Yes | — | per-user, per-org | The only state Home owns: presentation preferences (quiet-activity collapsed state, group collapse) |

No-double-homing boundary: Home owns no domain object. It is a projection floor over four owned stores (your-move query, period ledger, deadline engine, ActivityEvent store) plus the findings service. Intake owns arrival decisions (Home's arrival-decision rows deep-link to `/intake` selection); Inbox owns item-level presentation states (new/seen/snoozed) — Home does not render read/unread affordances at all (C-8).

## 4. Data and semantic model

- Source facts: none rendered directly (Home never shows a source cell; evidence opens from findings via the lit-row chain).
- Extracted values awaiting confirmation: appear only as counted your-move items ("Exceptions — extraction: n"), never as values.
- Confirmed values: not rendered; the book strip and counts are aggregates.
- Deterministic outputs: the your-move query (03 §4 — items blocked on a human decision: periods awaiting certify, awaiting send, unrecognized arrivals, extraction/normalization exceptions, failed tie-outs, findings awaiting disposition, drafts awaiting approval, deadline escalations; minus actively-snoozed items — snooze filtering executes inside the query so every render stays identical); period-status aggregates; deadline materializations (03 §5); finding evidence (signed headroom from TestResult rows).
- Agent proposals/drafts: surfaced as counts + links (drafts awaiting approval; drafted mitigant narrative linked from a finding card). Draft text never renders on Home beyond its title line.
- Human decisions: dispatch (navigation), finding acknowledge/disposition (typed act via the findings service).
- Certified values: never on Home; "certified n" in the book strip is a status count, not a value.
- Versions/periods/packages: every row carries loan+period identity chips; the book strip is grained per ReportingPeriod.
- Evidence/provenance: finding cards carry lit-row refs into their computed evidence (headroom figures open Evidence beside the claim per the frame contract, 08 §7); quiet-activity rows carry evidence refs per ActivityEvent grain.
- Permissions/read-only projections: one org-level query — the badge renders the same number for every member (the law's strongest reading; 08 §10 test 6). Role shapes affordances, not counts: a preparer sees certify-ready rows labeled "awaiting {owner-name}" with a nudge affordance instead of an enabled act; a reviewer sees all sections read-only with disposition controls absent. PMC mode scopes the whole floor per Client (02 §1).

Field grain: organization (the floor's scope) → client (PMC filter) → loan → reporting period (row identity) → requirement/test/finding/draft (item objectRef). Package, certification record, and send record appear only as status words in row titles ("Q2 package — certify-ready").

## 5. State machine and exceptions

Home is a projection; its states are render states of the queries beneath it.

| State | Render | Trigger/actor | Exit |
|---|---|---|---|
| empty (no your-move) | "Nothing needs you right now." + book strip + next deadlines; the success state, styled calm, never congratulatory confetti | query returns 0 | new item enters the query |
| awaiting-documents (holding) | NOT a your-move row (readiness gating). Appears only as: book strip "open n", and — if escalation policy fires — a Deadline-escalation row ("Bexley Q2: checklist incomplete, due in 4d, chases exhausted") | escalation ladder (03 §5; 04 §1 Holding lane gate) | checklist completes or human waives via Intake |
| loading/processing | skeleton rows per section with stable heights; counts render only when computed (no fixture placeholder numbers — 04 §2.5) | route mount | query resolves |
| partial/incomplete | if any sub-query fails, that section renders an honest error block naming the failed store; other sections render; the rail badge shows "—" rather than a wrong number | store error | retry |
| extracted-unconfirmed / low-confidence / conflict | one your-move class: Exceptions (extraction) rows with counts per loan+period | extraction pipeline | confirm/correct on the owning surface |
| stale/superseded | a stale-flagged confirmation raises an Exceptions row ("re-confirm 3 values — source replaced"); the affected period's book-strip status does not advance | engine stale flag (03 §2) | re-confirmation |
| failed tie-out | Exceptions (tie-out) row → Review Room stop | tie-out engine | disposition in Review |
| watch/shortfall/breach | watch = informational, appears only inside finding cards (headroom phrasing); shortfall/breach findings awaiting disposition are your-move rows. Vocabulary law enforced structurally: verdict chips render from TestResult basis — a monitored miss can only ever print "shortfall" (canon: Bexley 268/301 = 89.04% vs monitored 90% floor) | TestResult verdicts | disposition |
| permission denied | a row whose act the viewer lacks renders with the act-holder's name, never disappears (counts stay one number) | role check | role change or the right holder acts |
| read-only (reviewer) | full floor visible; zero action affordances; banner "read-only" | role | — |
| blocked/gated | fail-closed engine blocks (unknown form, missing definition) surface as Exceptions rows naming what is missing (04 §2.5) | engine | authoring/confirmation |
| certified (void-on-change) | a void event removes the period from "certified n", returns it to in-review in the strip, and raises a your-move row "re-review: certification voided — {what changed}" (03 §2) | void-on-change engine | re-certification after review |
| sent/sealed | leaves your-move; increments "sent n"; the seal event appears in quiet activity | send gate | — (seal-not-wipe; row links to the sealed record in Reports) |
| recovery/undo | quiet-activity rows for auto-filed arrivals carry Intake's undo-window re-route link (undo executes on Intake, not here). Certify/send have no undo by design — void and fresh acts only | — | — |

## 6. Engine / Agent / Human / Gate / Quiet Log

| Function | ENGINE (deterministic) | AGENT (prepares/proposes) | HUMAN (typed decisions) | GATE (interrupts) | QUIET LOG |
|---|---|---|---|---|---|
| Your-move section | Computes the one query (03 §4), incl. readiness gating and snooze filtering; orders by deadline proximity then age | — (agent work never adds itself to your-move; completed agent work is quiet log — 03 §4) | Dispatch by navigation | Deadline escalation rows are the gate's landing place | Row entry/exit events logged |
| Book strip + mini-agenda | Period-status aggregates; due-rule materialization slice (03 §5) | — | — | — | — |
| Findings | Trend series, headroom time-series, threshold-crossing detection (04 §1 proactive watch) | Frames findings ("you are $240K of NOI from a DSCR shortfall" — canon phrasing, 04 §1); drafts mitigant narratives; ranks by consequence | Acknowledge/disposition (typed act via findings service) | A finding blocking an imminent certify renders gate styling and also appears as a review stop | Findings raised + evidence logged |
| Quiet activity | Store integrity (append-only, identity-stamped — 04 §2.3) | Writes its completed autonomous work here | Reads; follows undo links | — | This IS the quiet log's Home projection |

Lane invariants checked: no shipping number renders outside engine output (Home renders counts and engine-computed headroom only); no push from this surface except deadline escalation per policy (04 §2.3); agent presence is contextual (findings beside their evidence links; no chat box on Home — the Ask entry stays in the canvas panel per 08 §6).

## 7. Information hierarchy

1. Frame header (shell, 48px): book-scoped breadcrumb `Covenant / Home` — no loan/period crumbs at this altitude (08 §2).
2. Decision/status summary: YOUR MOVE header with the count ("7 moves") — the same number as the rail badge, by construction.
3. Primary work region: the grouped your-move slice (section §8 region 1).
4. Secondary context: THE BOOK THIS CYCLE strip + next-deadlines mini-agenda.
5. Evidence/proof: FINDINGS cards (each carrying lit-row refs into its computed evidence).
6. Actions: per-row deep links; finding acknowledge/disposition; nothing global.
7. Activity/history: QUIET ACTIVITY, collapsed, last.

Absent by design (DIRECTIONS): decorative charts; KPI tiles without decisions attached; news-feed noise; any unread/read affordance; any mark-as-done control; any chart that would not out-encode its adjacent text (the book strip is counts-as-instruments, deliberately not a chart).

## 8. Page anatomy and regions

Single work window (ruled pane model; Home never splits). Content column max-width 1120px, centered; quiet activity spans the column.

- Region 1 — YOUR MOVE (persistent, top). Purpose: dispatch. Content: group headers in fixed act order — Certify-ready · Send-ready · Arrival decisions · Exceptions (extraction, normalization, tie-out) · Findings awaiting disposition · Drafts awaiting approval · Deadline escalations (DIRECTIONS) — each header with its full count; up to 3 rows per group (the top slice, ordered by deadline proximity then age), then "all {n} in Inbox →". Empty groups do not render. Interaction: J/K row traversal, Enter opens the owning surface. Min height: one row; no collapse (the section is the page's reason to exist). Why inline list, not pane: dispatch needs no co-visibility — each row's context lives on its owning surface.
- Region 2 — THE BOOK THIS CYCLE (persistent). Two sub-blocks side by side ≥1440px, stacked below. (a) Period-status strip: `open n · in-review n · ready n · certified n · sent n` — five segments, uniform height, each an instrument (click → `/loans` filtered); counts in Geist Mono. (b) Next-deadlines mini-agenda: 7-day window, max 5 rows (deliverable title, loan, due date with due-rule tooltip), footer "full agenda → Calendar". Persistence: pinned; collapses to a single summary line on narrow viewports (labeled, never silently).
- Region 3 — FINDINGS (persistent when non-empty). Proactive watch cards, ranked by consequence (agent lane). Card anatomy in §11. Grid: 1-up at 1440, 2-up ≥1728, 3-up at 2048. Absent entirely when no findings (no empty-state card).
- Region 4 — QUIET ACTIVITY (persistent, collapsed by default). Header: "While you were away — {n} actions" + filter chips (actor, type, loan). Expanded: uniform event rows from the ActivityEvent store, newest first, virtualized past 50. Framing: "what the analyst did while you were away" (DIRECTIONS). Why collapsed: completed autonomous work is discoverable, never pushed (04 §2.3).
- Canvas panel (floating, summonable, separate slot): Ask; capability prompts rotate, engine-completable only. Never load-bearing for Home decisions.
- Modals: none on Home (no typed act with legal weight initiates here; finding disposition uses an inline typed confirm popover, not a modal — it is not a legal-weight act).

## 9. Co-visibility matrix

| Datum/surface A | Datum/surface B | Must be simultaneous? | User decision enabled | Default topology | Fallback |
|---|---|---:|---|---|---|
| Your-move row | Owning surface's full context | No | Dispatch only | Deep link, navigate away | — |
| Your-move group counts | Rail badge count | Yes (same viewport) | Trust ("one number") | Both render the one query | Badge shows "—" on query failure, never a stale number |
| Finding card claim | Its computed evidence (headroom inputs) | On demand | Judge the finding before disposition | Lit-row evidence summons beside the card (canvas-adjacent overlay from the right) | Below 1280px: evidence opens as overlay sheet |
| Book strip | Deadline mini-agenda | No (adjacent is enough) | Cycle awareness | Side-by-side ≥1440px | Stacked |
| Quiet-activity row | Undo affordance (auto-file re-route) | No | Follow link to Intake | Inline link | — |

No pane is created for information that navigation serves; Home stays a single window by rule.

## 10. Layouts and viewport behavior

- 1440px: rail expanded (240px); content column 1120px centered; Region 2 sub-blocks side by side (strip ~60% / agenda ~40%); findings 1-up.
- 1728px: identical topology; findings 2-up; more whitespace, rhythm unchanged (no new regions appear with width).
- 2048px: findings 3-up; content column stays 1120px for regions 1–2 (line-length discipline); findings grid may extend to 1360px.
- Narrow/compact (<1280px): rail auto-collapses to 48px icon rail (08 §9); regions stack single-column; Region 2 collapses to one labeled summary line ("Cycle: 2 open · 1 in-review · 1 ready · 1 certified · 2 sent — expand"); quiet activity stays collapsed.
- Focus behavior: J/K moves a single focus ring through your-move rows across groups; group headers announce on entry (screen reader).
- Compare behavior: none on Home (no compare mode; comparisons belong to spine surfaces).
- Proof/source behavior: finding evidence opens beside the card (overlay pane from the right, never covering the card clicked — lit-row law); Esc dismisses the pane before it ever navigates.
- Minimum viable dimensions: 1152×720 frame minimum (08 §9); Home remains fully usable at minimum (registers-class surface).
- No silent compression: sub-minimum regions become labeled collapsed lines with counts, never squeezed layouts.

## 11. Components and exact anatomy

- `CovenantShell` — REUSE (`src/components/covenant/CovenantShell.tsx`): rail, header, canvas panel; `/home` mounts it with initial view Home.
- `CountBadge` — REUSE (the ruled single shared component, snapshot §4: 37 hand-rolled atoms consolidate to one): renders the your-move count on the rail (Inbox item) and the YOUR MOVE header. One data source: the query.
- `YourMoveRow` — NEW (shared with Inbox; built once in `src/components/covenant/your-move/`): act-class icon (Lucide, per class), title ("Certify Bexley — 2026-Q2"), loan+period identity chips, deadline chip (relative, severity-tinted only when escalated), blocked-since age, chevron. On Home: no inline snooze/delegate (those are Inbox affordances); Enter/click navigates. Uniform row height 40px.
- `YourMoveGroupHeader` — NEW: act-class label + full-count `CountBadge` + "all {n} in Inbox →" link when the slice truncates (the slice is labeled — no U3-C1-class silent truncation).
- `BookCycleStrip` — NEW: five status segments (label + Geist Mono count), open-not-boxed with hairline separators; each segment a link. Not a chart by design.
- `DeadlineMiniAgenda` — NEW: uniform rows (deliverable · loan · due date); due-rule provenance on hover ("Q+45d per §8.02(b)(1)" — evidence-class cite rendered by Calendar's tooltip component, reused).
- `FindingCard` — NEW: severity-neutral card; title from trigger; one headroom sentence with engine values interpolated by reference (canon example: "You are $240K of NOI from a DSCR shortfall" — values re-render from the engine at view time, 04 §2.1); evidence link (opens lit-row chain); "drafted narrative →" link into Composer/Review; footer actions Acknowledge · Disposition (typed confirm popover naming the finding, the evidence, and the consequence — gate law anatomy).
- `QuietActivityFeed` — NEW over the ActivityEvent store: collapsed header with count; filter chips (actor: engine/agent/human; type; loan); `ActivityEventRow` (actor glyph, sentence, loan/period chips, timestamp, evidence/undo links). Virtualized.
- Evidence panel — REUSE (the review-room Evidence panel component) summoned for finding evidence, with the U1-F1 wrong-row fix as a stated dependency (cross-cutting brief).
- `CommandPalette` — REUSE: `G H` lands here; palette rows for "Home" and each your-move item class.
- Empty/error objects — NEW `SectionErrorBlock` (names the failed store, offers retry) and the calm all-clear state.

## 12. Interaction specification

- Selection: single-row focus (no multi-select on Home; bulk acts live on owning surfaces).
- Hover: row hover raises background one gray rung; deadline chips reveal due-rule tooltip; finding sentences reveal per-value provenance affordance.
- Focus: visible 2px accent (#7189FF) focus ring; focus order = DOM order = scan order (§7).
- Keyboard: `J/K` next/prev your-move row (crosses groups); `Enter` open owning surface; `G I` Inbox; `G L` Loans; `G C` Calendar; `⌘K` palette; `A` on a focused finding card = Acknowledge (opens typed confirm popover); `E` on a finding = open evidence; `.` toggles quiet activity. No snooze/delegate keys here (Inbox owns item-level states).
- Editing and validation: none (no editable values on Home).
- Bulk action: none by design.
- Undo/recovery: navigation is the only "action"; finding disposition popover has an explicit cancel; auto-file undo links route to Intake.
- Sorting/filtering: your-move order is fixed (deadline proximity, then age) — no user sort, so the surface always agrees with the badge's implied urgency; quiet activity is filterable (chips) but not reorderable.
- Drill-down and return path: every deep link carries loan+period in the URL; browser back returns to `/home` with scroll and focus restored.
- Source-linked selection: clicking any figure inside a finding's evidence lights its exact source line in the Evidence pane and it stays lit (frame contract 08 §7).
- Save/persistence: collapse states and activity filters persist per user; nothing else is savable here (no saved views on Home — it is not a register).
- Collaboration/commenting: none on Home; annotation lives in Review.

## 13. Visual craft direction

- Typography: section headers 13px/600 uppercase-tracked on the gray ladder's secondary ink rung; row titles 14px/450; meta (chips, ages) 12px on tertiary ink. Findings' headroom sentence 15px — the one enlarged text on the page, because it carries the surface's only figures.
- Financial numbers: Geist Mono, `tabular-nums slashed-zero` (ruled), for every count and headroom figure; counts right-align within `CountBadge`.
- Spacing rhythm: 8px base grid; 24px between regions; 40px uniform row heights; group headers 32px.
- Density: calm-register density (this is a glance floor, not a work grid): more open ground than any other Covenant surface.
- Open ground vs earned boundaries: no boxed cards except `FindingCard` (which earns a hairline + 1-rung raised ground because it is a discrete disposable object); rows separate by hairline only; the book strip is open with hairline dividers between segments.
- Dividers/elevation: ten-rung gray ladder only (adopted 2026-08-07 — snapshot §4); the raised rung reserved for the summoned evidence pane and canvas panel.
- Semantic color: accent family #7189FF for interactive/focus/links (hover #8EA1FF, active #6078F4); verdict/severity chips consume the ruled Review-9 verdict tokens — severity-only color, nothing decorative; deadline chips tint only at escalation. Zero new values proposed.
- Certified-sheet treatment: none — the six certified-sheet hexes are the sheet's paper and never paint Home. "Certified n" in the strip is plain ink.
- Focus/selected/hover states: consume the existing Covenant control-layer tokens (Ruling-J measurement: the layer exists, zero consumers — Home becomes a consumer, never an author).
- Chart style: no charts (absent by design; the strip would not out-encode its own counts — chart doctrine applied by omission).
- Motion: rows enter with a 120ms fade/slide; count changes swap numerals without pulsing; `prefers-reduced-motion` disables both.
- Long-session ergonomics: not a long-session surface; the design optimizes re-visit scanning — stable region order, no layout shift between visits, skeletons hold heights.

## 14. Benchmark research and synthesis

| Product | Limited role | Official evidence | Exact pattern to take | Covenant adaptation | What to reject | Why this reference beats alternatives |
|---|---|---|---|---|---|---|
| Ramp | "Needs your attention" queue donor | https://support.ramp.com/hc/en-us/articles/4417421399699-Transaction-Reviews (R5 research) | One inbox across heterogeneous object types with a count badge; readiness gating — items enter only when actionable; blocked items live elsewhere with a Remind action | Your-move admits only ready items (period closed, inputs present, readiness true); blocked work stays in Inbox's waiting-on-inputs lane; Home shows only escalations | Approval-hierarchy routing (borrower teams are 1–5 people) | It is the only researched product that documents gating + the separate blocked surface as policy, which is exactly the C-8/U3-F7 repair shape |
| Linear | Concentration + snooze semantics donor | https://linear.app/docs/inbox (R5/R1 research) | One delta surface; snooze hides until a chosen time or new activity, whichever first | Snooze lives on Inbox but the query (shared with Home) applies it; auto-unsnooze on new activity resurfaces work without human vigilance | Unread/read as a count concept (C-8: unread is not your-move) | Linear's Inbox is the best-documented single-queue discipline; its snooze-until-activity is the only snooze that cannot hide work forever |
| Vanta | Deadline-in-the-row rollup donor | https://help.vanta.com/en/articles/11345374-the-tests-page ; https://help.vanta.com/en/articles/11345547-test-priorities-and-slas (R5 research) | Urgency as a property of the row (soft-warning vs overdue), not a position on a grid; different machinery for soft vs hard states | Deadline chips on your-move rows; escalation (gate) vs watch (informational) drive different render classes, mirroring Vanta's Failing-vs-Needs-Attention trigger split | SLA-workforce apparatus | Proves compliance deadlines need no calendar on a status floor |
| Karbon | Time-decayed agenda donor | https://help.karbonhq.com/en/articles/5724366-best-practices-for-managing-your-work-in-my-week (R5 research) | Auto-promoting time buckets (Later → Next Week → This Week) with no human re-filing | The 7-day mini-agenda is the "This Week" slice only; full buckets live on Calendar | The full My Week page on Home (duplication with Calendar) | Closest analog to recurring obligations promoting themselves by time trigger |
| Stripe | Event-log projection donor | https://docs.stripe.com/payments/payment-records ; https://docs.stripe.com/development/dashboard/events (R5 research) | Append-only event log from which state is derived, never overwritten | The ActivityEvent store; quiet activity and every other feed are filtered projections of it (the U3-F2 structural fix) | Stripe's filter surface area | The cleanest documented statement of events-as-truth, which is the exact contradiction-killer Home needs |

Synthesis: Home becomes original by inverting the dashboard genre — it renders zero KPIs and zero charts, and instead concentrates the borrower's constitutional role (review, certify, send — the only things the human must do, 04 trust hierarchy) into one gated queue, with the cycle's state and the analyst's autonomous work demoted to glanceable and collapsed strata. Ramp contributes the gate, Linear the concentration and snooze, Vanta the row-borne urgency, Karbon the self-promoting week, Stripe the single event truth. No researched product combines a human-gate queue with a deterministic-engine provenance chain and an agent whose completed work is deliberately quiet — that combination is Covenant's, and it is what makes Home a borrower-side lender-reporting floor rather than a generic work dashboard.

## 15. Domain references

Terminology and workflow semantics only: Finley (credit-facility deliverable tracking; deliverable-register framing and pre-violation early warnings — the findings section's domain shape) and LoanBoss (multifamily critical-date taxonomy: covenant test dates, extension notices, maturities — informing which deadline classes the mini-agenda may eventually carry) (R5 research: Finley, LoanBoss). Domain authority does not equal visual authority: neither product's rendering governs a pixel here, and covenant semantics — what is a covenant, what is monitored, what a verdict means — come from the loan documents and Terry's rulings (05 §1), never from any referenced product.

## 16. Accessibility, performance, and safety

- WCAG contrast: all ink/ground pairs from the ten-rung ladder meet AA at their assigned roles; severity chips carry text labels, never color alone (verdict words print).
- Focus: visible accent ring on every interactive element; skip-link to YOUR MOVE.
- Keyboard completeness: every action reachable without pointer (§12); J/K traversal announces group context.
- Screen-reader semantics: your-move groups are labeled lists; the count badge is `aria-live="polite"` (count changes announce); quiet activity is a `feed` role, collapsed by default with an explicit disclosure.
- Table virtualization/large data: your-move slice is bounded (3/group); quiet activity virtualizes past 50 events; the query itself is bounded by book size (2–50 loans).
- Loading/latency: skeletons hold layout; counts never render from anything but the resolved query — no optimistic numbers, no fixture strings (04 §2.5).
- Destructive confirmation: none needed (no destructive acts on Home); finding disposition uses a typed confirm popover.
- Certify/send safety: not performable here — Home links into the certificate/send ceremonies; it never hosts them (typed acts stay on their owning surfaces with their modals).
- Source immutability: Home reads projections only; it holds no mutation path to any store except finding disposition (via the findings service) and its own presentation preferences.
- Auditability: dispatch is not logged (navigation), but every finding disposition and every escalation render is backed by ActivityEvent rows; the badge's value at any moment is reproducible from persisted state (deterministic query).

## 17. Acceptance tests and fixtures

Fixtures: the Bexley canon loan (occupancy 268/301 = 89.04% shortfall vs monitored 90% floor; $15,232,500 UPB) and the Calloway Park FYE-2018 evidence spine (Q+45d / FYE+120d due-rules per §8.02(b); T-12 NOI $1,218,877; tie-outs Net Income $460,159 = T-12 Net Income). Seed: a 7-loan book with periods staged across all five statuses.

1. One-number law: with a seeded state producing 7 your-move items, assert rail badge = YOUR MOVE header count = Inbox register count = 7, all reading `yourMove()`; mutate state (certify one period via the certificate route) and assert all three decrement atomically to 6 (07 §2; 08 §10 test 6).
2. U3-F7 regression: mark an item "seen" (Inbox state); assert no count anywhere changes — unread/seen is provably absent from the badge query (C-8).
3. U3-F2 regression: write one ActivityEvent ("filed T-12 to Calloway Park 2018-FY"); assert Home quiet activity and Loan Detail activity render the identical event (same eventId), and that no code path exists to render a feed row without an ActivityEvent (static check: feed components accept only store projections).
4. Readiness gating: Bexley 2026-Q2 with incomplete checklist → assert NO certify-ready row; period shows in book strip "open"; exhaust the chase ladder within deadline−N → assert exactly one Deadline-escalation row appears (Ramp-gating per R5).
5. Grouping and slice labeling: 5 extraction exceptions on one loan → group header shows "Exceptions — 5", slice shows 3 rows + "all 5 in Inbox →" (no silent truncation).
6. Done-by-action only: assert the Home DOM contains no dismiss/done/clear affordance on any your-move row; the only row-removal paths are owning-surface acts (mutation test: attempt a client-side row removal API — none exists).
7. Finding card: seed the canon shortfall finding → card renders "shortfall" (never "breach" — assert string), headroom sentence values match engine TestResult output to the cent, evidence click lights the exact occupancy source row and it stays lit (U1-F1 fix verified on this renderer); Acknowledge writes a findings-service disposition + ActivityEvent.
8. Void-on-change: void a certified period (replace a source doc feeding a package field) → book strip decrements "certified", your-move gains "re-review: certification voided" naming the change; no silent re-certify path exists.
9. Viewport fixtures: 1440/1728/2048/1280 screenshots — region topology per §10; at 1279px assert the strip collapses to its labeled summary line (no squeezed layout).
10. Keyboard fixture: J/K/Enter full traversal with rail collapsed; `G H`/`G I` chords; focus restored on back-navigation.
11. Accessibility: axe-clean; badge announces via aria-live on the certify mutation from test 1; verdict chips pass color-independence (labels present).
12. Benchmark challenger review: a reviewer walks Ramp's gating and Linear's snooze docs against the shipped surface and files any divergence from §14's take/adapt/reject as defects.

## 18. Build plan

- Dependencies: persisted period ledger + orchestration spine states (gap 5); the your-move query (`src/lib/covenant/your-move.ts` — new engine function, test-first); the ActivityEvent table + append-only writer (migration; gap 3/persistence); findings service (agent brief D2); deadline materialization (F5, Calendar brief); notification service for the badge transport (07 A7). Tenancy: org scoping as on the send route; Client scoping for PMC mode.
- Foundation work: define the your-move item shape and query semantics as a tested library function BEFORE any UI (the badge law is a data contract); define ActivityEvent write sites across engine/agent code paths.
- Components first: `YourMoveRow` + `YourMoveGroupHeader` (shared with Inbox — build in `your-move/` once), `CountBadge` adoption (the ruled shared atom), `BookCycleStrip`, `QuietActivityFeed`.
- Vertical slice (send-vertical pattern): `/home` route mounting the shell; ONE loan (Bexley) with a persisted certified period → Home renders exactly one real Send-ready row reading the real gate state; clicking lands on the certificate route's send panel; completing the REAL send (`POST /api/covenant/send`) removes the row and writes the seal ActivityEvent that quiet activity renders. One route, one loan+period, engine data end to end.
- Migration from fixture data: the current Home view's fixture feed and your-move arrays in `book.ts` projections are deleted, not adapted — no fixture number may survive behind a computed section (04 §2.5); sections whose stores are unbuilt render honest "not yet wired" blocks behind the flag rather than fixtures.
- Rollout/feature flag: `covenant-home-live`; `/review-room` Home view swaps to the same components once green.
- Proof artifacts: badge-equality test run (test 1) recorded; U3-F2/U3-F7 regression tests green in CI; 4-viewport screenshot set; quiet-log write coverage report (every autonomous code path names its ActivityEvent).
- Final gate: `ADJUST` confirmed — the section order and dispatch concept of the built fixture survive; the data plane beneath them is replaced wholesale. PASS requires tests 1–8 green against persisted state with zero fixture reads.
