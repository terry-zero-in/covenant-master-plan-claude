# INBOX

## 1. Identity and verdict

- Product layer: portfolio floor (the book's full decision register).
- Route/address in the basis-v2 repository: today the Inbox view inside `/review-room`'s shell (FIXTURE, inventory B2 — 07 §1); target deep route `/inbox` in the `(covenant)` route group, mounting `CovenantShell` with initial view Inbox (08 §1). Components land in `src/components/covenant/inbox/`; shared row components in `src/components/covenant/your-move/`; item-state store in `src/lib/covenant/inbox-item-state.ts`; the query itself stays in `src/lib/covenant/your-move.ts` (one query — never a second Inbox variant).
- Current build state: `FIXTURE` — two named defects: the badge counts unread while labeled a review count (U3-F7), and the awaiting-documents lane renders 3 of 7 outstanding documents with no indication of truncation (U3-C1) (snapshot §3).
- Existing-surface verdict: `ADJUST` — the register-plus-lane concept stands; the semantics are replaced: the badge becomes the your-move count (C-8 fix), the register becomes the full computed query, and truncation becomes structurally impossible (the register is complete or says exactly what is filtered).
- Research tier: B.
- Primary users/roles: owner; PMC preparer (client-scoped); reviewer (read-only projection).
- Frequency and session duration: opened whenever the badge is non-zero — 1–5 minute working sessions of serial disposition; heaviest in weeks 2–3 and deadline week of each cycle (03 §4 rhythm).
- Error cost: an item hidden here is work that silently never happens — a missed certify, an unfiled arrival, an unanswered lender question; the downstream cost is a late or incomplete package (a compliance failure). The register therefore has no mechanism to hide work: no mark-as-done, no delete, snooze only until time-or-activity, and completeness is asserted in the footer.
- Success criterion: the register empties only through real acts on owning surfaces; at any moment the footer's arithmetic holds (visible = total − named filters − snoozed), and the badge equals the register equals Home — one number.

## 2. User job and decisions

- Primary job-to-be-done: "Work the queue: every decision the book is blocked on, one register, in order, with enough context beside each row to route myself correctly."
- Decisions made here:
  - open the owning surface for a row (dispatch — the primary act);
  - snooze-with-reason (defer until a chosen time or new activity, whichever first — Linear semantics, R5/R1 research);
  - delegate (assign the item to a named teammate);
  - nudge (on waiting-on-inputs rows: approve an agent-drafted off-cadence chase now);
  - nothing terminal: no disposition completes here. "Done" is only ever achieved by the owning surface's real act (done-by-action).
- Questions the surface must answer in scan order:
  1. How many items, and does that number match the badge I clicked?
  2. What class is each item, and which loan+period?
  3. Which are deadline-pressed?
  4. What context does this row need before I open it (preview)?
  5. What is the book waiting on from others (waiting-on-inputs lane)?
  6. What have I snoozed, and why — and when does it come back?
- What the user should not have to decide here: whether an item is actionable yet (readiness gating admits only actionable items; blocked work sits in the waiting-on-inputs lane — R5 research: Ramp); whether the list is complete (the footer asserts it); what "unread" means (nothing — seen-state is a dot, never a count, C-8); how to mark something finished (impossible by design).
- Entry paths: rail item Inbox with the `CountBadge`; `G I` chord; Home group headers ("all {n} in Inbox →"); ⌘K; deep links from escalation notifications.
- Exit paths: every row → its owning surface with loan+period in the URL (07 §3); arrival rows → `/intake` with the arrival selected (Intake owns the decision); waiting-on-inputs rows → `/intake` holding checklist; preview pane links → evidence, Loan Detail, Calendar.
- Completion/advancement conditions: a row leaves when the owning surface's act completes and the query recomputes; a waiting-on-inputs row leaves when the checklist item arrives, is waived, or its period closes; the empty register ("Nothing needs you") is the success state.

## 3. Object and ownership model

| Object/action/status | Owned here? | Summary/link elsewhere | Address/identity | Notes |
|---|---:|---|---|---|
| The your-move register (full rendering of the query) | Rendering yes; the query no — engine-owned (`your-move.ts`, 03 §4) | Home renders the grouped top slice; rail badge renders the count | YourMoveItem (shape in home-overview §4) | ONE query; Inbox is its complete, first-class rendering |
| Item-level presentation states: new / seen / snoozed / done-by-action | **Yes** (DIRECTIONS: item-level states live here) | Home renders none of them (C-8) | `(itemRef, state, snoozeUntil?, snoozeReason?, activityWatermark)` | Seen/new is per-user cosmetic; snooze is org-visible with reason and applies inside the shared query; done-by-action is a transition, not a stored flag |
| Delegation (assignee on an item) | Yes (the assignment act) | Owning surfaces show "assigned to {name}" on the blocked object | assignee on the item | Changes routing, never the count (org-level number) |
| Arrival decisions (file/replace/hold/dismiss) | No — Intake owns (02 §3) | Arrival rows here deep-link into `/intake` selection | arrivalId | Inbox never renders the decision inspector |
| Holding checklist items (arrived/missing/chased/waived) | No — Intake owns (02 §3) | The awaiting-documents lane renders checklist gaps across loans as links — summary, not ownership | (loanId, period, itemId) | Waive and cadence changes execute on Intake |
| Chase send (the nudge's effect) | No — agent executes on approved cadence (04 §1 Holding lane) | Nudge here = approving one off-cadence chase; the send itself is agent work, quiet-logged | chase ref | Mirrors Ramp's Remind (R5 research) |
| Draft contents (narratives, replies) | No — Composer / Correspondence own | Preview shows the draft's title + first line + link | draftId | Approval happens on the owning surface |
| ActivityEvent store | No — cross-cutting (02 §2) | Snooze/delegate/nudge acts write events; the preview shows the item's event trail | eventId | No local activity arrays (U3-F2-class fix) |

No-double-homing boundary: Inbox owns exactly one thing — item-level presentation state over the shared query (plus the delegate/snooze/nudge acts, each quiet-logged). Every domain object it shows is a summary with a link to its owner. Intake keeps arrival decisions and the holding checklist; Home keeps the grouped glance; Calendar keeps deadlines.

## 4. Data and semantic model

- Source facts: never rendered as values; the preview pane may render a document thumbnail via the Documents renderer (read-only link), never a re-filed copy.
- Extracted values awaiting confirmation: rows of class exception-extraction; the preview names the fields and confidence, values render as engine-proposed with their state labels (three-actor language: source/inferred/certified legible at point of use).
- Confirmed values: not rendered.
- Deterministic outputs: the your-move query (03 §4) with readiness gating and snooze filtering executed inside it; the waiting-on-inputs projection (holding checklist gaps across loans — engine-computed from ChecklistItem states, 02 §2); footer arithmetic (total, filtered, snoozed counts).
- The item-state overlay contract (owned here; the one place presentation state attaches to query items):

```text
InboxItemState {
  itemRef                         ← stable identity of the YourMoveItem's objectRef + class
  seenBy: userId[]                ← cosmetic; never a count input (C-8)
  snooze?: {
    until,                        ← chosen time T
    reason,                       ← required, org-visible
    activityWatermark             ← the underlying object's event cursor at snooze time;
  }                                  any newer event on the object re-enters the item (activity beats time)
  assignee?: userId               ← delegation; role-validated per item class
}
```
- Agent proposals/drafts: row titles + preview context (classification confidence beside an arrival row; the drafted chase text inside a nudge confirm; a draft narrative's title). The agent never adds items to your-move by finishing work — completed agent work is quiet log (03 §4).
- Human decisions: dispatch, snooze-with-reason, delegate, nudge-approve. All four are logged; none is terminal.
- Certified values: never here; certify/send rows carry status words only.
- Versions/periods/packages: identity chips on every row (loan + period); waiting-on-inputs rows carry the checklist item's doc-type and its requirement provenance ref.
- Evidence/provenance: preview pane renders the item's chain summary — for an exception row, the failing field and its source-region ref (lit-row link into the owning surface); for a finding row, the headroom evidence ref; for a waiting-on-inputs row, the chase history (last chase date, next scheduled — Intake data, linked).
- Permissions/read-only projections: one org-level count for everyone (the one-number law); a "Mine" filter narrows the view to items assigned to the viewer or unassigned-and-actionable-by-them — a filter, never a different number (the footer names it like any filter). Reviewers get the register read-only with all four acts absent. PMC mode scopes by Client.

Field grain: organization → client (PMC) → loan → reporting period → item objectRef (arrival / extracted value / normalized line exception / tie-out / finding / draft / deadline) → item-state overlay (per item, owned here).

## 5. State machine and exceptions

Item-level state machine (owned here; the underlying object's lifecycle lives on its owning surface):

```text
            enters query                     owning surface's real act completes
  (engine) ────────────▶ NEW ──viewed──▶ SEEN ────────────────────────▶ DONE-BY-ACTION
                          │                │                                (row resolves; count decrements
                          │   S (reason)   │  S (reason)                     everywhere atomically)
                          ▼                ▼
                        SNOOZED (until time T or new activity, whichever first — auto-returns)
```

- NEW → SEEN is per-user and cosmetic (a dot fades); it never affects any count (C-8).
- SNOOZED: org-visible, reason required, quiet-logged; excluded from the query (and therefore from every badge) until `T` elapses **or** new activity touches the underlying object — a lender email on a snoozed thread, a new arrival on a chased checklist item, a recomputed verdict — whichever comes first (Linear snooze semantics — R5/R1 research; Plain's auto-reopen reinforces the activity half — R1 research).
- DONE-BY-ACTION: not a stored flag — the item simply stops satisfying the query. The row animates out and appears under a collapsed "Resolved this session — {n}" group (session-scoped, informational, links to the acts' quiet-log events). There is no other exit: no done button, no dismiss, no delete, no archive.

| State (surface + rows) | Render | Notes |
|---|---|---|
| empty | "Nothing needs you." + the waiting-on-inputs lane still visible (the book may be waiting on others) + snoozed segment count | Success state |
| awaiting-documents (holding) | THE WAITING-ON-INPUTS LANE — a separate labeled strip, never mixed into your-move rows and never counted in the badge (readiness gating, R5: Ramp): rows = checklist gaps across loans (doc type · loan+period · missing/chased state · last chase · next scheduled · Nudge) | Escalation promotes a gap to a real your-move row (deadline-escalation class) when policy fires |
| loading/processing | skeleton rows, stable heights; badge renders only from the resolved query | No fixture numbers (04 §2.5) |
| partial/incomplete | a failed sub-store renders an honest error block naming it; footer says "waiting-on-inputs unavailable"; badge shows "—" rather than a wrong number | |
| extracted-unconfirmed / low confidence / conflict | exception-extraction rows; preview names fields, confidence, and reason | Resolve on owning surface |
| stale/superseded | re-confirm rows ("source replaced — 3 values affected") | |
| failed tie-out | exception-tieout rows → Review | |
| watch/shortfall/breach | finding-disposition rows carry the verdict chip from TestResult basis — monitored misses print "shortfall" only (canon: Bexley 268/301 = 89.04% vs monitored 90% floor); "breach" requires `basis=covenanted` | Vocabulary law structural |
| permission denied | rows whose act the viewer lacks render with the holder's name + nudge affordance; never hidden (count is one number) | |
| read-only | reviewer projection: register visible, acts absent, banner | |
| blocked/gated | fail-closed blocks render as exception rows naming what is missing | 04 §2.5 |
| certified (void-on-change) | a void raises a "re-review: certification voided — {what changed}" row; the voided period's send-ready row (if any) disappears in the same recompute | 03 §2 |
| sent/sealed | the send act resolves the row (done-by-action); seal event visible in the row's resolved-group link | Seal-not-wipe |
| recovery/undo | snooze is reversible (unsnooze affordance in the Snoozed segment); delegation is reversible (reassign); dispatch has browser-back; the four acts are all undoable — the acts they lead TO on owning surfaces carry their own rules (certify/send have no undo by design) | |

Lane rows mirror Intake's ChecklistItem lifecycle read-only (ownership stays with Intake — 02 §2):

| ChecklistItem state (Intake-owned) | Lane render here | Who resolves, where |
|---|---|---|
| expected (period opened, not yet due for chase) | row with "expected" chip, no chase data | agent matches arrivals (Intake) |
| missing (due, no arrival) | row with "missing" chip + Nudge | agent chases on cadence; human may nudge here |
| chased | "chased {last-date} · next {date}" + Nudge | sender reply auto-resurfaces via Intake (Plain-style auto-reopen — R1 research) |
| arrived/matched | row leaves the lane (resolved by the real arrival) | — |
| waived | not rendered here (resolved; who-waived visible on Intake's checklist) | human waives on Intake |
| escalated (deadline−N per policy) | leaves the lane; becomes a deadline-escalation your-move row | human resolves the blocker or waives |

## 6. Engine / Agent / Human / Gate / Quiet Log

| Function | ENGINE (deterministic) | AGENT (prepares/proposes) | HUMAN (typed decisions) | GATE (interrupts) | QUIET LOG |
|---|---|---|---|---|---|
| Register | Computes the one query; readiness gating; snooze/activity re-entry; footer arithmetic | — | Dispatch; snooze-with-reason; delegate | Deadline-escalation rows land here (and on Home) | Snooze/unsnooze/delegate events |
| Waiting-on-inputs lane | Computes checklist-gap projection from ChecklistItem states; escalation state machine (03 §5) | Drafts chase messages; schedules chases on approved cadence; drafts the nudge's off-cadence chase | Approve a nudge; (waive executes on Intake) | Checklist incomplete at deadline−N promotes to your-move | Each chase sent; each nudge approved |
| Preview pane | Serves engine-state context (verdicts, readiness reasons, deltas) | Serves proposal context (classification confidence + why; draft titles) | — (reading) | — | — |
| Item states | Query exclusion mechanics (snooze) | — | The S/Shift+D acts | — | All item-state transitions logged with actor |

Lane invariants: the agent never dispositions an item; no count derives from anything but the engine query; the only push class remains deadline escalation per org policy (04 §2.3); agent presence is contextual — confidence beside the arrival row, drafted chase inside the nudge confirm, no chat box.

## 7. Information hierarchy

1. Frame header: book-scoped breadcrumb `Covenant / Inbox`; no loan/period crumbs (08 §2).
2. Decision/status summary: register header — "{n} items" (the badge's number, restated) + segment tabs: Your move · Waiting on inputs · Snoozed.
3. Primary work region: the uniform-row register (filtered segment).
4. Secondary context: the summoned preview pane (right).
5. Evidence/proof: inside the preview — provenance refs, chase history, confidence explanations; deeper proof opens on owning surfaces.
6. Actions: per-row inline (Open · Snooze · Delegate; Nudge on lane rows); filter cluster above the list.
7. Activity/history: the "Resolved this session" collapsed group + each item's event trail inside its preview.

Absent by design: unread counts or any read/unread machinery (C-8); mark-as-done/dismiss/archive controls; charts of any kind; a compose/new button (items are born from the book's state, never authored here); notification settings (Settings owns the escalation policy).

## 8. Page anatomy and regions

```text
┌─ shell header — Covenant / Inbox ──────────────────────────────────────────┐
│ 7 items   [Your move] [Waiting on inputs · 4] [Snoozed · 2]                │
│ filters: [act type ▾] [loan ▾] [deadline ▾] [assignee ▾]     Mine ☐        │
│ ┌────────────────────────────────────────────┬─────────────────────────┐   │
│ │ ◉ Certify Bexley — 2026-Q2      due 4d  ▸  │  PREVIEW (summoned)     │   │
│ │ ○ File arrival: T-12 statement   —      ▸  │  item context: verdicts,│   │
│ │ ○ Tie-out failed — CAL 2018-FY   due 9d ▸  │  readiness reasons,     │   │
│ │ ○ Approve narrative draft …             ▸  │  chase history, links   │   │
│ │   … (complete list, virtualized)           │  [Open owning surface]  │   │
│ ├────────────────────────────────────────────┤  [Snooze] [Delegate]    │   │
│ │ ▸ Resolved this session — 2                │                         │   │
│ └────────────────────────────────────────────┴─────────────────────────┘   │
│ footer: 7 shown · 0 filtered out · 2 snoozed (register complete)           │
└────────────────────────────────────────────────────────────────────────────┘
```

### Region 1 — Header + segment tabs + filter cluster

- Purpose: scope the register without ever hiding its arithmetic.
- Content: item count; segment tabs (Your move default · Waiting on inputs · Snoozed); filter cluster — act type, loan, deadline proximity, assignee + "Mine" toggle (4–6 curated chips, the Reports filter-cluster pattern — Stripe-scale filtering rejected per R5).
- Persistence: persistent; filters persist per user per segment.
- Interaction: every applied filter is named in the footer ("3 shown · 4 filtered out by [act: exceptions]"); clearing is one click. This is the structural U3-C1 fix: the register is complete or says exactly what is filtered.

### Region 2 — The register (primary work window)

- Purpose: the full your-move rendering — every actionable item, first-class.
- Content: `YourMoveRow` (Inbox variant) in uniform 40px rows, ordered by deadline proximity then age (same order as the query; no user sort); grouped headers optional by act type when the act-type filter is off; "Resolved this session" collapsed group at bottom.
- Persistence: persistent; virtualized past 60 rows.
- Interaction: J/K traversal; Enter opens owning surface; S snooze; Shift+D delegate; hover reveals inline actions; selecting a row summons the preview.
- Minimum width: 720px (work-window minimum, 08 §4).

### Region 3 — Preview pane (summoned right inspector)

- Purpose: co-visibility for routing judgment — enough context to decide open-now/snooze/delegate without leaving the register (the pane's justification under the ruled pane model: it is a surface-interior inspector, not a second work window).
- Content per class: exception rows — failing fields, confidence, source-region ref; certify/send rows — readiness reasons + what-changed summary; arrival rows — envelope summary (sender, subject, attachments) + candidate match confidence with WHY (Intake's recognition evidence, summarized read-only); finding rows — headroom sentence + evidence link; draft rows — title + first line; lane rows — chase history. Footer actions: Open owning surface · Snooze · Delegate (· Nudge).
- Persistence: summonable (opens on selection; Esc closes; stays closed if dismissed until next explicit summon).
- Minimum width: 360px; preferred 420–480px.
- Why a pane, not navigation: serial triage across heterogeneous items is the one decision here that needs side-by-side context (Karbon Triage's split view — action column left, preview right — R5 research).

### Region 4 — Waiting-on-inputs lane (segment view)

- Purpose: what the book is waiting on from others — visible, never mixed into the count.
- Content: `WaitingOnInputsRow`s across loans: expected doc type · loan+period chips · state (missing / chased with last-chase date and next-scheduled) · Nudge action; rows link into `/intake`'s holding checklist (summary, not ownership). Waived items do not render (they are resolved; visible on Intake's checklist with who-waived).
- Persistence: a segment tab (not a strip crowding the register); its count renders on the tab.
- Interaction: Nudge opens a confirm popover with the agent-drafted chase text and recipient; approving sends via the agent (quiet-logged). No other act — filing, waiving, cadence changes happen on Intake.

### Modals and canvas

- Modals: none — snooze/delegate/nudge are popovers (no legal weight); typed legal acts live on owning surfaces.
- Canvas panel: Ask, as everywhere; never load-bearing.

## 9. Co-visibility matrix

| Datum/surface A | Datum/surface B | Must be simultaneous? | User decision enabled | Default topology | Fallback |
|---|---|---:|---|---|---|
| Register row | Item context (preview) | Yes, during triage | open-now vs snooze vs delegate | List ~60% + summoned pane ~40% | Below 1280px: preview becomes an overlay sheet |
| Register count | Rail badge | Yes (same viewport) | Trust (one number) | Both render the one query | Badge "—" on query failure |
| Row | Owning surface's full work context | No | Dispatch | Deep link, navigate | — |
| Waiting-on-inputs row | Chase history | On demand | Nudge or wait | Preview pane on selection | Inline expansion |
| Snoozed item | Its reason + return condition | On demand | Unsnooze | Snoozed segment rows carry both | — |

No second work window exists on Inbox: the preview is an inspector, and compare topologies belong to spine surfaces.

## 10. Layouts and viewport behavior

| Viewport | Rail | Register | Preview pane | Notes |
|---|---|---|---|---|
| 1440 | 240px expanded | ~660–720px | 420px summoned | preview overlays nothing; list compresses to its 720px floor before the pane refuses |
| 1728 | 240–280px | ~900px | 480px docked-when-summoned | most comfortable triage layout |
| 2048 | 280px | ~1160px | 480px | extra width goes to the register, not the pane |
| <1280 | 48px icon rail | full width | full-height overlay sheet | segment tabs become a select; filter cluster wraps |

- Default pane topology: single work window (the register) + summonable inspector; never a split of two work windows.
- Focus behavior: J/K keeps one focus ring; opening the preview does not steal focus (the list keeps keyboard ownership; Tab enters the pane).
- Compare behavior: none.
- Proof/source behavior: provenance refs in the preview link to owning surfaces where the lit-row chain opens (Inbox never hosts the Evidence panel itself — its previews are summaries).
- Minimum viable dimensions: frame minimum 1152×720; the register remains fully operable (registers-class); below 720px available list width the preview is overlay-only (no silent compression — the pane is labeled and dismissible, the list never squeezes below its floor).
- Tab stacking/replacement: segment tabs are views over one register, not separate pages; browser back restores segment + filters + scroll.
- No silent compression: when the summoned pane cannot hold its 360px minimum beside a 720px list, it converts to the labeled overlay sheet — the list never squeezes and the pane never shrinks below its floor (frame law, 08 §4/§9).
- Focus behavior across recompute: the focused row re-anchors by itemRef after any query recompute (an item resolving above the focus never shifts which item is focused).

## 11. Components and exact anatomy

- `CovenantShell` — REUSE; `/inbox` mounts it with initial view Inbox.
- `CountBadge` — REUSE (the ruled shared atom): rail badge; segment-tab counts; group-header counts. All read the query or its named sub-projections.
- `YourMoveRow` — REUSE the shared component (built for Home/Inbox in `src/components/covenant/your-move/`), Inbox variant adds:
  - seen-state dot (fades on view; cosmetic only);
  - inline action cluster on hover/focus: Open · Snooze (S) · Delegate (Shift+D);
  - assignee chip when delegated;
  - snoozed rows (Snoozed segment) add: reason text, return condition ("until Aug 15 or new activity").
- `InboxFilterCluster` — NEW (pattern REUSE from the Reports gold-standard filter cluster — snapshot §3): act-type, loan, deadline-proximity, assignee chips + Mine toggle; emits the footer's filter narrative.
- `RegisterFooter` — NEW: the completeness assertion — "{shown} shown · {filtered} filtered out by {named filters} · {snoozed} snoozed"; the U3-C1 structural fix rendered as arithmetic.
- `InboxPreview` — NEW summoned inspector. Parts:
  - identity header: class icon, title, loan+period chips, deadline chip, assignee chip;
  - per-class context body:
    - certify-ready / send-ready: readiness reasons (the same engine computation the certificate route renders) + what-changed-since-prior-period summary;
    - exception rows: failing field list with confidence + source-region ref (opens owning surface, lit);
    - arrival rows: envelope summary (sender, subject, received, attachment list) + top candidate match with confidence and WHY — Intake's recognition evidence, summarized read-only;
    - finding rows: verdict chip + headroom sentence (engine values by reference) + evidence link;
    - draft rows: draft title + first line + owning-surface link;
    - lane rows: chase history (each chase: date, recipient, channel) + next scheduled;
  - event trail: this item's ActivityEvents, newest first;
  - footer action bar: Open owning surface · Snooze · Delegate (· Nudge on lane rows). Read-only except those acts.
- `WaitingOnInputsRow` — NEW: doc-type label, loan+period chips, state chip (missing/chased), last-chase + next-scheduled dates (Intake data, linked), Nudge button.
- `SnoozePopover` — NEW: until-when picker (preset horizons + date) + reason field (required) + the fixed clause "returns earlier on new activity" (the semantics are not optional).
- `DelegatePopover` — NEW: teammate list (role-aware — certify-class items only offer certify-rights holders), optional note; writes assignment + ActivityEvent.
- `NudgeConfirm` — NEW popover: agent-drafted chase text (editable), recipient with provenance (lender/PM contact from memory, "learned {date} from {who}"), send-via-agent confirm.
- `ResolvedSessionGroup` — NEW: collapsed list of rows resolved this session with links to their quiet-log events.
- Empty/error objects: calm empty state per segment; `SectionErrorBlock` REUSE from Home.
- `CommandPalette` — REUSE: `G I` lands here; palette exposes "Snooze current item", "Delegate current item" as commands.

## 12. Interaction specification

- Selection: single-row selection drives the preview; no multi-select in v1 (bulk approval is an owning-surface concern; bulk snooze would be a work-hiding affordance).
- Hover: row hover raises one gray rung and reveals the inline action cluster; chips reveal tooltips (due-rule, assignee, snooze condition).
- Focus: 2px accent ring; roving tabindex within the register; Tab moves list → preview → footer.
- Keyboard: `J/K` next/prev; `Enter` open owning surface; `S` snooze (opens popover); `Shift+D` delegate; `N` nudge (lane rows); `Esc` close preview/popover; `1/2/3` switch segment tabs; `F` focus filter cluster; `G I` here, `G H` Home; `⌘K` palette. Every act is keyboard-complete.
- Editing and validation: snooze requires a reason (validated non-empty); nudge text editable before approve; nothing else editable.
- Bulk action: none by design (see Selection).
- Undo/recovery: unsnooze from the Snoozed segment; reassign/unassign from the row; nudge cannot be unsent (it is a real outbound chase — the confirm popover states this); dispatch recovers via browser back with state restored.
- Sorting/filtering: order fixed (deadline proximity, then age — the badge's implied urgency is the list's actual order); filters per §8 region 1 with footer naming; filters persist per user.
- Drill-down and return path: rows carry `actRoute` with loan+period; back returns to the same segment, filters, scroll, and focused row.
- Source-linked selection: preview provenance refs open owning surfaces with the lit-row chain (frame contract 08 §7); Inbox itself never lights source rows (its previews summarize).
- Save/persistence: filters, segment, preview-dismissed state persist per user; item states persist per §3 ownership; no saved views in v1 (the register's segments + 4 filters cover the 2–50 loan book; the shared SavedView mechanism can attach later without new machinery).
- Collaboration/commenting: delegation notes only; discussion lives on owning surfaces.

## 13. Visual craft direction

- Typography: register rows 14px/450 primary ink; segment tabs 13px/600; meta 12px tertiary ink; preview header 15px/600; footer 12px secondary ink.
- Financial numbers: none render here except counts and the preview's quoted engine figures — all Geist Mono `tabular-nums slashed-zero` (ruled).
- Spacing rhythm: 8px grid; 40px uniform row heights (uniform-row law); 12px row padding; 16px preview gutter.
- Density: work-register density — tighter than Home, looser than a data grid; the row is a sentence, not a record.
- Open ground vs earned boundaries: open-not-boxed list (hairline row separators only); the preview pane earns a hairline + one raised rung (it is a summoned inspector); segment tabs underline, never box.
- Dividers/elevation: ten-rung gray ladder only; raised rung for preview and popovers.
- Semantic color: accent #7189FF for focus/links/selected (selected row ground uses the tint role #A9B5FF at the control-layer's ruled usage; hover #8EA1FF; active #6078F4); verdict chips consume ruled Review-9 verdict tokens (severity-only); deadline chips tint at escalation only; the seen-dot uses the accent, small and quiet. Zero new values.
- Certified-sheet treatment: none — certified-sheet hexes never paint Inbox.
- Focus/selected/hover states: consume the existing Covenant control layer (Ruling-J: exists, zero consumers — Inbox becomes a consumer).
- Chart style: no charts (nothing here out-encodes text; doctrine applied by omission).
- Motion: resolved rows animate out over 160ms then reappear in the resolved group; snooze slides the row toward the Snoozed tab; `prefers-reduced-motion` replaces both with instant transitions + an aria-live announcement.
- Long-session ergonomics: triage sessions are keyboard-serial — stable row heights, no layout shift when the preview opens (list width reserved), focus never lost on recompute (the focused row is re-anchored by itemRef, not index).

## 14. Benchmark research and synthesis

| Product | Limited role | Official evidence | Exact pattern to take | Covenant adaptation | What to reject | Why this reference beats alternatives |
|---|---|---|---|---|---|---|
| Linear | Inbox/Triage separation + snooze donor | https://linear.app/docs/inbox ; https://linear.app/docs/triage (R5/R1 research) | Inbox vs Triage as distinct surfaces (personal delta vs decision queue); snooze `H` hides until a chosen time or new activity, whichever first, auto-unsnoozing on comments/activity | The separation maps to Inbox (the your-move register) vs Intake (the arrival decision queue) — arrival rows here deep-link there; snooze adopts the exact until-time-or-activity contract with a required reason | Unread/read as count machinery (`U`, mark-all-read — C-8: unread is not your-move); delete/`Backspace` (no item here may be deleted) | The cleanest documented statement of the two-surface split Covenant needs, plus the only snooze that structurally cannot bury work |
| Ramp | Readiness gating + Remind donor | https://support.ramp.com/hc/en-us/articles/4417421399699-Transaction-Reviews ; https://support.ramp.com/hc/en-us/articles/1500011601642-What-to-do-if-you-re-missing-a-receipt (R5/R1 research) | Items enter the approval inbox only when required fields are satisfied and cleared; incomplete items live elsewhere with a Remind action pushing the ball to whoever owes the input | Readiness gating inside the query; the waiting-on-inputs lane with Nudge = approving a single agent-drafted off-cadence chase | Approval-hierarchy routing chains | The documented pair (gated queue + separate blocked surface + remind) is precisely this brief's structure |
| Plain | Minimal queue-state donor | https://help.plain.com/article/statuses (R1 research) | Three status groups (Todo/Snoozed/Done) with reason-coded substates; auto-reopen — external activity flips a parked item back to Todo without human vigilance | Segment tabs mirror the trio (Your move / Waiting on inputs / Snoozed) with done existing only as done-by-action; auto-reopen = the activity half of snooze and the lane's auto-resurface on sender reply | Conversation-first framing and response-SLA per thread (items are decisions, not threads owed a reply) | The smallest documented state model that still handles parked work honestly |
| Karbon | Triage split-view donor | https://help.karbonhq.com/en/s/articles/5574239-overview-of-triage (R5 research) | Split view: action column left, preview right; practiced single-key verbs toward queue-zero | The register + summoned preview topology; J/K/Enter/S serial rhythm | Email-client ambitions (triaging a whole mailbox — Covenant triages only book-blocked decisions); "clear" as a verb (nothing clears here without a real act) | The best-documented triage split-view in a professional-services register |
| Floify | Needs-list domain donor (lane only) | https://help.floify.com/hc/en-us/articles/360040327032-Resend-Needs-List-Email-to-My-Borrower (R1 research) | Per-record needs list with item states and tunable pre/post-deadline reminder cadence; rejection reopens an item with a reason | The lane renders Intake's checklist gaps (arrived/missing/chased/waived per loan+period); nudge = one off-cadence resend; cadence config stays in Settings/Intake | Notification blasts at every state transition (quiet-filing law); direction inverts — the borrower chases their own PM/accounting senders, not a lender chasing borrowers | The closest living ancestor of the holding checklist + chaser, from mortgage workflow itself |
| Front | Anti-pattern | https://help.front.com/en/articles/1999 (R1 research) | — | — | The shared-mailbox model wholesale: human-touch-everything, archive-as-disposition (archived ≠ resolved-by-act), notification fan-out, inactivity auto-unassign. If Inbox ever grows archive or mark-done, completeness dies and the checklist stops being trustworthy | Documented proof of what this register must never become |

Synthesis: Inbox becomes original by fusing two patterns no researched product combines — a Ramp-gated, Linear-snoozed decision register whose every row is a projection of deterministic engine state, and a hard rule borrowed from nowhere: no terminal act exists on the surface at all. Linear and Plain contribute parked-work honesty (time-or-activity return), Karbon the split-view triage rhythm, Ramp the gate and the nudge, Floify the lane's domain shape, Front the boundary. What makes it Covenant's own is that the register's emptiness is provable: because items enter only by engine computation and leave only by real acts on owning surfaces, an empty Inbox is a verifiable statement about the book — every period is advancing, nothing is blocked on the borrower — rather than a claim about a human's tidying habits. That is borrower-side lender reporting rendered as a queue.

## 15. Domain references

Terminology and workflow semantics only: Floify (mortgage needs-list states — arrived/pending/accepted — informing the lane's vocabulary, with "waived" added because lenders excuse items; R1 research), Finley (deliverable-status register across credit facilities — the register-of-obligations frame; R5 research), and LoanBoss (critical-date reminder taxonomy feeding which escalations can reach the register; R5 research). Domain authority does not equal visual authority: none of these products' renderings governs a pixel here, and covenant semantics — what is owed, when, and on what basis — come from the loan documents and Terry's rulings (05 §1), never from any referenced product.

## 16. Accessibility, performance, and safety

- WCAG contrast and focus: ladder-token pairs at AA; verdict chips carry text labels (never color alone); visible accent focus ring; skip-link to the register.
- Keyboard completeness: §12 covers every act; popovers trap focus and return it to the originating row; segment tabs are arrow-key navigable.
- Screen-reader semantics: the register is a labeled list with row semantics (class, title, identity, deadline announced in order); the count is `aria-live="polite"`; row resolution announces "resolved by {act}"; the seen-dot is `aria-hidden` (cosmetic).
- Table virtualization/large data: virtualize past 60 rows; the query is bounded by book size (2–50 loans × items/period); preview content lazy-loads per selection.
- Loading and latency feedback: skeleton rows; the badge and footer render only from resolved queries; recomputes re-anchor focus by itemRef.
- Destructive action confirmation: nudge is the only externally-consequential act here — its confirm popover shows the outbound text and recipient and states it cannot be unsent; snooze/delegate are reversible and confirm inline.
- Certify and external-send safety: neither act exists on this surface — certify-ready and send-ready rows navigate to the certificate route's ceremonies (typed acts, approved bytes, void-on-change enforced there).
- Source immutability: Inbox holds no mutation path to any domain object — only item-state overlay, assignment, and the nudge approval (which delegates to the agent's chase machinery).
- Auditability: every snooze (with reason), unsnooze, delegation, and nudge writes an identity-stamped ActivityEvent; the register's contents at any timestamp are reproducible from persisted state + item-state history (deterministic query over both).

## 17. Acceptance tests and fixtures

Fixtures: Bexley canon (2026-Q2 period staged ready; occupancy shortfall finding 268/301 = 89.04% vs monitored 90% floor) and the Calloway Park FYE-2018 evidence spine (checklist per §8.02(b): quarterly YTD statement Q+45d, rent schedule, annual certification items; T-12 and rent-roll arrivals; JLL questionnaire draft). Seed: 7-loan book, 7 your-move items, 4 checklist gaps, 2 snoozed items.

1. Badge semantics (U3-F7 regression). Seed 7 items; view 3 of them (NEW→SEEN) → assert badge, header count, and Home count all remain 7; no code path feeds seen-state into any count (C-8).
2. Completeness (U3-C1 regression). Lane has 7 checklist gaps → the Waiting-on-inputs segment renders all 7; apply loan filter hiding 4 → footer reads "3 shown · 4 filtered out by [loan: Bexley]"; clear → 7. Assert no render path truncates without a footer statement.
3. One-number law. Assert `rail badge = register header = Home YOUR MOVE count` before and after: (a) certifying Bexley Q2 on the certificate route, (b) snoozing an item, (c) an escalation firing. All three recompute from `yourMove()` atomically.
4. Done-by-action only. Assert no done/dismiss/archive/delete affordance exists in the DOM for any row; resolve a tie-out on Review → the row animates to "Resolved this session" linking the disposition's quiet-log event; count decrements everywhere.
5. Snooze contract. Snooze the arrival row until +7d with reason → it leaves all counts; simulate a new email on the same thread at +2d → the row returns (activity beats time), snooze reason and return cause visible in preview (Linear semantics, R5/R1).
6. Readiness gating. Calloway Park 2018-FY with missing rent schedule → no certify-ready row exists; the gap sits in the lane with chase history ("chased {date}, next {date}"); Nudge opens the drafted chase, approving writes the chase ActivityEvent and updates next-scheduled; escalation at deadline−N promotes exactly one deadline-escalation row into your-move.
7. Delegation. Shift+D on a certify-ready row → only certify-rights holders are offered; assign to the owner → row shows assignee chip, owner's "Mine" filter includes it, count unchanged for all members; event logged.
8. Arrival boundary. An arrival-decision row's Enter and preview "Open" both land on `/intake` with that arrival selected; assert Inbox renders no file/replace/hold/dismiss affordances (Intake ownership).
9. Vocabulary law. The Bexley finding row and preview render "shortfall"; assert the string "breach" appears nowhere for `basis=monitored` items; a covenanted-basis test fixture (generic below-threshold input on a Westbrook-shaped loan — labeled test fixture) may render "breach".
10. Void-on-change. Void Bexley's certification (replace a feeding source doc) → send-ready row disappears and "re-review: certification voided" appears in the same recompute; both visible in one register frame, never a stale pair.
11. Viewport fixtures. 1440/1728/2048/1279 screenshots per §10; at 1279 the preview is a full-height overlay sheet and the list holds ≥720px equivalent behavior (no squeeze).
12. Keyboard fixture. Full serial triage with keyboard only: J/K through 7 items, Enter-open + back (state restored), S with reason, Shift+D, N on a lane row, 1/2/3 segments, F filters; focus never lost across a recompute.
13. Preview integrity. The certify-ready preview's readiness reasons equal the certificate route's readiness computation (same engine call); the exception preview's source-region ref opens the owning surface with the correct row lit (U1-F1-class check at the handoff).
14. Accessibility. axe-clean; count aria-live announces on test 3's mutations; row resolution announces its resolving act; popover focus return verified.
15. Benchmark challenger review. Reviewer walks Linear inbox/triage docs, Ramp's gating articles, and Plain's statuses against the shipped register; divergences from §14's take/adapt/reject columns filed as defects.

## 18. Build plan

- Dependencies: the your-move query (`src/lib/covenant/your-move.ts` — shared foundation with Home; built first, test-first); item-state store + migration (`inbox_item_states`: itemRef, state, snoozeUntil, snoozeReason, assignee, watermark — org-scoped with per-user seen overlay); ActivityEvent store (shared); ChecklistItem persistence for the lane (Intake brief dependency); chase machinery for Nudge (agent brief); escalation policy engine (Calendar brief, F5); tenancy as proven on the send route.
- Foundation work: the query's snooze/activity re-entry semantics implemented inside the engine function (never in the component); footer arithmetic derived from the same query call (one source for shown/filtered/snoozed).
- Components to build first: `YourMoveRow` Inbox variant (extending the shared atom), `RegisterFooter`, `InboxFilterCluster` (porting the Reports cluster pattern), `InboxPreview`, `SnoozePopover`.
- Vertical slice (send-vertical pattern): `/inbox` route mounting the shell; ONE loan (Bexley) with a persisted certified period → the register renders one real Send-ready row from gate state; Enter → certificate route → REAL send (`POST /api/covenant/send`) → return to Inbox shows the row in "Resolved this session" with the SendRecord's quiet-log link; footer and badge at 0. One route, one loan+period, engine data end to end.
- Migration from fixture data: the current Inbox view's fixture arrays (arrivals, awaiting-documents rows in `book.ts` projections) are deleted, not adapted; the lane renders only from persisted ChecklistItems — until that store lands, the lane renders an honest "not yet wired" block behind the flag, never the 3-of-7 fixture (04 §2.5).
- Rollout/feature flag: `covenant-inbox-live`; the `/review-room` Inbox view swaps to the same components once green; the rail badge flips to the query in the same release as Home (the one-number law makes a partial flip incoherent — badge, Home, and Inbox convert together).
- Proof artifacts required: recorded runs of tests 1–5 (the two named-defect regressions + the one-number law + done-by-action + snooze contract); 4-viewport screenshots; item-state audit trail sample showing snooze/delegate/nudge events.
- Final gate: `ADJUST` confirmed — the register + lane concept of the built fixture survives; semantics and data plane are replaced. PASS requires tests 1–10 green against persisted state with zero fixture reads.
