# SEND & RECORD

The second, heavier, irreversible gate — and the one place in Covenant where the API is already REAL and the human moment is not. Sources: DIRECTIONS `send-record.md`, R4 research, snapshot §3 (`POST /api/covenant/send`) / §6 gap 4, 02 §2/§5/§6, 03 §2 (gate 3), 04 §1 (Send row), 06 chain E, 08 §4, `certificate-certify-gate.md` (gate one, this surface's host).

## 1. Identity and verdict

- Product layer: reporting spine (per loan, per period — deal altitude).
- Route/address: not a standalone route. The surface is (a) the **post-certify state of `/covenant/[loanId]/[period]/certificate`** (the send panel in that route's right rail) and (b) the send-related cells of the `/reports` register rows. The API is `POST /api/covenant/send` — **REAL**: it runs the actual send gate against persisted covenant state, writes an append-only `package_sends` row, flips `reporting_periods.status → sent`, and fails honestly (503 without env, 404 on missing/foreign-org deal, 403 on gate deny); download-first; sender role currently server-fixed to admin (snapshot §3).
- Repository landing: components in `src/components/covenant/send/`; API at `src/app/api/covenant/send/` (exists); engine gate in `src/lib/covenant/`; tokens from `src/styles/covenant-tokens.css`.
- Current build state: `PARTIAL` — the pattern-proving vertical is real end to end at the API; **the human-facing moment is UNBUILT** (no UI is wired to the real route as the user's send act).
- Existing-surface verdict: `UNBUILT` (surface) over a REAL substrate — build the moment onto the proven gate; change nothing about the gate's honesty.
- Research tier: B.
- Primary users/roles: owner (the sender — send rights explicitly assignable in Settings Team & Roles, unfixing the server-admin-only role, gap 4); PMC preparer (prepares; sends only where the engagement assigns it); reviewer (read-only receipt viewer).
- Frequency and session duration: once per loan per period, immediately after certify or on a later deliberate visit; under one minute when the recipient block is remembered.
- Error cost: **maximal and permanent.** Certify is voidable; **send never is** (R4: the Stripe refund asymmetry) — a package sent to a lender cannot be unsent. This is why the second ceremony is the heavier of the two.
- Success criterion: a certified package reaches its lender as exactly the certified bytes, with an append-only receipt that answers who/when/what/to-whom forever; a failed gate renders its exact honest reason; no path — human, agent, or scheduled — sends without a typed human act.

## 2. User job and decisions

- Primary job-to-be-done: "Deliver to this lender exactly what I certified, and keep the proof."
- Decisions made here: send now / hold (and rely on reminders) / correct the recipient block (typed, provenance-stamped) / choose transport (download today; email when built — same gate) / retry after an honest failure (a fresh gate pass) / re-send the same certified bytes later (fresh gate pass, additive record).
- Questions the surface must answer, in scan order:
  1. Is this package certified and still valid (un-voided), and what is its hash?
  2. Who receives it — and where did those recipient details come from?
  3. Am I sending exactly the certified bytes? (The approved-bytes statement: hash(sent) == hash(certified), displayed.)
  4. When is it due, and how much margin remains?
  5. What exactly will happen when I confirm — and that it cannot be undone?
  6. Afterward: what is the receipt, and what does "sealed" mean?
- What the user should not have to decide here: nothing about content (the package is frozen by certification — any content doubt routes back through void + re-certify); no readiness re-litigation; no recipient research (lender-scope memory pre-fills with provenance).
- Entry paths:
  - The certify ceremony's completion — the panel activates in place, focus moves to it (the natural next act).
  - Home your-move "Send-ready" rows (deep link to the certificate route, panel section).
  - Calendar deadline escalation for certified-but-unsent packages (the Carta-style reminder ladder — R4).
  - Reports register: certified-not-sent rows link here; sealed rows open the read-only record.
- Exit paths:
  - Sent → the receipt + sealed banner; links to Reports and the sealed period record.
  - Failure → the exact failure card with its fix path (Settings / Certificate), with return.
  - Hold → nothing changes; the panel persists; reminders take over (a hold is a visible hold, never a snooze that hides the deadline).
- Completion/advancement conditions: gate 3 of the ruled lifecycle — `certified → packaged/sent`, period seals (seal-not-wipe) (03 §2). One transition, irreversible.

## 3. Object and ownership model

| Object/action/status | Owned here? | Summary/link elsewhere | Address/identity | Notes |
|---|---:|---|---|---|
| SendRecord (append-only) | YES | Reports lists send history; Loan Detail summarizes last-sent (02 §3) | `sendId` | timestamp, actor, transport, bytes hash, recipient, gate result |
| The send act + typed confirmation | YES | — | modal over the panel | Human only, ever |
| The receipt render (SendReceiptCard) | YES | Reports row cells re-render its fields | derived from `sendId` | The receipt is the record made visible |
| Sealed banner + seal explanation | YES (render) | Every surface shows the sealed period read-only | period state | Seal itself is the spine's transition |
| Recipient block content | NO — lender-scope MemoryEntry (agent layer store) | Rendered and corrected here with provenance ("learned {date} from {who}") | `memId` scope=lender | Corrections version the entry (03 §3) |
| Certification validity + hash | NO — Certificate surface | Displayed here as the approved-bytes statement | `certId` | Send requires certified + un-voided |
| Period status transitions (`→ sent`, seal) | NO — orchestration spine (the API flips it) | Status chip everywhere | `(loanId, period)` | Real today at the API |
| Deadline context | NO — Calendar/Obligations (due-rule engine) | Due date + days remaining rendered in the panel | deadline ref | Reminders are Calendar's escalation ladder |
| Reports register rows | NO — Reports | This brief specifies only the send-related cells | — | Certified(who/when) · Sent(when/transport/hash) · view sealed record |

No-double-homing boundary: this surface owns the send act, the SendRecord, and the receipt/seal rendering — nothing else. Recipient truth lives in the one memory store; certification truth lives with the certificate; period mechanics live in the spine; the register lives in Reports. Each renders summaries with links, never second homes (02 §3).

## 4. Data and semantic model

- Source facts: recipient contact details as remembered (lender-scope memory with provenance) or as corrected here (typed, versioned).
- Extracted values awaiting confirmation: none — nothing extracted renders here.
- Confirmed values: the recipient block after human correction (a versioned MemoryEntry, newest wins, never re-asked).
- Deterministic outputs: the gate verdict (pass / 503 / 404 / 403); the bytes hash of the outbound bundle; the equality check hash(sent) == hash(certified); deadline distance from the due-rule engine.
- Agent proposals/drafts: the prepared transport (bundle assembled for download; addressed email later) and the pre-filled recipient block — preparation only, never execution (04 §1 Send row).
- Human decisions: the typed SEND confirmation; recipient corrections; hold.
- Certified values: consumed, not created — the certified hash is the anchor this surface proves against.
- Versions/periods/packages: a send always names `(loanId, period, rev, certId)`; re-sends append new SendRecords against the same certified bytes; a voided certification retracts the panel (nothing sendable exists).
- Evidence/provenance: the receipt is itself a provenance object; the recipient block carries memory provenance; the hash chips link to the certification record.
- Permissions/read-only projections: send rights role-scoped (Settings; gap 4 unfixes server-admin); non-senders see the panel read-only with "sending reserved to {role}"; reviewers see receipts only.

Field groups and grain: organization (sender identity, role policy) · loan (lender/servicer identity) · reporting period (deadline, status) · package (rev, certified hash) · certification record (validity, who/when) · send record (sendId, when, actor, transport, recipient, bytes hash, gate result) · memory entry (recipient block, scope=lender, provenance, version chain).

## 5. State machine and exceptions

Each state carries: trigger · prerequisites · actor · render · reversibility · audit event · failure behavior.

- **dormant (pre-certify)**
  - Trigger: period not yet certified. Render: the send panel does not render at all — not disabled, absent (the rail shows readiness/history only, per the certificate brief).
  - Failure behavior: no path ever renders a send affordance for uncertified content.
- **ready-to-send**
  - Trigger: certification valid (present, un-voided). Actor: engine validates on view.
  - Render: recipient block (with memory provenance, editable), transport (Download bundle — live; Email — labeled roadmap, disabled honestly), the approved-bytes statement (both hashes + equality mark), deadline context (due date, days remaining, due-rule provenance), the SEND control.
  - Reversibility: full — nothing has happened yet. Audit: none yet.
- **awaiting-documents / partial / extracted-unconfirmed / low-confidence / conflict / failed tie-out / stale**
  - Not reachable here by construction: all block readiness upstream of certification; a certified package has passed them. They appear only as history (via the certificate's what-changed and readiness records). If any input changes now, the path is **void** (below), never an in-place exception.
- **loading/processing (the gate pass)**
  - Trigger: the typed act fired. Actor: engine (the real gate).
  - Render: SEND control held pressed with working state; panel locked against double-fire.
  - Failure behavior: timeout renders an honest unknown-outcome state instructing a receipt check before any retry (no blind resend).
- **gate failure — 503 (configuration)**
  - Trigger: persistence env not configured (the API's honest 503 — snapshot §3). Render exactly: "Sending is not configured for this workspace" + admin-facing fix path (Settings link, error identity for support); no retry-spam affordance.
  - Reversibility: retry after fix is a fresh gate pass. Audit: failure logged.
- **gate failure — 404 (scope)**
  - Trigger: missing or foreign-org deal (the API's 404). Render exactly: "This loan and period could not be found in your organization" — no existence leak beyond that; link back to Loans.
  - Failure behavior: never masked as a generic error; never retried automatically.
- **gate failure — 403 (gate deny)**
  - Trigger: the send gate refused — role lacks send rights, certification missing/voided, or gate checks failed (the API's 403). Render exactly: the deny reason the gate returned ("why"), mapped to its fix path (Settings for role; Certificate for cert state).
  - Failure behavior: the reason is always specific; a 403 without a rendered why is a defect.
- **voided-under-you**
  - Trigger: the certification voids between panel render and act (void-on-change upstream). Actor: engine.
  - Render: the panel retracts to the certificate's VOID banner state; the attempted act aborts server-side regardless (the gate re-checks cert validity — 02 §6).
  - Failure behavior: the client render is a courtesy; the gate is the enforcement.
- **certified-but-unsent, deadline approaching**
  - Trigger: due-rule distance crosses the escalation policy. Actor: agent reminds (Carta-style escalating reminders — R4); Calendar owns the ladder.
  - Render: deadline context in the panel gains escalation state; Home your-move shows the send-ready row.
  - Failure behavior: **pressure on humans, never automation of the act — no auto-send ever** (the Avalara auto-approve contrast, the named rejection — R4).
- **sent/sealed**
  - Trigger: gate passed; SendRecord written; period flipped `sent` and sealed. Actor: the engine records; the human acted.
  - Render: SendReceiptCard (timestamp, actor, transport, recipient, bytes hash, gate result) replaces the send form; SealedBanner explains the seal in-surface: "This period is sealed: read-only forever, fully inspectable — documents, values, verdicts, the certification, and every send record remain addressable" (seal-not-wipe, 03 §2).
  - Reversibility: **none, by design.** Audit: append-only SendRecord + seal event quiet-logged.
- **re-send (additive)**
  - Trigger: a later deliberate send of the same certified bytes (lender lost it; second contact). Prerequisites: same valid certification; fresh full gate pass.
  - Render: the sealed panel offers "send again" → the same ceremony; a new SendRecord appends; prior records untouched (append-only — corrections are new records, never edits; R4: Modern Treasury).
- **permission denied**
  - Trigger: viewer lacks send rights. Render: panel visible read-only; "sending reserved to {role/name}"; receipts visible.
- **read-only**: reviewer role or any viewer on a sealed period — receipts and seal render; no controls.
- **recovery/undo**
  - **There is no undo. There is no cancel window.** The ceremony states this plainly before the act ("this cannot be undone — a lender will have this package"). A mistaken send is handled outside the product's mechanics (correspondence with the lender) plus a subsequent corrective package — the record of the mistake is never edited or deleted (R4: Modern Treasury append-only; Stripe's plainly-stated irreversible boundary).

## 6. Engine / Agent / Human / Gate / Quiet Log

| Phase | ENGINE (deterministic) | AGENT (prepares/proposes) | HUMAN (typed decisions) | GATE (interrupts) | QUIET LOG |
|---|---|---|---|---|---|
| Preparation | Validates cert present + un-voided; computes bundle hash; deadline distance | Assembles the transport bundle; pre-fills recipient block from lender-scope memory with provenance | Corrects recipients (typed, versioned) | — | Bundle prepared |
| The act | The send gate: org scope, role, cert validity, approved-bytes hash; append-only record; honest 503/404/403 | — (the agent NEVER sends — locked trust hierarchy) | **The send act — human only, ever**: typed SEND confirmation | The send ceremony is the gate | SendRecord + period seal |
| Reminders | Due-rule distance; escalation state machine | Drafts reminder framing; chases per approved cadence | Acts, or holds | Deadline escalation (the one push-eligible class) | Reminders sent |
| After | Seal enforcement; record immutability | Answers "what did we send" with the receipt + chains | — | — | Receipt views not logged; re-sends appended |

Lane invariants (04 §2): no auto-send at any deadline, ever — the gate accepts only an authenticated human session performing a typed act, and there is no API path around it (R4 synthesis 12); honest failures render exactly; the log is append-only and identity-stamped.

## 7. Information hierarchy

Scan order (within the send panel, post-certify):

1. Panel header: "Send to lender" + certification validity chip (certified {who}, {when}).
2. Decision/status summary: deadline context — due date, days remaining, escalation state if any.
3. Primary work region: recipient block (with provenance line) → transport → the approved-bytes statement (hash equality, both chips).
4. Secondary context: what sending does (one sentence: gate → record → seal) + the irreversibility statement.
5. Evidence/proof: hash chips link to the certification record; recipient provenance links to the memory entry.
6. Actions: SEND (primary, typed ceremony); "download bundle" (the transport itself today); hold implicitly by doing nothing.
7. Activity/history: post-send, the receipt + prior SendRecords list (append-only, newest first).

Absent by design: charts; any content preview editing (the certificate sheet above the panel IS the content); auto-send scheduling of any kind; a snooze that hides the deadline (holds are visible holds); any second confirmation dialog stacked on the ceremony (NN/g budget — R4).

## 8. Page anatomy and regions

**Region A — the send panel (right rail of the certificate route, post-certify; pinned).**

- Purpose: the whole second gate in one column: who, what-exactly, when-due, the act.
- Content: RecipientBlock → TransportRow → ApprovedBytesStatement → DeadlineContext → SendControlZone; post-send, SendReceiptCard + SealedBanner + prior-sends list.
- Persistence: pinned (it is the rail's bottom section per the certificate brief §8 Region B).
- Interaction: recipient edit-in-place (typed, provenance-stamped); hash chips expand; SEND opens the ceremony modal.
- Minimum width: 300px (rail minimum); the panel never renders outside a valid certified context.
- Resize/collapse: follows the rail (tabs fallback below 300px — the panel becomes the "Send" tab).
- Why a rail panel: the act must sit beside the certified sheet it ships — co-visibility of hash chip (sheet header) and hash statement (panel) is the approved-bytes proof made visual.

**Region B — the send ceremony modal (transient; the second legal-weight modal).**

- Purpose: the typed act. Deliberately **not** the certify ceremony's shape (mutually distinct — NN/g via R4: distinct mechanics so neither trains reflex for the other).
- Content, single review-then-act frame: recipient list (restated in full) · transport · package identity (loan, period, rev) · both hashes with the equality mark · deadline line · the irreversibility statement in plain words ("This cannot be undone. Certify can be voided; a send cannot.") · then the typed confirmation: **type SEND** (the word — not your name; the name belongs to certify) · the Send button (accent family) arms on match.
- Persistence: transient; Esc cancels, nothing written.
- Minimum width: 560px; full-viewport takeover <1280px.
- Why a modal: legal-weight typed act (08 §4); everything needed is restated inside.

**Region C — the receipt (in-panel, post-send; persistent forever).**

- Purpose: the append-only SendRecord made human-readable — the proof object.
- Content: SendReceiptCard fields (timestamp · actor · transport · recipient · bytes hash · gate result) + SealedBanner with the in-surface seal explanation + "view in Reports" link.
- Persistence: persistent for the life of the record (forever — seal-not-wipe).
- Why in-panel: the receipt replaces the form in place — the act's result renders exactly where the act was taken.

**Region D — Reports register cells (in `/reports`, owned by Reports; specified here).**

- The send-related cells per row: Certified (who/when chip) · Sent (when/transport) · Hash (short chip; click = full + verify affordance) · Status (sealed) · "→ sealed record" opening the read-only certificate render with receipts.
- Certified-but-unsent rows render the deadline escalation state — the register is where stuck ceremonies are visible in aggregate (R4: Carta reminders; Avalara cadence).

## 9. Co-visibility matrix

| Datum/surface A | Datum/surface B | Must be simultaneous? | User decision enabled | Default topology | Fallback |
|---|---|---:|---|---|---|
| Approved-bytes statement | The certified sheet's hash chip | YES | "Am I sending exactly what I certified?" | Panel beside sheet (same viewport) | Narrow: the modal restates both hashes |
| Recipient block | Its memory provenance | YES | "Do I trust these recipients?" | Provenance line inside the block | Expand-in-place |
| Deadline context | The SEND control | YES | "Send now or hold?" | Same panel column | Same |
| Ceremony modal | Anything beneath | NO (restates all it needs) | The act | Modal | Full-screen takeover |
| Receipt | Sealed banner | YES post-send | "What do I hold as proof; what does sealed mean?" | Stacked in panel | Same |
| Gate failure card | Its fix path | YES | "What do I do about it?" | Failure card in panel with link | Same |

## 10. Layouts and viewport behavior

- The panel inherits the certificate route's topologies (certificate brief §10): rail 320 at 1440 / 380 at 1728 / 400 at 2048; the panel is the rail's bottom section, min-height 320px when active.
- **1440px**
  - Panel 320 wide; ceremony modal 560 centered; receipt stacks below history.
  - The certified sheet's hash chip and the panel's statement co-visible at rest.
- **1728px**
  - Panel 380; the modal offsets left so the sheet's hash chip stays visible during the ceremony (the approved-bytes co-visibility held even mid-act).
- **2048px**
  - Panel 400; with the Evidence overlay pinned (certificate §10), the panel remains rightmost — the act never hides behind evidence.
- **Narrow/compact (<1280px)**
  - The panel is the "Send" tab above the sheet; the ceremony is a full-viewport takeover with identical content.
  - The receipt renders in the tab and in the period header summary.
- Minimum viable dimensions: 1152×720 (the certificate route's minimum governs); below, the ceremony refuses with the frame's larger-window state.
- Focus behavior: after certify completes, focus moves to the panel header (the natural next act); after send, focus lands on the receipt.
- Compare/proof behavior: hash expand-in-place; no split windows here, ever.
- No silent compression: the panel becomes a labeled tab, never squeezes.

## 11. Components and exact anatomy

- **SendPanel** — NEW (`src/components/covenant/send/SendPanel.tsx`). Parts:
  - Header "Send to lender" + certification validity chip (certified {who}, {when} — links the record).
  - The stack per §7: RecipientBlock → TransportRow → ApprovedBytesStatement → DeadlineContext → SendControlZone.
  - Mounts only when a valid un-voided certification exists (absent otherwise, never disabled).
  - Post-send it re-renders as receipt + seal + prior-sends (§8 Region C).
- **RecipientBlock** — NEW. Parts:
  - Recipient rows: name · role/organization · address (per transport), uniform row heights.
  - Provenance line: "learned {date} from {who}" linking the lender-scope MemoryEntry (03 §3); corrected entries show version affordance.
  - Edit-in-place with typed save; a correction writes a new memory version (never edits history).
- **TransportRow** — NEW. Parts:
  - Download bundle — live; the act's transport today. The download IS the send transport: the gate runs, the record writes, then the bundle downloads (download-first per the real API; snapshot §3).
  - Email — rendered, labeled roadmap, disabled honestly ("email delivery is not yet available"); when built it rides the **same gate** as an additive transport, never a bypass (02 §6).
  - Transport is recorded on every SendRecord; the receipt names it.
- **ApprovedBytesStatement** — NEW. Parts:
  - Sentence: "You are sending exactly what you certified."
  - Two HashChips (REUSE from certificate brief): certified hash · outbound bundle hash, with the equality mark between; either expands to full.
  - If the engine ever computes inequality, the panel refuses to render the SEND control at all and shows the mismatch as a defect state (this state existing visibly is itself a safety property — it must be impossible-by-construction, and if constructed anyway, loud).
- **DeadlineContext** — NEW. Parts:
  - Due date · days remaining (tabular figures) · due-rule provenance ("Q+45d per §8.02(b) — evidence-class cite").
  - Escalation state chip when the reminder ladder is active (Carta-style marks — R4), linking the Calendar row.
  - Overdue renders loudly (the one push-eligible class per policy) — and still, only a human sends.
- **SendCeremonyModal** — NEW. Parts per §8 Region B: full restatement block · irreversibility statement · typed-SEND field with live match · Send button in the Covenant accent family (#7189FF, hover #8EA1FF, active #6078F4, label #0B1020 on-accent) — deliberately NOT the certify blue-violet: the two ceremonies must not look or feel alike (R4: NN/g adaptation; the paper hexes stay on the certificate's act).
- **SendReceiptCard** — NEW. Fields: timestamp · actor · transport · recipient(s) · bytes hash (HashChip) · gate result; rendered as a receipt object (document-adjacent card), exportable text; links: certification record, Reports row, sealed period.
- **SealedBanner** — NEW. The seal mark + the in-surface explanation of seal-not-wipe (§5 sent/sealed wording); renders here, on the certificate header, and on the sealed period everywhere via the period status.
- **GateFailureCard** — NEW. One component, three contents mapped 1:1 to the API's honest failures (503 config / 404 scope / 403 deny-with-why), each with its fix path; never a generic toast.
- **PriorSendsList** — NEW. Append-only list of SendRecords (re-sends); rows link their receipts.
- **CountBadge / Figure / Evidence overlay** — REUSE where counts, figures, or traces render (hash chips are not Figures; they link to records, not source rows).

## 12. Interaction specification

- Selection: recipient rows and receipt fields selectable for copy; hash chips copy on click.
- Hover: provenance line reveals full memory context; accent-family controls follow ruled hover states.
- Focus: panel is fully traversable; the ceremony traps focus (standard modal trap, Esc exits).
- Keyboard map:

| Key | Context | Behavior |
|---|---|---|
| `G F` | loan context | Reach the certificate route (this surface's host) |
| `Tab` / `Shift+Tab` | panel | Traversal: recipient → transport → statement → deadline → SEND |
| `Enter` | focused SEND control | Open the send ceremony modal |
| (typing `SEND`) | modal | The only arming mechanism — no shortcut arms or fires the act |
| `Enter` | armed Send button focused | Fire the act (single fire; button locks during the gate pass) |
| `Esc` | modal open | Cancel; nothing written |
| `Enter` | recipient row | Edit-in-place; typed save on `Enter`, revert on `Esc` |
- Editing and validation: recipient edits validate per transport (address shape); typed save; the SEND word must match exactly, case-sensitive, stated in the field's label.
- Bulk action: none — sends are per package, always singular; there is no "send all ready" anywhere in the product (the Avalara rejection made structural).
- Undo/recovery: none post-act (§5); pre-act cancel is free; an unknown-outcome timeout instructs a receipt check before retry.
- Sorting/filtering: none in the panel; Reports owns register filtering.
- Drill-down and return path: failure cards → their fix surface (Settings/Certificate) with return; receipt → Reports/sealed record.
- Source-linked selection: recipient provenance → memory entry; hash chips → certification record; not lit-row traces (no source documents are claimed here).
- Save/persistence: the act writes the SendRecord atomically at the gate; recipient corrections persist as memory versions immediately on typed save.
- Collaboration/commenting: none — correspondence with the lender after sending lives in Lender Q&A.

## 13. Visual craft direction

- The panel is a **frame surface**, not paper: ten-rung gray ladder ground, open-not-boxed sections, hairlines from the ladder — the certified-sheet hexes never paint here (they are the sheet's and the certify act's; kit law). The panel sits beside the paper and must read as machinery, not document.
- The Send control and ceremony use the **Covenant accent family** (#7189FF · hover #8EA1FF · active #6078F4 · tint #A9B5FF · on-accent #0B1020) — the ruled product accent, and the deliberate visual distinction from the certify blue-violet: two gates, two palettes, zero confusion about which ceremony you are in.
- Typography: panel headers 13/20 semibold; body 13/20; hashes and all figures in Geist Mono with `tabular-nums slashed-zero`; the irreversibility statement set at full body weight — never fine print (Stripe's plainly-stated boundary, R4).
- Financial-number treatment: deadline counts and any figures tabular slashed-zero; the receipt's timestamp in ISO-adjacent unambiguous form.
- Spacing rhythm: 8px grid; 16px between panel sections; the SEND control isolated with 24px clearance — nothing crowds the act.
- Density: compact rail density, but the ceremony modal is airy (the one place slowness is a feature).
- Open ground versus earned boundaries: sections separated by ladder hairlines; the receipt card earns its border (it is an object, not a region); the failure card earns its emphasis.
- Dividers/elevation: panel flat in the rail; modal at standard frame elevation; receipt one soft step.
- Semantic color: state communication leans on words + the ruled severity roles the frame already uses for escalation states; no new values; no severity color decoratively.
- The certified sheet's ruled paper treatment: touched only by adjacency — the panel references the sheet's HashChip; it never re-paints paper.
- Focus/selected/hover: frame tokens throughout; the armed Send button's transition from disarmed is a state swap, not an animation.
- Chart style: no charts (nothing here could out-encode the receipt's own text).
- Motion: panel activation after certify is a 200ms settle; receipt replaces form with a single crossfade; `prefers-reduced-motion`: instant swaps.
- Long-session ergonomics: not applicable — this is a one-minute surface; the design goal is deliberateness, not endurance.

## 14. Benchmark research and synthesis

| Product | Limited role | Official evidence | Exact pattern to take | Covenant adaptation | What to reject | Why this reference beats alternatives |
|---|---|---|---|---|---|---|
| Modern Treasury | Approval-then-execution split + append-only records | docs.moderntreasury.com/payments/docs/approval-rules-overview · moderntreasury.com/learn/data-immutability (R4) | Approval gates the external act; approved content is locked; records are append-only — "if mistakes are made, they are corrected with a subsequent entry," never overwritten | Certify approves; send executes — two human gates (MT's second gate is a bank; Covenant's is the human sender); SendRecords append-only; mis-sends annotated by subsequent records, never edited | Auto-transmission after approval | The reference architecture for approve-vs-execute with immutable records, stated as doctrine |
| Stripe Dashboard | Irreversibility asymmetry + object timeline | docs.stripe.com/refunds · docs.stripe.com/development/dashboard/events (R4) | The irreversible boundary stated plainly (refunds: a short cancel window, then never); every object's history as an append-only event timeline | **Certify is voidable; send never is** — so send carries the heavier review (recipient, rev, hash match) and states its irreversibility in full body text; the sealed period's history is its event trail | Any cancel window on send — a lender-bound package has no takesies-backsies; charts on registers | The cleanest public statement that irreversibility should be said, not hidden |
| GitHub Releases | Publish-then-immutable + on-artifact proof | docs.github.com/en/code-security/concepts/supply-chain-security/immutable-releases (R4) | Post-publish: assets frozen, immutability badged on the artifact, attestation independently verifiable | The seal badged in-surface (SealedBanner) with its meaning explained; the receipt's hash lets a lender verify they hold the certified bytes | Repository-resurrection-grade name permanence (irrelevant to periods) | Immutability rendered as UI on the artifact itself, not just enforced |
| Carta | Chasing stuck ceremonies | support.carta.com/kb/guide/en/how-to-remind-a-signatory-or-holder-to-sign-a-security-MOPXFr6S1q (R4) | Automatic escalating reminders (5/10/20/30/90-day marks) while a required act remains undone | Certified-but-unsent packages nearing deadline get escalating reminders per the org's ladder (Calendar owns it); pressure rises, the act stays human | Reminder automation sliding into act automation | The proven pattern for two-act flows where the second act stalls |
| Avalara Managed Returns | **The named rejection** | help.avalara.com/Returns/Monthly_filing_timeline_for_Managed_Returns (R4) | (Contrast) the deadline cadence and review-then-file split | Deadline pressure as loud escalation on humans | **Auto-approve/auto-file at the deadline — rejected absolutely and permanently: Covenant never sends on a human's behalf; a missed deadline surfaces loudly instead** (R4 synthesis 12) | The single clearest picture of the failure mode this gate is built to never become |
| Nielsen Norman Group | Ceremony distinctness | nngroup.com/articles/confirmation-dialog/ (R4) | Nonstandard confirmations reserved for the rarest, most dangerous acts — and they must not become standard | The send ceremony is mechanically distinct from certify (type SEND vs type your name; accent family vs paper blue-violet) so neither becomes reflex | A third heavy ceremony anywhere | The budget law: two ceremonies, mutually distinct, everything else frictionless |

Synthesis: the send moment is original because it composes the strictest half of each reference around an already-real gate — Modern Treasury's approve/execute split and append-only ledger, Stripe's plainly-stated irreversibility (inverted into the certify-voidable/send-never asymmetry), Releases' badge-the-immutability, Carta's chase-the-stall — onto a borrower-side act no benchmark has: shipping attested financials to a lender as provably the certified bytes, with a receipt that answers who/when/what/to-whom forever. The Avalara contrast keeps it honest: the deadline may shout, but only a human hand moves (R4 synthesis 6, 8, 9, 11, 12).

## 15. Domain references

Terminology and workflow-expectation references only: loan-servicer submission workflows as evidenced in the real record (JLL questionnaire transmission expectations; §8.02(b) delivery deadlines — evidence); Avalara-class filing cadence for "submitted/accepted" vocabulary; Carta-class issuance for two-party delivery vocabulary. **Domain authority does not equal visual authority** — no referenced product contributes a pixel; covenant semantics, deadlines, and deliverable definitions come from the loan documents and Terry, never from any referenced product (domain-content firewall).

## 16. Accessibility, performance, and safety

- WCAG contrast: accent-family controls verified against ladder grounds by canvas readback (oklch caution); the irreversibility statement at body contrast, never de-emphasized.
- Focus: visible everywhere; modal trap standard; post-act focus lands on the receipt (the outcome is announced, not implied).
- Keyboard completeness: the entire flow — recipient correction, ceremony, receipt, failure recovery — is pointer-free (§12).
- Screen-reader semantics: the panel is a labeled region; the ceremony modal is `alertdialog` carrying recipient, hash equality, and the irreversibility statement in its accessible description; gate failures are live-region announced once with their reason.
- Table virtualization/large data: not applicable (bounded lists).
- Loading and latency feedback: the gate pass holds a working state; unknown-outcome timeouts instruct receipt-check-before-retry; no optimistic "sent" ever renders before the record exists.
- Destructive action confirmation: send is the product's one irreversible act — the typed ceremony is the confirmation; nothing stacks on it.
- Certify and external-send safety (the template's typed-acts/approved-bytes triad, applied to gate two):
  - The gate re-checks org scope, role, certification validity, and the approved-bytes hash server-side on every pass — client state is never trusted (02 §6).
  - hash(sent) == hash(certified) or the gate refuses; there is no override.
  - No agent, scheduled job, or API path sends without an authenticated human session's typed act (R4 synthesis 12).
  - Failures render exactly as returned (503/404/403 with the gate's why); a masked failure is a defect.
  - The two ceremonies stay mutually distinct in mechanics and palette so neither decays into reflex (NN/g budget — R4).
- Source immutability: sent bytes are the certified bytes; the SendRecord is append-only; sealed periods are read-only forever.
- Auditability: SendRecord + seal event + the receipt reconstruct every delivery forever; Reports is the register; re-sends append, never replace.

## 17. Acceptance tests and fixtures

Fixtures: `FIX-CAL-2018` (Calloway Park FYE-2018 evidence spine, certified per the certificate brief's tests) and `FIX-BEX-Q` (Bexley canon quarterly, certified). Both begin certified + un-voided unless stated.

1. Panel mounting: pre-certify, the send panel does not exist in the DOM (not hidden, absent); post-certify it activates in place with focus moved to its header.
2. Approved bytes (`FIX-CAL-2018`): the panel renders certified hash and bundle hash with the equality mark; the stored SendRecord hash equals both; an artificially mismatched bundle causes the SEND control not to render and the mismatch defect state to show loudly.
3. The ceremony:
   - The modal restates recipients, transport, loan/period/rev, both hashes, deadline, and the irreversibility statement.
   - Typing anything but SEND keeps the act disarmed; SEND arms it; firing calls the real `POST /api/covenant/send`.
   - Success renders the SendReceiptCard with timestamp, actor, transport, recipient, hash, gate result; the period flips sent; the SealedBanner renders with the seal explanation.
4. Honest failures, rendered exactly as the API returns them:
   - 503 (env unconfigured): the config failure card with admin-facing fix path — assert no generic error, no auto-retry.
   - 404 (foreign-org deal): the scope card with no existence leak.
   - 403 (gate deny): the deny card rendering the gate's why (role vs cert-state), each with its fix link.
5. Retry is a fresh gate pass: after a 403 fixed via Settings role grant, the retry re-runs all gate checks (assert server-side re-validation, not a cached pass).
6. Void race: void the certification after panel render, then fire; assert the server rejects (403 cert-invalid), the client renders the retraction, and no SendRecord exists.
7. No-auto-send: with a certified-but-unsent package crossing every escalation threshold in the ladder, assert reminders escalate (quiet log + your-move + the one push class) and the package remains unsent forever absent a human act — there is no code path from scheduler to gate (the Avalara rejection test).
8. Re-send: on a sealed period, "send again" runs the full ceremony and appends a second SendRecord; the first record is byte-identical to before (append-only store assertion).
9. Recipient memory: the block pre-fills from the lender-scope entry with "learned {date} from {who}"; a correction writes a new version; the next period's panel pre-fills the corrected value and never re-asks (03 §3).
10. Reports cells: the sealed `FIX-CAL-2018` row renders Certified (who/when) · Sent (when/transport) · short hash (click → full + verify) · sealed status · working "→ sealed record" link; a certified-but-unsent row renders its escalation state.
11. Irreversibility copy: the ceremony's statement is present at body size in every viewport including the <1280px takeover (assert text, size, and contrast).
12. Keyboard fixture: correct a recipient, complete the ceremony, and reach the receipt pointer-free; Esc cancels cleanly pre-act with nothing written.
13. Accessibility: axe pass; `alertdialog` semantics; failure live-regions announce once; contrast by readback.
14. Data integrity: `package_sends` rows are append-only (update/delete attempts fail at the store layer); `reporting_periods.status` transitions only via the gate.
15. Benchmark challenger review: a reviewer armed with §14 hunts for a property present in a benchmark and absent here (append-only corrections, stated irreversibility, on-artifact seal badge, stall reminders, approve/execute split) — any hit fails the gate.

## 18. Build plan

- Dependencies: the REAL send gate (exists — the pattern-proving vertical; build onto it, change nothing about its honesty) · F2 persistence + roles (unfix sender from server-admin via Settings Team & Roles — gap 4) · F4 PDF/XLSX rendering (the bundle's contents; until built, the download bundle carries what exists and says so honestly) · the certificate surface's certified state (gate one, this panel's mount condition) · lender-scope memory store (recipient block) · the due-rule engine (deadline context; reminders via Calendar's ladder).
- Foundation work: map the API's three failure modes to the GateFailureCard contents 1:1; define the SendRecord → receipt render; thread the certified hash from the certification record into the panel.
- Components to build first: SendPanel + ApprovedBytesStatement (the proof spine) → SendCeremonyModal → SendReceiptCard + SealedBanner → GateFailureCard set → RecipientBlock with memory wiring → DeadlineContext + reminder states → Reports cells.
- Vertical slice: this IS the vertical — the API already runs gate → record → flip against persisted state (snapshot §3). The slice is UI-completion: one loan+period, panel → ceremony → real API → receipt → sealed render, end to end, no mock at any layer.
- Migration from fixture data: none to migrate in the gate (already real); the panel must launch wired — a fixture send panel is forbidden (it would demo an irreversible act against nothing).
- Rollout/feature flag: `covenant.send.moment` gates the panel. Order:
  1. Panel + approved-bytes statement + ceremony against the real API (download transport).
  2. Failure cards + reminder states.
  3. Reports cells wired to real records.
  4. Email transport later behind `covenant.send.email` on the same gate (additive transport, never a bypass — 02 §6).
- Proof artifacts required: a full-period screencast (certify → send → receipt → sealed) on seeded persistence; the three failure renders (503/404/403) captured against the real API; the append-only store assertion output.
- Final gate: `ADJUST` at the system level (the vertical stands; the moment completes it) — ship when tests 1–15 are green against the real gate, never against a mock.
