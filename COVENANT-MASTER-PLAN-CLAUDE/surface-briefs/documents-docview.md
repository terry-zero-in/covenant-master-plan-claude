# DOCUMENTS + DOCVIEW — the filed record and the paper sheet

## 1. Identity and verdict

- Product layer: portfolio floor (Documents register) + cross-cutting evidence terminus (DocView — every lit-row chain in the app ends here; 07-SURFACE-INVENTORY §3).
- Route/address in basis-v2: `/documents` and `/documents/[docId]` in the `(covenant)` route group (planned deep routes, 08-GLOBAL-FRAME-SHELL-PLAN §1); components in `src/components/covenant/` (existing Documents views + DocView porcelain sheet); pipeline and lineage logic in `src/lib/covenant/`; tokens consumed from `src/styles/covenant-tokens.css`.
- Current build state: `FIXTURE` — the register renders demo arrays; DocView is the porcelain (paper) sheet with "Recreated · searchable" tags, measured **orphaned** (no importer) in the 2026-08-06 phase-3 measurement (snapshot §3).
- Existing-surface verdict: `ADJUST` — keep the register spine and the paper sheet; give DocView its importer by wiring everything through the intake pipeline (gap 1 + gap 2), never a second ingestion path.
- Research tier: B/C.
- Primary users/roles: owner and PMC preparer (file, version, correct); reviewer read-only (inspect, export where permitted); the lender is never a user (02-OBJECT-RELATIONSHIP-MODEL §4).
- Frequency and session duration: weekly touches during a period's document-gathering weeks; short sessions (find/verify/version, 1–5 min); longer sessions at loan setup and audit/follow-up time.
- Error cost: a mis-versioned or silently replaced source document feeds a wrong number into a package — the terminal error is that number certified and sent to a lender. Version and replacement acts here sit directly upstream of the certify gate.
- Success criterion: any filed document, any version, any artifact, and any downstream use is findable in under 10 seconds; nothing ever enters the canon except through the one intake pipeline; a replaced source can never silently keep stale downstream values alive.

## 2. User job and decisions

- Primary job-to-be-done: maintain the canonical filed record of every document the book depends on — original bytes, honest derivatives, versions, and the map of what consumes them.
- Decisions made here: the typed filing question on re-upload (**new version** vs **replacement/correction** — never filename inference; R2 research: Box, rejected mechanic); Make Current promotion of a prior version; tombstone-delete (where a sealed period does not forbid it); suspect-word OCR corrections on the Recreated derivative; filing-metadata corrections (type, loan/period links).
- Decisions explicitly NOT made here: arrival routing and checklist matching (Intake owns them); extracted-value confirmation (Extraction & Confirmation owns it); COA mapping (Actuals & Computation); any verdict disposition (Review Room / findings).
- Questions the surface must answer in scan order: (1) is the book's paper complete and current for the periods in flight? (2) which documents still need verification? (3) which have open conflicts? (4) for THIS document: which version is canonical, what changed, and what downstream fields consume it — including sealed packages?
- Entry paths: rail item Documents (`G D`); the lit-row chain terminus (any figure → Evidence → DocView at the exact source region; 07 §3); Loan Detail per-loan document links; Intake "filed → link" after routing (02 §3); Composer attachment links; CommandPalette object results ("Bexley loan agreement v2").
- Exit paths: Extraction & Confirmation (confirm proposed values); Intake (resolve an ambiguous upload); Review Room Evidence (see this doc in its decision context); the sealed period record (Reports); Loan Detail.
- Completion/advancement conditions: filing a checklist-expected document advances the Holding checklist (Intake-owned computation); a confirmed replacement re-opens downstream confirmations and can void a certification — Documents never advances period status itself (the orchestration spine owns transitions; 02 §3).

## 3. Object and ownership model

| Object/action/status | Owned here? | Summary/link elsewhere | Address/identity | Notes |
|---|---:|---|---|---|
| Document (canonical filed record) | YES | Intake links after routing; Loan Detail lists links; Composer attaches refs; Review Room Evidence renders (never re-files) | `docId` (+ version) | 02 §2; record = document + typed properties (R2 research: Ironclad) |
| Version chain (`replaces`) | YES | Reports/sealed periods pin exact versions | `docId@vN` | Originals immutable; promotion + tombstones, never silent overwrite |
| Artifact set (Original / Recreated-searchable / Structured / Extracted-records refs) | YES | Evidence pane renders artifacts by ref | `docId@vN/{artifact}` | Four-artifact law (kit law 7; 06 §8) |
| Filing metadata (type, loan/period/client links, filed-by, channel) | YES | — | properties on `docId` | Box metadata-instance adaptation (R2) |
| SavedView instances for this register | YES | — | `viewId` | One shared SavedView mechanism app-wide (02 §2) |
| Arrival + routing decision, holding checklist | NO — Intake/Holding | Documents shows "arrived via intake on {date}" + link | `arrivalId` | The bulk-upload tray here is a projection of Intake's arrivals store (§8) |
| ExtractedValue states (proposed/confirmed/corrected) | NO — Extraction & Confirmation | Extracted Data mode renders read-only status + deep links | `valId` | Confirmation is a typed act on its owning surface |
| NormalizedFinancialLine / Metric / TestResult | NO — Actuals & Computation | Downstream-uses panel cites consumers by ref | per 02 §2 | No verdict chips render in Documents (§7, absent by design) |
| Package composition / CertificationRecord / SendRecord | NO — Composer / Certificate / Send | Downstream-uses panel lists sealed-package references with locks | `pkgId`, `certId`, `sendId` | Seal-not-wipe: sealed refs pin document versions forever |
| ActivityEvent (audit trail) | NO — cross-cutting store | Audit-Usage mode renders the doc-filtered view | `eventId` | One store; every feed is a filtered view (07 §2) |

No-double-homing boundary: **Intake owns arrivals and holding; Documents owns the filed canon** (kit law 7). Each may summarize + link to the other only. Bulk upload on `/documents` creates *arrivals* in Intake's store that flow through the one pipeline; Documents renders their recognition progress but the routing decision act, where needed, is Intake's.

## 4. Data and semantic model

- Source facts: original bytes + content hash per version, immutable (source class; 06 §7). Evidence instance: SLOT-4/5 executed JLL questionnaires — flattened scans with **zero text layer** (evidence; the Recreated artifact's reason for existing, 06 §8).
- Extracted values awaiting confirmation: OCR text/coordinates/confidence on the Recreated artifact; Structured-reading fields/tables with regions; ExtractedValue rows in `proposed` state (inferred class; rendered with dotted provenance underline, never bold-final).
- Confirmed values: ExtractedValue rows `confirmed/corrected` with confirmer stamp (owned elsewhere; summarized in Extracted Data mode).
- Deterministic outputs: content hashes; dedupe verdicts; version-chain integrity; the downstream-uses graph (computed from the lineage chain, 06 §1 — which normalized lines, metrics, package fields, and sealed packages consume this doc's values); stale-flag propagation on replacement.
- Agent proposals/drafts: classification (type + loan/period match + confidence); the Recreated and Structured artifacts themselves (labeled derivatives with per-region confidence); a recommendation inside the filing question ("looks like a corrected April T-12 — suggest replacement/correction"); conflict explanations.
- Human decisions: the typed filing answer; Make Current; tombstone/restore; suspect-word accept/correct; metadata corrections. Each writes an ActivityEvent with actor + evidence refs.
- Certified values: never created here; touched only as *impact* — a replacement whose values feed a certified package triggers void-on-change with the exact affected fields listed before the human confirms (02 §5).
- Versions/periods/packages: grain is `docId@vN`; documents link to loan(s) (a loan-agreement doc may serve the loan; a T-12 serves property+period); sealed packages pin `docId@vN` + artifact + hash.
- Evidence/provenance: DocView IS the provenance terminus — inbound deep links carry `{docId, version, artifact, page, region}` and light the region persistently (kit law 6).
- Permissions/read-only projections: Organization-scoped; Client-scoped in PMC mode (a preparer sees only their clients' documents; no "Client" column in owner mode — 02 §1); reviewers read-only; foreign-org docIds return honest 404 (the send-vertical pattern, snapshot §3).

Field groups and grain: organization → client → (property, loan via links) → document → version → artifact → region; reporting period and checklist item attach by link; requirement, test, metric, package section, certification record, and send record appear only as downstream-use refs.

## 5. State machine and exceptions

Document lifecycle (per `docId`): `arriving (Intake-owned) → processing → filed → [superseded-by-version | replaced-corrected] → referenced-by-sealed-package (locked)`; tombstoned versions remain listed and restorable except inside sealed periods.

| State | Trigger / actor | Behavior here | Reversibility · audit |
|---|---|---|---|
| empty | New org, no docs | Registers show the intake address + upload CTA ("file your first loan documents"), never a blank grid | — |
| awaiting documents | Checklist items expected, not arrived | Filed mode shows expected-but-missing rows as ghost links into Intake/Holding (summary only; Intake owns the chase) | n/a |
| loading/processing | Upload accepted; artifact generation running | Row shows per-artifact progress: Original stored+hashed first, Recreated/Structured pending; DocView opens with Source available and Readable/Extracted labeled "generating" | Engine/agent; quiet-logged |
| partial/incomplete | An artifact failed (e.g. OCR failure on a degraded scan) | Honest failure chip on the artifact, retry action; never a fake Readable (fail-closed, 04 §2.5) | Retryable; logged |
| extracted/unconfirmed | Structured reading produced `proposed` values | Doc appears in the **"Needs verification"** saved view (R2 research: Ironclad); Extracted Data mode shows per-field status | Cleared by confirmation on its owning surface |
| stale/superseded | A source doc replaced after downstream values were confirmed (03 §2) | Stale badge on the doc AND on every affected downstream figure with diff link; re-confirmation required | Engine flags; human re-confirms; logged |
| low confidence | OCR/classification confidence under threshold | Suspect-word overlay in Extracted mode (original pixels shown, guess never presented as truth — R2 research: Adobe); classification confidence beside the arrival | Correctable; versioned |
| conflict | Cross-source check fails (e.g. roll summary ≠ unit-row aggregation; T-12 total ≠ Σ lines) | Doc listed in Conflicts mode with both sources cited; resolution acts deep-link to the owning surface | Typed disposition; logged |
| failed tie-out | Downstream package tie-out fails (Review Room-owned) | Appears only as a downstream-uses flag with link — no tie-out UI here | n/a here |
| watch/shortfall/breach | Verdicts exist downstream | **Never rendered here** — no verdict chips on documents; the downstream-uses panel links to the surface where the verdict renders (identity guard; §7) | n/a |
| permission denied | Foreign org/client scope | Honest 404 on foreign-org docId; client-scoped listing in PMC mode | n/a |
| read-only | Reviewer role; sealed-period documents | All mutation affordances absent (not disabled-but-visible); export where permitted | n/a |
| blocked/gated | Re-upload matching an existing filed slot | Filing blocked pending the typed new-version-vs-replacement answer; a replacement touching confirmed/certified values additionally gates on the impact acknowledgment | Cancelable; logged |
| certified (void-on-change) | Confirmed replacement feeds a certified package | Void event fires, visible and reasoned; period returns to in-review (06 §6); the warning lists exact affected package fields BEFORE the act | Not silently reversible; void logged |
| sent/sealed | Doc version referenced by a SendRecord | Version pinned forever; delete/tombstone refused with reason; every artifact remains addressable (seal-not-wipe) | **Undo impossible by design** |
| recovery/undo | Wrong promotion or tombstone | Make Current re-promotes any version; tombstones restore; metadata edits versioned. Immutable forever: original bytes, hashes, sealed references, ActivityEvents | Logged |

## 6. Engine / Agent / Human / Gate / Quiet Log

| Concern | ENGINE (deterministic) | AGENT (prepares/proposes) | HUMAN (typed decisions) | GATE (interrupts) | QUIET LOG |
|---|---|---|---|---|---|
| Ingestion (one pipeline) | Content hash; dedupe by hash; org/client scoping | Classify type; match loan+period with confidence + evidence | Resolve ambiguous arrivals (in Intake) | Unroutable arrival near deadline (Intake-owned) | High-confidence auto-filed arrivals |
| Artifact generation | Store immutable Original + hash; validate artifact set completeness | Generate Recreated-searchable (OCR + coordinates + confidence) and Structured reading; label derivatives always | Suspect-word accept/correct (per region) | OCR failure blocks Readable honestly | Artifact completions with processing version |
| Versioning | Chain integrity; same-slot detection (by filed slot, never filename) | Recommend version-vs-replacement with evidence ("period matches filed v1; totals differ") | **The typed filing answer**; Make Current; tombstone/restore | Replacement touching confirmed/certified values → impact acknowledgment | Version filed/promoted/tombstoned |
| Downstream integrity | Compute the downstream-uses graph; propagate stale flags; void-on-change enforcement | Explain impact in plain terms ("this feeds 3 fields in the Q2 package") | Re-confirm affected values (on owning surfaces) | Void-on-change warning before a certified-impacting replacement | Stale flags raised/cleared; void events |
| Conflicts | Cross-source consistency checks (roll summary vs rows; statement totals vs Σ lines) | Draft conflict explanations with both sources cited | Disposition (on the owning surface) | A conflict blocking an imminent package readiness | Conflicts raised/resolved |
| Audit & usage | Access/reference integrity; sealed-ref pinning | Answer "where is this used?" via Ask with lit-row citations | — | — | Every view/export/change, actor-stamped |

Lane invariants honored: no shipping number is ever computed here; the agent never files past a human gate; agent presence is contextual — confidence beside the arrival, recommendation inside the filing question, explanation beside the impact — no chat chrome (04 §2).

## 7. Information hierarchy

1. Page/frame header: shell 48px header; book-scoped breadcrumb `Covenant / Documents` (altitude gate — no loan/period crumbs; 08 §2); mode switcher (the five register modes) in the header view-switcher slot.
2. Decision/status summary: count + CTA header per the Reports gold standard ("214 documents · 6 need verification · 2 conflicts" with CountBadge chips linking to those saved views).
3. Primary work region: the register grid (mode-dependent columns) — or, on `/documents/[docId]`, the DocView paper sheet.
4. Secondary context: DocView's right metadata rail (identity, versions, relationships, downstream uses, audit).
5. Evidence/proof: the document itself is the proof; lit regions render in-page; hash chips prove identity.
6. Actions: Upload (routes through the intake pipeline), per-row open/preview, per-version acts, filing-question dialog when triggered.
7. Activity/history: Audit-Usage mode (register) and the rail's audit section (DocView) — filtered views of the one ActivityEvent store.

Absent by design: covenant verdict chips (verdicts render only on covenant surfaces); charts (a document register out-encodes nothing with a chart — Reports gold standard: no charts); any second upload/ingestion UI distinct from the pipeline; resident-level outbound affordances (rent rolls anonymize outward; kit law 9).

## 8. Page anatomy and regions

### /documents — the register

| Region | Purpose · content | Persistence | Interaction · min size · collapse |
|---|---|---|---|
| Header band | Count + CTA ("Upload documents"), search `/`, filter cluster, saved views incl. "Needs verification" | persistent | Filters compose; saved views via the shared SavedView component |
| Mode tabs (header view switcher) | FILED · VERSIONS · EXTRACTED DATA · CONFLICTS · AUDIT-USAGE | persistent | One big pane, view-switching (ruled pane model); mode is URL-addressable (`/documents?view=`) |
| Register grid | Mode-dependent columns (below) | persistent | Uniform row heights, open-not-boxed; row → DocView; hover → peek affordance; ≥720px work window |
| Upload tray | Batch progress: per-file recognition results (type, matched loan+period, confidence, proposed filing) — a projection of Intake's arrivals store scoped to this batch | transient | Auto-filed rows link to the filed doc; exceptions carry "Resolve in Intake"; dismissible, state survives in Intake |
| Canvas panel (peek) | Quick document preview + Ask | summonable, floating | The ruled canvas slot — never load-bearing; Esc dismisses |

Mode columns — FILED: name · type · loan(s) · period · version chip (vN) · arrival channel (intake email / upload / bulk) · filed date · filed-by. VERSIONS: canonical slot · version stack summary (v3 current, v1 tombstoned) · last change · changed-by. EXTRACTED DATA: document · extracted records count · status rollup (proposed n / confirmed m) · owning surface link. CONFLICTS: document · conflict class · both sources · age · owning-surface link. AUDIT-USAGE: document · downstream consumers count · sealed refs count · last access · last change.

### /documents/[docId] — DocView

| Region | Purpose · content | Persistence | Interaction · min size · collapse |
|---|---|---|---|
| Sheet header | Doc name, type, loan/period chips, version selector, derivative label when applicable, hash chip (Source) | persistent | Version selector opens the stack; breadcrumb returns to register |
| Mode strip | **Readable \| Source \| Extracted \| Compare** | persistent | Readable = Recreated-searchable, permanently labeled "Recreated · searchable — derivative"; Source = original bytes + hash; Extracted = structured fields/tables with regions + confidence + suspect words; Compare = Original⇄Recreated toggle/overlay OR Version⇄Version 50/50 split |
| Paper sheet (work window 1) | The porcelain document render; lit regions; text selection (Readable) feeds HighlightToAsk | persistent | ≥640px; page thumbnails/TOC appear as the pane widens (the widening-pane TOC gasp, snapshot §5) |
| Metadata rail | Identity (docId, hash, artifacts) · Versions (Box-style stack) · Relationships (loan / riders / amendments / period filings / checklist item — Ironclad contract-family adaptation) · **Downstream uses** (field-level consumers incl. sealed-package refs with locks) · Audit | pinned | 280–360px; below minimum it becomes labeled summonable tabs (no silent compression) |
| Filing-question dialog | The typed act: "New version of the filed document, or replacement/correction?" + agent recommendation + downstream impact preview | modal | Justified as modal: a typed act with legal weight when confirmed/certified values are touched (kit law 5) |

Pane justification: DocView is one big pane (the sheet) + a pinned inspector rail — reading and identity/impact must be co-visible (§9). The ONLY two-work-window split is Version⇄Version compare (a genuine side-by-side decision). Original⇄Recreated is a toggle/overlay, not a permanent split, because visible pixels are materially identical (06 §8).

## 9. Co-visibility matrix

| Datum/surface A | Datum/surface B | Must be simultaneous? | User decision enabled | Default topology | Fallback |
|---|---|---:|---|---|---|
| Register row | Document preview | No | "Is this the doc I want?" | Canvas-panel peek | Navigate to DocView |
| DocView sheet | Metadata rail (identity/uses) | Yes | "Trust this doc? What does it feed?" | Pinned rail beside sheet | Rail → labeled overlay tabs under min width |
| Original | Recreated-searchable | No (pixels materially identical) | "Is the derivative faithful?" | Compare toggle/overlay with difference flash | Side-by-side never persists (ruled, 06 §8) |
| Version N | Version N−1 | Yes, during compare | "What changed between filings?" | 50/50 synchronized two-window split | Stacked labeled tabs below 1280px total (08 §4) |
| Extracted field | Its source region | Yes | "Does the value match the page?" | Field list lights region on the sheet (lit-region, persistent) | Field row carries page ref; tap jumps |
| Filing question | Downstream impact list | Yes | The version-vs-replacement act | Impact preview inside the dialog | Never separated — the act is illegal without its consequences visible |
| Doc audit trail | The sheet | No | "Who touched this?" | Rail audit section | Audit-Usage register mode |

## 10. Layouts and viewport behavior

- **1440px** — Register: rail 240px + one full-width grid (~1200px). DocView: sheet ~880px + rail 320px. Version compare: two 590px windows (rail auto-collapses to overlay tabs during compare). Canvas peek 420px floating.
- **1728px** — DocView: sheet 1024px + rail 360px + comfortable gutters; register gains columns (filed-by, channel visible by default). Compare: 2×~700px with rail restored as overlay.
- **2048px** — Sheet caps at 1120px (line-length ceiling for readable documents); surplus goes to gutters and a wider rail (360px) + page-thumbnail strip on the sheet's left (the widening-pane TOC). Compare: 2×~840px plus pinned rail.
- **Narrow/compact fallback** — Shell rail collapses to icons; register drops to name/type/status columns with detail-on-row-expand; DocView rail becomes bottom-sheet-style labeled tabs; Compare refuses split and offers stacked tabs (no silent compression, kit law 5).
- Focus behavior: `F` on the sheet hides the rail (summonable back); Esc restores before it ever navigates.
- Proof/source behavior: inbound lit-region links scroll the sheet to the region and light it persistently; the rail stays pinned so identity is never lost while proving.
- Minimum viable dimensions: register 720px; DocView 640px sheet + 280px rail (else rail overlays); compare windows ≥560px each or the split refuses (08 §4).

## 11. Components and exact anatomy

| Component | Reuse / NEW | Anatomy and notes |
|---|---|---|
| CovenantShell, breadcrumb, rail | REUSE `CovenantShell.tsx` | Deep routes mount the shell with initial view (08 §1) |
| DocumentRegister | ADJUST existing Documents views | Built on the Reports gold-standard list pattern: count+CTA header, filter cluster, no charts; uniform row heights; mode-dependent column sets |
| SavedView control | REUSE shared SavedView mechanism (02 §2) | Ships with the governed default **"Needs verification"** (R2 research: Ironclad) plus "Conflicts open", "Filed this period" |
| CountBadge | REUSE shared CountBadge (snapshot §4) | Header chips + rail badge |
| UploadTray | NEW | Dropzone + batch list; per-file: name, hash progress, recognized type, matched loan+period, confidence, proposed filing; reuses Intake's arrival-card internals; "Resolve in Intake" link on exceptions |
| DocViewSheet | ADJUST existing porcelain sheet | Page canvas, region highlight layer, selection layer (Readable), thumbnail/TOC strip at width |
| ArtifactModeStrip | NEW | Four segmented modes; Readable carries the permanent "Recreated · searchable — derivative" label chip; Source carries the mono hash chip; Extracted shows confidence summary; Compare opens the compare chooser (Original⇄Recreated / version pair) |
| VersionStack | NEW | Box-style list: per-version row (vN, date, filed-by, hash, size) with Preview · Download · **Make Current** · tombstone; tombstoned rows visible as unavailable with Restore (R2 research: Box) |
| FilingQuestionDialog | NEW (modal — typed act) | The explicit question, agent recommendation with evidence, downstream-impact preview (affected fields, certification warning), typed confirm; never triggered by filename inference (R2 research: Box, rejected) |
| MetadataRail | NEW | Five collapsible sections: Identity · Versions · Relationships (loan ↔ riders ↔ amendments ↔ period filings; Ironclad contract-family adaptation) · Downstream uses · Audit |
| DownstreamUsesPanel | NEW | Rows: consuming field → surface → period/package → status (live / stale / **sealed** with lock icon); sealed rows link to the sealed period record; live rows open with the figure's evidence lit |
| SuspectWordOverlay | NEW | Extracted mode: suspect regions boxed; selected suspect shows original pixels + "Recognized as {guess}" + Accept/Correct/Unreadable (R2 research: Adobe; ABBYY) |
| ExtractedFieldsList | NEW | Fields/tables with per-field confidence + region jump; read-only status here; "Confirm on {surface}" deep link (ownership boundary) |
| Evidence lit-region contract | REUSE the lit-row contract (A6; `cross-cutting/provenance-lit-row-trace.md`) | DocView implements the terminus: `{docId, version, artifact, page, region}` → persistent highlight |
| CommandPalette / SearchPalette | REUSE | `G D` place; object results with loan/period identity chips |
| AskPanel + HighlightToAsk | REUSE | Selection in Readable mode travels with provenance; answers cite lit-region refs |
| Empty/error/recovery objects | NEW (shared pattern) | Empty register CTA; artifact-failure chip with retry; honest 404 |

## 12. Interaction specification

- Selection: single row select (arrow keys); shift-range in register for bulk metadata edits; text selection in Readable mode only (Source is pixels; selection there marquee-selects a region for Ask).
- Hover: row hover reveals peek + overflow acts; extracted-field hover pre-lights its region faintly (committed light on click); version-row hover shows hash tooltip.
- Focus: visible 2px accent (#7189FF) focus ring on rows, tabs, mode strip, rail sections.
- Keyboard: `G D` Documents; `/` filter; `↑/↓` rows, `Enter` open; `1/2/3/4` DocView modes (Readable/Source/Extracted/Compare); `[`/`]` previous/next version; `.` toggles the Original⇄Recreated overlay while in Compare; `n/p` next/previous suspect word in Extracted; `F` focus sheet; `Esc` closes overlay → rail-tab → peek → then navigates back (in that order).
- Editing and validation: metadata edits inline in the rail (typed fields, validated against loan/period existence); suspect-word corrections per-region; every edit versioned.
- Bulk action: bulk upload (through the pipeline); bulk metadata assignment on selected register rows (loan/period tags) — never bulk version decisions (each filing question is per-document by design).
- Undo/recovery: Make Current re-promotes; tombstone Restore; metadata edit history in the rail. No undo: original bytes, hashes, sealed refs, ActivityEvents.
- Sorting/filtering: every column sortable; filters (type, loan, period, channel, status, client in PMC mode) compose into saved views.
- Drill-down and return path: register → DocView → (lit region) → back preserves register scroll + filters; inbound deep links land in DocView with a breadcrumb back to `Covenant / Documents`.
- Source-linked selection: click an extracted field → its region lights and stays lit; click a downstream-use row → the consuming surface opens with THAT figure's evidence lit (the same contract, run forward).
- Save/persistence: filters/views persist per user; mode + version + page survive in the URL.
- Collaboration/commenting: none in v1 — questions route through Ask; disputes about values route to the owning confirmation surface.

## 13. Visual craft direction

- Typography: register rows 13px/20 with 15px section heads; DocView sheet header 16px medium; the document render is the document's own face. Metadata labels 11px caps-tracked in a mid-gray rung; values 13px in the darkest ink rung of the ten-rung ladder (adopted 2026-08-07, snapshot §4).
- Financial numbers and hashes: Geist Mono, `tabular-nums slashed-zero` (Review-9, colors-only ruling keeps Geist Mono); hash chips truncate middle (`a3f2…9c1d`) with copy-on-click.
- Spacing rhythm: 8px base grid; register rows 40px uniform; rail sections 16px padded, 24px between.
- Density: register comfortable-dense (the Reports standard); DocView generous — the paper sheet breathes, chrome recedes.
- Open ground vs earned boundaries: open-not-boxed tables — hairline row dividers from the ladder's hairline rung only; the sheet earns its boundary (paper edge + subtle elevation over the frame ground); the rail separates by a single hairline, no card boxes.
- Semantic color: accent family only — selection/links #7189FF, hover #8EA1FF, active #6078F4; lit regions fill with the accent tint #A9B5FF at low opacity with a #7189FF edge (consuming the Evidence lit-trace treatment, the one paper-accent render live today, snapshot §4). Status chips use gray rungs + accent; NO verdict colors on this surface.
- Derivative honesty in type: the "Recreated · searchable — derivative" label is a permanent quiet chip (mid-gray rung, 11px) on the sheet header in Readable mode — visible without shouting, never omissible.
- Certified-sheet treatment: appears here ONLY when the viewed artifact is itself a certified package render (opened from a sealed reference) — that render arrives already carrying the ruled whole-sheet blue-violet treatment (#E7EBF8 panel etc.) from the Certificate surface; Documents adds nothing to it and applies none of those hexes to ordinary documents.
- Focus/selected/hover: selected row = accent-tint wash + 2px left accent edge; hover = one gray rung lift; focus ring accent.
- Charts: none (doctrine: a chart must out-encode adjacent text or not ship — counts and lists win here).
- Motion: 120ms ease on mode switches and rail collapse; lit-region highlight fades in 150ms and persists; reduced-motion honors `prefers-reduced-motion` (instant states, no flash on compare toggle — a static difference outline instead).
- Long-session ergonomics: the sheet's line-length cap (1120px), warm-neutral frame rungs, and zero idle animation keep audit sessions calm.

## 14. Benchmark research and synthesis

| Product | Limited role | Official evidence | Exact pattern to take | Covenant adaptation | What to reject | Why this reference beats alternatives |
|---|---|---|---|---|---|---|
| Ironclad | Record = document + properties; verification as repository state | https://support.ironcladapp.com/hc/en-us/articles/12277440413207-Manage-Records · https://support.ironcladapp.com/hc/en-us/articles/31344994944023-The-Repository-Dashboard-Views | Records carry typed properties + related records ("contract families"); default **"Needs verification"** view collects records with unverified AI predictions; Views = saved filters + columns | Document properties = filing metadata; families = loan ↔ riders ↔ amendments ↔ period filings; the five register modes are governed saved views | Emailed-CSV extraction review detached from the source page | The only benchmark treating verification as a first-class repository state, not a side workflow |
| Box | Version-stack mechanics; metadata beside preview | https://support.box.com/hc/en-us/articles/360043697054-Accessing-Version-History · https://support.box.com/hc/en-us/articles/360044196173-Using-Metadata | One canonical identity; per-version Preview/Download/**Make Current**; tombstoned deletes restorable; Details + Metadata panels beside preview | VersionStack + MetadataRail verbatim in spirit; tombstones never silent | **Same-filename-implies-new-version inference** — Covenant asks the explicit typed question instead (R2) | The cleanest documented promotion + tombstone model in mainstream file infrastructure |
| ABBYY (FineReader/Vantage) | The canonical searchable-derivative model | https://support.abbyy.com/hc/en-us/articles/19098939370515 | "Text under the page image": scan pixels on top, recognized text beneath — searchable yet visually the original | Exactly the Recreated · searchable artifact; ABBYY's own framing justifies the permanent derivative label | Character-by-character verification loops | The derivative's honesty contract comes straight from ABBYY's documented render order |
| Adobe Acrobat | The "suspect" concept | https://helpx.adobe.com/acrobat/desktop/create-documents/scan-documents-to-pdfs/fix-scanned-text.html | Uncertain OCR keeps the original bitmap visible; "Recognized as" + Accept per word; suspects are a designed-in state | SuspectWordOverlay; third verb "Unreadable" pins + escalates instead of forcing a guess | The one-word-at-a-time modal loop with no queue | Decades-old proof that low-confidence honesty is a shippable UX, not an apology |
| Hebbia | Citation click-through to the exact page | https://www.hebbia.com/blog/introducing-matrix-the-interface-to-agi | Every extracted cell deep-links to page/paragraph; citation previews peek inline before jumping | Extracted-records refs and downstream-use rows deep-link into DocView at `{page, region}` with hover peek | Free-prose answer cells as the stored record | The strongest citation-first posture in document AI — matches the lit-row law exactly |
| Instabase | Grounded correction; validation-first ordering | https://docs.instabase.com/automate/validating-documents | Corrections by typing OR selecting the source area (re-grounding); auto-revalidation; "show source for all fields" | Extracted mode's region jumps; cell-level lighting deliberately exceeds Instabase's documented whole-table limit | Table-level-only source lighting | Documents its own limitation, telling us precisely where to go further |
| Ocrolus | Cross-document consistency in lending | https://www.ocrolus.com/press/ocrolus-introduces-cross-source-validation-to-automate-corroboration-of-borrower-information-accuracy/ | Cross Source Validation corroborates values across a file's documents; inconsistencies surface as findings | The Conflicts mode's checks: roll summary vs rows, statement totals vs Σ lines, doc vs doc | Vendor-managed invisible human-in-the-loop | The one benchmark doing this in lending document reality, not generic OCR |
| DocuSign Navigator | Accept-or-edit attributes on a repository record | https://support.docusign.com/s/document-item?bundleId=pqz1702943441912&topicId=uwk1702945852405.html | Details pane of AI-extracted attributes; accepted-only data enters reporting | Extracted Data mode's status rollup; acts stay on the owning confirmation surface | Attribute verification without page-level source lighting | Shows the record-bound details pane; its grounding gap is what Instabase/Hebbia fix |
| Google Document AI HITL | ANTI-PATTERN | https://docs.cloud.google.com/document-ai/docs/deprecation | A review console detached from the system of record — deprecated by its own platform | Nothing; boundary marker | Bolting review onto an external labeling console | Proof that document review must live inside the product that owns the records |

Synthesis: the register is Ironclad's record model wearing the Reports gold-standard list clothes; versioning is Box hardened by replacing filename inference with a typed act; the artifact set is ABBYY's honest derivative + Adobe's suspect contract; every extracted value is Hebbia-grade citation-linked; conflicts are Ocrolus's cross-source checks re-homed to the borrower's own analyst. What makes it original and domain-correct: none of those products knows that a document's values flow into a *certified, sealed lender package* — the DownstreamUsesPanel, void-on-change warning inside the filing question, and sealed-version pinning are Covenant's own, derived from the lineage law (06 §1) and seal-not-wipe (02 §5), and they are exactly what borrower-side lender reporting needs that a contract repository or file locker cannot provide.

## 15. Domain references

Terminology and expected-data references: the servicer's own document demands in the evidence kit — JLL quarterly Property Questionnaire and Annual Certification (evidence: SLOT-4/5), Fannie Mae Form 6001.NR §8.02(b) deliverables with riders 6220/6241 (evidence: SLOT-3) — define what "the filed canon" must hold per period: statements, rent schedules with named fields, questionnaires, certifications, and rider-driven one-offs (the ENERGY STAR report exists only because this loan carries Form 6241 — per-loan, never templated; 06 §3). Ocrolus (above) doubles as the lending-domain document-AI reference. Generic servicer upload portals are cadence references only. **Domain authority does not equal visual authority** — none of these dictates a pixel; and covenant semantics come from the loan documents and Terry, never from any referenced product (kit law 8).

## 16. Accessibility, performance, and safety

- WCAG: AA contrast for all text on the gray ladder; the derivative label and hash chips meet 4.5:1; lit-region highlight never relies on color alone (edge + optional outline pattern).
- Keyboard completeness: every act in §12 keyboard-reachable; the filing dialog traps focus; mode strip is a proper `radiogroup`.
- Screen-reader semantics: register as `grid` with row/col headers; mode strip announces "Readable — recreated searchable derivative, not the original"; lit regions announce page + region context; version rows announce current/tombstoned state.
- Large data: register virtualized past ~200 rows (uniform heights make this cheap); DocView renders pages lazily with prefetch; hashes computed server-side.
- Loading/latency: per-artifact progress states (never a spinner over a fake sheet); optimistic UI only for metadata edits, never for filing acts.
- Destructive confirmation: tombstone requires typed confirm; sealed-period deletion refused outright with the sealing reason.
- Certify/send safety: the void-on-change pathway is honest and loud — the impact list renders before the act; sealed packages pin exact `docId@vN` + hash so what was sent is provable forever (approved bytes, 02 §6).
- Source immutability: Original bytes write-once + content hash; derivatives regenerate only as new processing versions, never in place.
- Auditability: every act writes an identity-stamped ActivityEvent (actor, evidence refs, loan/period, outcome) to the one append-only store.

## 17. Acceptance tests and fixtures

Fixtures: the Calloway Park (CAL) evidence kit — SLOT-3 loan agreement (6001.NR + riders 6220/6241), SLOT-1 T-12 (FYE-2018: Total OpEx $1,686,050 · NOI $1,218,877 · Net Income $460,159), SLOT-2 rent roll (322 rows → 301 units), SLOT-4/5 executed JLL questionnaires (flattened scans, zero text layer); plus the Bexley canon book.

1. **Four artifacts from a dead scan.** Upload the SLOT-4 executed questionnaire → Original stored + hashed; Recreated-searchable generated; Readable mode finds "management fee" by search while Source mode text-search honestly returns nothing (zero text layer, evidence); the "Recreated · searchable — derivative" chip is present in Readable at every viewport and cannot be dismissed.
2. **The typed filing question.** Upload a second CAL T-12 with an identical filename → NO automatic versioning; FilingQuestionDialog renders both options + agent recommendation + downstream impact; choosing "new version" yields v2 with v1 intact; choosing "replacement/correction" marks v1 superseded-with-reason; both paths write ActivityEvents. Filename inference provably absent (R2: Box reject).
3. **Void-on-change.** With Bexley Q2 certified (canon), replace a document feeding a package field → the dialog lists the exact affected fields; on confirm, the certification voids visibly, the period returns to in-review, and the void event appears in Audit-Usage.
4. **Needs verification.** SLOT-1 extraction leaves proposed values → the doc appears in the "Needs verification" saved view; confirming on Extraction & Confirmation clears it without any Documents-side mutation.
5. **Downstream uses + sealed pin.** The SLOT-1 T-12's panel lists the annual-statement fields and the tie-out consumer (cash-flow Net Income = $460,159, evidence) and, after send, a sealed-package row with lock; the sealed row still resolves after v2 is filed — it pins v1's bytes and hash.
6. **Lit-region terminus.** From a certified figure's provenance chain, the deep link opens DocView at the exact page and region, lit persistently — verified against the U1-F1 wrong-row defect class (the region must match the chain's region ref exactly, 3-of-3 renderers).
7. **One pipeline.** Bulk-upload 7 mixed files on `/documents` → 7 arrivals exist in Intake's store; high-confidence files auto-file (quiet-logged); one ambiguous file shows "Resolve in Intake" and is absent from Filed until resolved; no code path writes a Document except the pipeline (assert at the service layer).
8. **Version compare.** v1⇄v2 opens the 50/50 synchronized split at 1440 (≥560px each); at 1100px the split refuses and offers stacked labeled tabs; Original⇄Recreated never opens as a persistent split.
9. **Keyboard.** `G D` → `/` → type "T-12" → `Enter` opens DocView; `1..4` switch modes; `[`/`]` walk versions; `n` steps suspects; `Esc` order: overlay → rail tab → peek → navigate.
10. **Sealed immutability.** Attempt tombstone on a sent-period document → refusal names the SendRecord; no force path exists (undo impossible by design).
11. **Tenancy.** PMC preparer scoped to Client A requests a Client B docId → honest 404; reviewer role sees zero mutation affordances (absent, not disabled).
12. **Accessibility.** Axe-clean on both routes; mode strip radiogroup announced; contrast spot-checks on derivative chip + hash chip; reduced-motion disables the compare flash.
13. **Viewports.** Screenshot fixtures at 1440/1728/2048/narrow per §10 ratios; no horizontal scroll of the page body; rail collapse is labeled, never silent.
14. **Benchmark challenger review.** Walk Ironclad/Box/Hebbia patterns against the built surface; any regression from a "take" mechanic is a defect ticket.

## 18. Build plan

- Dependencies: permanent document storage + content-hash service (gap 2); the intake pipeline (gap 1 — classifier exists unwired: `intake-classify.ts`); tenancy FKs org→client→loan (gap 3); the lineage graph for downstream-uses (06 §1); the ActivityEvent store; the orchestration spine for void-on-change (gap 5).
- Foundation work: document store schema (document, version, artifact, filing-metadata, sealed-ref pin) with write-once Original enforcement; artifact pipeline (OCR → Recreated with coordinates/confidence → Structured); register reads move from fixture arrays to the store.
- Components first: VersionStack + FilingQuestionDialog (the safety core), ArtifactModeStrip + derivative label, MetadataRail, DownstreamUsesPanel, UploadTray-as-Intake-projection.
- Vertical slice (the send-vertical pattern): one route, one loan+period — upload the CAL SLOT-4 scan → pipeline files it under Bexley's open period → `/documents/[docId]` renders all four artifacts from real storage → its extracted questionnaire fields link into the period's evidence. This single seam retires "DocView orphaned" (snapshot §3) and makes the dump→recreated-searchable gasp real instead of a tag on a fixture card (gap 2).
- Migration from fixture: Documents fixture arrays and the `book.ts` document stubs retire behind a read-path flag; Intake/Composer document lists become checklist-filtered links to the same store (07 §2 duplication resolution).
- Rollout/feature flag: `covenant.documents.store` gating store-backed reads; `covenant.documents.upload` gating the tray; DocView deep links ship first (read-only) since they carry no write risk.
- Proof artifacts: before/after renders at the three viewports; a screen capture of the SLOT-4 scan becoming searchable; the one-pipeline assertion test green; lit-region terminus demo against the U1-F1 contract.
- Final gate: `ADJUST` — the register spine and paper sheet stand; the importer, version safety, and downstream-uses machinery are the work. Re-grade after the vertical slice lands.
