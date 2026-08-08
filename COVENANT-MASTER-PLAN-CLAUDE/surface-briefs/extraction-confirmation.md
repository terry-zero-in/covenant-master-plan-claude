# EXTRACTION & CONFIRMATION
## First-time loan setup (`/loans/[loanId]/setup`) + period-over-period exception reuse

## 1. Identity and verdict

- **Product layer:** reporting spine — workflow steps 4–5 (EXTRACTION → CONFIRMATION) and the completion of Gate 1 (03 §1, §2).
- **Route/address:** NEW `(covenant)` route `/loans/[loanId]/setup` in `src/app/(covenant)/loans/[loanId]/setup/` mounting `CovenantShell` (08 §1); plus period-scoped exception cards rendered inside `/covenant/[loanId]/[period]/actuals` and `/covenant/[loanId]/[period]/review` (same card components, exception projection only). Components land in `src/components/covenant/extraction/`; engine calls into `src/lib/covenant/` (loan-doc extraction + manual authoring + fail-closed dispatcher, all built per snapshot §3).
- **Current build state:** `ABSENT` as a surface. Substrate exists: the loan-doc extraction library is built and unwired; a hand-authored `deal_config` stands in for confirmed records today (snapshot §6 gap 2).
- **Existing-surface verdict:** `UNBUILT`.
- **Research tier:** C (deep benchmark synthesis, R2).
- **Primary users/roles:** owner and PMC preparer (both hold confirm rights by default — 02 §4); reviewer role is read-only.
- **Frequency and session duration:**
  - once per loan at onboarding — a 30–90 minute full pass over the executed agreement + riders (the Enter-walk is designed for exactly this session);
  - thereafter near-zero: amendments re-open affected records only, and per-period exceptions are seconds each inside Actuals/Review;
  - no deadline pressure at setup itself, but everything downstream inherits its accuracy — the pressure is correctness, not clock.
- **Error cost:** one tier above the terminal error. A mis-confirmed requirement propagates into every period's checklist, every test definition, and every composed package for the life of the loan. A wrongly confirmed due-rule makes packages late; a wrongly confirmed threshold makes every verdict wrong before certification is even reachable.
- **Success criterion:**
  - exactly one full human confirmation per loan (Gate 1), completed as a keyboard flow, not a form slog;
  - every confirmed record carries a verbatim definition quote + source region (or an explicit no-source declaration for self-authored monitored thresholds);
  - zero re-confirmation prompts across consecutive periods with unchanged facts (03 §3 acceptance test);
  - a rider-carrying loan's checklist differs from its base-form sibling exactly where the rider says so (evidence: Form 6241 adds the annual ENERGY STAR report — 06 §3);
  - an unrecognized document never produces a schedule — it produces a blocked card and a manual-authoring path.

## 2. User job and decisions

- **Primary job-to-be-done:** "Turn this loan's executed documents into the confirmed requirement schedule the whole product runs on — and never be asked about it again."
- **Decisions made here:**
  - per proposed record — Confirm as extracted / Correct (value, cadence, due-rule, definition span; basis is never a casual toggle — see §5) / Mark unreadable (pin + escalate) / Author manually (for fail-closed blocks and self-watched monitored thresholds);
  - per loan-term field — confirm/correct with source region;
  - per amendment — accept the re-opened diff or correct it;
  - per conflict — pick the governing source, with reason.
- **Questions the surface must answer in scan order:**
  1. How far through setup am I ("14 of 17 confirmed")?
  2. Which record am I deciding now, and what exactly does the agreement say — verbatim, on the page, lit?
  3. What kind of obligation is this, and on what basis (covenanted | monitored)?
  4. What does confirming/correcting this move downstream?
  5. What is blocked, and why — honestly?
- **What the user should not have to decide here:** verdicts (none render here); package composition; which periods exist (the engine materializes cadence occurrences after confirmation — 03 §5); anything already confirmed (ask-once); anything a remembered MemoryEntry already answers (rendered beside the field with "learned {date} from {who}" — 03 §3).
- **Entry paths:**
  - Loan Detail → "Complete setup" (loans in `setup needed` state, per the onboarding arc);
  - Home your-move "Extraction exceptions" group and Inbox extraction rows (deep links carrying loan identity);
  - onboarding step 2, the magic path — the first loan's documents produce this surface as the first extraction experience;
  - ⌘K palette ("setup {loan}");
  - amendment re-open notification → deep link to the affected records.
- **Exit paths:**
  - setup complete → Loan Detail SCHEDULE tab (the read-only confirmed schedule);
  - first period opens with its materialized checklist (Intake/Holding);
  - per-period exception cards return to their host surface (Actuals/Review) on disposition, scroll position kept.
- **Completion/advancement conditions:** all proposed records dispositioned (confirmed/corrected/authored; unreadable records block completion until resolved or explicitly waived-with-reason by a role holding confirm rights) → RequirementRecord set flips confirmed → checklists materialize → periods can open (02 §2 ChecklistItem is derived from RequirementRecords). Period-over-period: Gate 1 completion for a period requires zero open extraction exceptions (03 §2).

## 3. Object and ownership model

| Object/action/status | Owned here? | Summary/link elsewhere | Address/identity | Notes |
|---|---:|---|---|---|
| RequirementRecord (create/confirm/correct/author/amend) | YES | Loan Detail renders confirmed schedule read-only; Review cites definitions beside tests; Holding derives checklist | `reqId` per loan | The one owner (02 §3). `basis: covenanted \| monitored` is structural, never inferred (C-9; 05 §1.2) |
| ExtractedValue (loan terms: UPB, rate structure, IO→am schedule, maturity, program+form+riders) | YES (confirmation acts) | Loan master record renders on Loan Detail (its owner); Actuals consumes debt-service inputs | `valId` | Confirmation is a typed act; corrections version with downstream-impact notes (02 §5) |
| Requirement version history (amendments) | YES | Loan Detail SCHEDULE shows current version + "amended {date}" chip linking here | `reqId` + version | Amendment re-opens affected records only (02 §5) |
| Blocked extraction state (unknown form/rider) | YES | Home your-move counts it; quiet log records the block | per docId + region | Fail-closed: never a guessed schedule (04 §2.5) |
| Document + four artifacts | NO — Documents owns | Rendered read-only in the source pane; "filed {date} via intake" link | `docId` + version | Original immutable; viewer prefers Recreated-searchable for scans (06 §8) |
| ChecklistItem | NO — Intake/Holding owns | Downstream-impact strip names items that will materialize/retire | `(loanId, period, itemId)` | Derived from confirmed records |
| TestResult / Metric | NO — Actuals owns | Impact strip names tests a correction moves | — | No verdict chips render on this surface |
| MemoryEntry | NO — agent layer owns | Rendered inline beside the field it pre-fills, inspect/correct in place | `memId` | The agent never re-asks (law 10) |
| Period state (Gate 1 transition) | NO — orchestration spine owns | This surface completes the gate; the period header reflects it | `(loanId, period)` | No surface mutates status directly (02 §3) |

**No-double-homing boundary:** this surface owns requirement authoring/confirmation and term-value confirmation acts — nothing else. Documents owns the filed canon it merely renders; Intake/Holding owns the checklist it merely previews in the impact strip; Actuals owns every computation it merely names as affected. Every other appearance of a RequirementRecord in the app is a read-only summary linking here.

## 4. Data and semantic model

- **Source facts:** the filed loan documents — executed agreement, riders, amendments (evidence: SLOT-3, Fannie Mae Form 6001.NR + Form 6220 + Form 6241, executed 2017). Original bytes immutable + hash; flattened scans render via the Recreated-searchable derivative, always labeled (06 §8).
- **Extracted values awaiting confirmation:** proposed RequirementRecords per the 05 §1.2 schema —
  - kind (financial-covenant / operational-covenant / reporting-obligation / monitored-threshold / one-time / conditional);
  - **basis: covenanted | monitored — a stored structural field, never derived at render time (C-9)**;
  - title + `definition_text` verbatim + `definition_source_region` (doc/page/region);
  - optional test {metric_ref, comparator, threshold, calculation_notes_source_region} — present only when the agreement defines one;
  - optional watch band (authoring default floor × 1.05 — [VERIFY against library; never hard-code], 05 §1.2);
  - cadence + due-rule (e.g. "Q+45d", "FYE+120d" — evidence class);
  - optional deliverable {doc_type, named_fields[]};
  - confidence 0–1;
  plus proposed loan-term ExtractedValues, each with source region + confidence.
- **Confirmed values:** the same records with `status: confirmed (who, when)`; confirmed term values feeding the loan master.
- **Deterministic outputs:** the fail-closed dispatcher's form/rider recognition result; schema validation; conflict detection across documents; the downstream-impact computation (which checklist items/tests/sections a change moves).
- **Agent proposals/drafts:** the extraction pass itself (every proposal carries a source region — 04 §1 row 3); re-open proposals on amendment; conflict explanations.
- **Human decisions:** Confirm / Correct / Mark unreadable / Author manually / conflict pick-with-reason / amendment acceptance / unreadable waive-with-reason.
- **Certified values:** none originate here; confirmed records feed packages whose certification is downstream. A correction touching a certified package field triggers void-on-change (visible, never silent — 03 §2).
- **Versions/periods/packages:** records version by amendment; sealed periods pin the requirement version they consumed (seal-not-wipe: a sealed period forever cites the version in force when it certified).
- **Evidence/provenance:** the full 06 §1 chain per record: extraction logic + extractor version → source region → document identity/version/artifact → loan identity → confirmation/correction history with downstream impact.
- **Permissions/read-only projections:** reviewer role sees cards with states but disabled actions ("confirm rights required"); PMC preparers scoped per client (02 §1).
- **Grain:** organization → client → loan → document → requirement → version. This surface is loan-grain:
  - property enters only as pledge context in the header (a cross-collateralized loan's setup shows its pledged properties as identity, not as work);
  - sponsor enters only where a guarantor-statement obligation names one (evidence class: 6001.NR §8.02(b)(2) guarantor statements — 02 §2);
  - reporting periods appear only in the exception projection; packages, sections, certification records, and send records never render here (downstream identities the impact strip may *name*, never show).

## 5. State machine and exceptions

**Per-record states** (the core machine; Rossum's blue-proposed → green-confirmed adapted to ruled tokens — R2: Rossum):

| State | Trigger | Actor | Prerequisites | Reversible? | Audit event | Failure behavior |
|---|---|---|---|---|---|---|
| proposed | extraction pass completes | Agent | recognized form/rider; region located | — | quiet log: proposal batch | low confidence orders the card earlier, never hides it |
| confirmed | Confirm (typed) or Enter-advance | Human | source region visible at act time | via correction (versioned), never deletion | ConfirmationAct {who, when, valId/reqId} | — |
| corrected | Correct → edit → save | Human | source stays lit during edit; re-grounding allowed (draw/select a new region — R2: Instabase) | prior value kept in history | CorrectionAct + downstream-impact note | validation failure blocks save with field-level reason |
| unreadable | Mark unreadable | Human | region pinned | yes — resolves on better artifact/version | UnreadableFlag | blocks setup completion until resolved or waived-with-reason |
| blocked (fail-closed) | dispatcher meets unknown form/rider or unlocatable definition | Engine | — | resolves via manual authoring or library update | BlockEvent naming what is missing | NEVER a guessed schedule; the block renders as a card (04 §2.5) |
| authored | manual authoring saved | Human | source region pinned OR explicit "no source — self-watched monitored threshold" declaration | versioned like any record | AuthoringAct | schema validation fail-closed |
| amended-reopened | loan modification filed | Engine flags; Agent proposes diff | amendment document filed in Documents | confirm/correct closes it | AmendmentReopen {affected reqIds} | ONLY affected records re-open (02 §5); everything else stays confirmed |
| stale | source document replaced after confirmation | Engine | replacement filed via Documents | re-confirm clears | StaleFlag with diff link | affected records badge stale everywhere they render (03 §2) |
| conflict | two sources disagree on one value (e.g. UPB in agreement vs servicer statement) | Engine detects; Human picks | both chains renderable | pick is versioned | ConflictResolution {chosen source, reason} | blocks confirmation of the field until picked (R2: Ocrolus cross-source validation) |

**Surface-level states (the template checklist, each honestly rendered):**

- **empty** — no loan documents filed: "File the executed loan agreement and riders to begin — documents arrive via Intake or upload," linking to `/intake`. Never a dead end.
- **awaiting-documents** — agreement filed, a named rider referenced but missing: the missing rider renders as a holding row with chase state (a summary of Intake's checklist, linked — no double-homing).
- **loading/processing** — extraction running: staged progress naming the real stage (recognize form → locate articles/sections → extract records → locate regions). Never a fake spinner.
- **partial** — some records proposed while extraction continues: the queue is usable immediately; the progress header counts live.
- **extracted-unconfirmed** — the working state (the per-record machine above).
- **low-confidence** — sub-threshold cards are ordered after rule-failures and before the document-order remainder (R2: Instabase failing-validation-first fused with Rossum confidence); low confidence orders, never hides.
- **conflict** — see the per-record row; the surface-level banner counts open conflicts in the progress header.
- **failed tie-out** — not a state of this surface (tie-out is owned by Review, 02 §3); named here for completeness: this surface never renders tie-outs.
- **watch/shortfall/breach** — ABSENT BY DESIGN: no verdicts render on this surface. The basis badge is the only covenant-semantics UI here; verdict vocabulary belongs to Actuals/Review (law 3).
- **permission-denied / read-only** — reviewer projection: cards render with full states, actions disabled with the reason ("confirm rights required"); PMC client-scoping applies (02 §1).
- **blocked/gated** — the fail-closed per-record state above; at surface level, any open blocked card holds setup incomplete and renders in the progress header's blocked chip.
- **certified (void-on-change)** — a correction to a confirmed record that feeds a certified package surfaces a void warning in the impact strip before save; saving voids the certification visibly and returns the period to in-review (03 §2). Never silent.
- **sent/sealed** — sealed periods cite the requirement version in force when they certified; those citations are immutable and forever inspectable (seal-not-wipe).
- **recovery/undo** — an in-progress edit cancels with Esc; a confirmation act is never undone, only corrected (versioned). Undo-by-deletion is impossible by design: the act history is the trust substrate.

## 6. Engine / Agent / Human / Gate / Quiet Log

| Step | ENGINE (deterministic) | AGENT (prepares/proposes) | HUMAN (typed decisions) | GATE (interrupts) | QUIET LOG |
|---|---|---|---|---|---|
| Extraction pass | Fail-closed dispatcher: recognized forms/riders only; schema validation | Extracts requirement records + terms with source regions and confidence | — | Unknown form/rider → blocked card | Pass started/completed; batch counts |
| Proposal review | Ordering (rule-failures → low-confidence → document order); progress computation | Renders each proposal beside its lit source region; explains extraction ("from §8.02(b)(3), page 44") | Confirm / Correct / Mark unreadable | — | Each confirmation act (loud in history, quiet in notifications) |
| Correction | Re-validation on edit (R2: Instabase auto-revalidation); downstream-impact computation | Proposes re-grounding when a drawn region changes the value | The correction itself, with reason where it touches certified content | Void warning when a certified field is affected | Correction + impact note |
| Manual authoring | Schema enforcement; basis lock (a no-source record can only be `monitored`) | Pre-fills from any locatable partial evidence; never fabricates a definition | Authoring act; source pin or explicit no-source declaration | — | AuthoringAct |
| Amendment | Detects affected records from the amendment's regions; re-opens only those | Proposes the per-record diff (v1 → v2) | Accept/correct each re-opened record | Amendment touching a certified period's inputs → void path | Re-open + resolution |
| Conflict resolution | Cross-source detection (agreement vs servicer statement vs workbook values) | Explains both chains side by side | Pick-with-reason (typed) | Unresolved conflict blocks the field's confirmation | ConflictResolution record |
| Period-over-period | Confidence thresholds + rule checks decide what surfaces (exception-only) | Frames each exception with its source | Disposition exceptions inside Actuals/Review | Open extraction exception blocks Gate 1 for that period | Zero-exception periods: one summary entry |
| Memory | Storage integrity, provenance stamps | Pre-fills remembered answers beside fields ("learned {date} from {who}") | Correct a remembered answer (versioned) | — | Every reuse |

Lane invariants per 04 §2: no shipping number originates here; the agent proposes, the human confirms, the engine validates and fails closed.

## 7. Information hierarchy

1. **Frame header:** shell 48px header; breadcrumb `Covenant / {Loan} / Setup` (book→deal altitude: setup is loan-scoped, no period crumb — the period crumb appears only in the exception projection inside period surfaces).
2. **Decision/status summary:** progress header — "14 of 17 confirmed" (live counts), blocked-count chip if any, loan identity (outward name, program+form+riders as recognized).
3. **Primary work region:** the 65/35 split — source page (left, dominant: grounding is the point) beside the proposal inspector (right).
4. **Secondary context:** the extracted outline (Articles/Sections) inside the source pane; the loan-terms tab in the inspector; the holding row for missing riders.
5. **Evidence/proof:** the lit source region IS the proof, co-visible by construction; the deeper chain (extractor version, confirmation history) opens in the inspector's provenance drawer, beside — never a modal (law 6).
6. **Actions:** per-card action row (Confirm / Correct / Mark unreadable / Author manually); Enter-walk keyboarding.
7. **Activity/history:** per-record version history in the drawer; the loan's quiet log linked from the progress header.

**Absent by design:**
- verdict chips — no pass/watch/shortfall/breach renders anywhere on this surface; verdicts belong downstream (law 3);
- charts — nothing here out-encodes text (chart doctrine);
- a "Confirm all" bulk button — blind bulk acceptance defeats Gate 1; the Enter-walk makes per-record confirmation fast instead (R2: Rossum);
- a generic chat box — agent presence here is the proposed extraction beside its source and the remembered answer beside the field, contextual not chrome (law 10);
- any guessed schedule — where the dispatcher cannot recognize a form, the honest artifact is the blocked card, never a plausible default.

## 8. Page anatomy and regions

**LEFT — source pane (~65% of work area; work window 1).**
- *Purpose:* page-accurate grounding for every decision — the page is dominant because grounding is the point (R2 synthesis).
- *Persistence:* persistent. Never collapses; it is the surface's reason to exist.
- *Content:* the filed document rendered page-true (DocView renderer, read-only reuse); for flattened scans, the Recreated-searchable derivative with its "Recreated · searchable — derivative" label (06 §8; evidence: the executed forms carry zero text layer). Artifact toggle Original ⇄ Recreated per the document-modes law (toggle/overlay, never a permanent split).
- *Region layer:* every proposed record's source region renders as an interactive highlight; the current card's region is lit and **stays lit** (law 6).
- *Interior outline:* extracted Articles/Sections rail, base form and riders as distinct branches — Form 6220 and Form 6241 appear as their own nodes, so a rider's added obligations are visibly the rider's (evidence: SLOT-3). Collapsed to an icon toggle below 1728px; persistent column at ≥1728px — the ruled widening-pane-grows-a-TOC behavior (snapshot §5).
- *Interaction:* click a region → the inspector scrolls to its owning card (bidirectional — R2: Rossum interactive bounding boxes); a "show all regions" toggle reveals every proposed region on the page (adapting Rossum's ALT-hold reveal); text search within the document.
- *Minimum:* 560px page content + 200px outline when shown.
- *Why a pane:* co-visibility row 1 (§9) — the confirm act is illegitimate without the agreement's own words in view (05 §4).

**RIGHT — proposal inspector (~35%; pinned inspector pane — the ruled 65/35 doc-inspector split named in 05 §4, row "Confirm requirement").**
- *Purpose:* the decision queue. *Persistence:* pinned (an inspector pane per the frame's pane topology, 08 §4 — not a second work window; the split-max-2 budget stays free).
- *Content, top→bottom:*
  1. progress header — "{n} of {m} confirmed", blocked-count chip, open-conflict chip (consumes the shared CountBadge atom, snapshot §4);
  2. tab strip — **Requirements | Loan terms**;
  3. record cards grouped by the 05 §1.1 taxonomy: reporting obligations / financial covenants / monitored thresholds / operational covenants / one-time / conditional — each group with a count, confirmed groups collapsing to one summary row;
  4. blocked cards pinned above their group (they hold completion; they must not hide).
- *Card anatomy* (RequirementCard, NEW — component detail §11), top→bottom within the card:
  1. kind chip (taxonomy kind);
  2. **basis badge — `covenanted` | `monitored` — structural: rendered on every card, sourced from the record's `basis` field only, never inferred by any renderer (C-9; 05 §1.2)**;
  3. title;
  4. verbatim `definition_text` quote, clamped to 3 lines, expandable — the agreement's words, never a paraphrase;
  5. test block, present only when the agreement defines one: metric ref + comparator + threshold, verbatim-sourced; the watch-band authoring default rendered visibly and labeled as a default (floor × 1.05 — [VERIFY against library; never hard-code], 05 §1.2);
  6. cadence + due-rule with its citation ("Q+45d per §8.02(b)(1)" — evidence class);
  7. deliverable named fields where the agreement names them (e.g. the §8.02(b)(3) five-field rent-schedule list);
  8. confidence readout;
  9. source-region link — click lights the left pane;
  10. state stamp (proposed / confirmed {who, when} / corrected / unreadable / blocked / stale);
  11. action row: Confirm · Correct · Mark unreadable (· Author manually on blocked cards).
- *Loan-terms tab:* UPB, rate structure, IO→am schedule, maturity, program+form+riders — each field a confirmable LoanTermRow with source region, confidence, and state (evidence-workbook instance: $15,232,500 · 2-yr IO → 30-yr am · maturity 2027-07-07). A quiet note states: "Balances per period arrive later via servicer statements" — period balances are never confirmed here (05 §2.1).
- *Downstream-impact strip:* appears inside any card in Correct/Author mode and on every amendment diff — names the checklist items that will materialize/retire, the tests that move, the composer sections affected; escalates to a void warning when a certified package field is touched. Transient (lives with the edit), never a modal.
- *Minimum:* 360px; below, the inspector becomes a labeled tab over the source pane (no silent compression, law 5).

**Modals:** none for reading or deciding. The only dialog-weight moment is the void-warning confirmation on saving a correction that voids a certification (a typed act with legal consequence — permitted by law 5).

**Exception projection (period-over-period reuse inside `/actuals` and `/review`):**
- the same RequirementCard renders in a compact exception form — no second implementation, no styling fork;
- inside `/covenant/[loanId]/[period]/actuals` it appears in the exceptions region (REGION 1 of that brief) for sub-threshold or rule-failing extraction items surfaced this period;
- inside `/covenant/[loanId]/[period]/review` it appears as a stop in the exception walk (stale confirmations, amendment-touched records);
- an "open source" action summons the 65/35 arrangement as a focused overlay scoped to that one record, and disposition returns the user to the host surface with scroll position kept;
- what may appear here is strictly bounded: only records that are amendment-touched, stale, conflicted, or below the org confidence threshold — a confirmed record with unchanged facts can never re-enter any queue (ask-once, 03 §2 Gate 1).

## 9. Co-visibility matrix

| Datum/surface A | Datum/surface B | Must be simultaneous? | User decision enabled | Default topology | Fallback |
|---|---|---:|---|---|---|
| Proposed record card | Exact source region, lit | YES | Confirm/correct with the agreement's own words in view (05 §4) | 65/35 split | <1152px work area: inspector tabs over source; the act still requires the region rendered above the card |
| Correction editor | Source region (still lit) | YES | Correct without losing grounding (R2: Instabase) | inline edit in card, region stays lit | same tab fallback |
| Card | Downstream impact | YES during edit | Informed correction | impact strip in-card | — |
| Amendment diff (v1 ⇄ v2) | Amendment document region | YES | Accept/correct the re-open | diff in card + region lit | stacked |
| Blocked card | The unrecognized page | YES | Author manually against the real page | card + page | — |
| Loan-terms field | Its source region | YES | Term confirmation | same 65/35 | — |
| Record | Full provenance chain (extractor version, history) | NO — on demand | Audit | summonable drawer in inspector | — |
| Exception card (in Actuals/Review) | Its source region | YES at act time | Period-exception disposition | focused 65/35 overlay summoned from the host surface | — |
| Remembered answer (MemoryEntry) | The field it pre-fills | YES | Trust-but-inspect the pre-fill | inline beside the field, "learned {date} from {who}" | — |
| Setup progress | Everything else | NO — ambient | Pacing | header | — |

No pane exists merely because information exists: the outline, drawer, and impact strip are all interior or summonable.

## 10. Layouts and viewport behavior

Ratios are work-area px (frame rail per 08 §9).

- **1440px:** rail 240 → work 1200. Source 780 / inspector 420 (65/35). Outline collapsed to an icon toggle inside the source pane.
- **1728px:** rail 256 → work 1472. Source 952 — including the 200px persistent outline: the TOC appears at this width (the ruled widening-pane gasp behavior, snapshot §5) — / inspector 520.
- **2048px:** rail 280 → work 1768. Source 1208 (outline 220px) / inspector capped at 560, surplus going to the page — page fidelity outranks card width.
- **1280–1439px:** rail auto-collapses to 48px (08 §9) → work 1232–1391. The split holds: source ≥760 / inspector ≥360.
- **Narrow/compact (<1280px frame):** the inspector becomes a labeled tab ("Records — 3 unconfirmed") stacked over the source pane; confirming from the tab renders the region snapshot above the card so grounding survives the stack; a banner names the compression. Minimum viable: 1152×720 (frame law); below that, the spine-surface "larger window required" state (registers elsewhere stay usable; this surface does not).
- **Focus behavior:** the Enter-walk auto-scrolls both panes in lockstep; focusing a card always reconciles the lit region; the drawer opens without stealing card focus.
- **Tab stacking/replacement:** exactly one stacking rule exists (inspector → labeled tab); the outline never stacks — it collapses to its toggle; nothing replaces the source pane, ever.
- **Compare behavior:** amendment compare is v1 ⇄ v2 inside the card (diff rows), plus the amendment document lit left; Original ⇄ Recreated is a viewer toggle, never a second split (document-modes law). No 50/50 peer compare exists on this surface.
- **Proof/source behavior:** proof is co-visible by construction; the drawer opens over the inspector's lower half, never covering the current card's header or the lit region.
- **No silent compression:** every collapse states itself; the split refuses below minimums and tabs instead (law 5).

## 11. Components and exact anatomy

| Component | Reuse / NEW | Parts |
|---|---|---|
| Shell + header + breadcrumb | REUSE `CovenantShell` | loan-scoped crumb; setup has no period crumb |
| Source pane viewer | REUSE DocView renderer (read-only embed) | page canvas, artifact toggle + derivative label, zoom/fit, text search, region layer |
| SourceRegionHighlight | NEW | region rect anchored to page coordinates; states: proposed (accent-family treatment) / current-lit (stays lit) / confirmed (neutral); hover names the owning record (R2: Rossum) |
| ExtractedOutline | NEW | Articles/Sections tree; base form + rider branches; per-node record counts; click scrolls page |
| ProgressHeader | NEW (consumes shared CountBadge) | "{n} of {m} confirmed", blocked chip, group jump menu |
| RequirementCard | NEW | kind chip · basis badge · title · verbatim quote · test block (threshold verbatim; watch-band default labeled) · cadence/due-rule + citation · deliverable fields · confidence · source link · state stamp · action row (Confirm / Correct / Mark unreadable) |
| BasisBadge | NEW (shared atom — Loan Detail, Actuals, Review consume the same one) | `covenanted` \| `monitored`, sourced from the record field only |
| BlockedCard | NEW | names what is missing verbatim ("Rider 'Form {id}' is not in the recognized form library" / "definition for {term} not locatable"); actions: Author manually · view page; never a guessed schedule |
| ManualAuthoringForm | NEW (wraps the engine's built manual-authoring function, snapshot §3) | full 05 §1.2 schema fields; source-region picker (draw/select on page — R2: Instabase); explicit no-source declaration path that locks `basis = monitored` |
| DownstreamImpactStrip | NEW | affected checklist items / tests / composer sections; void warning variant |
| LoanTermRow | NEW | field label, value (Geist Mono, `tabular-nums slashed-zero`), source link, confidence, state, confirm/correct |
| AmendmentDiff | NEW | v1 ⇄ v2 field rows, changed fields marked, amendment-region link |
| ProvenanceDrawer | REUSE Evidence-panel trace machinery (lit-trace row is the one live paper-accent render — snapshot §4) | full 06 §1 chain; confirmation/correction history list |
| ConflictCard | NEW | both values, both source chains, pick-with-reason |
| Exception card (period projection) | RequirementCard compact variant | hosted by Actuals/Review; "open source" summons the focused 65/35 overlay |
| Command actions | REUSE `CommandPalette` | "setup {loan}", "next unconfirmed", group jumps |
| Empty/error/recovery objects | NEW (pattern-shared) | empty→Intake link; extraction-failure naming the stage; stale banner with diff link |
| Period/package selector | ABSENT by design | setup is loan-scoped (no period crumb, §7); the exception projection inherits its host surface's period identity |
| Certify control / send control | ABSENT by design | no certification or send act exists on this surface; the only certification-adjacent UI is the void warning in the impact strip |
| Chart/legend/readout | ABSENT by design | nothing here out-encodes text (chart doctrine gate, law 11) |

## 12. Interaction specification

- **Selection:** one current card at a time; selecting a card lights its region and scrolls the page to it; selecting a region selects its card (bidirectional).
- **Hover:** region hover names its owning record; card source-link hover peeks the region (scroll-less preview — R2: Hebbia citation peek); confirmed-state stamp hover shows confirmer + timestamp (Rossum's grey-tick provenance, adapted).
- **Focus:** visible focus ring (ruled accent family) on cards and in-card controls; focus and lit-region always agree.
- **Keyboard** (the Enter-walk is the surface's spine — R2: Rossum keyboard shortcuts):

| Key | Action |
|---|---|
| `J` / `K` (or `↓`/`↑`) | Next / previous card — region follows, page scrolls |
| **`Enter`** | **Confirm the current card and advance to the next unconfirmed card** — the Rossum Enter-to-next-unvalidated mechanic, verbatim take (R2: Rossum); the page auto-scrolls to the new card's region |
| `E` | Correct — opens the inline edit with the region still lit |
| `⌘Enter` / `Esc` | Save / cancel the edit |
| `U` | Mark unreadable (pins the region, escalates) |
| `A` | Author manually (on a blocked card) |
| `Tab` / `Shift+Tab` | Fields within an editing card |
| `1–6` | Jump to taxonomy group (order of 05 §1.1) |
| `P` | Toggle the provenance drawer for the current card |
| `O` | Toggle the outline rail |
| `⌘K` · G-chords | Palette · frame navigation (08 §5) |
- **Editing and validation:** inline in-card; schema validation on save (fail-closed); editing a value re-runs applicable validations immediately (R2: Instabase auto-revalidation); re-grounding by drawing/selecting a region updates `definition_source_region` — a correction always carries a source region or the explicit no-source declaration.
- **Bulk action:** none for confirmation (by design, §7). Bulk collapse/expand of groups only.
- **Undo/recovery:** `Esc` cancels an edit; confirmations are corrected, never deleted (versioned history).
- **Sorting/filtering:** default order rule-failures → low-confidence → document order; filter chips: unconfirmed / blocked / by group; "show confirmed" toggle (default collapsed).
- **Drill-down and return:** provenance drawer in place; exception cards in Actuals/Review summon the focused overlay and return on disposition (host surface keeps scroll position).
- **Source-linked selection:** the lit-region mechanic is this surface's core interaction — click a card's source link → the exact region lights and stays lit until the card changes (law 6; component contract per `cross-cutting/provenance-lit-row-trace.md`, wrong-row defect U1-F1 fixed first).
- **Save/persistence:** every act persists immediately (typed acts are transactions); setup is resumable at any point (progress header restores).
- **Collaboration:** none in v1 beyond role-scoped acts; every act is identity-stamped.

## 13. Visual craft direction

- **Typography roles:** shell scale for UI chrome; card titles one step up from body; verbatim definition quotes render in a document-quote style visibly distinct from UI copy — the agreement's words must read as the agreement's words; citations ("per §8.02(b)(1)") in the small caption role; all financial figures, thresholds, and due-rules in Geist Mono `tabular-nums slashed-zero` (settled law).
- **The proposed→confirmed state machine in ruled tokens (no new colors — law 4).** Rossum's blue-proposed / green-confirmed is taken as a *state machine*, not a palette:
  - **proposed** — region fill from the accent tint token (#A9B5FF) at low opacity, border #7189FF; card-side grammar is the ruled inferred-proposed treatment: confidence + dotted provenance underline, never bold-final (06 §7);
  - **current-lit** — the stronger accent treatment on the region, and it stays lit until the card changes (law 6);
  - **confirmed** — drops to quiet neutral: gray-ladder hairline region border, card stamp on a mid gray rung, confirmer + timestamp on hover (06 §7 inferred-confirmed grammar).
  - No green exists in the ruled system and none is introduced; the calm of a confirmed record is expressed by *withdrawing* accent, not by adding a success color.
- **Blocked/unreadable:** no invented alarm color — gray-rung emphasis, Lucide icon (`octagon-alert` class from the approved set), explicit text label; the honesty is in the words, not a hue.
- **Basis badge:** text-first badge (`covenanted` / `monitored`), neutral rung ground, uppercase label treatment; never color-coded by severity — basis is structure, not a verdict.
- **Spacing/density:** uniform card rhythm; open-not-boxed — cards separated by gray-ladder hairlines and whitespace, not enclosure; confirmed groups compress to single summary rows.
- **Dividers/elevation:** hairlines from the ten-rung ladder; the drawer is the only raised plane (one seam, per the ladder adoption measurement — snapshot §4).
- **Certified-sheet treatment:** not touched here (no certified paper renders on this surface).
- **Charts:** none (nothing here out-encodes text — chart doctrine gate).
- **Motion:** region-light is an instant state change with a ≤150ms settle; Enter-advance scrolls smoothly; `prefers-reduced-motion` → instant jumps. Long-session ergonomics: the Enter-walk keeps hands on keys for the full 30–90 minute pass; progress header pins.

## 14. Benchmark research and synthesis

| Product | Limited role | Official evidence | Exact pattern to take | Covenant adaptation | What to reject | Why this beats alternatives |
|---|---|---|---|---|---|---|
| Rossum | Keyboard-driven, source-linked validation screen | knowledge-base.rossum.ai/docs/interactive-bounding-boxes-in-rossum · /docs/keyboard-shortcuts · /docs/using-ai-confidence-thresholds-for-automation-in-rossum (R2) | Blue-proposed → green-human-validated box state machine; Enter-to-next-unvalidated; click-value-jumps-to-field bidirectionality; grey-tick provenance | States kept, palette re-expressed in ruled tokens (§13); Enter-walk verbatim; confidence thresholds become the period-over-period exception gate | Threshold auto-export past humans for first-time extraction — the schedule gets exactly one full human confirmation, no silent pass | The one shipping product whose validation screen is both keyboard-complete and region-grounded |
| Instabase | Correction mechanics grounded to the page | docs.instabase.com/automate/review · /automate/validating-documents (R2) | Type-or-draw-a-box re-grounding; edit → auto-revalidation; failing-validation-first ordering | Every correction carries a region; ordering fused with confidence into one queue | Table-level-only source lighting — definitions need region-level light | Documented correction grammar, not just display |
| ABBYY (Vantage/FineReader) | Verification staging + the searchable-derivative model | docs.abbyy.com/vantage/documentation/runtime/manual-review/manual-review · support.abbyy.com/hc/en-us/articles/19098939370515 (R2) | "Text under the page image" = the Recreated-searchable artifact, verbatim framing | Two-stage escalation → analyst confirms, exceptions escalate | Character-level verification loops — this is term-level review, not OCR proofreading | Names the derivative-honesty contract Covenant already rules |
| Ironclad | Repository verification state | support.ironcladapp.com/hc/en-us/articles/12277809086615 · /articles/31344994944023 (R2) | "Needs verification" as a persistent saved view; record = document + typed properties | The unconfirmed backlog IS the setup queue; verification teaches forward | Emailed-CSV review detached from the page | Proves verification as a first-class repository state, not a task list |
| DocuSign Navigator | Accept-or-edit per attribute | support.docusign.com/s/document-item?bundleId=pqz1702943441912&topicId=uwk1702945852405.html (R2) | Per-attribute accept/edit in a details pane; accepted-only data enters reporting | The loan-terms tab is exactly this, plus region lighting Navigator lacks | Attribute verification without page-level source lighting | Cleanest per-attribute act grammar on record |
| Litera Kira | Provision extraction defensibility | litera.com/products/kira (R2) | Extraction-links-to-source-text as the non-negotiable sign-off bar | Highlight-to-train → corrections improve the next loan's rider boilerplate extraction | One-off deal-room framing — Covenant is a living per-period system | States the defensibility argument in legal-review terms |
| Hebbia | Citation-first grid | hebbia.com/blog/introducing-matrix-the-interface-to-agi (R2) | Citation peek before jump | Card source-link hover peeks the region without losing queue position | Free-prose cells as stored records — Covenant persists typed fields | The peek keeps the Enter-walk unbroken |
| Ocrolus | Cross-source consistency in lending | ocrolus.com/press/ocrolus-introduces-cross-source-validation-... (R2) | Cross-document corroboration surfaced as findings | The ConflictCard (agreement vs servicer statement vs workbook) | Vendor-managed invisible HITL — confirmations are the customer's auditable acts | The only lending-native cross-source precedent |
| ANTI-PATTERN — Google Document AI HITL | Boundary marker | docs.cloud.google.com/document-ai/docs/deprecation (R2) | Only the confidence-trigger idea survived | — | A review console detached from the system of record: business-blind, correction-orphaned, deprecated by its own platform | Proves the confirmation surface must BE the product, writing states onto the loan's own records |

**Synthesis.** Every strong precedent converges on the same shape — fields beside the page, page dominant, human act per field — and none of them knows what a covenant is. The original, domain-correct move is what the cards contain: the 05 §1.1 taxonomy as the grouping spine, the **basis badge as structure** (no benchmark distinguishes covenanted from monitored, and that distinction is Covenant's core modeling fact — the evidence loan has *zero* recurring ratio covenants while Westbrook Flats carries a real 1.20x DSCR), verbatim definition quotes as the card body (the agreement's words, not a paraphrase), fail-closed blocked cards where every benchmark would guess, and ask-once as a product law rather than an automation threshold.

The period-over-period design is the second original move: where Rossum applies confidence thresholds to decide what a human sees *per document*, Covenant applies the once-per-loan confirmed schedule as the baseline and lets only deltas — amendments, staleness, conflicts, sub-threshold extractions — re-enter any queue. Confirmation is an asset that appreciates, not a task that recurs. The result is a validation screen in Rossum's grammar whose vocabulary is the loan agreement's own and whose lifecycle is borrower-side lender reporting's own.

## 15. Domain references

Domain truth for terminology, expected structure, and cadence comes from the evidence documents themselves:

- the Fannie Mae form family — Form 6001.NR base agreement, Form 6220 replacement-reserve waiver, Form 6241 Green Financing rider (evidence: SLOT-3) — establishes what a recognized-form library must know: base-form article structure plus rider deltas;
- the servicer-form layer (JLL quarterly questionnaire and annual certification — evidence: SLOT-4/5-class) establishes what the confirmed schedule must eventually feed;
- Finley-class covenant-tracking products and loan-servicing/agency-compliance tooling are referenced for *terminology and expected data shapes only* (05 domain framing).

**Domain authority does not equal visual authority** — none of these products contributes a pixel. And covenant semantics come from each loan's own documents and Terry's rulings, never from any referenced product (law 8: no invented thresholds, tests, or cadences — every instance in this brief is evidence-labeled or canon-labeled).

## 16. Accessibility, performance, and safety

- **WCAG contrast and focus:**
  - accent-tinted region fills maintain ≥3:1 boundary contrast against the page ground; document text never renders under a tint that drops it below AA;
  - the focus ring is visible on every interactive element, including region highlights on the page canvas;
  - state is never carried by color alone — every region state has a border-style + stamp-text counterpart (verified per the oklch caution by canvas readback, not eyeball — snapshot §4).
- **Keyboard completeness:** the entire setup pass — card navigation, confirm, correct, re-grounding (the region picker operates via arrow-key nudge + Enter), unreadable, authoring, drawer, outline — completes without a pointer (§12 map; acceptance-tested).
- **Screen-reader semantics:**
  - cards are list items with state announced ("Reporting obligation, covenanted, rent schedule, unconfirmed, confidence high");
  - a confirmation announces the act and the next card; region lighting announces the page + section landed on;
  - the basis badge reads as text, never as color;
  - the progress header is a live region announcing count changes politely.
- **Table virtualization/large data:** the card list virtualizes (an agreement + riders can propose dozens of records); the page canvas renders lazily per page with regions hydrated per visible page; uniform row heights (law 11).
- **Loading and latency feedback:** staged extraction progress with real stage names; per-card acts are optimistic with server confirmation; a failed act surfaces on the card itself — no act is ever silently dropped.
- **Destructive confirmation:** the only destructive-adjacent act is a correction that voids a certification — it requires the void-warning acknowledgment (typed dialog, permitted class).
- **Certify/send safety:** upstream contribution — confirmed records are version-pinned into packages; void-on-change is automatic and visible (03 §2).
- **Source immutability:** the viewer renders artifacts read-only; Original bytes are never mutated; derivatives always labeled.
- **Auditability:** every act (confirm/correct/author/waive/pick) is identity-stamped, versioned, quiet-logged, and renders in the provenance drawer forever — sealed periods cite the exact requirement versions they consumed.

## 17. Acceptance tests and fixtures

**Fixtures:** `CAL-6001NR-SETUP` = the Calloway Park evidence document set (Form 6001.NR + Form 6220 + Form 6241, SLOT-3) — the acceptance spine. `CAL-6001NR-NO-6241` = the same set minus the Green rider (synthetic variant). `BEXLEY-CANON` = the demo book loan (canon). `CAL-FYE2018` = the financial evidence spine for downstream references.

1. **Full-pass workflow (CAL-6001NR-SETUP).** Extraction proposes, at minimum, records for (evidence: SLOT-3 §8.02(b); 05 §1.1):
   - §8.02(b)(1) — quarterly YTD income & expense statement, due-rule **Q+45d** (Q1–Q3), kind reporting-obligation, basis covenanted;
   - §8.02(b)(2)(A)/(H) — annual statements including cash flows, due-rule **FYE+120d**;
   - §8.02(b)(3) — rent schedule at both cadences (Q+45d and FYE+120d) with the named deliverable fields: tenant, space occupied, lease expiration, current rent, paid-through date;
   - §8.02(b)(2)(E) — annual written certification: single-asset status, building-code notices, rezoning, liens;
   - §8.02(b)(2)(F) — security-deposit accounting with institution/account detail;
   - §8.02(b)(2)(G) — ownership-change confirmation;
   - §8.02(b)(4) — on-request items as `conditional`, the ≤1-per-6-months rate cap rendered on the card;
   - Form 6241 Article 16 — annual ENERGY STAR Energy Performance Metrics report (score, Source EUI, period, property ID) — proposed under the rider's own outline branch;
   - Form 6220 — the replacement-reserve modification as `operational`.
   Every proposal carries a verbatim quote + source region. Confirm all → setup completes → the first period's checklist materializes containing the rent-schedule and statement items.
2. **The core modeling fact.** On the same fixture, assert **zero** recurring ratio-test records are proposed (the executed 6001.NR carries none — evidence), and §9.03's casualty DSCR renders as `conditional`, never as a recurring test (05 §1.1). Contrast check: a Westbrook-class fixture with a real covenanted 1.20x DSCR proposes a financial-covenant record with `basis = covenanted` (canon).
3. **Riders change the checklist per loan.** `CAL-6001NR-SETUP` vs `CAL-6001NR-NO-6241`: the proposal sets differ by exactly one record — the annual ENERGY STAR report — proving the extracted checklist is per-loan, never templated (evidence: Form 6241; 06 §3).
4. **Fail-closed block.** A doc set containing an unrecognized rider form yields a BlockedCard naming the form verbatim, zero guessed records from that rider, a quiet-log BlockEvent, and a working Author-manually path; the authored record enters the confirmed set with its hand-pinned region.
5. **Enter-walk keyboarding.** From card 1 of `CAL-6001NR-SETUP`: `Enter` × 3 confirms three records; after each, focus moves to the next *unconfirmed* card (skipping any already-confirmed card in between), the lit region advances, and the page auto-scrolls (R2: Rossum Enter-to-next-unvalidated). `J/K` navigate without confirming; `E` opens an edit with the region still lit; `Esc` cancels leaving the record proposed; the walk completes the entire fixture pointer-free.
6. **Lit-region provenance.** Click the §8.02(b)(3) card's source link → the agreement page lights the §8.02(b)(3) region and it **stays lit** while the card is current (06 §3 Chain B); clicking a region on the page scrolls the inspector to its owning card; the "show all regions" toggle reveals every proposed region on the visible page. The wrong-row class of defect (U1-F1) has a named regression test here: the lit region must be the region stored on the record, byte-for-byte.
7. **Ask-once.** After full confirmation, open two consecutive periods with unchanged facts: zero re-confirmation prompts anywhere (03 §3 acceptance test); the quiet log shows the reused-schedule entry per period.
8. **Amendment scope.** File a modification amending §8.02(b)(1)'s due-rule: exactly that record re-opens (v1 ⇄ v2 diff, amendment region lit); all other records remain confirmed; the impact strip names the affected checklist item and composer section (02 §5).
9. **Correction impact + void-on-change.** Correcting a confirmed loan-term (UPB) that feeds a certified package field surfaces the void warning before save; saving writes the CorrectionAct, voids the certification visibly, and the period returns to in-review (03 §2). Never silent.
10. **Monitored authoring (BEXLEY-CANON).** Author the 90% occupancy floor via the no-source path: `basis` locks to `monitored`; the badge renders `monitored`; downstream the engine can emit at most `shortfall` for it — `breach` is unreachable (canon: 268/301 = 89.04% is a SHORTFALL; law 3).
11. **Conflict.** Term fixture where the agreement and a servicer statement disagree on a value → ConflictCard with both chains; confirmation blocked until pick-with-reason; the pick is versioned.
12. **Stale.** Replace the agreement with a corrected scan after confirmation → affected records badge stale with diff link; re-confirm clears; unaffected records untouched.
13. **Viewport fixtures.** 1440/1728/2048/1280 per §10 ratios; the outline column appears at ≥1728; below 1152px work area the inspector tabs with the named banner — no silent compression at any width.
14. **Accessibility.** Full pass keyboard-only on `CAL-6001NR-SETUP`; SR announces card state, basis, and act results; contrast checks on tinted regions; reduced-motion honored.
15. **Data integrity.** Post-confirmation invariants asserted directly against the store:
    - every confirmed record has either a non-null `definition_source_region` or an AuthoringAct carrying the explicit no-source declaration — no third case exists;
    - every correction row references its prior version (no orphaned versions; history is a complete chain);
    - the `basis` field on every stored record equals the badge every renderer painted (C-9 — no renderer-side inference path exists to diverge);
    - a sealed period's requirement-version citations resolve to immutable versions after two subsequent amendments.
16. **Benchmark challenger review.** Side-by-side against Rossum's validation screen on the R2 criteria (region fidelity, keyboard economy, provenance visibility): Covenant must match the Enter-walk economy and exceed on definition verbatim-ness and basis structure, or the gap is ticketed.

## 18. Build plan

- **Dependencies (named):**
  - F2 persistence — RequirementRecord + version tables, ExtractedValue store, tenancy FKs (snapshot gap 3); confirmation acts need real identity, so auth/tenancy precedes GA;
  - the document chain (gap 2) — filed storage + the four-artifact pipeline; Recreated-searchable rendering is a hard prerequisite for scan-heavy agreements (evidence: zero text layer on executed forms);
  - the recognized-form library feeding the fail-closed dispatcher (the dispatcher itself is built — snapshot §3);
  - the lit-row component contract (`cross-cutting/provenance-lit-row-trace.md`), with the U1-F1 wrong-row defect fixed before this surface adopts the mechanic;
  - the orchestration spine's Gate 1 hook (03 §2) for the period-exception projection.
- **Foundation work:** wire the built loan-doc extraction library to real filed documents; region-anchor rendering in the DocView embed (page coordinates → highlight layer, bidirectional hit-testing); the downstream-impact computation (requirement → checklist/test/section dependency graph).
- **Components to build first:** SourceRegionHighlight + the viewer embed → RequirementCard + BasisBadge (shared atom) → ProgressHeader → the Enter-walk controller → BlockedCard + ManualAuthoringForm → DownstreamImpactStrip → AmendmentDiff / ConflictCard / ProvenanceDrawer.
- **Vertical slice (the send-vertical pattern):** one route, one loan, engine data end to end — the Calloway Park evidence set uploaded → extraction pass → proposals rendered at `/loans/[loanId]/setup` → confirmations persisted → checklist materializes for one period. No fixture strings anywhere in the slice.
- **Migration from fixture data:** the hand-authored Bexley `deal_config` is re-expressed as confirmed RequirementRecords (it becomes the parity oracle: the confirmed set must reproduce today's engine inputs exactly before the config is retired).
- **Rollout/feature flag:** `covenant.extraction-confirmation`; setup route ships first; the period-exception projection in Actuals/Review follows once those surfaces mount engine data.
- **Proof artifacts:** screen recording of the full Enter-walk on `CAL-6001NR-SETUP`; region-accuracy report (every proposed region vs the page, reviewed); the two-period ask-once log (test 7); the rider-differentiation diff (test 3).
- **Final gate:** `PASS` requires acceptance tests 1–16 green, the parity oracle clean, and the challenger review closed; anything less holds the surface at `ADJUST` behind the flag.
