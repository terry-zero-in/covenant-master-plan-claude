# CERTIFICATE & CERTIFY GATE

The certified sheet and the first of the product's two heavy ceremonies. Sources: DIRECTIONS `certificate-certify-gate.md` (lead-reviewer decisions, expanded here, not re-decided), R4 research, snapshot §3/§4, 02 §2/§5, 03 §2 (gate 2), 04 §1 (Certify row), 06 chain E, 08 §2/§4/§5, `cross-cutting/provenance-lit-row-trace.md`.

## 1. Identity and verdict

- Product layer: reporting spine (per loan, per period — deal altitude).
- Route/address: `/covenant/[loanId]/[period]/certificate` — **EXISTS** with its own resolver + tests; it is the deep-route pattern proof for the whole `(covenant)` group (snapshot §3; 08 §1).
- Repository landing: `src/app/(covenant)/covenant/[loanId]/[period]/certificate/` (exists); components in `src/components/covenant/certificate/`; engine reads from `src/lib/covenant/`; tokens consumed from `src/styles/covenant-tokens.css`.
- Current build state: `PARTIAL` — route + resolver REAL; sheet content fixture; ceremony absent; and the load-bearing phase-3 negative: **the sheet paints none of the eight ruled paper-accent tokens** (six of the eight have zero consumers anywhere; the only live render of the family is the Evidence panel's lit trace row) (snapshot §4).
- Existing-surface verdict: `ADJUST` (route and chassis right; paint, ceremony, record, and wiring missing).
- Research tier: B.
- Primary users/roles: owner (the certifier — the accountability act is theirs); PMC preparer (prepares and inspects; certify rights only where the engagement assigns them — 02 §4); reviewer (read-only).
- Frequency and session duration: once per loan per cadence occurrence (monthly/quarterly + annual). Normal session 1–3 minutes at the end of the ~60-second review; deadline pressure is real (Q+45d / FYE+120d class due-rules — evidence: SLOT-3 §8.02(b)).
- Error cost: **terminal.** A wrong number certified here is a wrong number attested to a lender. Every safety mechanism in the product converges on this sheet.
- Success criterion: a ready period is certified in under two minutes with zero ambiguity about what is being attested (contents, hash, identity, capacity); a not-ready period cannot be certified and says exactly why; any post-certification change voids visibly, never silently.

## 2. User job and decisions

- Primary job-to-be-done: "Attest, with legal weight, that this exact package is what I report to this lender for this period."
- Decisions made here: certify now / go fix a blocking item (routed to its owner) / inspect a figure's chain before signing / acknowledge a void and re-certify. Nothing else. Send is a separate decision on a separate gate (see `send-record.md`).
- Questions the surface must answer, in scan order:
  1. Which loan and period is this, and at which package revision?
  2. Is it ready to certify — and if not, exactly what blocks it, with a link per blocker?
  3. What am I attesting — contents, key figures, verdicts, attachments?
  4. What changed since the prior period, and is the delta expected?
  5. Can I trust any figure I doubt (lit-row proof on that exact figure)?
  6. Who certified before, and were any certifications voided, and why?
- What the user should not have to decide here: no composition choices (Composer owns sections); no exception disposition (Review Room owns the walk); no recipient/transport choices (the send panel activates only post-certify); no threshold interpretation (the engine computed under the loan's own definitions — never re-litigated at the signature).
- Entry paths:
  - Review Room readiness CTA ("Proceed to certify") — the primary path, end of the 60-second review.
  - Home your-move "Certify-ready" rows (deep link with loan+period identity).
  - Composer readiness rail; Calendar deadline row → period → certificate.
  - Reports sealed row (read-only render of this surface inside the sealed record).
  - Breadcrumb period selector; palette; `G F` chord in loan context (08 §5).
- Exit paths:
  - Post-certify → the send panel in the right rail (same route; `send-record.md`).
  - Void or not-ready → the linked blocking item's owning surface, with a return-to-certificate path.
  - Sealed → Reports.
- Completion/advancement conditions: gate 2 of the ruled lifecycle — `ready → certified` on the typed act; any content change → void → back to `in-review` (03 §2). This surface performs exactly one state transition, ever.

## 3. Object and ownership model

| Object/action/status | Owned here? | Summary/link elsewhere | Address/identity | Notes |
|---|---:|---|---|---|
| CertificationRecord (the act + record) | YES | Composer shows certified state; Send requires it; Reports lists it (02 §3) | `certId` per `(pkgId)` | who, capacity, when, content hash, void state |
| Void event (void-on-change) | YES (part of certification history) | Review Room renders the void reason as stop #1 on re-entry | event on `certId` | Engine-raised; reason mandatory; never silent |
| The certified sheet render | YES | Reports re-renders it read-only inside sealed records | route | The paper artifact; PDF render = F4 |
| CertificationRecordArtifact (the human-readable certificate) | YES | Exports with the package; Reports links it | derived from `certId` | R4: DocuSign CoC × GitHub release attestation |
| Package contents/revision | NO — Composer | Sheet renders the composed rev; "open in Composer" link pre-certify | `(loanId, period, rev)` | This surface never edits content |
| Readiness (computed reasons) | NO — engine | Rendered here as the readiness proof rail — the same computed reasons Composer's rail consumes | per `pkgId` | Never decorative; each reason links to its blocker |
| Period status transitions | NO — orchestration spine | Status chip in sheet header + breadcrumb | `(loanId, period)` | No surface mutates status directly (02 §3) |
| SendRecord / send act | NO — Send & Record | Post-certify send panel mounts here but is the send surface's object | `sendId` | See `send-record.md` |
| Evidence chains | NO — cross-cutting contract | Summoned as overlay per figure | `ProvenanceRef` | `cross-cutting/provenance-lit-row-trace.md` |

No-double-homing boundary: this surface owns the certification act, its record, its voids, and the certificate artifact — nothing else. Composition, readiness authoring, evidence, sending, and period mechanics are consumed by reference and link back to their owners (02 §3). The send panel is a mount, not a merger: its objects, states, and failures belong to `send-record.md`.

## 4. Data and semantic model

- Source facts: none originate here. Every figure on the sheet arrives as engine output whose chain terminates in source documents (06 §1).
- Extracted values awaiting confirmation: never render on the sheet — an unconfirmed value upstream is a readiness reason ("2 confirmations outstanding"), not a sheet figure.
- Confirmed values: render inside sections with provenance underlines and confirmer stamps on hover (three-actor grammar, 06 §7).
- Deterministic outputs: the key-figure band (headline metrics per the loan's own definitions); TestResult verdict lines with signed headroom; tie-out status; the content hash itself (engine-computed over the exact package bytes of the rev); readiness reasons; the diff vs prior period.
- Agent proposals/drafts: only the pre-flight summary ("what you are certifying; what changed since last period") — assembled from engine diffs, quoting engine values by reference, generating no figures (04 §2.1).
- Human decisions: the certify act (typed attestation); the typed reason on any post-certify change (which becomes the void rationale); the implicit acknowledgment in re-certifying.
- Certified values: post-act, the whole sheet is certified-class — hash chip in the header, statement sentences in the ruled accent, record in history (06 §7, row "certified").
- Versions/periods/packages: sheet always names `(loanId, period, rev)`; certifying binds `certId` to the hash of that exact rev; a new rev after void is a fresh act (02 §5).
- Evidence/provenance: every material figure wraps in `<Figure>` with a `ProvenanceRef`; the certificate is renderer-adoption item #2 in the app-wide contract (cross-cutting §3).
- Permissions/read-only projections: reviewer sees the sheet without the Certify control (a read-only affordance, not a disabled button); a PMC preparer without certify rights sees "certification reserved to {role}" naming who can act (02 §4); sealed periods render read-only forever.

Field groups and grain:

- Organization: certifier identity, role/rights matrix (Settings Team & Roles — gap 4 unfixes the server-admin-only role), org policy on separation of duties.
- Client (PMC mode): the owner client the package belongs to; certify rights may be reserved to the owner (02 §4).
- Loan: outward name, lender/servicer, program+form (e.g. 6001.NR + riders — evidence), UPB, reporting-only flag.
- Reporting period: label, cadence occurrence, status, deadline distance (due-rule provenance from the requirement record).
- Package: rev, section manifest, attachment manifest, readiness, content hash.
- Requirement/test: verdict lines rendered per basis (covenanted vs monitored — structural, C-9).
- Certification record: who, capacity, when, session identity, full hash, void state + void events (what changed, when, by whom/what, typed reason).
- Send record: rendered post-certify by the mounted panel; owned by `send-record.md`.

## 5. State machine and exceptions

Each state carries: trigger · prerequisites · actor · render on this surface · reversibility · audit event · failure behavior.

- **empty**
  - Trigger: period exists, no package rev composed. Actor: n/a.
  - Render: sheet ghost frame + "no package composed yet" + link to Composer; no Certify control at all (not even disabled).
  - Reversibility/audit: n/a; no event. Failure behavior: never renders a blank sheet as if composed.
- **awaiting-documents (holding)**
  - Trigger: checklist incomplete upstream. Actor: agent chases on approved cadence (Q).
  - Render: readiness reason "checklist: {n} items outstanding", linked to Holding.
  - Reversibility: items arrive or are waived upstream. Audit: chases quiet-logged.
  - Failure behavior: deadline pressure escalates per org policy — never auto-advances, never auto-certifies (R4: the Avalara rejection).
- **loading/processing**
  - Trigger: route resolving; engine recompute-on-view. Actor: engine.
  - Render: paper ground paints immediately; figures skeleton; readiness rows shimmer.
  - Failure behavior: a resolver failure renders the route's honest error state, never a stale sheet.
- **partial/incomplete**
  - Trigger: some sections composed, others not. Prerequisite: a rev exists. Actor: Composer upstream.
  - Render: composed sections render; missing sections as named gaps; readiness false.
  - Audit: composition events upstream. Failure behavior: gaps are named, never silently omitted.
- **extracted-unconfirmed**
  - Trigger: upstream confirmations outstanding. Actor: human upstream (typed confirms).
  - Render: readiness reason with count, linked to the owning exception surface.
  - Failure behavior: an unconfirmed value never appears as a sheet figure.
- **stale/superseded**
  - Trigger: a source document replaced after downstream values were confirmed. Actor: engine flags; human re-confirms upstream.
  - Render: affected figures carry stale badges with diff links; readiness reason "stale confirmations: {n}".
  - Audit: stale-flag event. Failure behavior: certify is impossible while stale (readiness false).
- **low confidence / conflict**
  - Trigger: unresolved extraction exceptions. Actor: human upstream.
  - Render: readiness reasons only. Failure behavior: never surfaces as a sheet figure.
- **failed tie-out**
  - Trigger: prior-package reconciliation broke. Actor: engine detects; human dispositions in Review.
  - Render: readiness reason "tie-out failed: {field}" → Review Room compare.
  - Reversibility: by typed disposition. Audit: disposition event. Failure behavior: blocks readiness, fail-closed.
- **watch/shortfall/breach**
  - Trigger: test verdicts on the composed package. Prerequisite: tests computed per the loan's own definitions.
  - Actor: human dispositions shortfall/breach findings in Review; watch is informational.
  - Render: verdict lines on the sheet in ink type (canon: occupancy 268/301 = 89.04%, shortfall vs monitored 90% floor — never "breach" on a monitored basis); undispositioned shortfall/breach findings are readiness reasons.
  - Audit: finding events. Failure behavior: a breach verdict on a covenanted test is never auto-dispositioned (04 §1).
- **blocked/gated (readiness false)**
  - Trigger: any reason open. Actor: engine.
  - Render: Certify control disabled-with-linked-reasons — never a bare disabled button (R4: GitHub required checks).
  - Audit: readiness state logged. Failure behavior: no bypass exists for any role, including admins.
- **ready**
  - Trigger: all reasons discharged; readiness true. Actor: engine flips.
  - Render: Certify control live (#33419D); pre-flight summary available.
  - Reversibility: any new exception re-blocks. Audit: readiness flip logged.
  - Failure behavior: recompute-on-view guarantees no stale "ready" ever renders.
- **permission denied**
  - Trigger: viewer lacks certify rights.
  - Render: sheet fully visible; control replaced by "certification reserved to {role/name}".
  - Reversibility: role change via Settings (Team & Roles). Failure behavior: never a disabled button pretending the viewer could act.
- **read-only**
  - Trigger: reviewer role, or sealed period.
  - Render: sheet + history; no controls; sealed banner when sealed. All traces still work.
- **certified (void-on-change)**
  - Trigger: the typed act succeeded. Prerequisites: readiness true + identity match + hash pinned. Actor: **human only, ever**.
  - Render: hash chip in header; statement sentences #33419D; record card in history; send panel activates.
  - Reversibility: only by void-on-change. Audit: CertificationRecord written (loud in history, quiet in notifications — 04 §1).
  - Failure behavior: double-fire guarded; a hash race aborts with a fresh-content notice (§16).
- **voided**
  - Trigger: any change to any input feeding any package field. Prerequisite: a certified state existed.
  - Actor: engine detects; the change-making act carries its typed reason (the GitHub dismissal-comment mechanic — R4).
  - Render: VOID banner above the sheet (what changed, when, by whom/what, the reason); statement block gains "VOIDED {when}"; hash chip struck; period returns to in-review; the void stays in history forever.
  - Reversibility: re-certification is a fresh act on a new rev. Audit: void event + reason.
  - Failure behavior: never silent; the banner cannot be dismissed while the void is unresolved.
- **sent/sealed**
  - Trigger: send gate passed (owned by `send-record.md`). Prerequisites: certified + un-voided.
  - Render: sealed banner; the sheet is the permanent record; every trace works forever (seal-not-wipe).
  - Reversibility: **irreversible by design.** Audit: seal event. Failure behavior: nothing inside a sealed period is deletable (02 §5).
- **recovery/undo**
  - Certify has **no undo control**: the only reversal is void-by-change (visible, reasoned) or superseding re-certification.
  - Cancel is free any time before the modal's button fires; nothing writes until it fires.
  - Send is never reversible — the asymmetry is the point (R4: Stripe refunds; `send-record.md`).

## 6. Engine / Agent / Human / Gate / Quiet Log

| Phase | ENGINE (deterministic) | AGENT (prepares/proposes) | HUMAN (typed decisions) | GATE (interrupts) | QUIET LOG |
|---|---|---|---|---|---|
| Readiness | Computes reasons (sections present, tests verdicted, tie-out clean, confirmations current, findings dispositioned); recompute-on-view | Explains any reason on demand with lit-row evidence | Fixes route to owning surfaces | Certify disabled-with-linked-reasons while false | Readiness flips logged |
| Pre-flight | Content hash of the exact rev; diff vs prior period | Assembles the "what you are certifying / what changed" summary — engine values by reference only | Reads; may trace any figure | — | Summary generation |
| The act | Identity binding (session), hash pinning, record write | — (the agent NEVER certifies — locked trust hierarchy) | **The certify act — human only, ever**: typed attestation | The ceremony is the gate | CertificationRecord written |
| Void-on-change | Detects any mutation touching certified content; raises the void; returns period to in-review | Drafts the "what changed" explanation | The change-making act supplies the typed reason that becomes the void rationale | Void banner blocks send | Void event + reason |
| Re-certify | New rev, new hash; prior record + void kept | Fresh pre-flight including the void story | Fresh typed act (never a shortcut resume) | Same gate, no memory of the prior pass | New record appended |

Lane invariants honored (04 §2): no shipping number outside ENGINE; the gate names its typed decision options; the log is append-only and identity-stamped; agent presence is contextual (pre-flight beside the act, headroom beside the verdict) — no chat chrome.

## 7. Information hierarchy

Scan order:

1. Page/frame header: shell breadcrumb at deal altitude — `Covenant / {Loan} / {Period} / Certificate`; the period crumb is a switcher (08 §2).
2. Decision/status summary: period status chip + deadline distance; pre-certify, the readiness proof rail head ("{n} of {m} requirements met"); post-certify, the record chip (who/when).
3. Primary work region: **the sheet** — header identity → key-figure band → sections → certification statement block → the Certify control.
4. Secondary context: right rail — readiness proof, what-changed-since-prior-period, certification history (records + voids), then the send panel post-certify.
5. Evidence/proof: summoned overlay from the right on any figure click; never covers the clicked figure.
6. Actions: exactly one primary act (Certify); secondary: open Composer/Review (pre-certify), trace any figure, download the rendered PDF (F4 — renders honest-absent until built, never a dead button).
7. Activity/history: certification history list in the rail; the full quiet log lives on the period, linked.

Absent by design: charts of any kind; activity feeds; commentary editing; any second CTA competing with the act; severity color on the paper (verdicts are ink words on the sheet — the dashboard treatment lives on Review/Loan Detail); any generic chat box.

## 8. Page anatomy and regions

**Region A — the sheet (center, the primary work window).**

- Purpose: the paper artifact being attested; the single object of the ceremony.
- Content: SheetHeader (org + loan outward name, lender/servicer, program+form, period label, package rev, status chip; post-certify the hash chip) → KeyFigureBand → SectionBlocks in Composer order (statements, rent schedule, questionnaire transcription, narratives, attachment manifest — each a summary render with figures, not an editor) → CertificationStatementBlock (the lender form's certification language as transcribed by Composer + Covenant's act line) → CertifyControlZone → sheet footer (rev, generated timestamp).
- Persistence: persistent.
- Interaction: read + trace only; zero editing on this surface (edits happen at owners; any edit that lands post-certify voids).
- Minimum width/height: 720px wide (pane law 08 §4); max-width ~880px, centered.
- Resize/collapse: sheet width is fixed-max; extra viewport becomes margins and rail width; the sheet never reflows below 720px — the frame's larger-window state takes over instead.
- Why this pane: the ruled one-big-pane work window; a document being signed must render as one continuous artifact, not a panel cluster.

**Region B — right rail (pinned, 320–400px).**

- Purpose: proof and history beside the act — co-visibility for the sign decision without leaving the sheet.
- Content, top→bottom: (1) readiness proof — each row `reason · state dot · link-to-blocker` (reuse CountBadge for counts); (2) what-changed-since-prior-period — engine diff rows, each tracing to its sheet figure; (3) certification history — records and voids interleaved, newest first; (4) post-certify: the send panel (`send-record.md` owns its spec).
- Persistence: pinned.
- Interaction: rows link out (blockers) or trace in (diff rows); history cards expand in place.
- Minimum width: 300px.
- Resize/collapse: below minimum, the rail collapses to three labeled tabs above the sheet ("Readiness · Changes · History") — no silent compression.
- Why a rail: these answer "may I sign?" and must sit beside the sheet, but must never interleave with attested content.

**Region C — Evidence overlay (summonable).**

- Purpose: lit-row proof for any sheet figure (the lit-row law applied to the certificate).
- Content: Evidence panel with chain breadcrumb (figure → formula → inputs → normalized → region) and the lit source row (cross-cutting §2.3).
- Persistence: summonable; at ≥2048px it may pin open (surface-interior evidence pane, 08 §4 — not a second work window).
- Interaction: chain links re-light at their level; "view original" artifact toggle; Esc dismisses, focus returns to the figure.
- Minimum width: 420px; default 420–480px.
- Behavior: slides from the right edge **over Region B, never over the sheet column**; the clicked figure keeps ≥24px clearance — if the overlay footprint would cover it, the sheet column shifts left within its margin budget.
- Why an overlay: proof opens beside the claim, never a context-destroying modal (lit-row law; DIRECTIONS).

**Region D — the certify modal (transient; the one legal-weight modal).**

- Purpose: the typed act. Modals are permitted only for typed acts with legal weight and destructive confirms (08 §4) — this is the canonical case.
- Content, two beats (R4: DocuSign's two-beat rhythm adapted): **Beat 1 — review**: loan, period, package rev, content hash (short + full on expand), section manifest count, verdict summary, certifier identity + capacity (role), preparer identity where preparer ≠ certifier (both recorded distinctly — R4: Mercury separation-of-duties). **Beat 2 — attestation**: type your full name exactly as your identity records it; mismatch keeps the act disarmed with the mismatch stated in words; match arms the Certify button (#33419D).
- Persistence: transient; Esc cancels with nothing written.
- Minimum width: 560px; centered; at ≥1728px offset left so the rail's readiness proof stays visible.
- Why a modal: the act must interrupt; everything it needs is restated inside it so no co-visibility is required.

## 9. Co-visibility matrix

| Datum/surface A | Datum/surface B | Must be simultaneous? | User decision enabled | Default topology | Fallback |
|---|---|---:|---|---|---|
| Sheet figure | Its evidence chain (lit row) | YES on demand | "Do I trust this figure enough to sign?" | Overlay from right, figure kept visible | Narrow: full-height overlay sheet, figure re-quoted in its header |
| Sheet | Readiness reasons | YES pre-certify | "What blocks my signature?" | Rail beside sheet | Tabs above sheet |
| Sheet | What-changed-since-prior | On demand | "Is the delta expected?" | Rail card; rows trace into the sheet | Tab |
| Ceremony modal | The sheet beneath | NO (modal restates everything it needs) | The act itself | Modal over dimmed sheet | Full-screen takeover <1280px |
| Certification history | Sheet | On demand | "What happened before; why was rev 2 voided?" | Rail list | Tab |
| VOID banner | The changed values | YES when voided | "What changed; do I re-certify?" | Banner above sheet; each change row traces | Banner persists at all widths |
| Send panel | Certified hash chip | YES post-certify | Handoff to gate two | Rail below history | See `send-record.md` |

No pane exists merely because information exists: the rail carries only sign-decision inputs; everything else is a link out.

## 10. Layouts and viewport behavior

- **1440px**
  - Topology: shell rail 240 → center column ~860 (sheet 800, margins 30/30) → right rail 320. Ratios ≈ 17% / 61% / 22%.
  - Evidence overlay 420 covers the rail when summoned; the sheet holds position.
  - Ceremony modal 560, centered.
- **1728px**
  - Topology: shell rail 260 → sheet 880 centered in a ~1060 column → right rail 380.
  - Evidence overlay 480 docks over the rail; the sheet never moves.
  - Modal offsets left of center by half the rail width so the readiness proof stays visible.
- **2048px**
  - Topology: sheet 880; margins grow; right rail 400.
  - The Evidence overlay may pin open beside the rail (260 + 880 + 400 + 480 + margins) — pinned evidence is the one earned use of the extra width; nothing else widens.
- **Narrow/compact (<1280px)**
  - Shell rail auto-collapses to 48px icons (08 §9).
  - Right rail becomes three labeled tabs above the sheet ("Readiness · Changes · History").
  - Evidence is a full-height overlay sheet, the clicked figure re-quoted in its header.
  - The ceremony modal becomes a full-viewport takeover with identical content and beats — 08 §9 promised this brief the defined narrow ceremony; this is it.
- Minimum viable dimensions: 1152×720. Below: the frame's "larger window required for review work" state — registers elsewhere stay usable; the ceremony refuses rather than compresses.
- Focus behavior: figure focus ring per frame tokens; `Enter` traces; the overlay receives focus on the lit row; Esc walks overlay → modal → nothing (never navigates as a side effect).
- Compare behavior: none here — package-vs-prior compare lives in Review Room; the what-changed card links there. This surface never splits into two work windows.
- Proof/source behavior: per Region C; the deepest chain hop opens DocView in a new context with return path.
- Tab stacking/replacement: rail→tabs at <1280px or rail <300px; tabs are labeled, stateful, and restore scroll.
- No silent compression anywhere: every pane below minimum becomes a labeled tab or refuses.

## 11. Components and exact anatomy

- **CovenantShell + breadcrumb altitude gate** — REUSE (`CovenantShell.tsx`). Deal-altitude crumbs; the period crumb is a period switcher popover with status dots (08 §2).
- **CertifiedSheet** — NEW (`src/components/covenant/certificate/CertifiedSheet.tsx`). Parts:
  - SheetHeader: identity block (org, loan outward name, lender/servicer, program+form), period label, rev, status chip, HashChip slot (empty pre-certify).
  - KeyFigureBand: the period's headline engine outputs per the loan's own definitions (canon Bexley: occupancy 268/301 = 89.04% shortfall vs monitored 90% floor; evidence Calloway FYE-2018: NOI $1,218,877 · Net Income $460,159); every figure a `<Figure>`.
  - SectionBlock[]: section head row (title on #DDE4FB), summary body (figures, named fields, attachment names), per-section source note.
  - CertificationStatementBlock: the transcribed certification sentences (#33419D post-certify), the act line ("Certified by {name}, {capacity}, {timestamp} over content {short-hash}"), the record link.
  - CertifyControlZone: the Certify button + adjacent reasons list when blocked (disabled-with-linked-reasons; never bare).
  - Sheet footer: rev, generated timestamp.
- **Figure (provenance wrapper)** — REUSE (cross-cutting §2.2). The certificate is adoption item #2; every sheet numeral wraps; ref-less figures render the "no source" affordance and count toward the fixture burndown (cross-cutting §5).
- **HashChip** — NEW. Short hash in Geist Mono + copy affordance + expand-to-full; struck state when voided; reused by `send-record.md` for the equality display.
- **ReadinessProofList** — NEW. Rows: reason text · state dot · linked blocker; consumes the same engine readiness Composer's rail consumes (one computation, two renders); CountBadge REUSE for counts.
- **WhatChangedCard** — NEW. Engine diff rows vs prior period (field, prior → current, cause link); row click traces into the sheet; "open compare in Review" link.
- **CertificationHistoryList** — NEW. Record cards (who/capacity/when/short-hash) and void cards (when, what changed, by whom/what, the typed reason) interleaved newest-first; append-only render; empty state "no certifications yet".
- **CertifyModal** — NEW. Beat-1 summary block · identity + capacity line · preparer line where distinct · Beat-2 typed-name field with live match state · armed Certify button (#33419D / #5265C5 / #121459) · cancel. No keyboard shortcut arms or fires the act.
- **VoidBanner** — NEW. Above-sheet banner: "Certification voided {when}" · change list (each row traces) · actor/agent of the change · the typed reason · CTA "Return to review" (the void renders as Review stop #1). Not dismissible while unresolved.
- **CertificationRecordArtifact** — NEW. The generated human-readable certificate (R4: DocuSign Certificate of Completion × GitHub release attestation): who, capacity, when, session identity, full content hash, section manifest, verdict summary; renders in-app; exports with the package (PDF export = F4).
- **Evidence overlay** — REUSE (Evidence panel + `traceToEvidence`). Placement per Region C; lit-row band uses `--accT` per the cross-cutting contract.
- **SendPanel** — REUSE by mount; owned and specified by `send-record.md`; mounts in the rail post-certify.
- **Empty/error/permission objects** — NEW shared family: "no package composed" ghost, "certification reserved to {role}", sealed banner, the frame's larger-window refusal.

## 12. Interaction specification

- Selection: sheet figures are the only selectable objects; single selection; selection = provenance target.
- Hover: figures show the three-actor grammar (provenance underline; confirmer stamp on confirmed values); #33419D interactive elements (Certify control, record link) hover #5265C5; readiness rows underline their blocker link.
- Focus: full keyboard traversal of figures in reading order; readiness rows, history cards, and the control zone are focusable; focus ring per frame tokens.
- Keyboard map:

| Key | Context | Behavior |
|---|---|---|
| `G F` | anywhere in loan context | Open this loan+period's certificate (08 §5) |
| `⌘K` | anywhere | Palette (places/objects/computed answers) |
| `Tab` / `Shift+Tab` | surface | Traversal: header → band → sections → statement → control → rail |
| `Enter` | focused figure | Trace — Evidence overlay opens, source row lights, focus moves to the lit row |
| `Enter` | focused readiness row | Open the linked blocker with return path |
| `Esc` | overlay open | Dismiss overlay, focus returns to the figure |
| `Esc` | modal open | Cancel the ceremony; nothing written |
| (none) | modal | No shortcut arms or fires Certify — typing the name is the only arming mechanism (R4: NN/g deliberateness) |
- Editing and validation: the only editable field on the surface is the typed-name field in the modal; validation is an exact match against session identity, evaluated live, with the mismatch stated in words (never just a red border).
- Bulk action: none, by design — certification is per package, always singular; there is no "certify all ready periods" anywhere in the product.
- Undo/recovery: none for the act (§5 recovery row); cancel is free before the button fires; nothing writes until it fires.
- Sorting/filtering: none (the sheet is a document; the rail lists are short and ordered by the engine).
- Drill-down and return path: readiness reason → owning surface with return-to-certificate breadcrumb; void banner → Review with the void as stop #1; every exit preserves `(loanId, period)`.
- Source-linked selection (lit-row): click figure → overlay opens beside, exact source line lights and stays lit until dismissed or another trace starts; subsequent clicks move the light (cross-cutting §4).
- Save/persistence: the act writes CertificationRecord atomically with the pinned hash; no drafts, no partial saves, no autosave (there is nothing to save).
- Collaboration/commenting: none here — annotation lives in Review Room; the certificate is the artifact, not the discussion.

## 13. Visual craft direction

**The ruled paper paint map — where each of the six certified-sheet hexes paints, exactly, restyling nothing, proposing zero new colors:**

| Hex | Role | Paints exactly | Never paints |
|---|---|---|---|
| `#E7EBF8` | Panel ground | The entire sheet surface, edge to edge of the ~880px artifact — header, band, sections, statement block, control zone, footer margins | The frame, rail, overlay, modal chrome |
| `#DDE4FB` | Stronger tint | (a) the key-figure band's full-width ground; (b) every section head row's ground | Body rows, chips, rail, anything off-sheet |
| `#B3BCDA` | Hairline | All sheet rules: the sheet's outer border, section rules, in-section table row rules, the rule above the certification statement block, the HashChip border, the VOID overprint stroke on the statement block | Frame dividers (those use the gray ladder) |
| `#33419D` | Accent ink + the act | The certification statement sentences (every attestation line, post-certify); the Certify button fill wherever the control renders (sheet zone and modal — it is one control); the record link in the statement block | Any non-certification text; any element outside sheet + ceremony |
| `#5265C5` | Hover | Hover state of the Certify button and of #33419D interactive text | Rest states |
| `#121459` | Pressed | Pressed/active state of the Certify button (held during the write round-trip) | Anything else |

- Light document surface **always**: the sheet stays paper-light inside a dark frame — it is paper, not UI (ruled; snapshot §4). The frame around it follows the viewer theme on the ten-rung gray ladder.
- Typography roles and sizes: sheet title/identity in the app text face (~18/24 semibold; secondary identity 13/20 mid-rung); section heads 13/20 semibold on #DDE4FB; body 14/22; the certification statement sentences 15/26 — they are read, not scanned; figures in Geist Mono.
- Financial-number treatment: every figure `tabular-nums slashed-zero` in Geist Mono (ruled); right-aligned in section tables; signed headroom shows its sign explicitly.
- Sheet ink: body text and figures in the ladder's darkest rung; secondary lines mid-rung. Certify button label: the ladder's lightest rung (paper-white) on #33419D — a ruled-token role, not a new value. Disabled control: #E7EBF8 ground, #B3BCDA border, mid-rung label, reasons adjacent.
- Verdict words on the sheet render as ink type (weight + small caps), never severity chips — the sheet is a document; severity color lives on Review/Loan Detail. No charts ship on this surface at all (chart doctrine: nothing here could out-encode the sheet's own text).
- Spacing rhythm: 8px base grid; section blocks separated by hairline + 24px; the band and the statement block get 32px breathing room; density is document-density, not register-density.
- Open ground versus earned boundaries: sections are open (hairline-ruled, never boxed); the only boxed elements are the HashChip and the Certify button; tables inside sections keep uniform row heights.
- Dividers/elevation: the sheet floats on the frame with one soft elevation step; hairlines carry all interior structure; the overlay carries the frame's standard elevation, held apart by hairline + shadow.
- Semantic color: frame semantics (focus ring, lit-row band `--accT`, your-move counts) stay in the periwinkle accent family and gray ladder; the paper hexes and the accent family never trade roles.
- Focus/selected/hover states: frame focus ring even on paper (focus is a frame behavior); figure hover = provenance underline strengthens; lit state per the cross-cutting contract.
- Motion: overlay slides 200ms ease-out; the certified-state transition (hash chip appears, statement ink turns #33419D) is one 300ms settle — a single quiet moment, no celebration. `prefers-reduced-motion`: all transitions become instant state swaps.
- Long-session ergonomics: not the design driver here — the surface is built for short, deliberate visits; nothing blinks, polls, or nags on the sheet.

## 14. Benchmark research and synthesis

| Product | Limited role | Official evidence | Exact pattern to take | Covenant adaptation | What to reject | Why this reference beats alternatives |
|---|---|---|---|---|---|---|
| GitHub protected branches / PR review | Readiness gating + void-on-change | docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches · docs.github.com/en/pull-requests/how-tos/review-pull-requests/dismissing-a-pull-request-review (R4) | Required checks: the act disabled until every named requirement is green, each linking to detail; stale-review dismissal: approval bound to a content state, dismissed visibly with a mandatory typed reason when that state changes | Readiness reasons = the required checks; the content hash = the diff state; void = stale dismissal kept in the timeline with the typed reason | Bypass lists and admin exemptions — the gate binds everyone, including admins | The only mainstream system where approval binds to content, not to the object — exactly Covenant's void semantics |
| DocuSign | The ceremony + the audit certificate | support.docusign.com/guides/ndse-user-guide-history-coc · support.docusign.com/en/guides/signer-guide-signing-adopt-new (R4) | Identity bound before the act; the two-beat rhythm (adopt/attest → Finish); the auto-generated Certificate of Completion — a human-readable audit artifact that travels with the document | Typed attestation (type your name) replaces signature adoption; CertificationRecordArtifact is Covenant's Certificate of Completion, exportable with the package | Envelope routing and field-placement machinery — Covenant certifies an assembled package, not a form-filling workflow | The reference ceremony courts already accept; its two-beat rhythm is the proven deliberateness mechanic |
| GitHub Releases | Draft → publish → immutable + attestation | docs.github.com/en/repositories/releasing-projects-on-github/about-releases · docs.github.com/en/code-security/concepts/supply-chain-security/immutable-releases (R4) | Assemble fully in mutable draft; one publishing act freezes content; attestation = a cryptographically verifiable record; immutability shown on the artifact (lock badge) | Rev is the draft; certify freezes; the hash chip is the on-artifact badge; even narrative edits void (stricter than Releases' notes-stay-editable) | Tag-name permanence for voided drafts — revs recycle routinely; permanence belongs to certified+sent records | The cleanest public draft→frozen state machine with a verifiable record |
| Modern Treasury | Typed approvals + append-only records | docs.moderntreasury.com/payments/docs/approval-rules-overview · moderntreasury.com/learn/data-immutability (R4) | Approved content locked (mutable only in `needs_approval`); reviewer capacity recorded ("review as Role"); corrections are new records, never edits | Capacity on the record; history append-only; the record modeled so stacked approval chains (asset manager → controller) can be added later as rules | Auto-send after approval — MT's second gate is a bank; Covenant's second gate is the human send | The strictest record-immutability doctrine in fintech, stated as policy, not habit |
| Carta | The structural, non-disableable sign gate | support.carta.com/kb/guide/en/how-to-sign-company-securities-DUFbF63lNh (R4) | "Disabling the requirement for security signatures is not supported" — the gate is structure, not configuration; issued artifacts change only by cancel/reissue | Certify cannot be configured away for any org, plan, or role; post-certify change = void + fresh act (cancel/reissue semantics) | Multi-officer signature routing for v1 — one identity-bound certifier suffices | Proof a signing gate can be absolute in a commercial product and users accept it |
| Avalara Managed Returns | The named rejection | help.avalara.com/Returns/Monthly_filing_timeline_for_Managed_Returns (R4) | (Contrast only) the deadline-window review cadence against a specific artifact (the liability worksheet) | Deadline pressure as escalating urgency on humans (banners, reminders) | **Auto-approve at the deadline — rejected absolutely.** Covenant never certifies on a human's behalf; a missed deadline surfaces loudly instead | The single most instructive contrast in the research: what this gate must never become |
| Nielsen Norman Group | The ceremony budget | nngroup.com/articles/confirmation-dialog/ (R4) | Typed confirmation reserved for the most dangerous and rare actions; overused ceremony decays into reflex and increases errors | **Exactly two heavy ceremonies in the whole product** — certify and send — mutually distinct (typed name here; recipient-and-hash review + typed word there) so neither trains muscle memory for the other; zero confirm dialogs anywhere else | "Are you sure?" OK/Cancel dialogs anywhere in the product | The design law that keeps both ceremonies meaningful |

Synthesis: the composed ceremony is original because no benchmark contains it whole — GitHub's content-bound approval and required checks, DocuSign's identity-bound two-beat act and audit certificate, Releases' draft→frozen hash and on-artifact badge, Modern Treasury's append-only capacity-stamped record, and Carta's non-negotiable gate combine into one borrower-side moment: a paper sheet whose every figure proves itself (lit-row), gated by computed readiness, signed by a typed identity-bound act that pins a content hash, voided visibly by any change with a mandatory reason, and recorded as a human-readable certificate — with NN/g's budget guaranteeing the act never decays into reflex (R4 synthesis 1–5, 13). It is domain-correct for borrower-side lender reporting because the sheet renders the loan's own certification language and the loan's own definitions — never a template's, never a benchmark's.

## 15. Domain references

Terminology and expected-content references only: the servicer certification blocks that exist in the real record (JLL quarterly Property Questionnaire's signed certification block; annual certification items per Fannie Form 6001.NR §8.02(b)(2)(E)–(G) — evidence); Avalara-class filing approval for period-cadence vocabulary; Finley-class covenant tooling for tracking vocabulary. **Domain authority does not equal visual authority** — none of these products contributes a pixel to this surface; and covenant semantics come from the loan documents and Terry, never from any referenced product (domain-content firewall).

## 16. Accessibility, performance, and safety

- WCAG contrast: #33419D on #E7EBF8 and on #DDE4FB passes AA for text — verify by canvas readback, never eyeball (the oklch caution, snapshot §4); darkest-rung ink on #E7EBF8 targets AAA for body text; hairline #B3BCDA is decorative and never the only boundary carrier.
- Focus: visible ring on every focusable; focus is never trapped except inside the ceremony modal (standard trap with Esc exit).
- Keyboard completeness: trace, readiness navigation, history, and the full ceremony operate pointer-free (§12).
- Screen-reader semantics: the sheet is a `document` region with heading structure per section; figures expose value + provenance state; the HashChip announces short and full values; the ceremony modal is an `alertdialog` naming loan, period, rev, and hash in its accessible description; the VOID banner is a live region announced once.
- Table virtualization/large data: sheet sections are bounded summaries; no virtualization by design — the full statement grid lives on Actuals.
- Loading and latency feedback: paper ground paints immediately; figures skeleton; the act's write round-trip holds the pressed state (#121459) with a working note and never double-fires (idempotent client guard + server-side single-record constraint).
- Destructive action confirmation: certify is legal-weight, not destructive — the typed act is its own confirmation; no second dialog ever stacks on it (NN/g budget).
- Certify safety: typed identity-bound act; hash pinned at act time against the rendered rev — a rev change between render and act aborts with a fresh-content notice (never certifies unseen bytes); void-on-change engine-enforced; no API path certifies without an authenticated human session (R4 synthesis 12); no role bypass exists.
- Source immutability: the sheet renders from engine reads; originals immutable (document-artifact law); the certification record append-only.
- Auditability: CertificationRecord + void events + ActivityEvents reconstruct the full story of every period forever (seal-not-wipe); the CertificationRecordArtifact makes the record lender-legible.

## 17. Acceptance tests and fixtures

Fixtures: `FIX-CAL-2018` — Calloway Park FYE-2018 evidence spine (annual package; NOI $1,218,877; tie-outs: cash-flow Net Income $460,159 = T-12 Net Income; interest paid $644,017 = T-12 debt-service total; period-end cash $138,940 = balance-sheet cash — evidence). `FIX-BEX-Q` — Bexley canon quarterly (UPB $15,232,500 · 4.17% fixed · 301 units; occupancy 268/301 = 89.04% shortfall vs monitored 90% floor — canon).

1. Readiness gating (`FIX-BEX-Q`):
   - Given one undispositioned shortfall finding, the Certify control is disabled and renders that reason as a linked row (never a bare disabled button).
   - Clicking the reason lands on the owning Review stop with a return path.
   - After disposition, returning shows the control live without reload (recompute-on-view).
2. Ceremony (`FIX-CAL-2018`):
   - Given readiness true, the modal restates loan, period, rev, short hash (expandable to full), certifier identity + capacity.
   - Typing a mismatched name keeps Certify disarmed with the mismatch stated in words.
   - Typing the exact name arms the button; firing writes CertificationRecord {who, capacity, when, hash}.
   - The sheet re-renders certified: hash chip in header; statement sentences #33419D.
3. Separation-of-duties recording: with preparer ≠ certifier, the modal shows both and the record stores both distinctly (R4: Mercury); with preparer = certifier, the record says so explicitly.
4. Void-on-change (`FIX-CAL-2018`):
   - After certifying, replace the T-12 source upstream with a corrected version carrying a typed reason.
   - Assert the VOID banner renders what changed, when, by whom, and that reason; the period returns to in-review.
   - Assert the void card persists in history forever and the send panel retracts.
5. No silent re-certify: after a void, assert no path re-certifies without the full ceremony on the new rev (fresh hash, fresh typed act); the prior record and void remain in history.
6. Hash race: mutate the package rev between sheet render and modal fire; assert the act aborts with a fresh-content notice and writes nothing.
7. Lit-row provenance: click the sheet's NOI figure (`FIX-CAL-2018`); the Evidence overlay opens from the right, the T-12 source row lights and stays lit, and the clicked figure remains fully visible (≥24px clearance); the shuffled-fixture invariant (cross-cutting §2.4) passes on the certificate renderer.
8. Vocabulary law: the `FIX-BEX-Q` sheet renders "shortfall" for the monitored occupancy miss; assert the string "breach" appears nowhere on any monitored-basis line.
9. Permission fixtures: PMC-preparer-without-rights sees "certification reserved to {role}" (no disabled button); reviewer sees read-only; a sealed period renders the sealed banner with every trace still working.
10. Viewport fixtures: 1440 / 1728 / 2048 render the §10 topologies (assert pane widths); at 1279px the rail becomes labeled tabs and the ceremony becomes a full-viewport takeover with identical beats; at 1151×719 the ceremony refuses with the frame's larger-window state.
11. Paint audit (closing the phase-3 negative): canvas-readback asserts every §13 paint-map cell — sheet ground #E7EBF8, band + section heads #DDE4FB, hairlines #B3BCDA, statement sentences + control #33419D, hover #5265C5, pressed #121459 — and asserts zero other consumers of these hexes on the surface and zero non-ruled colors on the sheet.
12. Dark-frame fixture: with the frame in dark theme, the sheet renders the light paper treatment unchanged (light document surface always).
13. Keyboard fixture: certify end-to-end pointer-free (`G F` → Tab traversal → modal → typed act → certified render); trace-and-return per cross-cutting acceptance test 4.
14. Data integrity: the stored hash equals an independent recomputation over the rev's bytes; the record is append-only (an update attempt fails at the store layer).
15. Accessibility: axe pass; modal `alertdialog` semantics; VOID live-region announced once; contrast assertions by readback.
16. Benchmark challenger review: a reviewer armed with the §14 table attempts to find a ceremony property present in a benchmark and absent here (content-bound approval, mandatory void reason, capacity on record, on-artifact immutability badge, no-bypass, two-beat deliberateness) — any hit fails the gate.

## 18. Build plan

- Dependencies: F2 persistence (packages, certification records; roles unfixed from server-admin — gap 4) · **F4 PDF/XLSX package rendering** (the exportable artifact — until built, download affordances render honest-absent, never dead buttons) · V3 certification record + void-on-change machinery · the orchestration spine's period ledger (exists as safety core) · the cross-cutting provenance resolver (U1-F1 fix first).
- Foundation work: wire the eight paper-accent tokens to sheet consumers (they exist in `covenant-tokens.css`, unpainted — snapshot §4); one readiness computation consumed jointly with Composer.
- Components to build first: CertifiedSheet (paint map + Figure wrapping) → ReadinessProofList → CertifyModal + CertificationRecord write → VoidBanner + void engine hook → CertificationHistoryList → CertificationRecordArtifact.
- Vertical slice (the send-vertical pattern): one route, one loan+period (`FIX-CAL-2018` shape on real persistence), engine data end to end — render the sheet from engine reads, certify against a real record write, void by a real upstream change. The route already exists with resolver + tests; this widens the proven vertical one gate leftward.
- Migration from fixture data: sheet figures move from `book.ts` strings to engine reads behind the Figure contract; CI counts ref-less figures toward the fixture burndown (cross-cutting §5).
- Rollout/feature flag: `covenant.certificate.ceremony` gates the modal + record write; the painted sheet ships first (visual-only, zero risk), the ceremony second, the void machinery third.
- Proof artifacts required: the canvas-readback paint audit (test 11); the certified → void → re-certify event trail on a seeded period; a screenshot set at all four viewports against `paper-accent-periwinkle-render.png`'s stated fidelity target (snapshot §4).
- Final gate: `ADJUST` confirmed — route and chassis stand; ship = paint + ceremony + record + void, with tests 1–16 green.
