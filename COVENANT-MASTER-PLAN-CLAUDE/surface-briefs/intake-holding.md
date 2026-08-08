# INTAKE / HOLDING

The arrival door of the reporting spine: where documents land (designated intake email or upload), where the agent's recognition either files them quietly or surrenders the ambiguity to a human decision, and where each open period's holding checklist advances toward complete. The queue is empty by default; **an empty queue is the success state** (R1 research: synthesis).

## 1. Identity and verdict

- **Product layer:** reporting spine, chapter 1–3 of the arc (arrival → recognition → holding) (03 §1); mounted as a book-level place (arrivals span loans, so the breadcrumb is book-scoped: `Covenant / Intake`).
- **Route/address:** `/intake` in the `(covenant)` route group, mounting `CovenantShell` (08 §1 lists it among the planned deep routes). Per-period holding also projects into the period header on spine surfaces (07 §1 C1). Components land in `src/components/covenant/intake/`; pipeline logic in `src/lib/covenant/` (the existing unconnected `intake-classify.ts` is the classifier seed — snapshot §6 gap 1).
- **Current build state:** `FIXTURE` — Intake / IntakeFlow / Holding components render Terry's holding-pattern vision from `book.ts` fixtures; no inbound email, no classifier wiring, no routing, no persistence (snapshot §3, §6 gap 1).
- **Existing-surface verdict:** `ADJUST` (the idea is right; the machinery is absent) — overall a largely NEW build behind the kept idea (DIRECTIONS).
- **Research tier:** C (deep).
- **Primary users/roles:** owner and PMC preparer decide arrivals and manage holding; reviewer role sees read-only projections. In PMC mode every arrival, checklist, and decision is Client-scoped (02 §1).
- **Frequency and session duration:** bursty and short — documents cluster in weeks 1–2 of each cycle (03 §4); a healthy session is 0–2 minutes of ambiguity resolution, not a working residence. Deadline pressure rises toward due dates via the escalation ladder (03 §5).
- **Error cost:** a mis-filed document poisons everything downstream — extraction against the wrong loan or period produces wrong normalized lines and, unchecked, a wrong number moving toward certification (the terminal error). Second-order costs: a duplicate inflating a period, a stale prior version silently surviving a revision, a missing item discovered at the deadline.
- **Success criterion:** ≥ the org's threshold share of arrivals file with zero human touches, each quiet-logged with reasons; every ambiguous arrival is decidable in one screen without navigation; no period reaches its deadline with a silently missing checklist item (the gate escalates first); zero double-ingestion paths (bulk upload in Documents routes through this same pipeline — DIRECTIONS documents-docview).

## 2. User job and decisions

- **Primary job-to-be-done:** keep every open period's checklist advancing by resolving only what the agent cannot — ambiguity, conflict, and waiver — while everything recognizable files itself.
- **Decisions made here (the ruled decision set, 11-REFERENCE-EXEMPLAR):**
  1. **File** to a loan + period (+ the checklist item it satisfies);
  2. **Replace prior version** — supersede an already-filed document (shows what it supersedes and the downstream impact: which confirmed values go stale, which checklist items re-open);
  3. **Start new period** — the arrival is the first evidence of a cadence occurrence not yet open;
  4. **Merge duplicate** — same content, different bytes (changed hash) → collapse onto the canonical arrival;
  5. **Request missing item** — the arrival is partial (e.g. a package email missing its rent schedule): approve the agent's drafted request to the sender;
  6. **Hold** — park until a chosen date **or new activity** from the same sender/thread (auto-reopen);
  7. **Dismiss — not relevant** (with reason; recoverable from the dismissed lane).
- Plus checklist-side decisions: **waive** an expected item (typed, with reason, attributed) and **approve chase-cadence changes** (04 §1 Holding & chase row).
- **Relationship to the arc:** chapters 1–3 (03 §1) live here and are meant to complete *without* the borrower — this surface exists to keep the borrower's time concentrated at review/certify/send, so every design choice biases toward the quiet lane.
- **Questions the surface answers, in scan order:** (1) does anything need my decision, and how many? (2) what is this arrival — who sent it, what's attached? (3) where does the agent think it belongs, and why? (4) what happens downstream if I decide this way? (5) what is still missing for the matched period, and is a deadline close?
- **What the user should NOT have to decide here:** anything high-confidence (auto-filed quietly — only ambiguity reaches the queue); document type taxonomy (agent classifies); chase wording and scheduling within approved cadence (agent executes); which checklist items a period expects (engine derives them from confirmed RequirementRecords — 02 §2 ChecklistItem); the intake address itself (rendered read-only here — "send documents to {addr}" — managed in Settings; 02 §3).
- **Entry paths:** Inbox arrival rows deep-link into `/intake` selection (DIRECTIONS inbox); Home "Arrival decisions" your-move group; deadline-escalation items; command palette; direct URL. Not a rail item under the 08 §3 proposal — Inbox carries the arrival count into the rail badge.
- **Exit paths:** filed → the created Document in Documents (link, no re-homing); a correspondence-classified arrival → its Lender Q&A thread; checklist row → the period (period header / Review); chase policy → Settings; escalated deadline → Calendar context.
- **Completion/advancement conditions:** an arrival is complete when decided (or auto-filed); a checklist item completes on `matched` or `waived`; the period leaves holding when its checklist is complete and extraction is done — the OPEN → IN-REVIEW transition (03 §2). Checklist completeness is a readiness input downstream (05 §5).

## 3. Object and ownership model

| Object/action/status | Owned here? | Summary/link elsewhere | Address/identity | Notes |
|---|---:|---|---|---|
| Arrival (envelope + attachments + pipeline state) | YES | Inbox renders your-move projections that deep-link here | `arrivalId` (org-scoped; clientId in PMC mode) | NEW persisted object this brief introduces (see §4) |
| Routing decision (the 7-verb act + undo/re-route) | YES | Quiet log renders the record | `ActivityEvent` per decision | Append-only; identity-stamped (04 §2.3) |
| Holding checklist + item states | YES | Home counts; Calendar shows due-date pressure; Inbox "awaiting-documents" lane renders summary links; period header shows completeness | `(loanId, period, itemId)` | Derived from RequirementRecords (02 §2 ChecklistItem) |
| Chase schedule + chase history | YES (execution) | Settings owns cadence policy defaults; Calendar owns the deadline ladder | per checklist item | Agent-drafted, approved-cadence, quiet-logged (DIRECTIONS) |
| Filed Document (canon, versions, artifacts) | NO — Documents owns | Intake links to the filed doc after routing ("filed → {doc}") | `docId` | No-double-homing (02 §3); law §7 of the kit |
| Correspondence thread | NO — Lender Q&A owns | Recognition classifies correspondence-not-document and hands off | `threadId` | Quiet-logged handoff |
| Intake email address | NO — Settings owns | Rendered read-only here: "send documents to {addr}" | org (+ per-client sub-address in PMC mode) | Covenant's own address, never shared with any other product (02 §2 Organization) |
| Deadlines / escalation policy | NO — Calendar/Obligations owns | Deadline chips on queue rows and checklist tabs are computed summaries | `noteId` | Gap 9 machinery (03 §5) |
| Dismissed arrivals (recoverable lane) | YES | — | per arrival | Dismiss reasons logged; restore re-queues |
| Period status transitions | NO — orchestration spine | Holding completeness feeds OPEN → IN-REVIEW | `(loanId, period)` | No surface mutates status directly (02 §3) |

**No-double-homing statement:** Intake/Holding owns arrival decisions and the holding checklist — nothing else. Documents owns the filed canon (Intake links after filing and never lists a second document register); Inbox renders your-move projections that link here (it never hosts the decision); Home and Calendar summarize counts and deadline pressure only (02 §3; DIRECTIONS).

## 4. Data and semantic model

- **Source facts:** the arrival's immutable envelope (sender address, subject, received-at, message-id) and attachment bytes + content hash per attachment (Original artifact — kit law §7). Upload arrivals carry uploader identity instead of an envelope.
- **Extracted values awaiting confirmation:** none authored here — extraction proposals belong to Extraction & Confirmation. Intake's inferred layer is **recognition**: document type classification, loan+period candidate matches with per-candidate confidence and reason evidence (sender ↔ known contacts, content ↔ loan identity, filename/date signals), checklist-item mapping, duplicate/supersession detection.
- **Confirmed values:** the routing decision itself (which loan, period, checklist item, version relation) — a typed human act when queued, an agent act above threshold (always re-routable).
- **Deterministic outputs (engine):** content-hash dedupe; unprocessable-reason taxonomy (unsupported type / password-protected / encrypted / empty / corrupt-unrenderable); checklist materialization from RequirementRecords; completeness computation; chase-schedule materialization from cadence policy; deadline proximity (due-rule engine, 03 §5).
- **Agent proposals/drafts:** candidate matches with WHY; drafted chase messages; drafted request-missing-item messages; proposed sender rules ("statements from {addr} → {property}'s loans") minted as a side effect of a human resolution (R1 research: Dext).
- **Human decisions:** the 7 verbs; waive; cadence-change approval; undo/re-route.
- **Certified values:** none — nothing on this surface is certifiable, and no figure rendered here ships. Verdicts never render here (§5).
- **Versions/periods/packages:** replacement chains begin here (Replace prior version → the Document's `replaces` chain in Documents); Start new period creates the cadence occurrence via the spine.
- **Evidence/provenance:** every recognition candidate exposes its reason chain (which sender rule, which content signal, which filename/date parse); every auto-file quiet-log entry carries the same chain plus threshold-at-decision; every chase records message, recipient, timestamp. Three-actor data language legible on every row: source (envelope/bytes) / inferred (recognition, confidence-badged) / decided (human or above-threshold agent act, attributed).
- **Permissions/read-only projections:** reviewer role sees queue and checklist read-only (no decision affordances); PMC preparers see only their clients' arrivals; the intake address is read-only for everyone here.
- **The Arrival record (NEW persistence this brief introduces):**

```text
Arrival {
  arrivalId, orgId, clientId?                    ← tenancy grain (02 §1)
  channel: intake-email | upload | documents-bulk-upload   ← one pipeline, three doors
  envelope?: { sender, subject, receivedAt, messageId }    ← immutable; upload carries uploader identity
  attachments[]: { attachmentId, filename, mimeType, bytesRef, contentHash,
                   unprocessableReason?: unsupported-type | password-protected |
                     encrypted | empty | corrupt-unrenderable }
  pipeline: received → preflight → processing → recognized → (auto-filed | queued) → decided
  recognition?: { docType + confidence, candidates[]: { loanId, period, checklistItemId?,
                  confidence, reasons[]: { class: sender-rule | content | filename-date,
                  text, signalRef } }, supersedes?: docId, correspondence?: threadRef }
  decision?: { verb: file | replace | new-period | merge | request | hold | dismiss,
               actor (human | agent-above-threshold), thresholdAtDecision?, targetRefs,
               reason?, decidedAt }                        ← always re-routable
  receiptRef                                               ← the IntakeReceiptRecord
}
```
- **Field groups and grain:** organization (intake address, threshold policy) → client (PMC scoping, sub-address) → loan (candidate identity, sender rules) → reporting period (checklist, holding state) → document (created on filing) → requirement (checklist derivation) — plus arrival grain (envelope → attachment → recognition candidate) and chase grain (item → chase event). Property/portfolio appear only inside candidate identity chips; package/certification/send grains never appear here.

## 5. State machine and exceptions

### 5.1 Arrival lifecycle (two-lane routing: machine lane / human lane)

```text
received ──▶ preflight ──▶ processing ──▶ recognized ──┬─ confidence ≥ threshold ──▶ AUTO-FILED (quiet lane)
 (email/      hash dedupe;   classify;                  │   quiet-logged + re-route affordance
  upload)     unprocessable  match loan/period;         └─ ambiguous/conflict/duplicate-changed-hash
              taxonomy       split multi-attachment          ──▶ QUEUED (human lane) ──▶ DECIDED
                                                                  file / replace / new period / merge /
                                                                  request / hold / dismiss
exact-hash duplicate ──▶ suppressed (quiet-logged, linked to the original; never queued)
unprocessable ──▶ queued with named reason (human may still route or dismiss; receipt records the reason)
held ──▶ auto-reopen on chosen date OR new activity from the same sender/thread
```

| State | Trigger → next | Actor | Reversible | Audit | Failure behavior |
|---|---|---|---|---|---|
| received | intake email / upload / Documents bulk upload (same pipeline) | E | — | ActivityEvent: arrival | delivery failure surfaces in Settings → Intake health, not silently dropped |
| preflight | hash dedupe, org/client scoping, unprocessable check | E | — | receipt line per skipped file with named reason (R1 research: Ramp) | exact duplicate suppressed quietly; unprocessable → queued with reason |
| processing | classification + recognition running | A | — | — | timeout/failure → queued as "recognition failed," never guessed |
| auto-filed | all candidate fields clear the org threshold | A | YES — re-route forever; U inside undo window | quiet log with full reason chain | re-route after downstream confirmation triggers stale machinery (03 §2) |
| queued | any ambiguity, conflict, changed-hash duplicate, or unprocessable | E routes; H decides | decision undoable (U; stack) | ActivityEvent per decision | conflicting arrival **stops automation** for that item (R1 research: Rossum) |
| decided/filed | one of the 7 verbs | H | YES (re-route; dismissed lane recoverable) | ActivityEvent + Document link | replace shows downstream impact before commit |
| held | H parks until date/new activity | H | YES (reopen manually) | logged with reopen condition | auto-reopen on sender activity (R1 research: Plain) |
| dismissed | X verb with reason | H | YES — restore from the dismissed lane re-queues | dismissal (who, reason) | — |
| suppressed | exact-hash duplicate at preflight | E | not undone by design; original always linked | suppression + link | — |

### 5.2 Holding-checklist item machine (per loan + period; states per 02 §2)

```text
expected (missing) ──chase cadence──▶ chased (n sent · last {date} · next {date})
     │                                     │  new arrival matched from that sender/thread
     │                                     └────────────▶ arrived ──recognition confirm──▶ matched ✓
     ├── human waives (typed, reason, attributed) ──▶ waived (by whom, when)
     └── deadline−N with no arrival and chases exhausted ──▶ GATE: your-move escalation (03 §2)
matched ──filed doc replaced/voided──▶ re-opened to expected (stale flag downstream)
```

| Item transition | Trigger | Actor | Reversible | Audit |
|---|---|---|---|---|
| — → expected | Period opens; engine materializes items from confirmed RequirementRecords (e.g. §8.02(b)(1) quarterly statement, §8.02(b)(3) rent schedule — evidence: SLOT-3) | E | — | period-opened event |
| expected → chased | Cadence policy fires (first remind at policy offset before due) | A (executes) on approved cadence | chase itself irreversible (append-only); cadence pausable | chase sent (message, recipient, next scheduled) |
| expected/chased → arrived | Recognition matches an arrival to the item | A | yes — re-route reverts to expected | match event with reason chain |
| arrived → matched ✓ | Filing commits (auto or human verb) | A above threshold / H | yes — re-route/supersede re-opens | filed + Document link |
| expected/chased → waived | Human waives with typed reason | H | yes — un-waive re-opens the item | waiver (who, when, reason) |
| matched → expected (re-opened) | Filed doc superseded, or a certification void touches it | E flags | n/a (forward-only correction) | re-opened with cause |
| any → GATE | deadline−N reached, item not matched/waived, chases exhausted | E per escalation policy | resolves by decision | escalation event |

Completeness = all items `matched` or `waived`; feeds OPEN → IN-REVIEW and, later, readiness (05 §5).

### 5.3 Chase mechanics (the holding pattern's motor)

- **Recipient resolution:** the agent chases the sender who historically supplies that doc type for that property/loan (sender rules + chase history); first-ever items fall back to the org's named contacts; an unknown recipient is a queue question, never a guessed email.
- **Cadence:** org default in Settings (per-doc-type override), expressed as offsets around the item's computed due date (due-rule engine, 03 §5) — e.g. remind at due−14d, chase at due−7d, repeat every 3 business days after due — with blackout dates and day-of-week control (R1 research: Floify). The cadence is **approved once** (ask-once); the agent thereafter executes without asking, and any cadence *change* is a human approval.
- **Escalation ladder (03 §5):** remind → chase → your-move (the item becomes a Home/Inbox your-move row) → overdue push — the last rung is the single push-eligible notification class in the product, per org policy (04 §2.3).
- **Message grounds:** chase drafts name the loan (outward name), period, the missing item by its requirement title, and the due date — engine facts only; drafts never state figures. Internal-facing chases (the org's own PM/accounting senders) send on cadence without per-message approval; request-missing-item messages to external parties are human-approved before send (§6).
- **Every chase is quiet-logged** (message excerpt, recipient, timestamp, next scheduled); the checklist chip renders `chased (n sent · last {date} · next {date})`.
- **Auto-reopen (R1 research: Plain):** any reply or new upload from the chased sender/thread resurfaces the item's context — the new arrival lands in the queue pre-linked to the chase history, so the decision inspector shows why this document was being awaited.

### 5.4 Template state coverage

- **empty:** "All clear — nothing needs your decision," plus the read-only address card ("send documents to {addr}") and a link to open periods' checklists. The empty queue is the success state, rendered as such — never a hollow zero-state.
- **awaiting-documents (holding):** the period-level state; checklist tab shows missing/chased items with next-chase dates; Home/Inbox summarize.
- **loading/processing:** Processing group in the queue with per-arrival stage ("classifying · 2 of 3 attachments"); opening one shows a read-only preview; decisions unlock when recognition lands or the user forces manual routing (no mid-pipeline edit race — R1 research: Ramp reject).
- **partial/incomplete:** an arrival satisfying only part of a package (T-12 present, rent schedule absent) files what matched and proposes Request missing item for the rest.
- **extracted/unconfirmed:** N/A here by design — extraction proposals render on Extraction & Confirmation; Intake shows only "filed → extraction queued" links.
- **stale/superseded:** a Replace decision marks the prior version superseded in Documents and re-opens downstream confirmations (stale badges render where the affected figures live — 03 §2).
- **low confidence:** the queue's normal citizen — candidates listed with confidence and WHY; never auto-filed.
- **conflict:** two arrivals claiming the same checklist item, or content contradicting the matched loan's identity → automation stops, inspector forces the Merge/Replace/File decision.
- **failed tie-out:** N/A here (Review Room owns tie-out); a tie-out-triggered document question arrives as a re-opened checklist item.
- **watch/shortfall/breach:** verdicts never render on this surface by design — no covenant verdicts at the door; verdict rendering belongs to Actuals, Review, and Loan Detail. Nothing here may imply a verdict.
- **permission denied:** reviewer/foreign-client scope → read-only queue, disabled decision bar with reason ("Preparer or owner role required").
- **read-only:** sealed-period checklist rows render read-only forever (seal-not-wipe).
- **blocked/gated:** unrecognized arrival with an approaching deadline = the gate (03 §2 unrecognized-arrival): escalates to your-move with the exact ask, the deadline, and the candidate evidence.
- **certified (void-on-change) / sent-sealed:** not owned here; a Replace that touches a certified package's inputs surfaces the void warning in the downstream-impact strip before commit ("this supersedes {doc}; {period} is certified — filing voids the certification").
- **recovery/undo:** U undoes the last decision (session stack); every auto-file and every decision carries a re-route affordance forever; dismissed arrivals live in a recoverable dismissed lane. **Undo impossible by design:** a chase already sent (append-only record — recall is dishonest); suppression of an exact-hash duplicate is not undone but the original is always linked.

## 6. Engine / Agent / Human / Gate / Quiet Log

Surface-specific expansion of 04 §1 rows "Arrival & recognition" and "Holding & chase":

| Step | ENGINE (deterministic) | AGENT (prepares/proposes) | HUMAN (typed decisions) | GATE (interrupts) | QUIET LOG (no push) |
|---|---|---|---|---|---|
| Arrival & preflight | Content-hash dedupe; sender validation; org/client scoping; unprocessable taxonomy | — | — | — | Arrival received; duplicate suppressed (linked); per-file receipt with named skip reasons |
| Recognition | Confidence threshold evaluation (org-configurable, per-doc-type override) | Classify type; split multi-attachment emails; match loan+period+checklist item with confidence + WHY; detect supersession; classify correspondence-not-document | — | — | Recognition outcome per attachment |
| Quiet filing | Threshold gate; Document creation; checklist advance | Executes the filing it proposed | — | — | Auto-filed with full reason chain + re-route affordance |
| Queue decision | Decision validation (target exists, scope, period open) | Ranks candidates; renders WHY; proposes the likely verb | The 7-verb decision; 1–9 candidate pick | Unroutable arrival + approaching deadline → your-move escalation | Every decision, identity-stamped |
| Sender rules | Rule storage + versioning (MemoryEntry mechanics, 02 §2) | Proposes a rule from each human resolution; applies rules with provenance on later filings | Accept/correct the rule (ask-once — never re-asked) | — | Rule minted; each reuse ("pre-filled from rule {ref}") |
| Holding checklist | Materialize items from RequirementRecords; completeness | Match arrivals to items; flag partial packages | Waive (typed, reason); proceed-anyway | Checklist incomplete at deadline−N per escalation policy | Item matched; item re-opened |
| Chase | Schedule from approved cadence (pre/post-deadline, blackout dates) | Draft chase and request-missing-item messages; adapt recipient from history | Approve cadence changes; approve request-missing-item sends (external-facing → human-approved) | Overdue with no path to complete: the one push-eligible class (04 §2.3) | Each chase sent (message, recipient, timestamp) |
| Undo / re-route | Stale propagation when a re-route touches confirmed downstream values (03 §2) | Recomputes recognition for the new target | The undo/re-route act itself | — | Re-route recorded with cause, old and new targets |
| Address | Address provisioning/rotation (Settings-owned) | — | Managed in Settings only | — | Rotation events |

Lane invariants honored: no shipping number is produced here at all; routing chases are internal-facing and run on approved cadence, while request-missing-item messages to external senders are human-approved (04 §1 follow-up analogue); completed autonomous work never pushes. One more, structural: **the agent is the queue's default owner — humans get only ambiguity** (R1 research: Linear reject of triage rotations); any change that routes routine arrivals to humans is a regression, not a preference.

## 7. Information hierarchy

1. **Page/frame header:** book-scoped breadcrumb `Covenant / Intake`; queue count ("3 need your decision" — shared CountBadge); the read-only address card collapsed into the header's right cluster.
2. **Decision/status summary:** queue group headers — Needs your decision (n) / Processing (n) / Recently filed (collapsed).
3. **Primary work region:** the selected arrival's preview (center) — envelope, attachments, document.
4. **Secondary context:** the decision inspector's candidate evidence (right).
5. **Evidence/proof:** the WHY chain per candidate (expandable in place); attachment provenance (hash, received-at).
6. **Actions:** the decision bar (7 verbs, keyboard-first) pinned at the inspector's foot.
7. **Activity/history:** Recently filed (quiet-log projection, collapsed at the queue's foot); per-item chase history in the checklist tab.

**Absent by design:** covenant verdicts and headroom (no verdict language at the door); charts (nothing here out-encodes text); a second document register (Documents owns the canon); notification chrome (quiet filing is quiet); any compose-email UI beyond approving agent drafts; the intake address editor (Settings).

## 8. Page anatomy and regions

Precedent: the ruled reconcile-room 3-column interior arrangement (kept per frame-mirror law) — one work window with two pinned interior panes, not a work-window split (08 §4).

**HEADER (persistent, 48px shell header):** breadcrumb, queue CountBadge, address card trigger ("send documents to {addr}" — click = copy affordance + "managed in Settings" link; read-only always).

**LEFT — Arrival queue (pinned pane, 280–320px; min 240px):**
- Purpose: pick the next decision; show pipeline health at a glance.
- Content, grouped: **Needs your decision** (sorted deadline-proximity first, then oldest); **Processing** (stage chips); **Recently filed** (collapsed quiet-log projection with re-route affordances); a **Dismissed** lane behind a filter.
- Row anatomy (uniform 44px height, open-not-boxed): line 1 — sender or uploader (13px, medium) · attachment count chip; line 2 — subject/filename (12px, truncating middle) · best-candidate chip (loan outward name + period, 11px) with confidence · deadline chip when the matched period's due date is ≤7 days (Geist Mono date). Group headers 28px (11px caps + CountBadge).
- Interaction: J/K navigation, Enter opens; type-to-filter (32px field pinned above the groups).
- Collapse: below minimums the queue becomes a labeled tab, never squeezes (no silent compression).
- Why a pane: the queue must stay visible while deciding — the next item and the count are the pacing device.

**CENTER — Selected-arrival preview (primary work region, min 560px):**
- Purpose: see exactly what arrived before deciding.
- Content, top→bottom: **envelope block** (~72px: sender with known-contact chip when a sender rule exists, subject, received-at, message-id disclosure) → **attachment strip** (40px chip row, horizontally scrollable: each attachment — filename, type chip, hash short-code, unprocessable badge with named reason; selecting switches the preview) → **document preview** (remaining height, min 480px): the DocView renderer read-only (reuse — Documents' porcelain sheet component), rendering the Original artifact; flattened scans render as bytes (the Recreated-searchable derivative is produced after filing and lives in Documents, labeled — kit law §7).
- Persistence: persistent while an arrival is selected; empty-state guidance otherwise.
- Why the big pane: the document itself is the evidence for the routing decision; it must dominate.

**RIGHT — Decision inspector (pinned pane, 360px; min 320px):**
- Purpose: the decision, beside its evidence — never on a separate screen.
- Content, top→bottom: **recognition evidence** — candidate cards (1–9, ~64px each collapsed), each: keycap numeral, loan outward name + property chip (pledge-aware), period, the checklist item it would satisfy, confidence % (Geist Mono), and the expandable WHY (sender ↔ known contacts; content ↔ loan identity signals such as property name/address/unit count; filename/date parse — each reason line click-lights its signal at the source, §12). A correspondence-thread candidate may appear here when recognition reads the arrival as correspondence-not-document (File then hands off to Lender Q&A). Then the **decision bar** (pinned at the inspector's foot, 2×48px rows with key hints): File · Replace prior version · Start new period · Merge duplicate · Request missing item · Hold · Dismiss. Replace expands a **downstream-impact strip** (what it supersedes; which confirmed values go stale, counted with links; certification-void warning where applicable) before commit.
- Below, same pane, **tabbed:** the matched period's **holding checklist** — each expected item: arrived ✓ (filed link) / missing / chased (n sent · last {date} · next {date} · message popover) / waived (by whom, when, reason). Waive and cadence-change actions live here.
- Persistence: pinned; the checklist tab persists per selection.
- Why an inspector, not a modal: co-visibility of document ⇄ candidates ⇄ consequence is the decision (kit law §5-6); modals are reserved for nothing here — no intake act carries certify/send-grade legal weight.

**Canvas panel (floating, summonable):** Ask about this arrival (selection travels with provenance); never load-bearing for the routing decision.

## 9. Co-visibility matrix

| Datum/surface A | Datum/surface B | Must be simultaneous? | User decision enabled | Default topology | Fallback |
|---|---|---:|---|---|---|
| Document preview | Candidate matches + WHY | YES | The routing verb | center + right pane | inspector becomes overlay sheet <1280px work area |
| Candidate card | Its reason evidence | YES | Trusting/overriding the match | expand-in-place on the card | — |
| Replace decision | Downstream impact (stale set, void warning) | YES | Commit vs cancel | impact strip inside the inspector | — |
| Queue | Preview + inspector | YES (pacing + count) | Next-item selection | left pane persistent | queue collapses to labeled tab, never squeezed |
| Checklist item | Chase history | On demand | Waive / cadence change | popover on the chased chip | — |
| Arrival | The filed Document it became | NO (sequential) | — | link after filing | — |
| Duplicate pair | Both arrivals' envelopes | YES during Merge | Which is canonical | merge view: two envelope blocks stacked in the inspector, preview shows selected side | — |
| Checklist | The period's deadline | YES (summary chip) | Escalation awareness | deadline chip on the tab | Calendar link |
| Auto-filed row | Its receipt + threshold-at-decision | On demand | Trust calibration (loosen/tighten the threshold) | expand-in-place in Recently filed | Settings → Intake link |
| Request-missing-item draft | The partial arrival it responds to | YES during approval | Approve/edit the outbound ask | draft inline in the inspector, preview holds the arrival | — |

No pane exists merely because information exists: the checklist is tabbed (not a fourth column) because filing and completeness are sequential attentions, co-located but not simultaneous.

## 10. Layouts and viewport behavior

Interior ratios (work area = viewport − rail per 08 §9):

| Viewport | Rail | Queue | Center preview | Inspector |
|---|---|---|---|---|
| 1440px (rail 240) | expanded | 280px | 560px | 360px |
| 1440px (rail user-collapsed 48) | icons | 300px | 732px | 360px |
| 1728px (rail 240) | expanded | 300px | 828px | 360px |
| 2048px (rail 260) | expanded | 320px | ~1108px (preview gains all surplus; page-width documents render 1:1) | 360px |
| 1280–1439px | auto-icon 48 | 280px | ≥592px | 360px |
| <1280px work area | icons | queue full-width register | selecting opens preview as the single work window | summonable overlay sheet (360px) from the right; checklist a tab inside it |

- **Focus behavior:** Enter on a queue row moves focus into the inspector's first candidate; Esc returns focus to the queue (closing any overlay first — 07 §3 return-path law).
- **Compare behavior:** Merge duplicate is the only compare — inspector-stacked envelopes with preview switching; it never claims a second work window (the decision is which-is-canonical, not line-by-line diff; deep diffs belong to DocView Compare).
- **Proof/source behavior:** WHY chains expand in place; the filed-document link opens Documents (navigation, not overlay).
- **Minimum viable dimensions:** 1152×720 (frame law); the queue register remains usable below spine-comfort sizes; preview/inspector become sequential overlays.
- **Pane toggles:** `[` collapses/restores the queue and `]` the inspector — each to a labeled tab (never hidden without a label) — for full-width reading of a wide document (e.g. the 24-column rent roll); any decision key restores the inspector first.
- **Tab stacking:** below queue minimum (240px) the queue becomes a labeled tab above the preview. No silent compression anywhere: any pane below minimum collapses to its labeled tab.

## 11. Components and exact anatomy

Reuse (existing repo components per snapshot):
- **CovenantShell** — the chassis; `/intake` mounts it.
- **CountBadge** (shared atom, ruled consolidation) — queue count, group counts, rail/Inbox badge.
- **DocView renderer** — read-only preview of the Original artifact (Documents' component consumed here, not re-implemented).
- **CommandPalette** — G-chords + palette entry.
- **AskPanel / HighlightToAsk** — canvas-slot Ask over the selected arrival.
- Existing **Intake / IntakeFlow / Holding** fixture components: harvested for layout ideas; superseded by the below.

NEW components (all in `src/components/covenant/intake/`):
- **ArrivalQueue** — grouped virtualized list; parts: QueueGroupHeader (label + CountBadge + collapse), **ArrivalRow** (sender, subject, attachment chip, best-candidate chip + confidence, deadline chip), type-to-filter field.
- **ArrivalEnvelope** — sender + known-contact chip, subject, received-at (Geist Mono), message-id disclosure, copy affordances.
- **AttachmentStrip** — per-attachment chip: filename, type, hash short-code, unprocessable badge with named reason; selection state.
- **RecognitionCandidateCard** — numbered 1–9; loan outward name, property chip, period, target checklist item, confidence % (text, not a color bar), WHY expander listing reason lines each with its signal class (sender-rule / content / filename-date) and provenance ("rule learned {date} from {who}").
- **DecisionBar** — the 7 verbs as buttons with key hints; disabled-with-reason when no candidate selected where required; Replace opens **DownstreamImpactStrip** (superseded doc, stale value count with links, certification-void warning) requiring explicit commit.
- **HoldingChecklist** — tabbed panel; header row: period identity + completeness fraction ("4 of 6") + due-date chip with due-rule provenance ("Q+45d per §8.02(b)(1)" — evidence: SLOT-3); **ChecklistItemRow** (uniform 40px: item title with requirement provenance, e.g. "Quarterly rent schedule — §8.02(b)(3)" (evidence: SLOT-3), state chip arrived ✓/missing/chased/waived, filed-doc link, overflow: waive · adjust cadence); **ChaseHistoryPopover** (each chase: timestamp, recipient, message excerpt, next scheduled); **WaiveControl** (typed reason, attribution, un-waive path).
- **PeriodHoldingSummary** — the projection this surface exports to the period header on spine surfaces (07 §1 C1): completeness fraction + earliest-risk chip, linking back to `/intake` with the period's checklist tab open. A summary, never a second decision site.
- **DecisionBar states:** verbs requiring a target (File/Replace/Merge) disabled-with-reason until a candidate is picked; Hold opens an inline date field with "until new activity" as the Enter default; Dismiss and bulk actions require a reason; every disabled state names its unblock.
- **IntakeAddressCard** — read-only address + copy + "managed in Settings" link; per-client sub-address list in PMC mode.
- **IntakeReceiptRecord** — the per-arrival receipt rendered in quiet-log projections: filed n, skipped m with named reasons (R1 research: Ramp).
- **UndoToast / ReRouteAction** — U-undo confirmation; the permanent re-route affordance on every filed/auto-filed row.
- **SenderRuleChip** — "auto-filed by rule {n}" provenance chip; click = inspect/correct (versioned, ask-once mechanics).
- **EmptyQueueState** — "All clear" + address card + open-checklists summary links.

Tables/lists: uniform row heights, open-not-boxed (locked law). Charts: none — nothing here out-encodes text (chart doctrine honored by omission).

## 12. Interaction specification

- **Selection:** single-select queue; selection drives center + right; multi-select reserved for bulk Dismiss/Hold only (never bulk File — filing requires per-arrival evidence).
- **Hover:** row hover reveals quick actions (open, hold); candidate hover previews its WHY summary; chased chip hover shows last/next chase.
- **Focus:** visible focus ring (accent family) on rows, candidates, verbs; roving tabindex within the queue and candidate list.
- **Keyboard (the ruled map + expansion):** `J/K` queue nav · `Enter` open selection → inspector · `1–9` pick candidate · `F` File · `R` Replace · `N` Start new period · `M` Merge duplicate · `I` Request missing item · `H` Hold (date picker; Enter = until-new-activity default) · `X` Dismiss (reason prompt) · `U` Undo last · `Tab` cycles inspector⇄checklist tabs · `Esc` closes overlays then returns to queue · `⌘K` palette; `G`-chords per the frame map (08 §5).
- **Editing and validation:** the only free-text inputs are waive/dismiss reasons and chase-draft edits; File validates target (open period, scope) and names the failure ("period is sealed — choose Start new period or dismiss").
- **Bulk action:** bulk Hold/Dismiss on multi-select with one shared reason; logged per arrival.
- **Undo/recovery:** session undo stack (U); permanent re-route on every decision row; dismissed lane restore; sent chases are append-only (no recall — §5.4).
- **Sorting/filtering:** default deadline-proximity then age; filters: loan, client (PMC), doc type, state, sender; saved views via the shared mechanism (02 §2 SavedView).
- **Drill-down and return path:** filed-doc link → Documents; checklist row → period header/Review; Esc-before-navigate law honored (07 §3).
- **Source-linked selection (lit-row analogue):** clicking a WHY reason line highlights its signal at the source — the sender line in the envelope, the filename token in the attachment strip, or the matched content region in the preview (page-scrolled, region lit, stays lit until dismissed) — the frame provenance contract applied to recognition (08 §7).
- **Save/persistence:** every decision commits immediately (no draft state); optimistic UI with honest failure rollback ("filing failed — {reason}; nothing was filed").
- **Receipt inspection:** any Recently-filed or auto-filed row expands its IntakeReceiptRecord in place (filed n / skipped m with named reasons, threshold-at-decision, rule provenance) — the trust surface for loosening the auto-file threshold over time.
- **Address interactions:** the address card copies on click and offers a mailto-free "forward your next lender email here" hint; every management affordance routes to Settings → Intake (display, rotation, sender recognition list, per-client sub-addressing — DIRECTIONS settings).
- **Collaboration:** none beyond attribution; decisions are single-actor typed acts.

## 13. Visual craft direction

All inside the ruled system — zero new colors (kit law §4):

- **Typography:** UI text in the app face; hierarchy by weight/size within the shell's scale (row title ~13px, meta ~12px, group headers ~11px caps). All figures — dates, counts, confidence %, hashes, unit counts — in **Geist Mono with `tabular-nums slashed-zero`**.
- **Financial-number treatment:** no shipping figures render here; the same mono treatment still applies to any numeral so the door matches the book.
- **Spacing rhythm:** 8px base grid; queue rows uniform height (~44px); inspector sections separated by whitespace + hairline, not boxes.
- **Density:** register-dense on the queue; document-calm in the preview (the DocView sheet keeps its paper ground); inspector mid-density.
- **Open ground vs earned boundaries:** open-not-boxed throughout; the three regions separated by hairline dividers on the gray ladder's hairline rung; elevation only on the summoned overlay sheet and popovers (ladder surface-raised rung + shadow, per the ruled dropdown seam).
- **Semantic color:** accent family (#7189FF; hover #8EA1FF; active #6078F4; tint #A9B5FF for selected-row wash; on-accent #0B1020) for selection, focus, primary action (File), and the lit signal region. Grays from the ten-rung ladder for everything else. Deadline pressure escalates by type treatment (weight, then the your-move mechanism) — not by inventing a warning color; unprocessable/conflict badges use the ladder + iconography (Lucide), severity color reserved for verdict surfaces, which this is not.
- **Confidence rendering:** numeric text + reason list — never a colored gauge (chart doctrine: severity-only color; no chart here at all).
- **Iconography (Lucide-only, no emoji):** `mail` arrival via email · `upload` via upload · `paperclip` attachment · `file-check` filed/matched · `clock` held/next-chase · `bell-off` waived · `shield-alert` unprocessable/quarantine · `corner-up-left` re-route — one icon per state, never decorative pairs.
- **Certified-sheet treatment:** not touched — no certified paper renders on this surface; the six certified-sheet hexes do not appear here.
- **Focus/selected/hover states:** selected row = accent-tint wash + accent left edge; hover = ladder rung lift; focus ring accent, 2px, offset 1px.
- **Motion:** overlay sheet slides ≤160ms ease-out; lit-region fade-in ≤120ms; group collapse animates height; `prefers-reduced-motion` → instant state swaps, the lit region still visibly persistent (state, not animation, carries meaning).
- **Long-session ergonomics:** intake sessions are short by design; the queue's pacing device (count falling to zero) is the reward loop; no blinking, no badges that pulse.

## 14. Benchmark research and synthesis

All sources from R1 (checked 2026-08-08); limited roles as assigned there.

| Product | Limited role | Official evidence | Exact pattern to take | Covenant adaptation | What to reject | Why this reference beats alternatives |
|---|---|---|---|---|---|---|
| Linear Triage | interaction | https://linear.app/docs/triage · https://linear.app/changelog/2025-09-19-auto-apply-triage-suggestions | Few-verb one-key disposition grammar; suggestion + hoverable reasoning + accept/dismiss; auto-apply at high confidence | The 7-verb decision bar with single keys; RecognitionCandidateCard WHY = the hoverable reasoning; auto-apply = quiet filing, always logged | The personal-notification Inbox as the intake surface; human triage-responsibility rotations (the agent is the default owner) | The cleanest living grammar for "machine proposes, human disposes in one keystroke" |
| Ramp Bill Pay | workflow-state | https://support.ramp.com/hc/en-us/articles/35659701397395-Bill-Pay-AP-Email-Forwarding | Per-customer forwarding address; hash-level duplicate suppression; explicit unprocessable taxonomy; the machine-written intake receipt naming every skipped file and reason | Receipt → quiet-log IntakeReceiptRecord (optional auto-reply to the sending PM/accountant, org-configurable, default off); duplicate logic version-aware per loan+period+doc-type (a revised T-12 supersedes, not suppresses) | "Everything becomes a draft for humans to finish"; the edit-kills-OCR race | Only benchmark with an honest, named-reason account of what the machine did NOT process |
| Rossum | ontology | https://rossum.ai/help/article/guide-to-the-document-lifecycle/ · https://knowledge-base.rossum.ai/docs/guide-to-automation-process-in-rossum | Automation dial (Never/Confident/Always) with per-field confidence thresholds (reference default 97.5%); Postponed as a first-class holding state; duplicate detected → stop automation | The org-configurable auto-file threshold with per-doc-type overrides; Hold state; conflict/changed-hash → forced inspector | Export as the terminal goal (Covenant's unit of done is period package complete); per-queue tab silos fragmenting one borrower's arrivals | The formal model for the quiet-lane/human-lane fork, threshold semantics included |
| Instabase | evidence-provenance | https://docs.instabase.com/automate/review | Only failed-validation runs reach human review; field ↔ source-region provenance in the preview; reclassify-then-re-extract as one gesture | Validations = package rules (statement dates cover the period; property matches collateral; doc type satisfies the checklist item); WHY-line → lit signal region | The reviewer-workforce apparatus (managers, SLA benches, escalation hierarchies) | Proves "humans see only exceptions" at production scale with provenance intact |
| Plain | IA | https://help.plain.com/article/statuses | Three-state minimalism with reason-coded substates; auto-reopen on external activity | Chased — awaiting sender as the checklist substate; sender reply/new upload auto-reopens the item tied to its chase history | The conversation-first frame; response-time SLAs per email | Auto-reopen is the single mechanic that makes Hold safe without human vigilance |
| Floify | domain truth | https://help.floify.com/hc/en-us/articles/360040327032-Resend-Needs-List-Email-to-My-Borrower · https://floify.com/blog/update-deadline-reminder-scheduling-and-more | The per-record needs list (Docs Owed → Pending → Accepted); rejection re-opens the item with a reason; pre/post-deadline reminder cadence with blackout dates | Direction inverted (borrower chasing internal/PM senders, not lender chasing borrower); item states arrived/missing/chased/waived; waived (lender excused it) added — no Floify equivalent | Notification blasts at every transition; portal-gating on a mandatory email | The closest living ancestor of the holding checklist + chaser, from the adjacent loan-doc domain |
| Dext | shell | https://help.dext.com/en/articles/416754-submitting-items-with-extract-by-email · https://help.dext.com/en/articles/216125-using-supplier-and-customer-rules | Product-exclusive intake address; per-sender rules converting one human resolution into permanent auto-filing, shown as provenance | Sender rules = learned sender→property/loan mappings minted as a side effect of resolving ambiguity; SenderRuleChip provenance on later quiet filings | Pushing mode complexity (Single/Multiple, Costs/Sales addresses) onto senders — the agent splits and classifies multi-attachment emails itself behind one address | The proof that one address + learned rules beats address taxonomies |
| Ocrolus | workflow-state | https://www.ocrolus.com/platform/ | Automatic per-document routing to machine-only vs human-in-the-loop by quality + confidence — the requester never chooses | The invisible two-lane fork; HITL is the borrower's own team, low volume | The outsourced review-workforce model; lender-side framing (Ocrolus serves the lender judging the borrower) | Demonstrates the fork as infrastructure, not a setting the user must operate |
| Front | anti-pattern | https://help.front.com/en/articles/1999 | Only the audit-log grammar (archived/assigned/rule-triggered as typed history) | n/a | The whole team-mailbox model: every arrival a conversation, human-touch-everything, archive-as-disposition (archived ≠ filed to any loan/period; completeness lives in people's heads), notification fan-out | Named to keep the failure mode visible: if intake ever behaves like this, quiet filing is dead and the checklist stops being trustworthy |

**Synthesis.** No benchmark is a borrower-side lender-reporting intake; the composition is original because the unit of done is different — not "message handled" (Front), not "document exported" (Rossum), but **the period package complete**. Covenant fuses Dext's exclusive address + sender rules, Ramp's preflight honesty, Rossum/Ocrolus's confidence fork, Linear's disposition grammar, Instabase's exception-only human lane with provenance, Plain's auto-reopen, and Floify's needs-list-with-chase — then subordinates all of it to the holding checklist derived from each loan's own confirmed RequirementRecords (05 §1), so every arrival's meaning is "which obligation does this satisfy, for which loan, for which period." That question exists in no referenced product.

## 15. Domain references

For terminology, expected data, and workflow semantics only:

- **Floify** — loan-document needs-list vocabulary and reminder-cadence conventions (the chase's living ancestor).
- **Ocrolus** — the document classes a lender-side file expects (classification breadth as domain reality check).
- **The servicer's own artifacts in evidence** — the JLL quarterly Property Questionnaire and Annual Certification forms (evidence: servicer forms), whose named items seed what a period's checklist expects when the loan's requirements say so.
- **The executed agreement itself** — loan-servicing intake conventions (statement periods; rent schedules with the §8.02(b)(3) named fields: tenant, space, lease expiration, current rent, paid-through) (evidence: SLOT-3).

**Domain authority does not equal visual authority** — none of these products or documents contributes a pixel; and covenant semantics (what a period expects, what a requirement means, every threshold and cadence) come from the loan documents and Terry, never from any referenced product (kit law §8).

## 16. Accessibility, performance, and safety

- **WCAG contrast and focus:** all text on the gray ladder meets AA against its ground; accent-on-tint selections verified by canvas readback, not eyeball (oklch caution, snapshot §4); 2px visible focus ring everywhere.
- **Keyboard completeness:** the entire flow — queue → open → candidate → verb → undo, plus checklist waive and chase approval — operable with zero pointer use (§12 map); overlays trap and restore focus.
- **Screen-reader semantics:** queue = listbox with group labels and counts; ArrivalRow announces sender, subject, best candidate + confidence, deadline; decisions announce results ("Filed to Bexley, Q2 2018 — rent schedule item matched"); the lit signal region gets an aria-live description.
- **Table virtualization/large data:** ArrivalQueue virtualizes (bursty arrival days, long Recently-filed history); preview renders pages lazily; checklist lists are small by nature.
- **Performance budget:** queue navigation and candidate selection respond <100ms (local state); decision commits round-trip async with optimistic UI; recognition is fully asynchronous and never blocks the queue (a slow classification shows its stage, not a frozen surface).
- **Loading and latency feedback:** per-arrival stage chips during processing; decision commits show optimistic state with honest rollback; recognition timeouts surface as "recognition failed — route manually," never a spinner forever and never a guess.
- **Destructive action confirmation:** Dismiss requires a reason; bulk Dismiss confirms with count; Replace requires the downstream-impact strip acknowledgment. None of these are modals of legal weight — certify/send ceremonies do not exist here.
- **Certify and external-send safety:** not applicable by ownership — but the Replace path must render the certification-void warning when a supersession touches a certified package's inputs (void-on-change is never silent, 03 §2).
- **Source immutability:** arrival bytes are the Original artifact — immutable, hashed at preflight; re-routing never rewrites bytes; the Recreated-searchable derivative is always labeled and never represented as original (kit law §7).
- **Intake security posture (gap 1's named machinery, snapshot §6):** sender-authentication results (SPF/DKIM/DMARC) stored on the envelope and rendered as a recognition signal — a failing sender never contributes confidence to a sender-rule match and can never auto-file; attachment scanning at preflight (quarantine renders as an unprocessable class with its named reason, never silently dropped); the address is unguessable and rotatable in Settings with rotation events logged; per-client sub-addresses are scope-checked server-side (INT-20), mirroring the send vertical's org-scope honesty.
- **Auditability:** every arrival, suppression, receipt line, decision, rule mint, chase, waiver, and escalation is an append-only, identity-stamped ActivityEvent carrying loan/period identity (04 §2.3); the Recently-filed lane and the loan/period quiet logs are projections of the same store — one store, no contradictions (07 §2).

## 17. Acceptance tests and fixtures

Fixtures: **CAL-KIT** = the Calloway Park FYE-2018 evidence spine — T-12 (numbered COA 4010…8010; FYE-2018 Total OpEx $1,686,050 · NOI $1,218,877 · Net Income $460,159), rent roll (24 columns, 322 lease rows over 301 units; physical occupancy 279/301 = 92.69% at 04/30/2018), executed JLL quarterly Property Questionnaire (flattened scan, zero text layer) (evidence). **BEXLEY** = the demo canon loan ($15,232,500 UPB · Fannie 6001.NR reporting-only) (canon). Fixture bytes land as intake seed data (e.g. `src/lib/covenant/fixtures/intake/`), addressed by test id — never rendered as live arrivals outside tests (04 §2.5). Wherever a verdict could ever be phrased near Bexley's monitored 90% occupancy floor, the only permitted word is shortfall — verdicts do not render on this surface at all.

1. **INT-01 Quarterly package arrival, quiet lane.** Email to the org's intake address with the three CAL-KIT attachments; a sender rule exists for the PM accountant. Expect: preflight hashes all three; recognition splits and matches each to loan+period Q-FYE2018 checklist items (income statement §8.02(b)(1); rent schedule §8.02(b)(3); questionnaire); all clear threshold → zero queue rows; three Documents created; checklist items flip `matched`; one IntakeReceiptRecord + per-file ActivityEvents with reason chains; nothing pushes.
2. **INT-02 Ambiguous arrival, human lane.** Same email, no sender rule, two candidate loans share the property name. Expect: queue row in Needs your decision; inspector lists candidates 1–9 with confidence + WHY; pressing `2` then `F` files to the chosen loan+period; a sender rule is proposed and, on accept, minted with provenance; subsequent identical send auto-files citing the rule (ask-once: the question never recurs).
3. **INT-03 Exact duplicate.** The same email forwarded twice. Expect: second arrival suppressed at preflight (hash match), quiet-logged with a link to the original, never queued.
4. **INT-04 Revised T-12 supersedes.** A corrected CAL-KIT T-12 (changed bytes) arrives after INT-01. Expect: changed-hash + same doc-type/period stops automation; queue row; `R` shows the DownstreamImpactStrip naming the superseded document and the stale confirmations; commit files v2, marks v1 superseded in Documents, re-opens the affected checklist item, and flags downstream values stale (03 §2). If the period were certified, the strip shows the void warning and filing voids with a visible void event.
5. **INT-05 Unprocessable attachment.** A password-protected xlsx. Expect: named reason at preflight; queued with the reason; `I` sends the approved request-missing-item draft to the sender; receipt records the skip + the request.
6. **INT-06 Chase + auto-reopen.** BEXLEY Q2 checklist missing the rent schedule; cadence approved. Expect: agent-drafted chases sent on schedule (blackout dates honored), each quiet-logged with recipient + next-scheduled; item shows `chased (2 sent · last {date} · next {date})`; a reply with an attachment from that sender auto-reopens into the queue tied to the chase history; no push notification at any step.
7. **INT-07 Waive.** Human waives an on-request item (rate-capped per §8.02(b)(4), evidence). Expect: typed reason required; item shows `waived by {who} {date}`; completeness recomputes; ActivityEvent written; the waiver is visible in the period's holding projection.
8. **INT-08 Deadline gate.** Unrecognized arrival + BEXLEY Q2 due in 3 days with a missing item and chases exhausted. Expect: your-move escalation carrying the exact ask, deadline, and candidate evidence (03 §2 gate rows); this deadline path is the only push-eligible class per org policy.
9. **INT-09 Keyboard walk.** Entire INT-02 flow executed pointer-free: J/K → Enter → 1–9 → F/R/N/M/I/H/X → U; U within the session reverses the last filing and restores the queue row; the re-route affordance persists on the quiet-log row afterward.
10. **INT-10 Recognition provenance (lit signal).** Click each WHY line on a candidate: the sender line, filename token, or matched content region lights at its source and stays lit until dismissed; the wrong-region failure mode of U1-F1 is explicitly re-tested here on all three signal classes.
11. **INT-11 Viewports.** 1440 / 1728 / 2048 render the §10 ratios; at 1280–1439 the rail icon-collapses and all three regions hold minimums; <1280 work area the inspector becomes an overlay sheet and the queue a full-width register; no pane ever compresses below minimum (labeled-tab fallback verified).
12. **INT-12 Empty state.** Zero queued arrivals renders "All clear," the read-only address card (copy works; no edit affordance; "managed in Settings" links out), and open-checklist summary links.
13. **INT-13 Permission scope.** Reviewer role: decision bar disabled with reason; PMC preparer of Client A cannot see Client B arrivals (404-equivalent scope behavior consistent with the send vertical's honesty).
14. **INT-14 Accessibility.** Axe-clean; SR announces row content and decision outcomes; focus trap/restore on the overlay sheet; reduced-motion swap verified.
15. **INT-15 One-store audit.** Recently-filed lane rows, the loan quiet log, and the period quiet log all render the identical ActivityEvents for INT-01 (no independent feeds — 07 §2).
16. **INT-16 Start new period.** The CAL-KIT annual package (annual statements incl. cash flows, due FYE+120d per §8.02(b)(2) — evidence: SLOT-3) arrives before the engine has opened the annual occurrence. Expect: no open-period candidate clears threshold; queue row; `N` proposes the cadence occurrence with its due-rule provenance; commit opens the period via the spine (never a direct status write — 02 §3), materializes its checklist, and files the arrivals against it.
17. **INT-17 Merge duplicate.** The Q2 questionnaire scan arrives twice — once from the PM, once forwarded by the owner (different bytes, changed hash). Expect: automation stops; queue row flags the near-duplicate; `M` opens the merge view (both envelopes stacked, preview switchable); commit collapses onto the chosen canonical arrival, records the merge with both hashes, and files once.
18. **INT-18 Correspondence handoff.** An email from the servicer asking a question about the sent Q1 package (no attachments). Expect: recognition classifies correspondence-not-document; high confidence → handed to Lender Q&A quietly (thread created, quiet-logged, link rendered); ambiguous → queue row whose candidate list includes the correspondence thread; File hands off; no Document is created.
19. **INT-19 Processing race.** Open an arrival while classification is mid-flight. Expect: read-only preview with stage chip; decision bar disabled ("recognition running — route manually?" affordance enabled); forcing manual routing cancels recognition cleanly and records the manual decision; no half-applied extraction (R1 research: Ramp reject).
20. **INT-20 PMC scope + sub-addressing.** Client A's per-client sub-address receives a document referencing a Client B property. Expect: recognition confines candidates to Client A's loans; the mismatch surfaces as a conflict question, not a cross-client filing; the Client B preparer never sees the arrival.
21. **INT-21 One pipeline, three doors.** Upload the CAL-KIT rent roll via Documents' bulk upload. Expect: the identical preflight → recognition → filing pipeline runs (channel = documents-bulk-upload); the uploader sees the same recognition results inline; no second ingestion path exists anywhere in the codebase (static check: exactly one entry into the filing service).
22. **INT-22 Benchmark challenger review.** Walk the nine §14 rows against the built surface; each take-pattern demonstrably present, each reject-pattern demonstrably absent (special attention: no conversation-first framing anywhere; no per-arrival notification; no second ingestion path around the pipeline).

## 18. Build plan

- **Dependencies:** F2 persistence + tenancy (arrival, checklist, decision, rule tables with org/client FKs — 05 §6 gap 3); F1/V2 intake wave (per-org email provisioning, classifier wiring of `intake-classify.ts`, two-lane routing, intake security posture — 05 §6 gap 1); document storage + artifact chain (gap 2) for filing to create real Documents; the single ActivityEvent store; the due-rule engine F5 for deadline chips and escalation (gap 9); Settings → Intake for address + threshold policy.
- **Foundation work:** `/intake` route in `(covenant)` mounting CovenantShell; `intake_arrivals`, `checklist_items`, `sender_rules` migrations; the recognition service contract (classify → match → confidence per field) over the existing classifier seed.
- **API surface (send-vertical honesty grammar — 503 config / 403 role / 404 scope):** `POST /api/covenant/intake/inbound` (transport webhook → preflight), `GET /api/covenant/intake/arrivals` (org/client-scoped queue), `POST /api/covenant/intake/arrivals/[arrivalId]/decide` (the typed decision act, gate-checked and append-only like `POST /api/covenant/send`), `POST /api/covenant/intake/checklist/[itemId]/waive`.
- **Components to build first:** ArrivalQueue + ArrivalRow → RecognitionCandidateCard + DecisionBar (the decision heart) → HoldingChecklist → ArrivalEnvelope/AttachmentStrip (DocView reuse for preview) → receipts/undo/rules.
- **Vertical slice (the send-vertical pattern):** one org, one real intake address, one CAL-KIT email in → preflight → recognition → one human File decision to BEXLEY's open period → Document created → checklist item matched → ActivityEvents written — end to end against persisted state, honest failures included, before any breadth.
- **Migration from fixture data:** the fixture Intake/IntakeFlow/Holding screens retire behind a flag once the slice runs; `book.ts` arrival fixtures become seed/test data only; no screen ships rendering fixture arrivals as if live (04 §2.5).
- **Rollout/feature flag:** `intake-v1` flag gating route + rail-badge wiring; email provisioning dark-launched per org; auto-file threshold ships conservative (human lane default) and loosens per org after receipt-audit review.
- **Proof artifacts required:** the INT-01 quiet-lane run recorded (receipt + quiet log + checklist advance); INT-04 supersession with stale propagation; INT-06 chase-and-auto-reopen trace; INT-10 lit-signal captures at all three signal classes; the INT-21 single-entry static check; viewport captures at the four §10 sizes.
- **Final gate:** `ADJUST` confirmed — the holding-pattern idea stands; the machinery above is the build. PASS requires INT-01…INT-22 green against persisted state with the fixture screens retired.
