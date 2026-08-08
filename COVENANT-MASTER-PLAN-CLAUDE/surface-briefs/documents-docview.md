# DOCUMENTS + DOCVIEW — the filed record and the paper sheet

## 1. Identity and verdict

- Product layer: portfolio floor (the Documents register) + cross-cutting evidence terminus (DocView — every lit-row chain in the app ends here: "any figure anywhere → click → its evidence → deeper → DocView (source artifact)"; 07-SURFACE-INVENTORY §3).
- Route/address in basis-v2: `/documents` and `/documents/[docId]` in the `(covenant)` route group (planned deep routes, 08-GLOBAL-FRAME-SHELL-PLAN §1); components in `src/components/covenant/` (existing Documents views + the DocView porcelain sheet); pipeline, hashing, and lineage logic in `src/lib/covenant/`; tokens consumed from `src/styles/covenant-tokens.css` — never redefined.
- Current build state: `FIXTURE` — the register renders demo arrays; DocView is the porcelain (paper) sheet with "Recreated · searchable" tags, measured **orphaned** (no importer) in the 2026-08-06 phase-3 measurement (snapshot §3).
- Existing-surface verdict: `ADJUST` — keep the register spine and the paper sheet; give DocView its importer by wiring everything through the one intake pipeline (gap 1 + gap 2). Bulk upload on this surface routes through the SAME pipeline; there is no second ingestion path, ever.
- Research tier: B/C.
- Primary users/roles: owner and PMC preparer (file, version, correct); reviewer read-only (inspect, export where permitted); the lender is never a user (02-OBJECT-RELATIONSHIP-MODEL §4).
- Frequency and session duration: weekly touches during a period's document-gathering weeks (find/verify/version, 1–5 min); heavier sessions at first-time loan setup (filing the loan agreement + riders) and during audit or lender follow-up (tracing a sealed figure back to paper).
- Error cost: a mis-versioned or silently replaced source document feeds a wrong number into a package — and the terminal error is that number certified and sent to a lender. The version and replacement acts on this surface sit directly upstream of the certify gate; this is why the filing question is typed and why replacement impact renders before the act.
- Success criterion: any filed document, any version, any artifact, and any downstream use is findable in under 10 seconds; nothing enters the canon except through the pipeline; a replaced source can never silently keep stale downstream values alive; a sealed package's paper is provable forever.

## 2. User job and decisions

- Primary job-to-be-done: maintain the canonical filed record of every document the book depends on — original bytes, honest derivatives, versions, and the map of what consumes them.
- Decisions made here:
  - the typed filing question on re-upload — **new version** vs **replacement/correction** — never inferred from filename collision (R2 research: Box, rejected mechanic);
  - Make Current promotion of a prior version; tombstone-delete and restore (where no sealed period forbids it);
  - suspect-word OCR corrections on the Recreated derivative (accept / correct / unreadable);
  - filing-metadata corrections (type, loan/period/client links, outward-safe naming).
- What the user should not have to decide here: arrival routing and checklist matching (Intake owns them); extracted-value confirmation (Extraction & Confirmation owns it); COA mapping (Actuals & Computation); verdict dispositions (Review Room / findings); period status (the orchestration spine owns transitions, 02 §3).
- Questions the surface must answer in scan order:
  1. Is the book's paper complete and current for the periods in flight?
  2. Which documents still need verification (proposed extracted values, unconfirmed)?
  3. Which documents carry open conflicts?
  4. For THIS document: which version is canonical, what changed between versions, and what downstream fields consume it — including sealed-package references?
- Entry paths: rail item Documents (`G D`); the lit-row chain terminus (any figure → Evidence → DocView at the exact source region); Loan Detail per-loan document links; Intake "filed → link" after routing (02 §3); Composer attachment links; CommandPalette object results ("Bexley loan agreement v2" with loan/period identity chips, 08 §5).
- Exit paths: Extraction & Confirmation (confirm proposed values); Intake (resolve an ambiguous upload); Review Room Evidence (the doc in its decision context); the sealed period record (Reports); Loan Detail; back-navigation always restores register scroll + filter state.
- Completion/advancement conditions: filing a checklist-expected document advances the Holding checklist (Intake-owned computation); a confirmed replacement re-opens downstream confirmations and can void a certification. Documents itself never advances period status — it feeds the surfaces that do.

## 3. Object and ownership model

| Object/action/status | Owned here? | Summary/link elsewhere | Address/identity | Notes |
|---|---:|---|---|---|
| Document (canonical filed record) | YES | Intake links after routing; Loan Detail lists links; Composer attaches refs; Review Room Evidence renders (never re-files) | `docId` (+ version) | 02 §2; record = document + typed properties (R2 research: Ironclad) |
| Version chain (`replaces`) | YES | Reports/sealed periods pin exact versions | `docId@vN` | Originals immutable; promotion + tombstones, never silent overwrite (02 §5) |
| Artifact set (Original / Recreated-searchable / Structured / Extracted-records refs) | YES | Evidence pane renders artifacts by ref | `docId@vN/{artifact}` | Four-artifact law (kit law 7; 06 §8) |
| Filing metadata (type, loan/period/client links, filed-by, arrival channel) | YES | — | properties on `docId` | Box metadata-instance adaptation (R2) |
| SavedView instances for this register | YES | — | `viewId` | One shared SavedView mechanism app-wide (02 §2) |
| Suspect-word corrections (OCR layer) | YES | — | `docId@vN/recreated/{region}` | Artifact-level acts; distinct from value confirmation |
| Arrival + routing decision, holding checklist | NO — Intake/Holding | Documents shows "arrived via intake on {date}" + link | `arrivalId` | The bulk-upload tray here is a projection of Intake's arrivals store (§8) |
| ExtractedValue states (proposed/confirmed/corrected) | NO — Extraction & Confirmation | Extracted Data mode renders read-only status + deep links | `valId` | Confirmation is a typed act on its owning surface |
| NormalizedFinancialLine / Metric / TestResult | NO — Actuals & Computation | Downstream-uses panel cites consumers by ref | per 02 §2 | No verdict chips render in Documents (§7, absent by design) |
| Package composition / CertificationRecord / SendRecord | NO — Composer / Certificate / Send | Downstream-uses panel lists sealed-package references with locks | `pkgId`, `certId`, `sendId` | Seal-not-wipe: sealed refs pin document versions forever |
| ActivityEvent (audit trail) | NO — cross-cutting store | Audit-Usage mode renders the doc-filtered view | `eventId` | One store; every feed is a filtered view of it (07 §2) |

No-double-homing boundary: **Intake owns arrivals and holding; Documents owns the filed canon** (kit law 7). Each may summarize + link to the other only. Bulk upload on `/documents` creates *arrivals* in Intake's store that flow through the one pipeline; Documents renders their recognition progress inline, but a routing decision, where one is needed, is Intake's act — the tray links to it rather than duplicating it.

## 4. Data and semantic model

- Source facts: original bytes + content hash per version, write-once immutable (source class; 06 §7). Evidence instance: the SLOT-4/5 executed JLL questionnaires are flattened scans with **zero text layer** (evidence) — the Recreated-searchable artifact's reason for existing (06 §8).
- Extracted values awaiting confirmation: OCR text + coordinates + per-region confidence on the Recreated artifact; Structured-reading fields/tables with source regions; ExtractedValue rows in `proposed` state — rendered in the inferred grammar (confidence + dotted provenance underline, never bold-final; 06 §7).
- Confirmed values: ExtractedValue rows `confirmed/corrected` with confirmer stamp on hover (owned elsewhere; summarized read-only in Extracted Data mode).
- Deterministic outputs (engine): content hashes; dedupe verdicts; version-chain integrity; the downstream-uses graph — computed from the lineage chain (06 §1): which normalized lines, metrics, package fields, and sealed packages consume this document's values; stale-flag propagation when a source is replaced.
- Agent proposals/drafts: classification (type + loan/period match + confidence, shown beside the arrival); the Recreated and Structured artifacts themselves (labeled derivatives, per-region confidence); a recommendation inside the filing question ("period matches filed v1; totals differ — suggest replacement/correction"); conflict explanations citing both sources.
- Human decisions: the typed filing answer; Make Current; tombstone/restore; suspect-word accept/correct/unreadable; metadata corrections. Every one writes an identity-stamped ActivityEvent.
- Certified values: never created here; touched only as *impact* — a replacement whose values feed a certified package triggers void-on-change, with the exact affected package fields listed before the human confirms (02 §5, 06 §6).
- Versions/periods/packages: grain is `docId@vN`; a document may link to loan(s) directly (loan agreement, riders), to property+period (T-12, rent roll), or to a specific package section (executed questionnaire); sealed packages pin `docId@vN` + artifact + hash.
- Evidence/provenance: DocView IS the provenance terminus — inbound deep links carry `{docId, version, artifact, page, region}` and light the region persistently (kit law 6; the lit-region contract in `cross-cutting/provenance-lit-row-trace.md`).
- Permissions/read-only projections: Organization-scoped; Client-scoped in PMC mode (a preparer sees only their clients' documents; owner mode never shows a Client column — 02 §1); reviewers read-only; foreign-org docIds return honest 404 (the send-vertical pattern, snapshot §3). Resident-level source documents (rent rolls, deposit audits) are inward-only — no outbound affordance exists on this surface (kit law 9).

Field groups and grain: organization → client → (property, loan via links) → document → version → artifact → page/region; reporting period and checklist item attach by link; requirement, test, metric, package section, certification record, and send record appear only as downstream-use references.

## 5. State machine and exceptions

Document lifecycle (per `docId`):

```text
arriving (Intake-owned) → processing (artifact generation)
→ filed (canonical, current version)
→ superseded-by-version | replaced-corrected (tombstoned original retained)
→ referenced-by-sealed-package (version pinned, locked)
```

Transitions:

| Transition | Trigger | Actor | Prerequisites | Reversible? | Audit event | Failure behavior |
|---|---|---|---|---|---|---|
| arriving → processing | Pipeline accepts (recognized or human-routed) | Engine/agent | Hash computed; org/client scope resolved | n/a | arrival-accepted | Unroutable stays in Intake; near-deadline gate is Intake's |
| processing → filed | Artifact set complete (Original always; Recreated/Structured may trail) | Engine | Original stored + hashed | n/a | document-filed (channel, filed-by) | Artifact failure → filed-partial with honest per-artifact failure chips + retry |
| filed → superseded-by-version | Typed answer "new version" | Human | FilingQuestionDialog completed | Yes — Make Current re-promotes | version-filed | Cancel leaves canon untouched |
| filed → replaced-corrected | Typed answer "replacement/correction" | Human | Impact preview acknowledged; void warning acknowledged if certified values affected | Prior version retained as tombstone; the void is not silently reversible | replacement-filed (+ void event where triggered) | Cancel leaves canon untouched |
| any → tombstoned (version) | Tombstone act | Human | No sealed-package reference pins this version | Yes — Restore | version-tombstoned | Sealed ref → refusal naming the SendRecord |
| filed → referenced-by-sealed-package | Send completes (Send & Record surface) | Engine (on the send gate) | CertificationRecord valid; approved-bytes hash match | **Never** (seal-not-wipe) | sealed-ref-pinned | — |

Register and DocView states (all mandatory kit states covered):

| State | Trigger | Behavior here |
|---|---|---|
| empty | New org, no docs | Register shows the designated intake address + upload CTA ("file your first loan documents"), never a blank grid |
| awaiting documents | Checklist items expected, not arrived | Filed mode shows expected-but-missing rows as ghost links into Intake/Holding (summary only; Intake owns the chase) |
| loading/processing | Artifact generation running | Row shows per-artifact progress — Original stored first; DocView opens with Source available, Readable/Extracted labeled "generating," never a fake sheet |
| partial/incomplete | An artifact failed (degraded scan OCR) | Honest failure chip on that artifact + retry; Readable absent rather than fabricated (fail-closed, 04 §2.5) |
| extracted/unconfirmed | Structured reading produced `proposed` values | Document appears in the **"Needs verification"** saved view (R2 research: Ironclad); Extracted Data mode shows per-field status |
| stale/superseded | Source replaced after downstream values were confirmed (03 §2) | Stale badge on the doc AND on every affected downstream figure with diff link; re-confirmation required on the owning surfaces |
| low confidence | OCR/classification confidence under threshold | Suspect-word overlay in Extracted mode — original pixels shown, the guess never presented as truth (R2 research: Adobe); classification confidence beside the arrival in the tray |
| conflict | Cross-source check fails (roll summary ≠ unit-row aggregation; statement total ≠ Σ lines) | Listed in Conflicts mode with both sources cited; resolution deep-links to the owning surface |
| failed tie-out | Downstream package tie-out fails (Review Room-owned) | Appears only as a downstream-uses flag with link — no tie-out UI here |
| watch/shortfall/breach | Verdicts exist downstream | **Never rendered here.** No verdict chips on documents; the rent roll feeding Bexley's monitored-occupancy shortfall (canon) carries no shortfall badge in Documents — the downstream-uses panel links to the covenant surface where the verdict renders |
| permission denied | Foreign org/client scope | Honest 404 on foreign-org docId; client-scoped listing in PMC mode |
| read-only | Reviewer role; sealed-period documents | Mutation affordances absent (not disabled-but-visible); export where permitted |
| blocked/gated | Re-upload matches an existing filed slot | Filing blocked pending the typed answer; certified-impacting replacement additionally gates on the impact acknowledgment |
| certified (void-on-change) | Confirmed replacement feeds a certified package | Void event fires — visible, reasoned, quiet-logged; period returns to in-review (06 §6); the warning lists exact affected fields BEFORE the act |
| sent/sealed | Version referenced by a SendRecord | Pinned forever; tombstone refused with the sealing reason; every artifact stays addressable |
| recovery/undo | Wrong promotion or tombstone | Make Current re-promotes any version; tombstones restore; metadata edits versioned. **Undo impossible by design:** original bytes, content hashes, sealed references, ActivityEvents, and executed void events |

Exception flows that ride alongside the lifecycle:

- **Duplicate-hash upload.** The engine detects byte-identical content → no new version, no dialog; the tray row reports "already filed as {docId}@vN" with a link (dedupe by hash, 04 §1). Quiet-logged as duplicate suppression.
- **Replacement during an open review.** A replacement confirmed while the period is in-review stales the affected in-review figures immediately (recompute-on-view, 04 §1 Review row); the Review Room surfaces the stale badges — Documents only raises the flags.
- **Amendment filing.** Filing a loan-modification document re-opens extraction for the affected RequirementRecords only (02 §5) — the relationships section links the amendment into the loan family, and the re-opened confirmations appear on Extraction & Confirmation, not here.
- **Wrong-loan filing discovered late.** Correcting a loan/period link after downstream values confirmed behaves as a replacement: impact preview, stale propagation, possible void — never a silent re-tag.
- **Client-scope conflict (PMC).** An arrival matching a document slot in another client's book cannot be resolved by this preparer; the tray shows a scope-refusal and routes to Intake for an authorized user.

## 6. Engine / Agent / Human / Gate / Quiet Log

| Concern | ENGINE (deterministic) | AGENT (prepares/proposes) | HUMAN (typed decisions) | GATE (interrupts) | QUIET LOG |
|---|---|---|---|---|---|
| Ingestion (one pipeline) | Content hash; dedupe by hash; org/client scoping | Classify type; match loan+period with confidence + evidence | Resolve ambiguous arrivals (in Intake) | Unroutable arrival with approaching deadline (Intake-owned, linked) | High-confidence auto-filed arrivals; duplicate suppression |
| Artifact generation | Store immutable Original + hash; validate artifact-set completeness | Generate Recreated-searchable (OCR + coordinates + confidence) and Structured reading; label derivatives always | Suspect-word accept/correct/unreadable per region | OCR failure blocks Readable honestly (no fake sheet) | Artifact completions with processing version |
| Versioning | Chain integrity; same-slot detection by filed slot, never by filename | Recommend version-vs-replacement with evidence ("period matches filed v1; totals differ") | **The typed filing answer**; Make Current; tombstone/restore | Replacement touching confirmed/certified values → impact acknowledgment | Version filed/promoted/tombstoned |
| Downstream integrity | Compute the downstream-uses graph; propagate stale flags; enforce void-on-change | Explain impact in plain terms ("this feeds 3 fields in the Q2 package") | Re-confirm affected values (on their owning surfaces) | Void-on-change warning before a certified-impacting replacement | Stale flags raised/cleared; void events |
| Conflicts | Cross-source consistency checks (roll summary vs rows; statement totals vs Σ lines; doc vs doc) | Draft conflict explanations citing both sources | Disposition (on the owning surface) | A conflict blocking an imminent package readiness | Conflicts raised/resolved |
| Audit & usage | Reference integrity; sealed-ref pinning; access records | Answer "where is this used?" via Ask with lit-row citations | — | — | Every view/export/change, actor-stamped |

Lane invariants honored (04 §2): no shipping number is ever computed on this surface; the agent never files past a human gate; agent presence is contextual — confidence beside the arrival, recommendation inside the filing question, explanation beside the impact — never a chat box added to demonstrate AI.

## 7. Information hierarchy

1. Page/frame header: shell 48px header; book-scoped breadcrumb `Covenant / Documents` (altitude gate — no loan/period crumbs on a book floor; 08 §2); the five register modes in the header view-switcher slot.
2. Decision/status summary: count + CTA header per the Reports gold standard — "214 documents · 6 need verification · 2 conflicts" with CountBadge chips that ARE links to those saved views.
3. Primary work region: the register grid (mode-dependent columns) — or, on `/documents/[docId]`, the DocView paper sheet.
4. Secondary context: DocView's right metadata rail (identity, versions, relationships, downstream uses, audit).
5. Evidence/proof: the document itself is the proof; lit regions render in-page; hash chips prove identity; Source mode is the bytes.
6. Actions: Upload (routes through the intake pipeline), per-row open/peek, per-version acts, the filing-question dialog when triggered.
7. Activity/history: Audit-Usage mode (register) and the rail's audit section (DocView) — both filtered views of the one ActivityEvent store.

Absent by design: covenant verdict chips (verdicts render only on covenant surfaces); charts (a document register out-encodes nothing with a chart — Reports gold standard: count+CTA, filter cluster, no charts); any second upload/ingestion UI distinct from the pipeline; outbound affordances on resident-level documents (kit law 9); any period-status control.

## 8. Page anatomy and regions

### /documents — the register

**Header band.** Purpose: orientation + the only global actions. Content: count + CTA ("Upload documents"), search (`/`), filter cluster (type, loan, period, channel, status, client in PMC mode), SavedView selector with the governed default "Needs verification". Persistence: persistent. Interaction: filters compose and persist per user; the CTA opens the UploadTray. Min height 96px; never collapses. Why: the Reports gold-standard header pattern, reused verbatim in structure.

**Mode tabs (header view-switcher slot).** Purpose: the five register modes — **FILED · VERSIONS · EXTRACTED DATA · CONFLICTS · AUDIT-USAGE**. Persistence: persistent; mode is URL-addressable (`/documents?view=`). Why tabs, not panes: one big pane + view-switching is the ruled pane model (kit law 5); the modes are alternative projections of one register, never needed simultaneously.

**Register grid (work window 1).** Purpose: the canonical inventory under the active mode. Content by mode:

- **FILED** — the canonical inventory: name · type · loan(s) · period · version chip (vN) · arrival channel (intake email / upload / bulk) · filed date · filed-by. Default sort: filed date desc. This is the mode the rail item lands on.
- **VERSIONS** — replacement chains: canonical slot · stack summary ("v3 current · v1 tombstoned") · last change · changed-by · sealed-pin indicator. Row-expand reveals the full VersionStack inline.
- **EXTRACTED DATA** — documents → their extracted records: document · extracted-records count · status rollup (proposed n / confirmed m) · confidence floor · owning-surface link ("Confirm on Extraction & Confirmation"). Feeds the "Needs verification" saved view.
- **CONFLICTS** — documents with open extraction conflicts: document · conflict class (internal-sum / cross-document / doc-vs-confirmed-value) · both sources cited · age · owning-surface link. Empty state says so plainly ("no open conflicts") rather than hiding the mode.
- **AUDIT-USAGE** — downstream use and history: document · downstream consumers count · sealed refs count · last access · last change. Row-expand reveals the DownstreamUsesPanel inline for that document.

Persistence: persistent. Interaction: row → DocView; hover → peek affordance; uniform 40px rows, open-not-boxed. Min width 720px; below it, secondary columns drop behind row-expand (labeled, never silent).

**Upload tray.** Purpose: bulk upload through the ONE pipeline, with recognition visible where the user is. Content: per-file rows — name, hash progress, recognized type, matched loan+period, confidence, proposed filing; auto-filed rows link to the filed document; exceptions carry "Resolve in Intake". Persistence: transient (dismissible; state survives in Intake because the rows ARE Intake arrivals, projected). Interaction: drag-drop or picker; no routing decisions executed here. Min width 360px docked bottom-right. Why a tray, not a page: uploading is an act, not a place; the place for unresolved arrivals is Intake.

**Canvas panel (peek).** Purpose: quick preview + Ask without leaving the register. Content: first-page render, identity chips, "Open" action. Persistence: summonable, floating (the ruled canvas slot — separate from the split count; 08 §4). Esc dismisses. Never load-bearing: every decision is reachable without it.

### /documents/[docId] — DocView

**Sheet header.** Purpose: identity above the paper. Content: doc name, type, loan/period chips, version selector (current + count), the derivative label chip when in Readable, mono hash chip in Source, "arrived via intake on {date}" link. Persistence: persistent. Min height 56px.

**Mode strip.** Purpose: the four artifact modes — **Readable | Source | Extracted | Compare**. Content and law per mode:
- *Readable* — the Recreated-searchable derivative, permanently labeled **"Recreated · searchable — derivative"**; text selection and search live here; never represented as the original (kit law 7).
- *Source* — the original bytes rendered + content hash chip; no text layer pretense: text search over a zero-text-layer scan honestly returns nothing (evidence: SLOT-4/5).
- *Extracted* — the structured reading: fields/tables with source regions + per-field confidence; the suspect-word overlay for low-confidence OCR.
- *Compare* — Original⇄Recreated as a toggle/overlay (NOT a permanent split — visible pixels are materially identical; 06 §8), or Version⇄Version as a 50/50 synchronized split.
Persistence: persistent; mode in URL. Interaction: segmented control, keys `1–4`.

**Paper sheet (work window 1).** Purpose: the document itself — the porcelain render. Content: page canvas, lit-region highlight layer, selection layer (Readable), page thumbnails/TOC strip appearing as the pane widens (the widening-pane-grows-a-TOC gasp, snapshot §5). Persistence: persistent. Min width 640px. Resize: thumbnails collapse first, then gutters; the page itself never silently crops.

**Metadata rail.** Purpose: identity and consequence beside the paper. Content, five collapsible sections: Identity (docId, type, hash, artifact set with per-artifact status) · Versions (Box-style stack) · Relationships (loan ↔ riders ↔ amendments ↔ period filings ↔ checklist item — Ironclad contract-family adaptation) · **Downstream uses** (field-level consumers incl. sealed-package refs with locks) · Audit (doc-filtered ActivityEvents). Persistence: pinned. Width 280–360px; below minimum it becomes labeled summonable tabs — no silent compression. Why a pinned rail: reading and identity/impact must be co-visible (§9); an inspector that vanishes while scrolling paper would orphan the trust questions this surface exists to answer.

**Filing-question dialog.** Purpose: the typed act — "New version of the filed document, or replacement/correction?" Content: both options with plain-language consequences, agent recommendation + evidence, downstream-impact preview (affected fields; void warning when a certification is touched), typed confirm. Persistence: modal. Why a modal is legitimate here: a typed act with legal weight (it can void a certification) and a destructive-adjacent choice — exactly the ruled modal exceptions (kit law 5).

## 9. Co-visibility matrix

| Datum/surface A | Datum/surface B | Must be simultaneous? | User decision enabled | Default topology | Fallback |
|---|---|---:|---|---|---|
| Register row | Document preview | No | "Is this the doc I want?" | Canvas-panel peek | Navigate to DocView |
| DocView sheet | Metadata rail (identity/uses) | Yes | "Do I trust this doc? What does it feed?" | Pinned rail beside sheet | Rail → labeled overlay tabs under min width |
| Original | Recreated-searchable | No (pixels materially identical) | "Is the derivative faithful?" | Compare toggle/overlay with difference outline | Side-by-side never persists (ruled, 06 §8) |
| Version N | Version N−1 | Yes, during compare | "What changed between filings?" | 50/50 synchronized two-window split | Stacked labeled tabs below 1280px total (08 §4) |
| Extracted field | Its source region | Yes | "Does the value match the page?" | Field list lights region on the sheet, persistently | Field row carries page ref; tap jumps |
| Filing question | Downstream impact list | Yes | The version-vs-replacement act | Impact preview inside the dialog | Never separated — the act is illegal without its consequences visible |
| Doc audit trail | The sheet | No | "Who touched this?" | Rail audit section | Audit-Usage register mode |
| Upload batch | Recognition results | Yes | "Did my dump land correctly?" | Inline per-file results in the tray | Intake shows the same arrivals (one store) |

No pane exists merely because information exists: the rail earns its pin through the trust questions; the only two-work-window split is version compare, the one genuine side-by-side decision.

## 10. Layouts and viewport behavior

- **1440px**
  - Register: shell rail 240px + one full-width grid (~1200px work window); all FILED columns visible.
  - DocView: sheet ~880px + metadata rail 320px (73/27).
  - Version compare: two 590px windows, synchronized scroll; the rail auto-collapses to overlay tabs for the duration.
  - Canvas peek: 420px floating, positioned away from the selected row.
- **1728px**
  - Register: filed-by + channel columns on by default; grid ~1488px.
  - DocView: sheet 1024px + rail 360px with comfortable gutters (74/26).
  - Compare: 2×~700px with the rail as summonable overlay.
- **2048px**
  - Register: no additional columns — surplus becomes gutter (a wider grid does not earn new data).
  - DocView: sheet caps at 1120px (line-length ceiling for readable documents); surplus goes to gutters, the 360px rail, and the page-thumbnail/TOC strip on the sheet's left (the widening-pane TOC gasp).
  - Compare: 2×~840px plus the pinned rail.
- **Narrow/compact fallback**
  - Shell rail collapses to icons + badges.
  - Register drops to name/type/status with detail-on-row-expand.
  - DocView rail becomes labeled bottom-sheet tabs (Identity · Versions · Uses · Audit).
  - Compare refuses the split and offers stacked labeled tabs. No silent compression anywhere (kit law 5).
- Focus behavior: `F` on the sheet hides the rail (summonable back); Esc restores focus mode before it ever navigates.
- Compare behavior: Original⇄Recreated is `.`-toggled overlay with a 150ms cross-fade (static outline under reduced motion); Version⇄Version scroll/zoom lock together; either side can be promoted to full width and back.
- Proof/source behavior: inbound lit-region links scroll the sheet to the region and light it persistently; the rail stays pinned so identity is never lost while proving.
- Minimum viable dimensions: register 720px; DocView 640px sheet + 280px rail (else rail overlays); compare windows ≥560px each or the split refuses and stacks (08 §4).

## 11. Components and exact anatomy

**CovenantShell / breadcrumb / rail** — REUSE `CovenantShell.tsx`; deep routes mount the shell with initial view (08 §1). Rail badge count via the shared **CountBadge** (snapshot §4: one shared component, ruled).

**DocumentRegister** — ADJUST the existing Documents views. Built on the Reports gold-standard list pattern (count+CTA header, filter cluster, no charts). Parts:
- header band (count + CTA + status chips that link to saved views);
- filter cluster (type, loan, period, channel, status, client in PMC mode);
- SavedView selector;
- mode-driven column model (one grid component, five column sets);
- virtualized grid body, uniform 40px rows, open-not-boxed;
- row overflow menu: Open · Peek · Download original · Copy link.

**SavedView control** — REUSE the shared SavedView mechanism (02 §2). Ships with governed defaults: **"Needs verification"** (documents with proposed, unconfirmed extracted values — R2 research: Ironclad), "Conflicts open", "Filed this period", "Recently replaced".

**UploadTray** — NEW. Rows are projections of Intake arrivals — one store, one pipeline. Parts:
- dropzone (drag-drop + picker; multi-file);
- batch header (n files · aggregate progress · dismiss);
- per-file row: name · hash progress · recognized type · matched loan+period · confidence chip · proposed filing · state;
- auto-filed rows link to the filed document;
- exception rows carry "Resolve in Intake" (the decision act stays Intake's);
- internally reuses Intake's arrival-card row anatomy.

**DocViewSheet** — ADJUST the existing porcelain paper sheet. Parts:
- page canvas (per-artifact render);
- region-highlight layer (the lit-region contract);
- selection layer (Readable only; marquee-region in Source for Ask);
- page navigator + zoom control;
- thumbnail/TOC strip (width-gated — the widening-pane TOC);
- in-document search (Readable/Extracted only; Source search honestly absent on zero-text-layer originals).

**ArtifactModeStrip** — NEW. Parts:
- four segments: Readable | Source | Extracted | Compare (keys `1–4`);
- the permanent "Recreated · searchable — derivative" label chip bound to Readable;
- the mono hash chip bound to Source;
- confidence summary chip bound to Extracted ("3 suspects · min 0.62");
- compare chooser bound to Compare: Original⇄Recreated overlay, or pick-two-versions split.

**VersionStack** — NEW (rail section + full view in VERSIONS mode). R2 research: Box — take the stack, reject filename inference. Parts:
- per-version row: vN chip · date · filed-by · hash (truncated, copy-on-click) · size;
- per-version actions: **Preview · Download · Make Current · Tombstone**;
- tombstoned rows visible as unavailable, with **Restore**;
- sealed-pinned rows carry a lock and refuse tombstone with the SendRecord named;
- "compare with current" shortcut per row (opens Compare mode pre-loaded).

**FilingQuestionDialog** — NEW modal (typed act; the modal is legitimate per kit law 5). Never triggered by filename inference — only by the pipeline detecting a same-slot filing (R2 research: Box, rejected mechanic). Parts:
- the question: "New version of the filed document, or replacement/correction?";
- two option cards with plain-language consequences;
- agent recommendation strip with evidence link ("period matches filed v1; totals differ");
- downstream-impact preview: affected fields grouped by surface; certified fields flagged with the void warning;
- typed confirm + cancel; focus-trapped; Esc cancels safely.

**MetadataRail** — NEW. Parts: five collapsible sections (Identity · Versions · Relationships · Downstream uses · Audit), each with a count chip; section open/closed state persists per user; below min width the rail renders as labeled summonable tabs.

**DownstreamUsesPanel** — NEW; the void-on-change warning's data source. Parts:
- consumer rows: consuming field → owning surface → period/package → status (live / stale / **sealed** with lock);
- live rows open the consuming surface with THAT figure's evidence lit;
- stale rows carry the diff link to re-confirmation;
- sealed rows link to the sealed period record (Reports) and pin `docId@vN`.

**SuspectWordOverlay** — NEW (Extracted mode). R2 research: Adobe suspects; ABBYY low-confidence marks. Parts:
- suspect-region boxes over the page, confidence-ordered queue;
- selected-suspect card: original-pixels crop · "Recognized as {guess}" · actions **Accept · Correct · Unreadable**;
- Unreadable pins the region and escalates rather than forcing a guess;
- `n/p` queue navigation; queue count in the mode strip's confidence chip.

**ExtractedFieldsList** — NEW (Extracted mode side list). Read-only here; the confirm act lives on Extraction & Confirmation. Parts:
- field rows: name · value in the inferred grammar (dotted provenance underline) · confidence · region-jump;
- table blocks with cell-level region jumps (deliberately exceeding Instabase's documented whole-table lighting limit — R2);
- status footer with "Confirm on {owning surface}" deep link.

**Evidence lit-region contract** — REUSE the lit-row contract (A6; `cross-cutting/provenance-lit-row-trace.md`). DocView implements the terminus: `{docId, version, artifact, page, region}` → scroll + persistent highlight.

**CommandPalette / SearchPalette** — REUSE; `G D` place chord; object results with loan/period identity chips (08 §5).

**AskPanel + HighlightToAsk** — REUSE; a selection in Readable mode travels with its provenance refs; answers cite the same lit-region chain (08 §6).

**Empty/error/recovery objects** — NEW from the shared pattern: empty-register CTA with the intake address; per-artifact failure chip with retry; honest 404 page; sealed-refusal notice naming the SendRecord.

## 12. Interaction specification

- Selection: single row select (arrow keys); shift-range in the register for bulk metadata edits; text selection in Readable only (Source is pixels — marquee-select there captures a region for Ask).
- Hover: register row hover reveals peek + overflow; extracted-field hover pre-lights its region faintly (committed light on click); version-row hover shows full hash tooltip; downstream-use row hover shows the consuming figure's current value with its class grammar.
- Focus: visible 2px accent (#7189FF) focus ring on rows, tabs, mode segments, rail sections, dialog controls.
- Keyboard (G-chords and the palette per 08 §5):

| Key | Context | Action |
|---|---|---|
| `G D` | anywhere | Go to Documents |
| `⌘K` | anywhere | Palette (places / objects / computed answers) |
| `/` | register | Focus filter/search |
| `↑/↓` · `Enter` | register | Move rows · open DocView |
| `Space` | register row | Canvas-panel peek |
| `1/2/3/4` | DocView | Readable / Source / Extracted / Compare |
| `[` / `]` | DocView | Previous / next version |
| `.` | Compare | Toggle Original⇄Recreated overlay |
| `n` / `p` | Extracted | Next / previous suspect word |
| `F` | DocView | Focus the sheet (rail summonable back) |
| `Esc` | everywhere | Overlay → rail tab → peek → then navigate back (closes summoned panes before it ever navigates, 07 §3) |
- Editing and validation: metadata edits inline in the rail (typed fields validated against loan/period existence and client scope); suspect-word corrections per region; every edit versioned with actor + timestamp.
- Bulk action: bulk upload (through the pipeline only); bulk metadata assignment (loan/period tags) on selected register rows. Never bulk version decisions — each filing question is per-document by design; safety over throughput at the canon boundary.
- Undo/recovery: Make Current re-promotes; tombstone Restore; metadata edit history in the rail. No undo: original bytes, hashes, sealed refs, ActivityEvents, executed voids.
- Sorting/filtering: every column sortable; filters compose into saved views; filter + scroll state survives navigation away and back.
- Drill-down and return path: register → DocView → lit region → back restores register scroll + filters; inbound deep links land in DocView with the book-floor breadcrumb back to `Covenant / Documents`.
- Source-linked selection (the lit-row behavior): click an extracted field → its region lights and stays lit; click a downstream-use row → the consuming surface opens with that figure's evidence lit — the same contract run forward and backward.
- Save/persistence: mode, version, page, and zoom survive in the URL (addressability rule, 08 §1); views persist per user.
- Collaboration/commenting: none in v1 — questions route through Ask; value disputes route to the owning confirmation surface; the audit trail is the shared record.

## 13. Visual craft direction

- Typography: register rows 13px/20; section heads 15px medium; DocView sheet header 16px medium; the document render is the document's own face — chrome never competes with paper. Metadata labels 11px caps-tracked in a mid gray rung; values 13px in the darkest ink rung of the ten-rung ladder (adopted 2026-08-07, snapshot §4).
- Financial numbers and hashes: Geist Mono with `tabular-nums slashed-zero` (Review-9 colors-only ruling keeps Geist Mono); hash chips truncate middle (`a3f2…9c1d`), copy-on-click.
- Spacing rhythm: 8px base grid; register rows 40px uniform; rail sections 16px padded, 24px between sections; sheet gutters grow with viewport per §10.
- Density: register comfortable-dense (the Reports standard); DocView generous — the paper breathes.
- Open ground vs earned boundaries: open-not-boxed tables — hairline row dividers from the ladder's hairline rung only; the sheet earns its boundary (paper edge + subtle elevation over the frame ground); the rail separates by a single hairline, no card boxes.
- Dividers/elevation: one elevation step for the floating canvas panel and the filing dialog; everything else flat on the frame ground.
- Semantic color — accent family only: selection/links #7189FF, hover #8EA1FF, active #6078F4; lit regions fill with the accent tint #A9B5FF at low opacity with a #7189FF edge, consuming the Evidence lit-trace treatment (the one paper-accent render live today, snapshot §4). Status chips use gray rungs + accent. NO verdict colors on this surface. Zero new color values proposed.
- Derivative honesty in type: the "Recreated · searchable — derivative" chip is a permanent quiet label (mid gray rung, 11px) on the sheet header in Readable — visible without shouting, never omissible, never restyled per context.
- Certified-sheet treatment: appears here ONLY when the viewed artifact is itself a certified package render opened from a sealed reference — that render arrives already carrying the ruled whole-sheet blue-violet treatment (#E7EBF8 panel · #DDE4FB tint · #B3BCDA hairline · #33419D accent) from the Certificate surface. Documents adds nothing to it and applies none of those hexes to ordinary documents.
- Focus/selected/hover states: selected row = accent-tint wash + 2px left accent edge; hover = one gray-rung lift; focus ring accent 2px.
- Charts: none (doctrine — a chart must out-encode adjacent text or not ship; counts and lists win on a register).
- Motion: 120ms ease on mode switches and rail collapse; lit-region highlight fades in over 150ms and persists; compare cross-fade 150ms. Reduced motion: instant state changes, static difference outline instead of the compare flash.
- Long-session ergonomics: sheet line-length cap 1120px, neutral frame rungs, zero idle animation — audit sessions stay calm.

## 14. Benchmark research and synthesis

| Product | Limited role | Official evidence | Exact pattern to take | Covenant adaptation | What to reject | Why this reference beats alternatives |
|---|---|---|---|---|---|---|
| Ironclad | Record = document + properties; verification as repository state | https://support.ironcladapp.com/hc/en-us/articles/12277440413207-Manage-Records · https://support.ironcladapp.com/hc/en-us/articles/31344994944023-The-Repository-Dashboard-Views | Records carry typed properties + related records ("contract families"); default **"Needs verification"** view collects records with unverified AI predictions; Views = saved filters + columns | Document properties = filing metadata; families = loan ↔ riders ↔ amendments ↔ period filings; the five register modes are governed saved views | Emailed-CSV extraction review detached from the source page | The only benchmark treating verification as a first-class repository state, not a side workflow |
| Box | Version-stack mechanics; metadata beside preview | https://support.box.com/hc/en-us/articles/360043697054-Accessing-Version-History · https://support.box.com/hc/en-us/articles/360044196173-Using-Metadata | One canonical identity; per-version Preview/Download/**Make Current**; tombstoned deletes restorable; Details + Metadata panels beside preview | VersionStack + MetadataRail in spirit; tombstones never silent; sealed pins harden the model | **Same-filename-implies-new-version inference** — Covenant asks the explicit typed question instead (R2) | The cleanest documented promotion + tombstone model in mainstream file infrastructure |
| ABBYY (FineReader/Vantage) | The canonical searchable-derivative model | https://support.abbyy.com/hc/en-us/articles/19098939370515 | "Text under the page image": scan pixels on top, recognized text beneath — searchable yet visually the original | Exactly the Recreated · searchable artifact; ABBYY's own framing justifies the permanent derivative label | Character-by-character verification loops | The derivative's honesty contract comes straight from ABBYY's documented render order |
| Adobe Acrobat | The "suspect" concept | https://helpx.adobe.com/acrobat/desktop/create-documents/scan-documents-to-pdfs/fix-scanned-text.html | Uncertain OCR keeps the original bitmap visible; "Recognized as" + Accept per word; suspects are a designed-in state, not an error | SuspectWordOverlay; the third verb "Unreadable" pins + escalates instead of forcing a guess | The one-word-at-a-time modal loop with no queue, no confidence ordering | Decades-old proof that low-confidence honesty is shippable UX, not an apology |
| Hebbia | Citation click-through to the exact page | https://www.hebbia.com/blog/introducing-matrix-the-interface-to-agi | Every extracted cell deep-links to page/paragraph; citation previews peek inline before jumping | Extracted-records refs and downstream-use rows deep-link into DocView at `{page, region}`; hover-peek before jumping | Free-prose answer cells as the stored record | The strongest citation-first posture in document AI — matches the lit-row law exactly |
| Instabase | Grounded correction; validation-first ordering | https://docs.instabase.com/automate/validating-documents | Corrections by typing OR selecting the source area (re-grounding); auto-revalidation; "show source for all fields" | Extracted mode's region jumps; cell-level lighting deliberately exceeds Instabase's documented whole-table limit | Table-level-only source lighting | Documents its own limitation, telling us precisely where to go further |
| Ocrolus | Cross-document consistency in lending | https://www.ocrolus.com/press/ocrolus-introduces-cross-source-validation-to-automate-corroboration-of-borrower-information-accuracy/ | Cross Source Validation corroborates values across a file's documents; inconsistencies surface as findings | The Conflicts mode's checks: roll summary vs unit rows, statement totals vs Σ lines, doc vs doc | Vendor-managed invisible human-in-the-loop workforces | The one benchmark doing this in lending-document reality, not generic OCR |
| DocuSign Navigator | Accept-or-edit attributes on a repository record | https://support.docusign.com/s/document-item?bundleId=pqz1702943441912&topicId=uwk1702945852405.html | Details pane of AI-extracted attributes; accepted-only data enters the reporting layer | Extracted Data mode's status rollup; the acts stay on the owning confirmation surface | Attribute verification without page-level source lighting | Shows the record-bound details pane; its grounding gap is what Instabase/Hebbia close |
| Google Document AI HITL | ANTI-PATTERN | https://docs.cloud.google.com/document-ai/docs/deprecation | A review console detached from the system of record — deprecated by its own platform | Nothing; boundary marker | Bolting document review onto an external labeling console | Proof that review must live inside the product that owns the records — Covenant's DocView is the system of record's own window |

Synthesis: the register is Ironclad's record model wearing the Reports gold-standard list clothes; versioning is Box hardened by replacing filename inference with a typed act; the artifact set is ABBYY's honest derivative plus Adobe's suspect contract; every extracted value is Hebbia-grade citation-linked; conflicts are Ocrolus's cross-source checks re-homed to the borrower's own analyst. What makes the result original and domain-correct for borrower-side lender reporting: none of those products knows that a document's values flow into a *certified, sealed lender package*. The DownstreamUsesPanel, the void-on-change warning inside the filing question, and sealed-version pinning are Covenant's own — derived from the lineage law (06 §1) and seal-not-wipe (02 §5) — and they are exactly what a contract repository or file locker cannot provide: the paper's consequences, visible at the moment the paper changes.

## 15. Domain references

Terminology and expected-data references come from the evidence kit itself: the servicer's document demands — JLL quarterly Property Questionnaire and Annual Certification (evidence: SLOT-4/5) — and the loan agreement's deliverables — Fannie Mae Form 6001.NR §8.02(b) with riders 6220/6241 (evidence: SLOT-3) — define what the filed canon must hold per period: statements, rent schedules with named fields, questionnaires, certifications, and rider-driven one-offs. The ENERGY STAR report requirement exists only because this loan carries Form 6241 — the expected-document set is per-loan, never templated across loans (06 §3). Ocrolus (§14) doubles as the lending-domain document-AI reference; generic servicer upload portals are cadence references only. **Domain authority does not equal visual authority** — none of these products dictates a pixel; and covenant semantics come from the loan documents and Terry, never from any referenced product (kit law 8).

## 16. Accessibility, performance, and safety

- WCAG contrast and focus: AA contrast for all text on the gray ladder; the derivative chip and hash chips meet 4.5:1; lit-region highlighting never relies on color alone (tint fill + edge + optional outline pattern); 2px visible focus ring throughout.
- Keyboard completeness: every act in §12 is keyboard-reachable; the filing dialog traps focus and restores it on close; the mode strip is a proper `radiogroup`; the suspect queue is fully `n/p`-drivable.
- Screen-reader semantics: register as `grid` with row/column headers; the mode strip announces "Readable — recreated searchable derivative, not the original document"; lit regions announce page + region context; version rows announce current/tombstoned/sealed state; the filing dialog announces both options' consequences.
- Table virtualization/large data: register virtualized past ~200 rows (uniform heights make this cheap); DocView renders pages lazily with adjacent-page prefetch; hashes computed server-side; artifact generation is asynchronous with resumable progress.
- Loading and latency feedback: per-artifact progress states, never a spinner over a fabricated sheet; optimistic UI for metadata edits only — never for filing acts, which round-trip.
- Destructive action confirmation: tombstone requires typed confirm; replacement/correction requires the impact acknowledgment; sealed-period deletion is refused outright with the sealing reason (not a confirm — a refusal).
- Certify and external-send safety: the void-on-change pathway is loud and honest — the impact list renders before the act, the void event after it; sealed packages pin exact `docId@vN` + hash so what was sent is provable forever (approved bytes, 02 §6).
- Source immutability: Original bytes are write-once with content hash; derivatives regenerate only as new processing versions, never in place; the processing version is part of every provenance chain (06 §1).
- Auditability: every act writes an identity-stamped ActivityEvent (actor, evidence refs, loan/period/package, outcome) to the one append-only store; Audit-Usage mode and the rail's audit section are filtered views of it.

## 17. Acceptance tests and fixtures

Fixtures: the Calloway Park (CAL) evidence kit — SLOT-3 loan agreement (Form 6001.NR + riders 6220/6241), SLOT-1 T-12 (FYE-2018: Total OpEx $1,686,050 · NOI $1,218,877 · Net Income $460,159), SLOT-2 rent roll (322 lease rows → 301 units), SLOT-4/5 executed JLL questionnaires (flattened scans, zero text layer) — plus the Bexley canon book (7 loans, monitored 90% occupancy floor).

1. **Four artifacts from a dead scan.** GIVEN the SLOT-4 executed questionnaire is uploaded, WHEN processing completes, THEN the Original is stored + hashed, the Recreated-searchable exists, Readable-mode search finds "management fee", Source-mode text search honestly returns nothing (zero text layer, evidence), AND the "Recreated · searchable — derivative" chip is present in Readable at every viewport and cannot be dismissed.
2. **The typed filing question.** GIVEN a CAL T-12 is filed, WHEN a second T-12 with an identical filename uploads into the same slot, THEN no automatic versioning occurs; the FilingQuestionDialog renders both options + the agent recommendation + downstream impact; "new version" yields v2 with v1 intact; "replacement/correction" marks v1 superseded-with-reason; both paths write ActivityEvents. Filename inference is provably absent (R2: Box, rejected).
3. **Void-on-change.** GIVEN Bexley's Q2 package is certified (canon), WHEN a document feeding a package field is replaced and confirmed, THEN the dialog listed the exact affected fields beforehand, the certification voids visibly, the period returns to in-review, and the void event appears in Audit-Usage (06 §6).
4. **Needs verification.** GIVEN SLOT-1 extraction leaves proposed values, THEN the document appears in the "Needs verification" saved view; WHEN the values are confirmed on Extraction & Confirmation, THEN the row clears with zero Documents-side mutation (ownership boundary held).
5. **Downstream uses + sealed pin.** GIVEN the SLOT-1 T-12 is filed and its period sent, THEN its DownstreamUsesPanel lists the annual-statement consumers and the tie-out consumer (cash-flow Net Income = T-12 Net Income $460,159, evidence) and a sealed-package row with lock; WHEN v2 is later filed, THEN the sealed row still resolves to v1's bytes + hash.
6. **Lit-region terminus.** GIVEN a certified figure's provenance chain, WHEN its deep link opens DocView, THEN the sheet lands at the exact page and region, lit persistently — verified against the U1-F1 wrong-row defect class: the lit region matches the chain's region ref exactly, in 3 of 3 renderers.
7. **One pipeline.** WHEN 7 mixed files are bulk-uploaded on `/documents`, THEN 7 arrivals exist in Intake's store; high-confidence files auto-file (quiet-logged); one ambiguous file shows "Resolve in Intake" and stays absent from Filed until resolved; a service-layer assertion proves no code path writes a Document except the pipeline.
8. **Version compare.** GIVEN v1 and v2 of a document, WHEN Compare opens the pair at 1440px, THEN a 50/50 synchronized split renders (≥560px each); at 1100px the split refuses and offers stacked labeled tabs; Original⇄Recreated never opens as a persistent split (06 §8).
9. **Keyboard.** `G D` → `/` → "T-12" → `Enter` opens DocView; `1..4` switch modes; `[`/`]` walk versions; `n/p` step suspects; `Esc` order verified: overlay → rail tab → peek → navigate.
10. **Sealed immutability.** WHEN tombstone is attempted on a sent-period document version, THEN the refusal names the SendRecord and no force path exists (undo impossible by design).
11. **Tenancy.** GIVEN a PMC preparer scoped to Client A, WHEN a Client B docId is requested, THEN honest 404; a reviewer role sees zero mutation affordances (absent, not disabled); owner mode shows no Client column.
12. **No verdicts here.** GIVEN the SLOT-2 roll feeds the canon occupancy chain (268/301 = 89.04% shortfall vs the monitored 90% floor), THEN the document row and DocView carry no verdict badge — the downstream-uses row links to the covenant surface where the shortfall renders.
13. **Accessibility.** Axe-clean on both routes; mode-strip radiogroup announced with the derivative wording; contrast spot-checks on the derivative and hash chips; reduced motion disables the compare cross-fade.
14. **Viewports.** Screenshot fixtures at 1440/1728/2048/narrow per §10 ratios; no horizontal scroll of the page body; every collapse is labeled, never silent.
15. **Benchmark challenger review.** Walk the Ironclad/Box/Hebbia "take" mechanics against the built surface; any regression from a taken mechanic files as a defect ticket.

## 18. Build plan

- Dependencies: permanent document storage + content-hash service (gap 2); the intake pipeline (gap 1 — `intake-classify.ts` exists unwired); tenancy FKs org→client→loan (gap 3); the lineage graph powering downstream-uses (06 §1); the ActivityEvent store; the orchestration spine for void-on-change (gap 5).
- Foundation work: document-store schema (document, version, artifact, filing-metadata, sealed-ref pin) with write-once Original enforcement; the artifact pipeline (OCR → Recreated with coordinates/confidence → Structured reading); register reads migrated from fixture arrays to store queries.
- Components to build first: VersionStack + FilingQuestionDialog (the safety core — nothing else matters if versioning can lie), then ArtifactModeStrip + the derivative label, MetadataRail, DownstreamUsesPanel, UploadTray-as-Intake-projection.
- Vertical slice (the send-vertical pattern — one route, one loan+period, engine data end to end): upload the executed-questionnaire scan fixture → the pipeline files it under the fixture loan's open period → `/documents/[docId]` renders all four artifacts from real storage → its extracted fields deep-link into that period's evidence. This single seam retires "DocView orphaned" (snapshot §3) and turns the dump→recreated-searchable gasp from a tag on a fixture card into a working demonstration (gap 2).
- Migration from fixture data: the Documents fixture arrays and `book.ts` document stubs retire behind a read-path flag; Intake and Composer document lists become checklist-filtered links to the same store (07 §2 duplication resolution — one register, linked everywhere).
- Rollout/feature flag: `covenant.documents.store` gates store-backed reads; `covenant.documents.upload` gates the tray; DocView deep links ship first (read-only, no write risk), then upload, then versioning acts.
- Proof artifacts required: before/after renders at the three viewports; a capture of the SLOT-4 scan becoming searchable in Readable while Source stays honestly unsearchable; the one-pipeline service-layer assertion green; the lit-region terminus demo passing the U1-F1 contract; the filing-question flow with a void event end to end.
- Final gate: `ADJUST` confirmed — the register spine and paper sheet stand; the importer, version safety, and downstream-uses machinery are the work. Re-grade after the vertical slice lands.
