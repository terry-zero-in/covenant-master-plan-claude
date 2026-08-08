# LENDER Q&A / CORRESPONDENCE

The follow-up surface: after a package ships, the lender or servicer writes back — a question about a figure, a request for a schedule, the recurring quarterly ask. Threads live per loan, tied to loan+period; the intake pipeline classifies correspondence-vs-document on arrival; the agent drafts replies grounded ONLY in the sealed record and filed documents with lit-row citations; ungroundable questions flag "needs your input" instead of inventing; outbound is ALWAYS human-approved (external-facing law, 04 §1 follow-up row). This is workflow step 14 (03 §1) and the payoff of seal-not-wipe: "forever after, the sealed period answers lender follow-ups with exact provenance" (06 chain E).

## 1. Identity and verdict

- Product layer: reporting spine (inventory C8, 07 §1).
- Route/address in the basis-v2 repository: `/loans/[loanId]/correspondence` (proposed; loan-scoped with a period filter `?period=`), in the `(covenant)` route group, mounting `CovenantShell`. Threads deep-link as `/loans/[loanId]/correspondence/[threadId]`.
- Current build state: `ABSENT` (07 C8).
- Existing-surface verdict: `UNBUILT`.
- Research tier: B.
- Primary users/roles: owner (approves and sends outbound — the external-facing act); PMC preparer (reviews/edits drafts; approval right role-configurable, default reserved per the engagement, 02 §4); reviewer read-only.
- Frequency and session duration: episodic, clustering after sends and around the servicer's quarterly cadence (the recurring ask is real: the servicer's quarterly Property Questionnaire — evidence: SLOT-5); sessions 2–15 minutes; most sessions are one approval.
- Error cost: HIGH — this surface produces outbound external communication to the lender. A wrong figure told to a lender in prose is the terminal error's next-door neighbor. The defenses are structural: drafts ground only in sealed/filed material, every figure carries a citation, figures interpolate by reference (04 §2.1), and nothing leaves without a typed human approval.
- Success criterion: a lender question is answered in minutes from the sealed record with zero invented figures; recurring questions arrive pre-answered from lender-scope memory with provenance; the borrower's act shrinks to read → approve.

## 2. User job and decisions

- Primary job-to-be-done: "answer the lender accurately, fast, from what we actually sent — and stop re-answering the same questions."
- Decisions made here: approve / edit-then-approve / reject a drafted reply; supply the missing input on a "needs your input" block; close a thread with a reason; snooze to awaiting-lender; correct a remembered answer (versions the MemoryEntry); re-route a mis-matched thread to the right loan/period.
- Questions the surface must answer in scan order:
  1. Which threads need my move? (drafts awaiting approval, needs-your-input blocks)
  2. What did the lender ask, verbatim?
  3. What does the draft say, and is every figure cited?
  4. Which loan+period does this concern, and what state is that period in?
  5. Have we answered this before? (recurring-question memory, with provenance)
  6. What's still waiting on the lender?
- What the user should not have to decide here: whether an arrival is a document or correspondence (intake's classifier decides; ambiguity resolves in the Intake queue); what a sealed figure is (engine reads only); covenant verdicts (computed elsewhere; summarized in the context rail); chase cadence (Intake/holding machinery).

**The recurring-ask evidence (why memory is this surface's second pillar):** the servicer's quarterly Property Questionnaire is a real, cadenced, repeating interrogation — 11 questions every quarter, including occupancy by month-end, capital improvements by category, subordinate financing, and the management-fee % with its 4–6% band explanation (evidence: SLOT-5). The loan agreement itself contract-shapes ad-hoc asks: §8.02(b)(4) on-request items are rate-capped at once per 6 months absent default (evidence: SLOT-3). Lender questions therefore repeat by construction — quarter after quarter, the same asks return — which is exactly what lender-scope ask-once memory exists to absorb (03 §3's lender-scope row uses the management-fee question as its canonical example). The questionnaire form itself is a Composer deliverable; the conversations around it land here; both draw from the one memory store.
- Entry paths: Home YOUR MOVE "Drafts awaiting approval" rows (deep link); Inbox rows (same query, full register); Loan Detail CORRESPONDENCE tab (summaries → threads); the quiet log entry "inbound logged; draft prepared"; command palette (thread as an Object result); an intake arrival classified correspondence auto-materializes the thread and logs it.
- Exit paths: citation chip → Evidence (lit-row) → deeper to DocView or the sealed period record (read-only Review/Certificate render via Reports); context-rail links → `/covenant/[loanId]/[period]/*` sealed views; "correct this memory" → the same inline inspector Settings→Agent uses.
- Completion/advancement conditions: threads never advance a period (the period is typically sealed). A thread completes when closed-with-reason; the your-move count decrements only when the owning act happens here (approve/send or close) — no mark-as-done that hides work (C-8 semantics).

## 3. Object and ownership model

| Object/action/status | Owned here? | Summary/link elsewhere | Address/identity | Notes |
|---|---:|---|---|---|
| CorrespondenceThread | YES | Loan Detail CORRESPONDENCE tab summarizes open threads; Home counts your-move drafts (02 §3) | `threadId` (loan FK, period FK) | Thread status machine below |
| Message (inbound/outbound) | YES | Quiet log records arrival + draft-prepared events | `messageId`, direction, artifact refs | Inbound original bytes are a Document artifact (immutable); the thread renders them |
| Draft reply + citations | YES | — | `draftId` per message | Agent-authored; figures by reference, never by generation |
| Outbound approval + send record | YES (the act happens here) | Reports does NOT list these (package sends only); the thread renders the receipt | append-only `OutboundMessageRecord {who, when, contentHash, transport, recipient}` | Mirrors the SendRecord grammar (02 §6) at message scope |
| Arrival classification (correspondence-vs-document) | NO | Thread header shows "arrived via intake {date} · classified correspondence ({confidence})" with link | — | Intake owns arrivals and ambiguity (02 §3); only classified correspondence materializes here |
| Filed documents an inbound email carried | NO | Attachment chips link to Documents canon | `docId` | Mixed emails split: attachment → document lane, body → thread; cross-linked both ways |
| Sealed period record (grounding source) | NO | Context rail summarizes verdicts/package with links | `(loanId, period)` | Read-only forever (seal-not-wipe) |
| TestResult verdicts | NO | Context rail renders verdict chips with basis badges as summaries | `(loanId, period, reqId)` | Single source: the engine (07 §2 duplication fix) |
| MemoryEntry (lender scope) | NO (one store, agent layer) | Rendered inline here beside the question it answers; central inspector in Settings→Agent | `memId`, scope=lender | Ask-once law; corrections version (03 §3) |
| Chase messages to internal senders | NO | — | — | Chases are Intake/holding machinery, not lender correspondence — different audience, different owner |

No-double-homing boundary, stated: Intake owns the arrival decision; Documents owns any attached file; the engine owns every figure; the agent layer owns memory (one store). This surface owns the thread — its messages, drafts, approvals, outbound records, and status — and nothing else. Every other appearance of a thread (Home, Inbox, Loan Detail, quiet log) is a summary with a link.

## 4. Data and semantic model

- Source facts: inbound message bytes (email via the designated intake address, immutable Original artifact + hash), sender identity, received timestamp; the sealed period's package contents; filed documents.
- Extracted values awaiting confirmation: the classifier's correspondence-vs-document call with confidence (high-confidence auto-routes; ambiguity sits in Intake, never here); proposed loan+period match for the thread (sender ↔ known lender/servicer contacts, content ↔ loan identity — same recognition evidence grammar as Intake).
- Confirmed values: thread↔loan+period binding (auto at high confidence, quiet-logged with re-route undo; human-confirmed when ambiguous); remembered answers (lender-scope MemoryEntries, versioned, provenance-stamped).
- Deterministic outputs: engine reads quoted in drafts — sealed package figures, TestResult verdicts with `basis_echo`, checklist/send-record facts. All read-only here; recompute never happens on sealed records (they are frozen truth).
- Agent proposals/drafts: the reply draft — prose + figure references + citation refs + zero or more "needs your input" blocks, each naming exactly what grounding is missing ("I don't have a certified Q2 package to answer from" — refusal grammar per the agent brief); recurring-question match proposals ("this matches the question answered in Q1 and Q2 — remembered answer v2").
- Draft lifecycle (versioned):
  1. `drafting` — agent assembling from the grounding set; the inbound renders immediately, the draft slot shows the preparing state.
  2. `draft-ready` — grounded portions complete; any ungroundable slots hold NeedsInputBlocks; thread enters your-move.
  3. `edited` — human edits version the draft; figure tokens stay live references; edit diffs feed outcome learning (diff classes per the agent brief).
  4. `approved` — the typed act freezes text + hash; the draft becomes the outbound message.
  5. `returned` — sent back to the analyst with a note; agent re-drafts as a new version with the note as context.
- Human decisions: approve/edit/reject the draft (typed approve-and-send act); supply missing inputs; close-with-reason; snooze; memory corrections; re-route.
- Certified values: never created here. Drafts cite certified/sealed values with their hashes intact; the three-actor class of every quoted figure renders at point of use (06 §7).
- Versions/periods/packages: a thread binds one loan and one period (the period chip); a lender email spanning periods spawns sibling threads cross-linked ("also asked about Q1 →") so period identity never blurs. Draft edits version; the approved outbound freezes the final text + hash.
- Evidence/provenance: every cited figure opens the full 06 §1 chain — figure → engine read → package field → normalized row → source region → document identity → certification/send history. Citations in outbound text render as plain references (e.g. "per the certified Q2 statement"); the chain itself never leaves the app.
- Permissions/read-only projections: reviewer reads everything, approves nothing; PMC preparer drafts/edits, approval role-gated; resident-level data structurally withheld from outbound (§5, §16).

Grain map: organization → client → loan (the route grain) → thread → message → draft → outbound record; period is the thread's binding FK; lender/servicer identity is the memory scope.

## 5. State machine and exceptions

Thread status model — Plain's three-state queue with reason-coded substates, plus auto-reopen (R1 research: Plain):

| State (group) | Substates | Trigger in | Trigger out | Actor | Reversible? | Audit |
|---|---|---|---|---|---|---|
| OPEN (todo) | agent-drafting · draft-awaiting-approval (your-move) · needs-your-input (your-move) | Inbound classified correspondence; auto-reopen | Approve-and-send; close | Agent drafts; human acts | Draft edits yes; send no | Every transition an ActivityEvent |
| AWAITING-LENDER (snoozed) | replied-awaiting-response · snoozed-until-date | Outbound sent; manual snooze | **Auto-reopen: any inbound reply on the thread flips it back to OPEN** — external activity resurfaces parked work without human vigilance (R1 research: Plain) | Engine | Yes | Logged |
| CLOSED (done) | resolved · no-response-needed · withdrawn-by-lender | Close-with-reason (typed) | Auto-reopen on reply (returns OPEN with full history) | Human closes; engine reopens | Yes (reopen) | Close reason recorded |

Full exception coverage:

- empty: no threads for this loan — "No correspondence yet. Lender emails to your intake address thread here automatically once a package has shipped." No fake sample threads.
- awaiting documents (holding): not a thread state — but a draft may be blocked on a document ("the lender asks for the annual ENERGY STAR report — not yet filed"); renders as a needs-your-input block linking the checklist item (evidence: Form 6241 rider makes such an ask real for a Green-rider loan).
- on-request obligation bridge: when an inbound is a contract-shaped on-request item (e.g. §8.02(b)(4)'s monthly statements / leasing report / ownership statement, rate-capped once per 6 months — evidence: SLOT-3), the thread does not become the deliverable. The recognition proposes a one-time checklist item on the loan (owned by Intake/Holding, deadlines by Calendar); the thread cross-links it and the reply cites its status. The rate-cap context renders beside the ask ("last on-request item: {date}") as an engine read — informational, never auto-refusing; whether to push back on an over-cap request is the human's judgment.
- loading/processing: inbound arrived, classifier/draft in flight — thread shows the inbound immediately with "the analyst is preparing a draft" (no invisible latency); draft appears when grounded.
- partial/incomplete: a draft with some grounded answers and some needs-your-input blocks — ships to approval only as a whole; the blocks render in-place so the human answers inline.
- extracted/unconfirmed: thread↔loan+period match below confidence → the binding renders as proposed with the recognition evidence; confirming is one click; mis-matches re-route (below).
- stale/superseded: a cited source is superseded after drafting (e.g. a document replaced) → the draft flags "re-ground required," affected citations marked stale, agent re-drafts; never silently sends stale citations. Note: sealed package figures cannot go stale (sealed = frozen); staleness applies to drafts citing pre-seal material or filed documents.
- low confidence: classification ambiguity lives in Intake, not here; a thread only exists post-classification. The thread header always discloses classification confidence + link to the arrival record.
- conflict: thread bound to the wrong loan/period → "re-route" action (typed, with reason); the thread moves, both loans' quiet logs record it; memory entries do not travel on re-route (scope integrity).
- misclassification recovery (both directions):
  - A document wrongly threaded as correspondence → "this is a document" action re-routes it through the filing lane; the thread annotates "re-classified and filed → {doc}" and closes with reason.
  - Correspondence wrongly filed as a document → surfaced from the Documents side (its brief); the correction materializes the thread here with the original arrival timestamp preserved.
  - Every correction teaches the classifier (correction anchored to the arrival that was wrong — the LangSmith write-path pattern, R6 research).
- failed tie-out: n/a as owned state; if a lender's question exposes a discrepancy in a sealed package, the draft cannot paper over it — the agent flags "this question concerns a discrepancy — needs your input," and any correction path runs through the owning surfaces (a sealed period never mutates; a corrective letter is a new outbound, and a corrected FUTURE package is the remedy).
- watch/shortfall/breach: verdicts render in the context rail as engine summaries with basis badges; drafts quoting a verdict inherit the structural vocabulary — `basis=monitored` can never print "breach" in a draft (05 §3's basis_echo makes the violation impossible; canon: Bexley 268/301 = 89.04% quotes as a SHORTFALL against a monitored 90% floor).
- permission denied: reviewer sees threads read-only, approve/send affordances absent (not disabled — invisible, R6 research: GitHub); cross-Client threads never render in PMC mode.
- read-only: closed threads and all sealed-record citations; the thread history is append-only.
- blocked/gated: outbound is gated — no draft leaves without the typed approval act; a draft whose every figure is uncited cannot be approved until each figure is cited or explicitly marked "manual assertion by {approver}" (the lint, §12).
- certified (void-on-change): not applicable to threads; cited certification records render with their void state if a cert was voided post-send ("cited package was certified {date}; cert later voided — see void notice") — honesty even in history.
- sent/sealed: an approved outbound is append-only and immutable — no unsend, no edit-after-send by design; the receipt (who, when, hash, transport, recipient) renders in-thread permanently.
- recovery/undo: draft edits are freely undoable; approval has a confirm step; after send the only recovery is a follow-up message (the surface offers "send a correction" which quotes the prior outbound) — undo of an external communication is impossible by design, and the UI says so at the gate.

## 6. Engine / Agent / Human / Gate / Quiet Log

The 04 §1 follow-up row, expanded phase by phase:

| Phase | ENGINE (deterministic) | AGENT (prepares/proposes) | HUMAN (typed decisions) | GATE (interrupts) | QUIET LOG |
|---|---|---|---|---|---|
| Arrival & threading | Content hash dedupe; sender validation; thread/loan/period linkage integrity | Classify correspondence-vs-document (in intake); match to loan+period with evidence + confidence | Resolve ambiguous classification/binding (in Intake); confirm proposed bindings here | Unroutable correspondence with deadline pressure escalates via Intake's gate | Inbound logged; classification + binding recorded |
| Drafting | Grounding reads (sealed packages, TestResults, send/certification records, filed docs); citation-ref resolution at render time (figures by reference, 04 §2.1) | Compose the reply from the grounding set only; cite every figure; flag ungroundable asks as NeedsInputBlocks — never invent | Supply missing inputs; edit prose | A draft that would require resident-level data outbound blocks on policy | Draft prepared (version n); grounding set recorded |
| Memory | Storage integrity; provenance stamps; version chains | Detect recurring questions at lender scope; pre-fill with "learned {date} from {who}"; never re-ask | Correct/confirm remembered answers (versions the entry) | — (memory never gates, 04 §1) | Every reuse ("pre-filled from memory {ref}") |
| Approval & send | Outbound content hash; role/scope checks; append-only OutboundMessageRecord; honest transport failures | Pre-flight summary (recipient, ask, what the draft cites) | **The approve-and-send act — human only, ever** (external-facing law) | Every outbound reply — no auto-send tier exists at any confidence | The send receipt (loud in thread history, quiet in notifications) |
| Follow-through | Auto-reopen on inbound reply; substate transitions | Re-draft on return-to-analyst; note thread patterns for findings where material | Close-with-reason; snooze; re-route | — | Reopen/close/re-route events |

Lane invariants checked (04 §2): no shipping figure originates outside ENGINE — drafts interpolate engine values by reference, so an edited draft can never carry a stale or invented number into an outbound; the gate is a decision with named options, not an event; the agent's presence is the draft beside the question and the remembered answer beside the field — no chat chrome.

## 7. Information hierarchy

1. Page/frame header: shell chrome; breadcrumb `Covenant / {Loan} / Correspondence` (deal altitude, loan crumb is the switcher; the period crumb is absent by design because the surface spans periods — each thread carries its own period chip, satisfying the altitude gate's real test: every thread can say exactly which loan+period it serves, 08 §2).
2. Decision/status summary: thread-list group counts (Needs your move n · Awaiting lender n · Closed) — the your-move slice is the same computed query Home/Inbox render (one number everywhere, 08 §8).
3. Primary work region: the selected thread — inbound message(s), then the draft card.
4. Secondary context: the right rail — loan+period card, package links, recurring-question memory.
5. Evidence/proof: citations open the Evidence trace in the rail's second tab, beside the draft (never a modal).
6. Actions: approve-and-send (gated), edit, needs-input answers, close/snooze/re-route.
7. Activity/history: the thread IS its history (append-only messages + receipts); a collapsed per-thread event strip (classified, drafted, reopened) sits under the last message.

Absent by design:

- Charts — nothing here out-encodes text (chart doctrine).
- A compose-new-outbound-to-lender path in v1 — Covenant answers the lender's asks; unsolicited outreach is out of scope until ruled.
- Notification bells and response-time SLAs on the borrower (R1 research: Plain — rejected: arrivals are not owed a "first response" clock by a borrower team of 1–5).
- Any agent chat box — the agent's presence is the draft beside the question and the remembered answer beside the recurring ask (agent-presence law).
- Bulk/mail-merge outbound — every external message is an individual typed act by law.

## 8. Page anatomy and regions

- **Region A — thread list** (persistent left pane, 280–320px): grouped Needs your move / Awaiting lender / Closed (collapsed).
  - Row anatomy: lender/servicer name (line 1, medium weight), subject line (line 2, one line, truncating with title attr), then a chip row: `PeriodChip` ("Q2-2026"), `SubstateTag` (draft-awaiting-approval / needs-your-input / replied-awaiting-response / …), age.
  - Group headers carry the shared CountBadge (your-move count = the computed query, C-8 semantics).
  - Sort: within Needs your move, oldest-awaiting-approval first; within Awaiting lender, most-recent-outbound first; Closed collapsed by default.
  - Persistence: persistent pane. Why a pane: the queue must stay visible while working a thread (pick-next-without-navigating, the review-walk pattern); precedent: the ruled reconcile-room left pane. Collapse: to a 72px icon strip with group badges below 1440px expanded-rail, per §10.
- **Region B — thread view** (primary work window, flexible, min 560px): message stack, chronological:
  - Inbound message card: envelope (sender, to-address, received date), body rendered from the Recreated-searchable artifact (labeled derivative), attachment chips → Documents links, classification line ("arrived via intake {date} · classified correspondence · {confidence} · arrival record ↗").
  - Outbound message card (post-send): final text, approver stamp ("approved and sent by {who}, {when}"), receipt chip (hash short-form, transport, recipient; click = full record).
  - **Draft card** (the working object, bottom): agent draft with each figure rendered as a citation chip — the figure in Geist Mono + a provenance underline; chip click lights the source in the rail's Evidence tab. "Needs your input" blocks render inline where an answer would go: amber-free, standard exception treatment, each naming the missing grounding and offering the exact input control (text answer / pick a document / decline-to-answer note). Below: the edit affordance (full text editing; figures stay live references — deleting one is allowed, retyping a number by hand triggers the uncited-figure lint), then the approval bar.
- **Region C — context rail** (pinned right, 320–360px, two tabs):
  - CONTEXT tab, top to bottom:
    - loan+period card: period status chip, certified stamp (who/when), sent stamp (when/transport), "view sealed package →" (read-only render via Reports).
    - verdict summary strip: TestResult rows — requirement title, basis badge (covenanted|monitored, structural), verdict chip, signed headroom in both denominations — engine summaries linking to the sealed review; single source, never re-stated strings (07 §2 duplication fix).
    - related items: the checklist item a request maps to, prior threads on this topic, sibling-period threads.
    - recurring-question memory panel: "This question has been asked 3 times (Q1, Q2, Q3) · remembered answer v2 · learned {date} from {who} during {context}" with inspect/correct inline (the correction versions the entry — one store, 03 §3).
  - EVIDENCE tab: the lit-row trace for the clicked citation; auto-switches on chip click, back-link returns to CONTEXT. Why tabs in one rail: citations and context are never needed simultaneously (the citation IS the context zoom), and this keeps the surface at one work window + one inspector pane (pane law).
- **Approval bar** (bottom of draft card): "Approve and send" primary control → typed-act modal (allowed: legal weight): restates recipient, loan+period, the outbound content hash, lists any manual-assertion figures for explicit acknowledgment, requires the deliberate confirmation; writes the OutboundMessageRecord. Secondary: "Return to analyst" (with note → agent re-drafts), "Close without reply" (reason required).
- Modals: exactly one — the approve-and-send typed act. Everything else opens beside.

## 9. Co-visibility matrix

| Datum/surface A | Datum/surface B | Must be simultaneous? | User decision enabled | Default topology | Fallback |
|---|---|---:|---|---|---|
| Lender's question (inbound) | The draft reply | YES | "Does this draft answer what was asked?" | Same column, stacked in Region B (question above draft) | Always stacked; never separated |
| Draft figure (citation chip) | Its source line | On demand | "Is this figure real?" | Evidence tab lights beside (Region C) | Overlay sheet <1440px |
| Draft | The period's verdicts/package state | YES during approval | "Is the framing consistent with what we sent?" | CONTEXT tab pinned beside | Summonable overlay |
| Thread | The thread queue | YES | Pick next without losing place | Region A persistent | Collapses to 72px icon strip, groups as badges |
| Recurring question | The remembered answer + provenance | YES when detected | "Reuse or correct?" | Memory panel in CONTEXT tab, beside the draft that used it | Same |
| Inbound attachment | Documents canon | No | Deeper audit | Chip link with return path | — |
| This thread | A sibling-period thread | No | Cross-period asks | Cross-link chip navigates | — |
| Correction draft | The prior outbound it corrects | YES during correction | "What exactly am I correcting?" | Prior outbound quoted inside the new draft card | Same |

No pane exists merely because information exists: prior threads render as links, not a fourth column.

## 10. Layouts and viewport behavior

| Viewport | Rail | Work area | Topology (ratios) | Notes |
|---|---|---|---|---|
| 1440px | 240px or collapsed 48px | ~1200–1392px | A 280px + B 640–792px + C 320px | All three fit at collapsed rail; at expanded rail C summons over B (pinned-on-toggle), honoring B ≥ 560px — no silent compression |
| 1728px | 240–280px | ~1448–1488px | A 300px + B 812–868px + C 336px | Default full topology; Evidence tab text comfortable |
| 2048px | 280px | ~1768px | A 320px + B 1088px + C 360px | Draft card max-width ~760px centered in B for measure; message cards likewise |
| <1280px | icon rail 48px | remainder | List⇄thread stack navigation (A becomes the screen; opening a thread replaces it with B; back returns); C becomes an overlay sheet | Spine-surface operability preserved; approval modal full-screen sheet |

- Focus behavior: selecting a thread focuses B; `F` expands B full-width (A collapses to icons, C stays summonable) for long drafts.
- Compare behavior: none native; "view sealed package" navigates with return path (peer compare belongs to Review).
- Proof/source behavior: citation click → C's Evidence tab lights the row; at narrow widths an overlay sheet from the right; the lit line stays lit until dismissed (frame contract, 08 §7).
- Minimum viable dimensions: operable at the frame minimum 1152×720 in stacked mode; the approve modal always fits (it is text, not layout).
- Tab stacking: C's two tabs are the stacking mechanism by design; below 1280px A/B/C become sequential screens — labeled, never squeezed.

## 11. Components and exact anatomy

- `CovenantShell` — REUSE (route mounts it; loan crumb switcher per 08 §2).
- `ThreadList` — NEW. Parts:
  - `ThreadGroupHeader` — label + shared CountBadge (REUSE the consolidated CountBadge atom, snapshot §4).
  - `ThreadRow` — lender, subject, `PeriodChip`, `SubstateTag`, age; keyboard cursor; selected state from the existing control layer.
  - `ListFilterCluster` — period / lender / substate chips (REUSE the shared filter primitives).
- `ThreadView` — NEW. Parts:
  - `MessageCard` — variants inbound/outbound; envelope block (sender, to-address, received/sent date); artifact-labeled body render (REUSE DocView's Recreated-searchable renderer for message bodies — "Recreated · searchable" label included); attachment chips → Documents links; classification line with confidence + arrival-record link.
  - `ThreadEventStrip` — collapsed classified/drafted/reopened/re-routed events (a filtered ActivityEvent view — one store, 07 §2).
  - `SiblingThreadLink` — cross-period chip ("also asked about Q1 →").
  - `InternalNote` — annotations that never send; visually separated from outbound text (distinct background from the gray ladder + "internal — never sent" label).
- `DraftCard` — NEW, the core object:
  - `CitationChip` — figure in Geist Mono `tabular-nums slashed-zero` + dotted provenance underline + basis badge where the figure is a verdict; click → Evidence tab lit; hover → peek popover of the source region (R2 research: Hebbia peek-before-jump).
  - `NeedsInputBlock` — names the missing grounding verbatim ("no certified Q2 package exists to answer from"; "resident-level data is withheld from outbound by policy — decide how to respond"), plus the exact input control and a "learned answers are remembered" note when the answer will mint a MemoryEntry.
  - `UncitedFigureLint` — inline flag on hand-typed numerals: "uncited figure — cite a source or mark as your manual assertion"; manual assertions are named in the approval modal.
  - `DraftEditor` — plain rich-text editing; figure references are atomic tokens (deletable, not editable in place — editing a figure routes to "this number comes from the engine; correct it at its source or replace the citation").
- `ApprovalBar` + `ApproveSendModal` — NEW: the typed act per §8; consumes the standard modal primitives; the confirm control uses the ruled accent family (this is not the certify ceremony — the certified sheet's #33419D paper hexes stay on the certificate surface; here the standard accent #7189FF family applies).
- `OutboundReceipt` — NEW: who/when/hash/transport/recipient; click expands the full append-only record. Mirrors the send-record receipt grammar (send-record brief) at message scope.
- `ContextRail` — NEW: `LoanPeriodCard` (REUSE period status chip components), `VerdictSummaryStrip` (REUSE the covenant strip row rendering from Loan Detail — TestResult rows with basis badges; summaries, single source), `MemoryPanel` (REUSE the inline memory-render component from the agent layer: "learned {date} from {who}" + inspect/correct), `RelatedItems`.
- Evidence panel — REUSE the app-wide Evidence component with the repaired lit-row trace (U1-F1 fix precedes adoption).
- `ReRouteControl` — NEW: typed re-route with reason; shows recognition evidence for the proposed correct binding.
- `CloseThreadControl` — NEW: reason-coded close (resolved / no-response-needed / withdrawn).
- Empty/error/recovery objects — REUSE app-wide empty state; classifier-latency state; honest failure states for the outbound transport mirroring the send vertical's grammar (503 config with admin fix path, 403 role deny with why, 404 scope — the same honest-failure law).
- Saved views/filters — REUSE shared mechanism (filter by period, lender, substate).

## 12. Interaction specification

- Selection: single thread selection (list cursor); within B, citation chips and needs-input blocks are the focusable atoms.
- Hover: citation chips peek their source region; memory entries reveal full provenance; receipt chips reveal full hash.
- Focus: A → B → C order; the draft card traps focus only inside the approve modal.
- Keyboard:

| Key | Action |
|---|---|
| J / K | Next/previous thread in list |
| Enter | Open thread (focus B) |
| N | Jump to next needs-your-input block or uncited-figure lint in the draft |
| E | Edit draft (focus editor) |
| A | Open approve-and-send modal (never sends directly) |
| S | Snooze → awaiting-lender (with date option) |
| C | Close-with-reason |
| R | Return to analyst with note |
| ⌘↵ | Confirm inside the typed-act modal only |
| Esc | Close Evidence/peek → exit editor → deselect (never sends, never navigates as a side effect) |
| ⌘K / G-chords | Palette and global navigation (08 §5); threads surface as Object results |

- Editing and validation:
  - Draft prose freely editable; edits autosave and version.
  - Figure tokens are atomic — deletable, not editable in place; attempting to edit one explains "this number comes from the engine" and offers replace-citation or remove.
  - The lint runs on save and before approval: uncited hand-typed numerals, resident-identifier patterns, and stale citations each produce a named flag.
  - The approval modal enumerates every manual assertion and lint override for explicit acknowledgment; acknowledgments enter the OutboundMessageRecord.
  - NeedsInputBlocks validate their input type (text answer / document pick / decline note) and offer memory capture where the answer is reusable.
- Bulk action: none on outbound (each send is an individual typed act, by law); bulk close of stale never-answered threads allowed with one shared reason, quiet-logged per thread.
- Undo/recovery: draft edit history undoable; close/snooze reversible; re-route reversible (re-route back, both logged); send irreversible by design — the modal says "This sends externally. It cannot be unsent."
- Sorting/filtering: period filter (`?period=`), lender filter, substate filter; groups always preserve the your-move-first order; the register is complete or says what's filtered (no silent truncation — the U3-C1 class fix).
- Drill-down and return path: every outbound link (DocView, sealed period, Reports) carries a return crumb to the thread.
- Source-linked selection: the lit-row behavior on every citation (frame contract); the lit line stays lit while the user edits the adjacent draft text.
- Save/persistence: drafts autosave with versions; approval state persists across sessions (a period can wait on its human indefinitely without decay — R6 research: Temporal's waiting-on-a-named-human posture).
- Collaboration/commenting:
  - "Return to analyst" notes attach to the draft version they critique; the agent's re-draft references the note.
  - `InternalNote` annotations thread beside messages — never sent, structurally excluded from the outbound composer, visually separated (§11).
  - PMC preparer ↔ owner handoff is the draft's awaiting-approval state itself — no separate assignment machinery for a 1–5 person team (R1 research: Instabase's reviewer-workforce apparatus rejected).
  - Reviewer role may read and annotate internally where permitted, never approve.

## 13. Visual craft direction

- Typography: message bodies at the reading size on the light document-adjacent surface DocView uses for derivatives; UI chrome in the app sans; all figures in Geist Mono `tabular-nums slashed-zero`.
- Financial-number treatment: citation chips render figure + unit tight, mono; signed headroom in the verdict strip keeps its dual denomination ("+0.96pp · $—" style per engine output) — never rounded prose.
- Spacing rhythm: 8px grid; message cards separated by 24px open ground; the draft card gets 32px isolation above (it is the work object).
- Density: threads list comfortable (44px rows, uniform); message stack is reading-density, not register-density.
- Open ground vs earned boundaries: open-not-boxed — message cards separated by whitespace + a hairline only between inbound and draft; the draft card earns a soft raised treatment from the adopted gray ladder (the one elevation on the page).
- Dividers/elevation: rail seam hairline; Evidence tab uses the Evidence panel's existing elevation; the approve modal is the standard modal elevation.
- Semantic color: accent family for interactive/selected/focus (#7189FF; hover #8EA1FF; active #6078F4); verdict chips use the app's existing severity treatment (severity-only color law) with basis badges in neutral grays; needs-input blocks use the standard exception treatment; zero new colors (settled law).
- Certified-sheet treatment: NOT used here — cited certified figures carry the certified badge grammar (hash chip), but the blue-violet paper hexes (#E7EBF8/#DDE4FB/#B3BCDA/#33419D/#5265C5/#121459) paint only the certificate surface's sheet; this brief consumes none of them, deliberately.
- Focus/selected/hover states: consume the existing Covenant control layer (Ruling-J: it exists, ported, use it).
- Chart style: no charts (absent by design, §7).
- Internal-vs-outbound legibility: internal notes and outbound text must be distinguishable at a glance and by structure — different ground (gray-ladder step), a persistent "internal — never sent" label, and exclusion from the approval hash; a screenshot of the thread can never confuse the two.
- Motion: draft-appears and auto-reopen transitions at standard short duration; reduced-motion collapses to instant; no attention-seeking animation on drafts (quiet law).
- Long-session ergonomics: n/a-short sessions, but reading measure capped ~760px in B at wide viewports.

## 14. Benchmark research and synthesis

| Product | Limited role | Official evidence | Exact pattern to take | Covenant adaptation | What to reject | Why this reference beats alternatives |
|---|---|---|---|---|---|---|
| Plain | Queue lifecycle | https://help.plain.com/article/statuses · https://www.plain.com/changelog/new-support-queue-statuses-in-plain (R1 research) | Exactly three status groups (Todo/Snoozed/Done) with reason-coded substates; **auto-reopen on customer reply** | The §5 thread machine verbatim-adapted: OPEN/AWAITING-LENDER/CLOSED; a lender reply resurfaces a parked thread with zero human vigilance | Response-time SLAs and tiering — a borrower team owes accuracy, not a first-response clock | The minimal state set proven in production support; every added state elsewhere is queue noise |
| Front | Draft-in-thread approval | https://help.front.com/en/articles/2076 (R1 research) | Rules produce drafts that wait inside the conversation for a human to finish | The agent's draft sits in-thread awaiting typed approval — the draft IS the agent's presence (law 10) | The entire shared-inbox model: human-touch-everything, archive-as-disposition, notification fan-out (R1's named anti-pattern) | It is the living example of machine-drafts-human-sends inside a thread, which is exactly this surface's gate shape |
| Linear | Snooze + your-move semantics | https://linear.app/docs/inbox (R1/R5 research) | Snooze until a chosen time OR new activity, whichever first; badge = actionable count, never unread | AWAITING-LENDER = snoozed-until-activity; the thread-list badge renders the same computed your-move query as Home/Inbox (C-8 fix) | Personal-notification framing for arrivals | Snooze-until-activity is the exact awaiting-lender mechanic; one query everywhere kills badge drift |
| Hebbia | Citation-first answers | https://www.hebbia.com/blog/introducing-matrix-the-interface-to-agi (R2 research) | Every generated output citation-linked to the exact source location; peek previews before jumping | Every draft figure is a CitationChip with peek + lit-row click-through; "fully sourced" is the draft's default posture and the hallucination control | Free-prose cells as stored records | The strongest live example of citation-first AI output as an auditability requirement, not decoration |
| LangSmith | Correction capture | https://docs.langchain.com/langsmith/annotate-traces-inline (R6 research) | Corrections anchored to the exact step that was wrong, storing the "should have been" value | An edited draft writes the diff as learning input; a corrected remembered answer versions the MemoryEntry with the correction anchored to the question it mis-answered | Token/latency metrics in user-facing UI | It is the precise write-path model for ask-once memory and outcome learning |
| Digits | Confidence-gated queue posture | https://digits.com (R6 research) | Agent output surfaces as a reviewable queue with named blocking statuses — never a chatbox | The correspondence queue: high-confidence classification routes quietly, drafts queue for approval, "needs your input" is a named blocking status | Auto-commit — Digits books entries autonomously; Covenant's outbound NEVER auto-sends (the gate moved one notch safer, per R6's own synthesis) | Closest live analog of agent-prepares/human-releases in a finance domain |
| ChatGPT memory | Memory transparency | https://help.openai.com/en/articles/8590148-memory-faq (R6 research) | Central inspectable memory with per-entry edit/delete and visible capture moments | The MemoryPanel's inline "learned {date} from {who}" + Settings→Agent central inspector; capture is announced in-flow when a needs-input answer is remembered | Opaque synthesis — every remembered fact traces to a named human's answer | The reference UI for user-correctable agent memory, upgraded with provenance typing |

Synthesis: the composed surface is a **grounded-reply queue**, not a support inbox and not a chat. Plain gives the thread lifecycle and auto-reopen; Front proves the draft-waits-in-thread shape while its inbox model supplies the anti-pattern boundary; Hebbia sets the citation bar for every figure; LangSmith/ChatGPT-memory give the ask-once loop its write and read paths; Digits proves the queue posture; and the domain inversion makes it original — the "support team" is a borrower answering its own lender from a sealed, hash-anchored record, so grounding is not a quality preference but a legal posture: the draft can only say what the sealed record can prove, and a human signs everything that leaves. No referenced product has the sealed-record grounding constraint, the monitored/covenanted vocabulary law, or the certify-adjacent error cost; those come from Covenant's own laws and evidence.

## 15. Domain references

Terminology and expected-content semantics come from: the servicer's own recurring instruments (the JLL quarterly Property Questionnaire's 11 questions and Annual Certification items — evidence: SLOT-5 — proving that lender-side asks are cadenced and repeatable — the factual ground for lender-scope memory); Fannie Form 6001.NR §8.02(b)(4)'s on-request items with their once-per-6-months rate cap (evidence: SLOT-3 — proof that ad-hoc lender requests are a contract-shaped category, and that a loan may bound their frequency); and Finley-class deliverable-tracking and LoanBoss-class debt-management products (R5 research) for how borrower-side tools name lender interactions. The questionnaire itself is a package deliverable owned by the Composer — this surface handles the conversation around and after it; both fill from the same lender-scope memory store, which is why an answer learned here pre-fills the recurring form there (one store, 03 §3). Domain authority does not equal visual authority — and covenant semantics (what a verdict is, what a definition means) come from the loan documents and Terry, never from any referenced product.

## 16. Accessibility, performance, and safety

- WCAG contrast and focus: AA at all sizes on the ten-rung ladder; visible focus rings; the lit-row highlight pairs with aria-live announcements.
- Keyboard completeness: every action in §12 keyboard-reachable including the typed-act modal (fully operable without pointer); J/K/N/A/S/C/R never fire while the editor has focus.
- Screen-reader semantics: thread list as a listbox with group labels; message stack as an article sequence with direction announced ("from lender", "sent reply"); citation chips announce figure + source summary; needs-input blocks are labeled form regions.
- Table/list virtualization: thread lists virtualize past ~100 threads; message stacks paginate past ~50 messages.
- Loading and latency feedback: classifier and drafting states named in-thread; no spinner-only states; drafts never render partially-hallucinated placeholders (fail-closed honesty, 04 §2.5).
- Destructive action confirmation: close-with-reason confirms; re-route confirms with evidence shown.
- Certify and external-send safety: outbound is a typed act restating recipient + content hash + manual assertions; approved bytes — what sends is byte-identical to what was approved, the OutboundMessageRecord hash proves it (the send vertical's approved-bytes law applied at message scope); honest transport failures render exactly as returned (503/403/404 grammar) and retry is a fresh gate pass.
- Resident-data safety (Terry resident-data law), four enforcement layers:
  1. The drafting service's grounding set excludes resident-level identifiers from outbound composition — the agent cannot quote what the composer path never receives.
  2. Delinquency lists and deposit-account detail are withheld from outbound attachments; a lender request for them produces a needs-your-input block naming the policy, never an auto-attached list.
  3. The outbound linter pattern-matches resident identifiers in human-typed text the way it flags uncited figures.
  4. The approval modal surfaces any override as an explicit named acknowledgment written into the OutboundMessageRecord — the human can decide, but never silently.
- Recipient integrity: the outbound recipient list pre-fills from lender-scope memory (lender/servicer contacts with provenance — same source the send panel uses); edits at the approval modal are logged into the record; the modal always restates the full recipient list before confirm.
- Source immutability: inbound originals immutable with hash; sealed records read-only; thread history append-only.
- Auditability: every thread answers who asked what, when, what we replied, who approved it, what it hashed to, and which sealed figures it cited — the complete external-communication audit trail per loan; retention follows the org's Data settings, with sealed-period citations never expiring ahead of their records.

## 17. Acceptance tests and fixtures

Fixtures:

- `BEX-sealed-Q2` — Bexley canon: sealed Q2 period, certified + sent package, occupancy 268/301 = 89.04% shortfall vs monitored 90% floor, UPB $15,232,500 (canon).
- `CAL-6001` — Calloway Park evidence loan: Fannie 6001.NR + Forms 6220/6241 riders, JLL questionnaire on file (evidence: SLOT-3/5).
- Inbound email fixtures: `LENDER-Q-OCC` ("please confirm quarter-end occupancy"); `LENDER-Q-MGMT-FEE` (the questionnaire's management-fee % ask, quarters 1–3); `LENDER-Q-UNGROUNDABLE` ("provide the updated capital plan"); `LENDER-Q-DELINQ` ("send the delinquency list"); `LENDER-MIXED` (question + attached statement PDF); `LENDER-Q-ONREQUEST` (a §8.02(b)(4)-class leasing-report request).

- T1 (classification and materialization): `LENDER-Q-OCC` arrives at the intake address → classified correspondence at high confidence → thread materializes bound to Bexley Q2 with the classification line + arrival-record link; the quiet log records it; nothing pushes.
- T2 (grounded draft with citations): the draft for T1 quotes 268/301 = 89.04% as CitationChips; each chip's click lights the sealed-package source line in the Evidence tab and it stays lit; the draft's verdict language reads "shortfall" — an automated assertion proves the string "breach" cannot render for `basis=monitored` (vocabulary law, structural).
- T3 (ungroundable → needs-your-input): `LENDER-Q-UNGROUNDABLE` produces a draft whose answer slot is a NeedsInputBlock naming the missing grounding; no invented content anywhere in the draft (string-level review against the grounding set); supplying the input completes the draft and offers memory capture.
- T4 (recurring-question memory): `LENDER-Q-MGMT-FEE` in Q1 → human answers → MemoryEntry (lender scope) minted with provenance; the same ask in Q2 and Q3 pre-fills with "asked 3 times · remembered answer v1 · learned {date} from {who}"; correcting in Q3 versions to v2; Q4 uses v2 and the agent never re-asks (the zero-repeat-question acceptance bar, 03 §3).
- T5 (approval gate): "A" opens the typed-act modal restating recipient, loan+period, content hash; a hand-typed figure without citation blocks approval until cited or acknowledged as a manual assertion; confirming writes the append-only OutboundMessageRecord and flips the thread AWAITING-LENDER; no code path sends without this act (API-level test).
- T6 (auto-reopen): a reply on an AWAITING-LENDER thread flips it OPEN with the full history intact; same for a CLOSED thread; the your-move count increments identically on Home, Inbox, and the thread-list badge (one query, three renders).
- T7 (mixed email split): `LENDER-MIXED` routes its attachment through the document lane (filed in Documents, chip-linked in-thread) and its body to the thread; cross-links resolve both ways; no second ingestion path exists.
- T8 (resident-data law): `LENDER-Q-DELINQ` produces a needs-your-input block citing the withholding policy; automated assertion: no resident identifier can appear in outbound text or attachments without the explicit named override acknowledgment in the approval record.
- T9 (re-route): a thread mis-bound to Westbrook Flats re-routes to Bexley with reason; both loans' quiet logs record it; memory entries do not travel.
- T10 (honest failures): transport 503 renders the config-fix path; 403 renders the role reason; retry requires a fresh gate pass — grammar identical to the send vertical's.
- T11 (viewports): full topology at 1728/2048; the 1440 summon behavior; <1280 stacked navigation with the full approval flow operable; no silent compression anywhere.
- T12 (keyboard): complete §12 pass including modal-only ⌘↵ and editor-focus key suppression; screen-reader pass on the thread stack and citation chips.
- T13 (permissions): reviewer sees no approve affordance (absent, not disabled); PMC preparer drafts but cannot approve where the engagement reserves it; cross-Client isolation proven.
- T14 (provenance integrity): the lit-row trace lights the RIGHT source line for every citation class (sealed figure, document field, verdict) — regression-guarded against U1-F1.
- T15 (stale citation re-ground): after a draft cites a filed document, replacing that document version flags the affected citations stale; the draft cannot be approved until re-grounded; the re-drafted citation points at the new version with the supersession visible.
- T16 (sibling-period split): an inbound asking about Q1 and Q2 in one email produces two threads (one per period) cross-linked both ways; neither thread's period chip ever shows two periods.
- T17 (empty and latency states): a loan with no correspondence renders the empty state naming how threads arrive; an in-flight classification shows the inbound with the preparing state — at no point does an unsourced draft render.
- T18 (Calloway fixture pass): `CAL-6001` + a questionnaire-season inbound exercises the memory loop against the real 11-question instrument shape (evidence: SLOT-5), including the ENERGY STAR rider ask resolving to a checklist-linked NeedsInputBlock when the report is unfiled (evidence: Form 6241).
- T18b (on-request bridge): `LENDER-Q-ONREQUEST` proposes a one-time checklist item on the loan, cross-linked in-thread; the rate-cap context line renders from engine reads; no auto-refusal exists.
- T19 (quiet-log integrity): the full T1→T6 arc writes exactly the expected ActivityEvents (inbound logged, draft prepared, memory reused, sent receipt, reopen) with actor and identity stamps; zero push notifications fire at any point in the arc.
- T20 (benchmark challenger): reviewer walks Plain's 3-state + auto-reopen and Hebbia's citation bar against the build; any miss returns ADJUST.

## 18. Build plan

- Dependencies, named: F2 persistence + tenancy (thread/message/draft/outbound tables, Client scoping, roles — unfixing send-class rights from server-admin, gap 4); F1/V2 intake pipeline with the correspondence lane of the classifier (the existing pure `intake-classify.ts` function gains its correspondence output — snapshot §6.1); the sealed-record read API (the send vertical's persisted state is the seed); the agent grounding service (W4 — shared with Ask: same grounding set, same refusal grammar); the one MemoryEntry store (W4); the repaired lit-row trace (U1-F1 fix precedes citation chips).
- Foundation work, in order:
  1. Thread/message/draft schema with append-only outbound records; route scaffold `/loans/[loanId]/correspondence` in the `(covenant)` group mounting `CovenantShell` (components in `src/components/covenant/correspondence/`; gate + grounding reads in `src/lib/covenant/`; tokens consumed from `src/styles/covenant-tokens.css` — build-target law).
  2. The OutboundMessageRecord gate endpoint modeled directly on `POST /api/covenant/send` (same gate shape: scope, role, approval, hash, honest 503/404/403 — the proven vertical is the template, snapshot §3).
  3. Classification→materialization wire from intake (the correspondence lane).
  4. Grounding-service contract shared with Ask: one grounding set definition, one refusal grammar, two consumers.
- Components to build first: `ThreadList` + `ThreadView`/`MessageCard` (render fixtures of real shape), then `DraftCard` with `CitationChip` + `NeedsInputBlock` (the surface's soul), then `ApprovalBar`/modal + receipt, then `ContextRail`/`MemoryPanel`.
- Vertical slice (the send-vertical pattern): one route, one loan and period, engine data end to end — Bexley Q2 sealed fixture + `LENDER-Q-OCC` inbound → real classification → real grounded draft with live citations → typed approval → append-only record → auto-reopen on a scripted reply. T1/T2/T5/T6 green on the slice before widening.
- Migration from fixture data: no legacy surface exists (net-new); Loan Detail's CORRESPONDENCE tab summaries wire to the same store from day one (no fixture summaries permitted — the U3-F2 lesson).
- Rollout/feature flag: `lenderCorrespondence` flag; enable per-org once the org has at least one sealed period (the grounding precondition — the surface without a sealed record can only produce needs-your-input drafts, which is honest but thin); email-reply transport ships later on the same gate, download/copy-then-mark-sent first (transport parity with the send vertical).
- Proof artifacts required: T2 lit-row screen recording; T5 gate test in CI (no send path without the typed act); T4 memory-versioning test output; T8 policy-linter report; screenshot set at all four viewports.
- Final gate: `PASS` when T1–T20 green; any failure of T2 (grounding/citations), T5 (gate), or T8 (resident data) is an automatic `ADJUST` return — those three are the surface's laws.
