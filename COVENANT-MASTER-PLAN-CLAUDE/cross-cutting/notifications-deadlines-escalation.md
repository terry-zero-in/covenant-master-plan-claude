# CROSS-CUTTING — NOTIFICATIONS, DEADLINES, AND ESCALATION (GAP 9 MACHINERY)

Ruling context: notifications/calendar machinery is ABSENT in the current build (snapshot §6 gap 9; inventory 07 §1 A7); obligation/due-date information today renders in Calendar, Home, and period headers from independent fixtures (07 §2 duplication audit). This file is the one system that replaces all of it: a due-rule engine (roadmap F5), one escalation ladder, one computed your-move query, and one notification-delivery contract. Surfaces render projections of this system; none of them own a second copy (02 §3: Calendar/Obligations owns deadlines; Home and period headers summarize).

## 1. Why this is a system, not a feature

Three defects become structurally impossible only if this is one machine:

1. **Contradictory counts** (U3-F7-class): the Inbox badge, Home groups, and rail count showed different fixture numbers because "your move" was three implementations. Here it is ONE computed query (03 §4) with three renders.
2. **Invented urgency**: a reminder with no due-rule provenance is a guess. Every Deadline row here descends from a human-confirmed RequirementRecord and can light its source clause (lit-row law).
3. **Notification noise**: the agent completes work constantly. The quiet log absorbs all of it (04 §2.3); the ladder's push rung is the single, policy-gated exception. Deadline consequence is real — a late package is a compliance failure (03 §5) — which is exactly why the push channel must stay scarce: a channel that cries about routine work is mute when the deadline is actually at risk.

## 2. The due-rule engine (F5)

### 2.1 Inputs — confirmed requirements only

The engine consumes `RequirementRecord.cadence.due_rule` (05 §1.2) — e.g. `Q+45d`, `FYE+120d` — from records in `confirmed` state only. Proposed (unconfirmed) records materialize NOTHING: an unconfirmed schedule is an extraction exception (a your-move item), never a guessed calendar (fail-closed, 04 §2.5). Evidence instances (SLOT-3, the executed 6001.NR):

- Quarterly YTD income statement **Q+45d** per §8.02(b)(1); annual statements incl. cash flows **FYE+120d** per §8.02(b)(2).
- Rent schedule at both cadences per §8.02(b)(3) (named fields: tenant, space, lease expiration, current rent, paid-through).
- Annual certification bundle (single-asset, building-code, rezoning, liens; security-deposit accounting; ownership-change confirmation) per §8.02(b)(2)(E)–(G), annual.
- On-request items per §8.02(b)(4), rate-capped at once per 6 months absent default — event-materialized, not cadence-materialized (§2.3).
- Rider-added: Form 6241 annual ENERGY STAR report — the rider is a first-class due-rule source, proving the engine reads the confirmed record set, never a form template's assumption.

### 2.2 Output — materialized Deadline rows

When the orchestration spine opens a cadence occurrence (03 §4: month start, E schedules cadence occurrences, new OPEN periods appear with checklists), the engine materializes one Deadline row per requirement occurrence:

```ts
// src/lib/covenant/deadlines.ts (NEW; pure — F5)
type Deadline = {
  deadlineId: string                      // keyed (loanId, period, reqId, occurrence)
  loanId: string; period: string; reqId: string
  lender_deadline: string                 // ISO date, computed from the due-rule
  internal_due: string                    // ISO date, the org's prep buffer (never shown as the lender's date)
  due_rule: {
    text: string                          // verbatim rule, e.g. "Q+45d"
    source_ref: ProvenanceRef             // → definition_source_region (the clause; lit-row traceable)
    version: number                       // RequirementRecord version that produced this row
  }
  ladder_state: 'scheduled'|'reminded'|'chased'|'your-move'|'overdue'|'met'|'withdrawn'
  snooze?: { until: string; or_activity: true; reason: string; by: string }
  escalation_history: ActivityEventRef[]  // every transition quiet-logged
}
```

**Due-rule provenance is kept, always.** Everywhere a deadline renders, its rule renders beside it in the pattern **"due {date} — Q+45d per §8.02(b)(1)"**, and clicking the citation traces to the exact clause region via the one resolver (`cross-cutting/provenance-lit-row-trace.md` §2.3). A date with no rule citation is a rendering bug, not a style choice.

### 2.3 Materialization mechanics

1. **Trigger**: a cadence occurrence opens (period creation) → every confirmed RequirementRecord whose cadence matches the occurrence yields one Deadline row. Several requirements sharing one date (the §8.02(b)(1) statement and the §8.02(b)(3) rent schedule both land Q+45d) stay **separate rows** — each keeps its own citation and its own met/unmet fate — but Calendar and the period header may group them visually by date.
2. **The period's governing deadline** (the single date the period header and Loans register "Next deadline" column show) is a projection: `min(lender_deadline)` over the period's un-met rows. It is computed, never stored a second time (07 §2 duplication resolution).
3. **Checklist linkage**: ChecklistItems derive from RequirementRecords for the period (02 §2); each Deadline row references the checklist items that feed its deliverable, which is how the ladder knows "inputs missing" (chase guard, §3) versus "blocked on the borrower" (your-move guard).
4. **Event-materialized rows**: on-request items (§8.02(b)(4)) materialize when a written request is filed through Intake (classified correspondence → typed acceptance), carrying the request date and a rate-cap context note when a second request arrives inside 6 months (rendered as context, adjudicated by the human — never auto-refused).
5. **One-time items** (post-closing deliverables, repair certifications — 05 §1.1) materialize once at confirmation with their own due terms; `met` retires them permanently.

### 2.4 Date math

- `lender_deadline` = period end + the rule's literal term, in **calendar days exactly as the agreement states them** — the engine never shifts a lender deadline for weekends/holidays unless the agreement's own text says so (unknown convention = escalate to the human, never guess; domain firewall).
- `internal_due` = `lender_deadline` − org prep buffer — the Karbon dual-date pattern: "deadline" is external/statutory, "due date" is the firm's own buffer (R5 research: Karbon, https://help.karbonhq.com/tasks/faqs/how-do-task-due-dates-work). Buffer is Settings-owned (authoring default 10 calendar days [org-editable; product config, not a lender fact]) with per-loan override. Business-day preferences apply ONLY to `internal_due`, never to `lender_deadline` (business-day math precedent: Vanta's configurable business-day SLAs, R5 — adopted for the internal date alone).
- **Dual-date rendering law**: the lender deadline is the date of record — it appears on Calendar rows, period headers, the Loans register, and escalation copy. The internal date renders as secondary context ("prep by {date}") on Calendar and Home only. The internal date NEVER appears in any outbound artifact, correspondence draft, or send-panel copy — it is the org's own buffer, not a fact about the loan.

### 2.5 Versioned rules (the Vanta pattern)

A loan amendment that changes a due-rule (extraction amendment flow, 02 §5) versions the RequirementRecord. **In-flight Deadline rows keep the original deadline of the version that materialized them; only future occurrences materialize under the new version** — deadline rules are versioned against the item, not retroactive (R5 research: Vanta SLA behavior — SLA changes apply to new items only; open items keep the deadline active when they were detected, https://help.vanta.com/en/articles/11346006-editing-slas-in-vanta). Both versions render with their own citation; a mid-period amendment shows an "amended — applies from {next period}" note on the in-flight row. The one exception: if the amendment explicitly restates the current period's obligation, the human confirms the re-dated record and the engine re-materializes — a typed act with the old row's history preserved, never a silent overwrite.

## 3. The escalation ladder (state machine, org-configurable)

```text
                 ┌────────────────────────────[SendRecord written]──────────────▶ MET (terminal)
                 │
 scheduled ──[now ≥ internal_due − remind_at]──▶ reminded
                 │                                   │
                 │                     [checklist inputs missing ∧ chase policy]
                 │                                   ▼
                 │                                chased ◀──(chase cadence loop, agent-drafted, quiet-logged)
                 │                                   │
 reminded|chased ──[now ≥ internal_due − your_move_at  ∨  the period is blocked on a human act]──▶ your-move
                 │                                   │
                 │                    [now > lender_deadline ∧ no SendRecord]
                 │                                   ▼
                 └──────────────────────────────  overdue  ── the ONE push-eligible state
 any ──[requirement waived/withdrawn by amendment, typed act]──▶ WITHDRAWN (terminal, logged)
```

State semantics (each transition writes an ActivityEvent; the ladder never acts, it applies pressure — the acts stay human per the trust hierarchy):

| State | Entered when | Renders as | Who moves it forward |
|---|---|---|---|
| scheduled | Row materialized | Calendar "Later/Scheduled future" groups only; no badge, no feed | Time, or arrivals advancing the period |
| reminded | Remind threshold reached, period not sent | Calendar "at-risk" treatment; Home next-deadlines mini-agenda; NO your-move entry yet | The borrower's own prep (or nothing — it may ripen) |
| chased | Inputs missing from third parties (holding checklist gaps) | Chase history on the checklist item (Intake owns it); Calendar row notes "chasing — last {date}, next {date}" | The agent chases on approved cadence (quiet-logged); documents arriving exits the state |
| your-move | Human-blocking threshold reached, or the period's next step is a human act (certify-ready, send-ready, exception unresolved) with the deadline inside the window | Enters the computed your-move query (§5): rail badge, Home group, Inbox row | Only the owning surface's real act |
| overdue | `lender_deadline` passed without a SendRecord | Overdue treatment per `cross-cutting/status-vocabulary-shortfall-breach.md` tokens (no new colors); pinned atop Calendar and Inbox; **push-eligible per policy** | Only certify/send (or a typed waiver/withdrawal) |
| met | SendRecord written before/after deadline | Calendar cycle summary ("met {n}"); late met rows keep an honest "sent {n}d late" note forever (seal-not-wipe) | Terminal |
| withdrawn | Amendment/waiver removes the occurrence | History only, with the typed act cited | Terminal |

Ladder notes:

- `chased` is a **sub-loop, not a mandatory rung**: a period whose documents all arrived skips it entirely; a chase can run while `reminded` or `your-move` (the states track different actors — chase tracks third parties, your-move tracks the borrower).
- `reminded` and `your-move` thresholds anchor to `internal_due` (the org's own pressure schedule); `overdue` anchors to `lender_deadline`, always — the org can tighten its own nerves, never the lender's date.
- **Ramp-style readiness gating** applies inside `your-move`: a period enters via threshold OR via genuine human-blockage; items still waiting on third-party inputs are `chased`, not `your-move` — the queue admits only actionable work (R5 research: Ramp — transactions enter the review inbox only when required fields are satisfied; blocked items live elsewhere with a Remind action, https://support.ramp.com/hc/en-us/articles/4417421399699-Transaction-Reviews).

### 3.1 The policy object (Settings-owned, versioned)

```ts
// stored per org; edited at /settings/notifications (settings.md direction); every edit versioned with editor identity
type EscalationPolicy = {
  version: number; edited_by: string; edited_at: string
  internal_buffer_days: number            // default 10 [authoring default; org-editable]; per-loan override lives on the loan
  remind_at_days: number                  // before internal_due → reminded
  your_move_at_days: number               // before internal_due → your-move
  chase: { cadence_days: number; max_attempts: number; template_scope: 'approved-only' }
  push: { enabled: boolean; classes: ['overdue'] | ['overdue','final-your-move']; audience_roles: RoleId[] }
  certified_unsent_marks: number[]        // days-remaining marks for the Carta-style reminder track (§4)
  quiet_log_retention: RetentionRule      // rendering retention only; the ActivityEvent store itself is append-only
}
```

A policy change re-evaluates ladder states at the next tick but **never moves any deadline date** — dates come only from due-rules (§2.5). Ladder evaluation is a deterministic engine tick (hourly): same inputs, same states, idempotent re-runs.

**Snooze is an overlay, not a ladder state**: snooze-until-time-or-activity (R5 research: Linear Inbox — snoozed items return at the chosen time OR on new activity, whichever first, https://linear.app/docs/inbox), always with a reason, always quiet-logged. Snoozing hides the your-move row; it never moves `lender_deadline`, and the overdue transition **pierces snooze** — an overdue item is unsnoozeable by design.

## 4. The quiet-log relationship (the noise firewall)

- **Completed autonomous work NEVER pushes.** Every autonomous completion (auto-filed arrival, extraction pass, chase sent, computation finished, draft prepared) writes an append-only, identity-stamped ActivityEvent (04 §2.3) and is discoverable in place — Home §4 quiet activity, Loan Detail activity tab, per-period logs. No banner, no toast-that-demands, no badge increment. (The counter-example is ruled: Microsoft's ambient "Copilot everywhere" chrome drew a documented public retreat; invoked assistance is welcomed, imposed announcement is despised — R6 research, anti-pattern control.)
- **The ONE push-eligible class is deadline escalation per policy** (04 §2.3; 08 §8; 03 §5: "overdue items are the one class allowed to escalate from quiet log to push notification"). Concretely: only rows in `overdue` (and, if the org opts in, the final pre-deadline your-move mark) may generate a push, to the policy's named roles.
- **Chases are agent work, not notifications to the user**: agent-drafted, approved-cadence, quiet-logged (intake-holding direction). The user hears about a chase only if it fails long enough to ripen the ladder.
- **Certified-but-unsent gets Carta-style reminders**: when a CertificationRecord exists un-voided but no SendRecord follows, escalating reminders fire at policy-defined days-remaining marks — the Carta mechanic (automatic reminders at the 5/10/20/30/90-day marks while a security remains unsigned/unaccepted; R4 research: Carta, https://support.carta.com/kb/guide/en/how-to-remind-a-signatory-or-holder-to-sign-a-security-MOPXFr6S1q), adapted from elapsed-time marks to deadline-relative marks. Same track for due-but-uncertified READY periods. These reminders are in-app your-move decorations until the ladder itself reaches a push-eligible state — the reminder track never mints a second push channel.
- **Pressure on humans, never automation of the act** (R4 synthesis 11–12): there is no auto-certify and no auto-send at any deadline, ever. The gates accept only an authenticated human session performing a typed act; the ladder's job ends at making the human impossible to surprise.

## 5. Your-move: ONE computed query, three renders

Definition (03 §4, fixing C-8): the count of items **blocked on a human decision**. Unread is not your-move; completed agent work is quiet log, not your-move. One implementation:

```ts
// src/lib/covenant/yourMove.ts (NEW; pure query over the stores)
yourMove(orgId, scope?: {loanId, period}) → YourMoveItem[]   // every render consumes THIS
```

### 5.1 The item classes, precisely

| # | Class | Source predicate | Deep link |
|---|---|---|---|
| 1 | Certify-ready period | ReportingPeriod status=ready ∧ no un-voided CertificationRecord | `/covenant/[loanId]/[period]/certificate` |
| 2 | Send-ready period | Un-voided CertificationRecord ∧ no SendRecord | certificate route, send panel |
| 3 | Arrival decision | Intake queue item awaiting routing (ambiguous only — auto-filed arrivals are quiet log) | `/intake` with the arrival selected |
| 4 | Extraction exception | ExtractedValue/RequirementRecord proposal blocking (incl. unconfirmed setup, fail-closed blocks) | `/loans/[loanId]/setup` or the period's exception card |
| 5 | Computation exception | Unmapped COA / control-total break / normalization conflict / book-basis flag | `/covenant/[loanId]/[period]/actuals` |
| 6 | Failed tie-out | TieOut status=failed | `/covenant/[loanId]/[period]/review` (tie-out stop) |
| 7 | Finding awaiting disposition | Finding state=raised | Home card → owning loan/period |
| 8 | Draft awaiting approval | Correspondence reply / narrative draft in awaiting-approval | `/loans/[loanId]/correspondence` or composer section |
| 9 | Deadline escalation | Deadline ladder_state ∈ {your-move, overdue} | `/calendar` row → the period it belongs to |

**Dedup law (one item, one count):** a class-9 row whose blocking act already appears as classes 1–8 for the same (loanId, period) does not add a second item — it decorates the existing row with deadline proximity ("due in {n}d — Q+45d per §8.02(b)(1)"). Class 9 stands alone only when nothing else is blocked on the human (e.g., overdue with documents never arrived and the decision is waive/escalate/chase-harder).

### 5.2 Where each renders (and nothing else — 08 §8)

| Render | What it shows |
|---|---|
| Rail badge (Inbox/Home, shared `CountBadge` — the consolidated atom, snapshot §5) | The total deduped count. One number everywhere it appears (frame test 08 §10.6) |
| Home §1 groups | The grouped top slice, 7 groups (classes 4–6 merge into "Exceptions"): Certify-ready / Send-ready / Arrival decisions / Exceptions / Findings / Drafts / Deadline escalations, each row a deep link with loan+period identity |
| Inbox register | The FULL register — every item a first-class row with inline disposition affordances (open owning surface / snooze-with-reason / delegate); item-level states new/seen/snoozed live here; "done" only by the owning surface's real act (inbox.md direction) |
| Deal-altitude header chip | Permissible slim count when the CURRENT loan+period itself has blocking items; links to the blocking item, not a feed (08 §8) |

No other surface renders a your-move count. Calendar renders ladder states (it owns Deadline rows); Loan Detail renders "this loan's your-move" via the same query scoped by `loanId`. Every YourMoveItem carries loan+period identity and its deep link — the row IS the navigation (no "go find it" copy anywhere).

## 6. Notification channels

- **v1: in-app only.** The system's whole delivery surface is the four renders above plus Calendar's ladder treatments. No email, no browser push, no digest.
- **Later: email push, one class.** When email ships, it carries ONLY the deadline-escalation class per policy: loan (outward name), period, deadline date + due-rule citation text, and a deep link. **Never package contents, never figures, never resident-level data** (names law; a figure outside the app has no lit-row and therefore does not travel). Same policy object, same audience roles, no separate opt-in matrix.
- Delivery is a thin service over ladder transitions (`NotificationDelivery`, NEW): it renders states; it never computes them. It consumes `escalation_history` events and the policy; a delivery failure is itself quiet-logged and retried — never silently dropped, never duplicated (delivery records are keyed by transition event id, idempotent).
- Quiet-log retention is a Settings rendering preference (settings.md direction); the underlying ActivityEvent store is append-only regardless.

## 7. Benchmarks (limited roles; exact mechanics; official URLs from R4/R5 research)

| Product | Limited role | Mechanic taken | Take/adapt/reject |
|---|---|---|---|
| Karbon (https://help.karbonhq.com/tasks/faqs/how-do-task-due-dates-work) | Recurring-work instantiation | Dual dates: external "deadline" vs internal "due date"; repeating schedules auto-create work with offset-derived dates | TAKE dual dates + recurrence-with-offsets; ADAPT My Week's auto-promoting buckets into Calendar's due-window groups; REJECT whole-inbox email triage |
| Vanta (https://help.vanta.com/en/articles/11346006-editing-slas-in-vanta) | Deadline-annotated status list | Deadline in the row with soft/overdue states; SLA rule changes apply to new items only; business-day math | TAKE rule versioning (in-flight keeps its date) + deadline-in-the-row; ADAPT business-day math to the internal date only; REJECT burying due rules in per-control settings |
| Linear (https://linear.app/docs/inbox) | Snooze semantics | Snooze hides until chosen time OR new activity, whichever first | TAKE snooze-until-time-or-activity; ADAPT with mandatory reason + overdue pierce; REJECT decline/duplicate verbs — obligations can't be declined |
| Ramp (https://support.ramp.com/hc/en-us/articles/4417421399699-Transaction-Reviews) | Actionability gating | Items enter the attention queue only when actually actionable; blocked items live elsewhere with a Remind action | TAKE readiness gating for your-move; ADAPT Remind → agent chase on approved cadence; REJECT approval-hierarchy routing |
| Carta (https://support.carta.com/kb/guide/en/how-to-remind-a-signatory-or-holder-to-sign-a-security-MOPXFr6S1q) | Stuck-ceremony reminders | Automatic escalating reminders at day marks while unsigned/unaccepted | TAKE the escalating-marks track for certified-but-unsent / ready-but-uncertified; ADAPT marks to deadline-relative; REJECT any auto-execution at any mark |
| LoanBoss (https://www.loanboss.com/notifications) | Domain critical-date taxonomy | Reminders span covenant tests, extension notice windows, maturity events | ADAPT: the Deadline row type must admit notice-window and one-time kinds, not only reporting packages; REJECT rates/hedging breadth |
| Finley (https://www.finleycms.com/blog/product-deep-dive-credit-facility-deliverable-tracking) | Agreement→deliverable decomposition | Credit agreements decomposed into due-date schedule + deliverable templates; early warnings before violation | TAKE the decomposition framing (it is RequirementRecord→Deadline exactly); early-warning trajectory belongs to Findings (agent brief), not this ladder |
| Google Calendar month grid (support threads, R5) | Anti-pattern control | "+N more" truncation; workload invisible when heaviest | REJECT month-grid-as-primary; deadlines cluster by construction (Q+45d stacks the book onto one fortnight) — the agenda + ladder is the primary surface (calendar-obligations.md) |

## 8. Ownership boundaries (what this file does NOT own)

- **Calendar surface anatomy** (agenda groups, month heat-strip proposal D-3, right-rail cycle summary) — `surface-briefs/calendar-obligations.md`. Calendar owns rendering Deadline rows; this file owns the rows.
- **Inbox row anatomy and item-level states** (new/seen/snoozed rendering, J/K/S/Shift+D keys) — `surface-briefs/inbox.md`. This file owns the query the register renders and the snooze semantics.
- **Chase drafting and approval** — `surface-briefs/intake-holding.md` (chases live on checklist items); this file owns only the `chased` ladder guard.
- **Findings** (headroom trends, watch-band entries) — `surface-briefs/agent-ask-watch-memory.md`. A Finding is a your-move class-7 item once raised; its triggers are not deadline machinery.
- **The escalation policy editor UI** — `surface-briefs/settings.md`; this file owns the policy schema and its evaluation semantics.

## 9. Build target (basis-v2)

| Piece | Address | Status |
|---|---|---|
| Due-rule engine F5 (`materializeDeadlines`, `evaluateLadder`) | `src/lib/covenant/deadlines.ts` (pure, test-covered) | NEW |
| Your-move query | `src/lib/covenant/yourMove.ts` | NEW |
| Deadline/Notification store (02 §2 `noteId` rows + ladder history) | migration + store alongside the period ledger | NEW |
| `DeadlineChip` (date + rule citation + ladder treatment) | `src/components/covenant/deadlines/DeadlineChip.tsx` | NEW; consumed by Calendar, Home, Inbox, period headers, Loans register |
| `EscalationBanner` (overdue/your-move pinned treatment) | `src/components/covenant/deadlines/EscalationBanner.tsx` | NEW |
| `CountBadge` | existing consolidated atom (snapshot §5) | REUSE — fed by `yourMove()` only |
| `NotificationDelivery` service | `src/lib/covenant/notify.ts` | NEW; renders transitions, computes nothing |
| Escalation policy editor | `/settings/notifications` | NEW per settings.md |

Tokens: ladder treatments consume the status vocabulary and gray-ladder/accent tokens only (`src/styles/covenant-tokens.css`); zero new colors. Icons Lucide-only. Deadline dates in running copy are proportional type; dates in table columns align with `tabular-nums` per the tables law.

## 10. States

| State | Behavior |
|---|---|
| No confirmed schedule (new loan) | Calendar shows "schedule unconfirmed — confirm in Setup" (itself a class-4 your-move item); zero guessed deadlines |
| Awaiting documents | `chased` sub-loop renders chase history on the checklist item; the deadline row shows honest input-state ("waiting on T-12 — chased {date}") |
| Requirement amended mid-flight | In-flight row keeps its version's date + "amended — applies from {period}" note (§2.5) |
| Deadline waived/withdrawn | Terminal `withdrawn` with the typed act cited; history readable forever |
| Snoozed | Hidden from your-move renders; auto-returns at time OR activity; overdue pierces |
| Certified then voided | Void-on-change returns the period to in-review; the ladder re-evaluates (a voided certification can re-ripen your-move); the void event is cited on the row |
| Sealed period | `met` rows read-only forever, incl. late-met honesty note; escalation history intact |
| Permission-limited viewer | Sees counts only for loans in scope; policy editor invisible (not disabled) to non-admins |
| Engine tick failed / stale evaluation | Renders last-evaluated timestamp honestly ("as of {time}"); never a silently stale badge |
| Push channel unconfigured (v1) | Policy editor shows push rungs as "email — roadmap"; no dead toggles pretending to work |

## 11. Acceptance tests (ticket-ready; fixtures: Calloway Park FYE-2018 spine + Bexley canon)

1. **Materialization + provenance.** Given Calloway Park's confirmed schedule (evidence: SLOT-3 §8.02(b)), a Q2 occurrence (period end 06-30) materializes a Deadline with `lender_deadline` = 08-14 rendered "Q+45d per §8.02(b)(1)"; the annual occurrence (FYE 12-31) yields 04-30 rendered "FYE+120d per §8.02(b)(2)". Clicking either citation lights the exact clause region in Evidence via `traceToEvidence` (stays lit).
2. **No guessing.** A loan with its schedule un-confirmed produces zero Deadline rows and one class-4 your-move item; no surface renders a guessed date.
3. **Separate rows, one governing date.** The Q2 occurrence yields distinct rows for the §8.02(b)(1) statement and the §8.02(b)(3) rent schedule, both 08-14; the period header shows one governing date; meeting one row does not retire the other.
4. **Versioned rules.** Amend the quarterly rule 45d→30d mid-flight: the open Q2 row keeps 08-14 (with version note); the Q3 occurrence materializes at Q+30d. (Vanta pattern, R5.)
5. **Ladder determinism.** Fixture clock walks Bexley's Q2 row through scheduled → reminded (internal_due − remind_at) → your-move (threshold) → overdue (lender_deadline + 1d, no SendRecord); every transition has an ActivityEvent; re-running the tick on the same inputs is idempotent.
6. **Dual-date containment.** The internal prep date renders on Calendar/Home only; grep-level assertion that send-panel copy, correspondence drafts, and outbound bundle metadata contain `lender_deadline` only.
7. **Dedup law.** Bexley Q2 READY with deadline inside the your-move window = ONE item (certify-ready, decorated with proximity), not two; rail badge, Home group count, and Inbox row count all agree (frame test 08 §10.6).
8. **Snooze-until-time-or-activity.** Snoozing a class-3 arrival decision hides it; a new attachment on the same arrival returns it before the snooze time; flipping its deadline to overdue returns it regardless (pierce).
9. **Push firewall.** With policy push enabled, a full simulated cycle (arrivals auto-filed, extractions confirmed, drafts prepared, chases sent) generates ZERO push events until a row enters overdue; exactly one push fires, to policy roles only, containing no figures and no resident data; delivery is idempotent per transition event.
10. **Certified-but-unsent.** Certify Bexley Q2 without sending: reminder events fire at the policy marks (Carta pattern, R4), each quiet-logged; no auto-send exists on any path (API assertion: send requires an authenticated human session + typed act — R4 synthesis 12).
11. **Void re-ripening.** Certify, then change an input feeding a package field: the certification voids, the period returns to in-review, and the ladder re-raises the your-move item citing the void event.
12. **Done-by-action only.** Sending the package flips the row to `met` and clears the your-move item; no mark-as-done affordance exists anywhere in Inbox/Home for classes 1–9.
13. **Quiet-log integrity.** Every ladder transition, chase, snooze, policy edit, and delivery appears in the append-only ActivityEvent register with actor identity; completed autonomous work never appears in any push or badge count.
