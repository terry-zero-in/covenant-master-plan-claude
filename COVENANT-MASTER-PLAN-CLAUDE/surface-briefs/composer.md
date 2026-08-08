# COMPOSER — PACKAGE ASSEMBLY (per loan, per period)

## 1. Identity and verdict

- Product layer: reporting spine (per loan, per period).
- Route/address: `/covenant/[loanId]/[period]/composer` — planned deep route in the `(covenant)` group mounting `CovenantShell` (08-GLOBAL-FRAME-SHELL-PLAN §1); today the Composer view mounts through `/review-room`'s view switcher. Components: existing `Composer` view in `src/components/covenant/` (snapshot §3).
- Current build state: `FIXTURE` — screen built and reachable; the readiness affordance is **decorative** (snapshot §3, §6 gap 4: "the readiness gate on the composer surface" is named missing machinery).
- Existing-surface verdict: `ADJUST` — the assembly idea and section framing are right; readiness must become engine-computed, transcription must lock engine values, narratives must interpolate by reference, attachments must pin Document versions.
- Research tier: B/C.
- Primary users/roles: PMC preparer (assembles/approves drafts per client scope), owner (final content decisions; often the same person as certifier), reviewer (read-only projection).
- Frequency and session duration: once per loan per period (monthly/quarterly, plus the heavier annual package). Target session: minutes, not hours — steps 1–9 of the arc happen without the borrower (03 §1); the Composer session is approving drafted narratives, resolving needs-input fields, and confirming attachments. Deadline pressure is real (Q+45d / FYE+120d due-rules, evidence: SLOT-3 §8.02(b)).
- Error cost: terminal-adjacent — this surface composes the content that gets certified and sent. A drifted number here becomes a wrong number in front of a lender. The design answer: numbers cannot drift here (locked chips + live refs; §4, §12).
- Success criterion: a period's package assembles itself from the lender-form template + confirmed requirement deliverables; every readiness reason is a computed, named row linking to its blocker; the human touches only narratives, free-text answers, and genuine decisions; Certify is reachable the moment readiness is honestly true.

## 2. User job and decisions

- Primary job-to-be-done: "Turn this period's confirmed values into exactly the deliverable this lender's form requires, and know precisely what still blocks certification."
- Decisions made here: approve/edit drafted narratives; answer or confirm free-text form fields the engine cannot fill (memory pre-fills recurring ones); include/exclude optional sections the form permits; pick/confirm attachment versions; reorder sections where the form allows; confirm the export naming convention once per lender; route to a blocker via its readiness row.
- Questions the surface must answer in scan order: Which loan+period am I composing? → How close is this package to ready (n of m reasons green)? → Which sections need me (needs-input / drafted-awaiting-approval)? → What changed since the prior package? → What exactly blocks certify, and where do I fix it?
- What the user should not have to decide here: any computed value (engine-owned, locked); the section list itself (derived, not authored); readiness (computed, never assertable); which engine value maps to which form field (the field map comes from the confirmed RequirementRecord deliverable + lender-form template); test verdicts (dispositioned in Review, rendered here).
- Entry paths: Review room "open in Composer" link; Home your-move rows (drafts awaiting approval, readiness-blocked periods); Loan Detail → period; Calendar deadline row → period → Composer; palette (`⌘K` "Composer — {Loan} {Period}"); `G P` chord within a loan context (08 §5).
- Exit paths: "Proceed to certify" → `/covenant/[loanId]/[period]/certificate` (enabled only at readiness true); readiness rows → owning surfaces (Actuals, Review, Intake, Documents) with return path; breadcrumb altitude gate back to Loans/Home.
- Completion/advancement conditions: readiness true (all reasons discharged) makes the certify CTA live; the period stays `ready` while the package draft stays mutable; the certify act on the Certificate surface freezes content (05 §5; R4 research: GitHub Releases draft→publish).

Boundary with the Review Room (both are per-period spine surfaces; they never duplicate): Review owns the **exception walk and dispositions** — findings, failed tie-outs, stale confirmations, changed-vs-prior stops, and the compare mode. Composer owns the **deliverable's shape and content acts** — sections, transcription answers, narrative approvals, pins, naming. The 60-second review happens in Review; the composition acts that review inspects happen here. Cross-links are structural: Review's narrative-approval stops deep-link to the Composer section; Composer's readiness rows for tests/tie-out link to Review stops. A zero-exception period may skip Composer entirely after a glance — the agent assembled everything, the needs-input count is zero, and Review's summary stop plus readiness carry the user straight to certify.

## 3. Object and ownership model

| Object/action/status | Owned here? | Summary/link elsewhere | Address/identity | Notes |
|---|---:|---|---|---|
| Package (sections, field map, revision) | YES | Review links in; Home shows readiness state; Certificate consumes rev + hash | `pkgId = (loanId, period, rev)` | Owner per 02 §3 |
| PackageSection (kind, status, order) | YES | Review's section list mirrors read-only | `sectionId` | kinds: statement / rent-schedule / questionnaire / certification / narrative / attachment (02 §2) |
| Narrative draft (agent) + approval act | YES (approval is the human act) | Quiet log records revisions | draft ref on section | Figures interpolate by reference only (04 §2.1) |
| Attachment pin (docId + version per checklist item) | YES (the pin) | Documents owns the canon; pin links to it | `(sectionId, docId, version)` | Version-pinned; stale on replacement (§5) |
| Export name (per lender convention) | YES (the confirm-once act) | Memory store owns the entry | MemoryEntry scope=lender | Evidence: SLOT-4 filenames follow "{Property} {Statement} {MM.DD.YY}" |
| Readiness (boolean + reasons[]) | NO — engine-computed; rendered here as the primary rail | Home/Review summarize | on Package | Never decorative again; each reason links to its blocker (05 §5) |
| ChecklistItem states | NO — Intake/Holding owns | Rendered here as the attachments checklist projection | `(loanId, period, itemId)` | Waive acts route to Holding |
| Metric / TestResult / TieOut | NO — Actuals & Computation / Review | Rendered as locked chips and verdict rows with lit-row refs | engine grain | Disposition happens in Review, never here |
| CertificationRecord / SendRecord | NO — Certificate / Send & Record | Certified/sent state renders as banner + hash chip | `certId` / `sendId` | Composer goes read-only when frozen |
| MemoryEntry (form answers, naming) | NO — agent layer one-store | Rendered inline beside the fields they fill | `memId` | "learned {date} from {who}" grammar |
| ActivityEvent slice (this package) | NO — one app-wide store | Filtered view at the rail foot | `eventId` | Append-only; 07 §2 duplication fix |
| Deadline for this period | NO — Calendar/Obligations owns | Header readout with due-rule provenance | `noteId` | Escalation policy lives in Settings |

No-double-homing boundary: Composer owns **composition** — sections, field map, narrative approval, attachment pins, export naming. Documents owns filed canon (Composer holds links, never copies — the duplication audit's fixture-array defect dies here, 07 §2). Intake/Holding owns checklist state. Actuals owns every number. Certificate owns the certify act. Review owns exception disposition.

## 4. Data and semantic model

- Source facts: the lender-form template registry entry for this loan's program+form (per servicer; SLOT-4/5 are the evidence instances — JLL Property Questionnaire, JLL Annual Certification); the confirmed RequirementRecord deliverables with named fields (05 §1.2), e.g. §8.02(b)(3) rent schedule: tenant, space, lease expiration, current rent, paid-through (evidence: SLOT-3).
- Extracted values awaiting confirmation: none originate here; unresolved extraction/normalization exceptions surface only as readiness reasons linking out ("confirmations current" fails).
- Confirmed values: COA mappings, requirement records, loan terms — consumed via the engine, never re-confirmed here (ask-once, 03 §3).
- Deterministic outputs (the only fillers of numeric form fields): normalized statement lines, occupancy aggregates, metrics, TestResults, tie-out state — each carrying a ProvenanceRef (cross-cutting/provenance-lit-row-trace §2.1).
- Agent proposals/drafts: assembled section drafts; transcribed form answers (free-text proposals flagged for approval); drafted narratives with interpolated engine refs; proposed attachment matches from the checklist; proposed export name from lender-scope memory.
- Human decisions: narrative approval/edit; free-text answers; optional-section inclusion; attachment pin confirmations; waive requests (routed to Holding); naming confirmation.
- Certified values: none created here; once a CertificationRecord binds this rev's content hash, the whole surface renders frozen (§5).
- Versions/periods/packages: package revs increment on any content change; the prior sealed package is the tie-out and what-changed baseline; the period identity rides the URL (08 §1).
- Evidence/provenance: every numeric chip and interpolated figure opens the full chain (06 §1); the golden exemplar for package shape is **SLOT-4** — the FYE-2018 package as actually submitted: balance sheet, income statement, cash flows, trial balance, vendor aging, ownership structure, loan/interest/escrow schedule, executed questionnaire, executed annual certification, plus three withheld resident-level files (as-submitted rent roll, deposit audit, delinquent & prepaid report) that the real package included (evidence: SLOT-4; resident-data law below).
- Permissions/read-only projections: reviewer role sees the draft read-only; PMC preparer scope is per-client (02 §1); certified/sealed states are read-only for everyone.

Field groups and grain: organization (naming memory scope=lender lives org-wide), client (PMC scoping), loan (form/template identity), reporting period (the composing unit), document (attachment pins), requirement (deliverable → section derivation), test (verdict rendering), metric (chip values), package section (the work grain), certification record (freeze), send record (seal).

### The section list derivation (the real evidence shape)

Derived, never hand-authored: `lender-form template ∪ RequirementRecord.deliverable[]` for this period's cadence. For the evidence loan at annual cadence this yields: statements group (§8.02(b)(2) incl. cash flows), rent schedule (§8.02(b)(3), built from the anonymized roll derivative), servicer questionnaire (the 11 questions — transcription targets), annual certification (its 3 items: security-deposit institution/account accounting, ownership-structure attachment, financial-position-change yes/no — evidence: SLOT-4), rider deliverables (Form 6241 ENERGY STAR report — exists only because this loan carries the Green rider, 06 §3), attachments (trial balance, vendor aging, loan/interest/escrow schedule per the exemplar). Quarterly cadence derives the smaller set: YTD income statement (§8.02(b)(1)), rent schedule, questionnaire (SLOT-5 evidences the recurring quarterly ask). A loan without a rider never shows the rider's section — per-loan, never templated (06 §3).

### The field map (the transcription contract)

Each form-kind PackageSection carries a field map binding the lender form's own fields to their fillers — the per-field lineage the chips render:

```text
PackageSection.field_map: FieldBinding[]
FieldBinding {
  form_field_id            ← the template's field identity (label verbatim, position, format)
  fill:  { kind: 'engine',  value_id }            → LockedChip (uneditable; ProvenanceRef chain)
       | { kind: 'memory',  mem_id }              → FreeTextField pre-filled + MemoryNote (editable; edit versions the entry)
       | { kind: 'human' }                        → FreeTextField, needs-input until answered
       | { kind: 'blocked', missing: string }     → fail-closed card (e.g. occupancy resolver pending reader — gap 7)
  state: filled | needs-input | blocked | stale
}
```

The map is validated by the engine against the template (every required form field bound or explicitly blocked — "sections present" consumes this); it is authored by derivation + memory, never hand-built per period.

### The questionnaire as transcription target (evidence: SLOT-4/5)

The JLL Property Questionnaire's 11 questions, each a transcription target with a structural fill class:

| # | Question (evidence) | Fill class | Source / behavior |
|---|---|---|---|
| 1 | Physical occupancy at each month-end of the quarter (units occupied, % of total) | engine | Occupancy chain terminus (06 §4): roll-derivative aggregates per month-end date; currently BLOCKED on the rent-roll reader (gap 7) → renders the blocked card, never a guess |
| 2 | Ownership/management contact changes | human + memory | Pre-filled "no change" only if memory holds a prior confirmed answer AND no org-record change occurred this period; else needs-input |
| 3 | Significant sub-market job losses / economic events | human | Needs-input each period (time-sensitive; memory never pre-fills a market judgment) |
| 4 | Casualty / criminal activity on the property | human | Needs-input each period; a "yes" flags the narrative section for a drafted explanation |
| 5 | Down units (number, reason, timeline) | engine + human | Admin/Down count from the roll status taxonomy (evidence: 2 Admin-Down units in SLOT-2); reason/timeline free-text |
| 6 | Capital improvements across the form's 15 categories, separated from expense lines | engine + human | Category totals proposed from mapped capex-class lines where mappings exist; the expense-line separation renders as a chip-beside-answer check; category assignment confirmable |
| 7 | Subordinate financing / new debt | engine + human | Supplemental-loan linkage (`supplemental_of`, 02 §1) proposes the answer; human confirms |
| 8 | Property operated 12 full months? | engine | Period-coverage check from the T-12 month columns |
| 9 | Other-income breakdown | engine | Normalized other-income lines (e.g. the "4135 RAF → Other Income" mapping, 06 §2) |
| 10 | Non-recurring / extraordinary expenses | engine + human | Candidate lines proposed by variance vs prior period; inclusion is a human call with the chip beside it |
| 11 | Management fee % (with explanation when outside the form's 4–6% band) + incentive-fee sub-questions | engine + memory | Fee % computed from the mapped fee line over collections per the form's own arithmetic; band explanation free-text, remembered lender-scope (the recurring-answer exemplar, 03 §3) |

The signed certification block at the questionnaire's foot renders as a locked preview — signature happens at the Certificate, never here. Executed questionnaires in evidence are flattened scans with zero text layer — the Recreated-searchable artifact exists for reading prior submissions back (kit evidence; document-artifact law).

### The annual certification as transcription target (evidence: SLOT-4)

Three items, three fill classes: (1) security-deposit accounting — institution and account detail: engine-fillable from the balance-sheet/escrow schedule mappings where confirmed, institution identity from loan-scope memory ("learned at setup"); resident-level deposit detail never attaches (law below). (2) Ownership-structure attachment: an attachment pin (the exemplar package carries `Ownership Structure.pdf`) — version-pinned to Documents, staleness-checked against org records. (3) Financial-position change yes/no: human answer with the tie-out summary co-visible (the honest basis for "no").

### The rent-schedule section (evidence: SLOT-3 §8.02(b)(3))

The section maps anonymized roll-derivative columns onto the agreement's named fields — tenant (anonymized label), space occupied, lease expiration, current rent, paid-through date (06 chain B). The mapping is per-loan and confirmed once (the loan-scope memory question "is the PMS detail export acceptable to this servicer?" — 03 §3); the section renders the roll-derivative table read-only with its as-of date and dedup note ("322 rows → 301 units").

### Resident-data law, enforced at composition

Rent schedule sections build only from the anonymized roll derivative; the attachment picker structurally refuses resident-level document classes (deposit audits, delinquency lists) for outbound sections, rendering the refusal with the rule ("resident info NEVER travels outward" — Terry ruling 2026-08-07) — not a silent absence, an explained one. The SLOT-4 exemplar's three withheld files prove the real package historically included these classes; Covenant's package shape substitutes the anonymized rent schedule and omits the rest, stated on the section list so the preparer knows the delta from their old manual package.

### What-changed-since-prior-package (right rail, below readiness)

Field-level rows computed from the tie-out deltas plus composition diffs: changed engine values (field, prior → current, cause link), new/removed sections (cadence or amendment driven), replaced attachments (version chain), changed free-text answers (memory version link). Each row links to Review's 50/50 compare (Review owns compare — no second compare surface here). Zero-change periods render one line: "nothing changed beyond expected; tie-out clean" — the same sentence the Review walk uses for its summary stop (review-room brief), so the two surfaces never disagree.

### Export naming and the bundle manifest (lender-scope memory)

The bundle name and per-file names follow the lender's remembered convention. Evidence for the convention's reality: the SLOT-4 filenames as submitted — "{Property} {Statement} {MM.DD.YY}" (e.g. balance sheet, income statement, cash flows each named property-first with the period date). First export per lender: the agent proposes the pattern inferred from the golden exemplar's filenames; the human confirms once; MemoryEntry scope=lender stores it with provenance; every later period pre-fills silently with the "pre-filled from memory" note. Corrections version the entry (03 §3). The manifest lists every file with its section, document version, and hash — the self-contained evidence-pack discipline (R3 research: DataSnipper stores references and files in the workbook).

## 5. State machine and exceptions

### The five readiness reasons, defined (the computed gate this surface renders)

GitHub required-checks pattern (R4): every reason a named row with granular state, linking to its blocker — never a bare disabled button. Engine-computed per 05 §5 ("readiness (boolean + reasons[])"; reasons each link to a blocking item).

| Reason row | Computed predicate | State grammar | Blocker link target |
|---|---|---|---|
| Sections present | Every derived section exists and every required form field is bound or explicitly blocked (field-map validation) | green / "2 sections incomplete" | The first incomplete section, selected |
| Tests verdicted | Every TestResult for the period carries a verdict AND every shortfall/breach finding is dispositioned (Review owns disposition) | green / "1 finding awaiting disposition" | The Review stop for that finding |
| Tie-out clean | Prior-package reconciliation clean or every failed row dispositioned with reason | green / "3 fields failed tie-out" | Review's 50/50 compare on the first failed field |
| Confirmations current | No value feeding any package field is unconfirmed, stale, or superseded (source replaced after confirmation) | green / "occupancy inputs stale" | The owning surface (Actuals exception / Extraction card) |
| Checklist complete/waived | Every ChecklistItem for the period is arrived+matched or waived-by-named-human | green / "1 item outstanding (chased {date})" | The Holding checklist row |

Readiness is the conjunction; the CTA enables only at all-green. There is no manual override, no admin bypass (R4: rejecting GitHub's bypass lists), and no path for the agent to assert a reason true — each predicate reads engine state.

Failure behavior per the gate law: each red row states the exact ask ("disposition the occupancy shortfall finding"), why it blocks (the predicate), the evidence link, the affected package fields, and the consequence of delay (the deadline readout); the audit record is the readiness transition in the quiet log. A reason that cannot be computed (engine unavailable, resolver missing) renders as its own honest state — "cannot evaluate: {what's missing}" — which also blocks; unknown never passes.

### Per-section status machine

`awaiting-inputs → in-assembly (agent) → needs-input (human field/answer outstanding) → drafted-awaiting-approval (narratives) → complete`; any upstream change can knock a section back (`complete → stale → needs-input`), quiet-logged with the cause. Attachment sections use `expected → proposed-pin → pinned → stale-pin`. Section status rolls up to the list chips and the "sections present" reason.

### Package draft lifecycle

GitHub-Releases shape — assemble fully in a mutable draft; one act freezes; R4 research:

| State | Trigger → next | Actor | Reversible? | Audit |
|---|---|---|---|---|
| assembling (draft) | agent assembles sections as upstream steps complete | Agent/Engine | — | quiet log per revision |
| needs-input | any section awaits a human answer/approval | Engine (computed) | resolves by act | your-move row |
| ready (draft, mutable) | readiness reasons all discharged | Engine | any change → back | quiet log |
| frozen (certified) | certify act on Certificate binds content hash | Human (there) | void-on-change only | CertificationRecord |
| voided → draft | any input feeding any package field changes | Engine (automatic) | re-certify = fresh act | visible void event with reason (03 §2) |
| sealed (sent) | send act completes; period seals | Human (send gate) | never | append-only SendRecord |

Required states, all covered:

- **empty**: new period, template known → skeleton section list renders with every section `awaiting inputs`; nothing fake.
- **awaiting-documents**: checklist items missing → affected sections show "awaiting {doc type} (chased {date})" linking to Holding; readiness row "checklist complete/waived" red.
- **loading/processing**: engine assembly in flight → per-section skeletons, never spinner-walls.
- **partial/incomplete**: some sections complete, others not — the normal mid-cycle state; section statuses carry it.
- **extracted-unconfirmed**: an upstream value awaiting confirmation → its chip renders inferred-proposed (dotted underline, 06 §7) and "confirmations current" blocks readiness.
- **stale/superseded**: a pinned attachment's document got replaced in Documents, or a source behind a chip changed → stale badge + diff link + re-pin/re-confirm action; if frozen, this is the void trigger.
- **low confidence**: OCR-uncertain transcription source → confidence note beside the field, suspect-word styling per DocView.
- **conflict**: two candidate documents satisfy one checklist item → the pin becomes a decision (pick version), linked from readiness.
- **failed tie-out**: readiness row "tie-out clean" red, linking to Review's 50/50 compare (Review owns disposition).
- **watch/shortfall/breach**: verdict chips render on covenant-relevant sections with basis badges (monitored can never read breach — vocabulary law); a shortfall with an undispositioned finding blocks readiness via "tests verdicted".
- **permission-denied**: out-of-scope client/loan → honest 403-equivalent state, no skeleton leak.
- **read-only**: reviewer role, or frozen/sealed package → editing affordances absent, not disabled-gray mystery.
- **blocked/gated**: unknown lender form (no template) → fail-closed card naming what's missing, offering template authoring; never a guessed form. Export artifact: "PDF/XLSX render pending (roadmap F4)" — honest, on-surface (§18).
- **certified (void-on-change)**: frozen banner with hash chip; every figure still lit-row traceable; edit attempts explain the freeze and offer the void path deliberately.
- **sent/sealed**: seal banner; the composed package remains fully inspectable forever (seal-not-wipe).
- **recovery/undo**: narrative edits are versioned with undo; pin changes are reversible pre-certify; the certify freeze and the send seal are irreversible by design — the draft state is exactly where reversibility lives (R4: Stripe's stated-irreversibility discipline).

## 6. Engine / Agent / Human / Gate / Quiet Log

The composition chapter of the master five-lane table (04 §1), expanded to this surface's activities:

| Activity | ENGINE (deterministic) | AGENT (prepares/proposes) | HUMAN (typed decisions) | GATE (interrupts) | QUIET LOG |
|---|---|---|---|---|---|
| Section derivation | Derive list from template ∪ deliverables; validate completeness | Order proposal within form freedom | Reorder where allowed; include/exclude optional sections | Unknown form/template → fail-closed authoring card | Derivation run + template version |
| Form transcription | Fill every `engine` binding; field-map validation; blocked-card emission where a resolver is missing | Propose `memory` fills with provenance; flag free-text needing answers | Answer/confirm free-text; correct a memory fill (versions the entry) | — | Each fill with its value_id / mem_id |
| Narratives | Re-render interpolated figures at view time (04 §2.1); flag drafts whose figures changed | Draft prose around live refs; re-draft on request | Approve/edit (edit-diff captured → memory); re-approve after figure change | — | Draft revisions + approval acts |
| Attachments | Staleness detection on pinned versions; resident-class refusal enforcement | Match checklist items to filed documents; propose pins | Confirm pins; choose among conflict candidates; request waive (routes to Holding) | Conflict (two candidates) surfaces as a decision | Pins, re-pins, refusals |
| Readiness | The five predicates + conjunction; content-hash per revision; void-on-change | Explain a red row on request (grounded) | Fix via blocker links (acts happen on owning surfaces) | Readiness false → CTA disabled-with-reasons (the gate this surface renders; the certify act lives on Certificate) | Readiness transitions |
| Export naming | Name-pattern application; per-file manifest | Propose from lender-scope memory | Confirm once per lender; correct (versions memory) | F4 absent → render-pending state, never a fake artifact | Naming confirmations + reuses |

Lane invariant honored: no shipping number is produced outside ENGINE; the agent transcribes and drafts around engine values, never generates them (04 §2.1). Agent presence is contextual — the draft beside its section, the memory note beside its field, the match beside its checklist item; no chat chrome.

## 7. Information hierarchy

1. Frame header: deal-scoped crumbs `Covenant / {Loan} / {Period} / Composer` (altitude gate, 08 §2 — the period crumb is a switcher with status dots) + period status chip + `PackageRevisionChip` (rev + short hash) + the deadline readout for this period's governing due-rule ("due {date} · FYE+120d per §8.02(b)(2)" — computed provenance, Calendar owns the deadline).
2. Decision/status summary: readiness summary ("3 of 5 reasons green") top of the right rail; section-status counts atop the left list.
3. Primary work region: the center section workspace (transcription / narrative / attachments per selected section).
4. Secondary context: what-changed-since-prior-package summary (right rail, below readiness).
5. Evidence/proof: summonable Evidence pane — any chip/figure traces its chain beside the claim (never a modal).
6. Actions: "Proceed to certify" (right rail CTA, disabled-with-reasons); per-section approve/answer; export name control.
7. Activity/history: this package's quiet-log slice, collapsed at the rail's foot.

Absent by design: charts (nothing here out-encodes text); any editable numeral; any "mark ready" manual override; generic chat box (agent presence is the draft beside its section, the memory note beside its field).

## 8. Page anatomy and regions

| Region | Purpose · content | Persistence | Interaction | Min width | Resize/collapse |
|---|---|---|---|---|---|
| HEADER (48px, frame) | Deal-scoped crumbs (loan switcher + period switcher per 08 §2) · period status chip · PackageRevisionChip · deadline readout with due-rule provenance | persistent | crumb switchers; hash chip click → full hash + cert state | — | frame-owned; never collapses |
| LEFT — section list (280px) | The package's map: derived sections, each row = icon (kind) + title + status chip (complete / needs-input / drafted-awaiting-approval / blocked) + deliverable cite on hover (e.g. "§8.02(b)(3)") | pinned | click/J-K select; drag to reorder where the form allows (drag handle only on reorderable groups); status chip filters | 240px; 56px icon-strip collapse (status dots keep meaning) | collapses to labeled icon strip below 1440 shell-expanded (§10); never silently hidden |
| CENTER — section workspace | The selected section's work: (a) form sections → transcription view: lender form fields laid out in the form's own order; mapped engine values as **locked chips**; free-text fields editable with memory notes; (b) narrative sections → draft editor with live refs; (c) attachment sections → checklist-driven picker of Documents links, version-pinned | persistent (work window 1) | per §12 | 720px | the one big pane; paper-adjacent max-width 880px column for form/narrative rendering, centered at wide viewports |
| RIGHT — readiness rail (320px) | COMPUTED readiness: the five reason rows, each named, stateful, linking to its blocker; below: what-changed-since-prior summary (field-level, from tie-out deltas); below: certify CTA; foot: quiet-log slice | pinned | reason row → owning surface with return path; changed-field row → compare in Review | 280px | collapses to a summary chip strip ("3/5 · view") when the Evidence split is summoned at ≤1727px; restored on dismiss |
| Evidence pane | Lit-row trace target for any traced figure | summonable (work window 2) | per cross-cutting/provenance contract | 560px | overlay at narrow widths; never covers the clicked figure |
| Modals | none native to Composer — certify/send ceremonies live on their own surfaces | — | — | — | typed-act modals are Certificate/Send territory (pane law) |

Pane justification: the section list is navigation-with-status (map + progress — the GitHub-Releases draft assembly checklist made spatial); the readiness rail must be co-visible with editing because every edit can flip a reason (gate law: the gate renders beside the blocked step); center is the single big work pane. Evidence summons as the second work window only on demand — max-2 law respected.

## 9. Co-visibility matrix

| Datum A | Datum B | Simultaneous? | Decision enabled | Default topology | Fallback |
|---|---|---:|---|---|---|
| Form field (locked chip) | Its per-field lineage | on demand | "is this the right value from the right source?" | hover = chain summary card; click = Evidence pane lights source | overlay sheet <1280 |
| Narrative draft | The engine figures it cites | always (in-line) | approve prose knowing numbers are live | live refs render in the prose | — |
| Readiness reason | Its blocker | on demand | "what do I fix, where?" | link-out with return path | same |
| Section workspace | Section list + statuses | always | "what's next?" | left pinned rail | icon strip / tab |
| Attachment pin | The filed document + version | on demand | "is this the right file/version?" | preview via DocView renderer in Evidence slot | new-tab DocView |
| Package draft | Prior sealed package | on demand | "what changed?" | what-changed rows → Review's 50/50 compare (Review owns compare) | link-out |
| Free-text field | Its remembered answer + provenance | always when filled | "still true?" | memory note inline beside the field | — |
| Verdict chip (covenant section) | The requirement's definition text | on demand | "is this rendered per the loan's own definition?" | definition summonable beside the chip (05 §4 co-visibility row) | Evidence pane |
| Export name | The lender-scope memory entry + its provenance | on confirm | "is this still the convention?" | inline note on the control | Settings → Agent memory |

No pane exists merely because information exists: compare lives in Review; the Composer links to it.

## 10. Layouts and viewport behavior

| Viewport | Shell rail | Interior topology (px) | Evidence behavior |
|---|---|---|---|
| 1440px | collapsed 48 → 1392 work | 280 list · 792 center · 320 rail | summons as overlay pane docking right; readiness rail → chip strip while open |
| 1440px | expanded 240 → 1200 work | 56 icon-strip list (labeled) · 824 center · 320 rail | same |
| 1728px | expanded 240 → 1488 work | 280 · 888 center (880 paper column) · 320 | overlay or split; on split, rail → chip strip |
| 2048px | expanded 240 → 1808 work | 280 · 764 center · 764 evidence (split) · rail chip strip; without evidence: 280 · ~1200 center (880 column centered) · 320 | true split, both work windows ≥560 (max-2 law) |
| <1280 | icon rail 48 | single work window; list and readiness as labeled tabs ("Sections · 2 need input" / "Readiness 3/5") | overlay sheet |

Minimum viable: the frame's 1152×720 (08 §9); below, spine surfaces show the larger-window state.

Tab stacking/replacement rules: when the list and rail become tabs (<1280), they stack in a fixed order — Sections · Readiness · Activity — above the workspace; opening Evidence replaces the workspace (single window) with a persistent "back to {section}" affordance; tab badges carry the needs-input and red-reason counts so collapsed state loses no signal. Focus mode (`F`) at any width collapses both rails to strips; strips always render their counts — no state exists whose collapse hides a blocking condition.
- Focus behavior: selecting a section scrolls the workspace to top; F toggles a focus mode that collapses both rails to strips (labeled).
- Compare behavior: none native — changed-field rows route to Review compare.
- Proof/source behavior: trace summons Evidence beside; the lit row persists until dismissed (cross-cutting contract §2.3).
- No silent compression anywhere: every collapse is to a labeled strip/tab with state preserved.

## 11. Components and exact anatomy

Reuse: `CovenantShell` (frame), `CommandPalette`/`SearchPalette` (nav), `CountBadge` (needs-input counts — the ONE shared badge atom, snapshot ruling), Evidence panel + `<Figure>` + `traceToEvidence` (cross-cutting/provenance §2), DocView renderer (attachment preview), TestResult row grammar (from Actuals/Review), the existing `Composer` view as the adjusted host.

NEW components (in `src/components/covenant/composer/`):

- `SectionList` / `SectionRow`: kind icon (Lucide), title, status chip, deliverable-cite tooltip, drag handle (reorderable groups only), needs-input CountBadge per group.
- `FormTranscription`: renders the lender form's own field order; parts: `FieldRow` (label verbatim from the form template), `LockedChip` (engine value in Geist Mono `tabular-nums slashed-zero`; lock glyph; three-actor state grammar; wraps `<Figure>` so click traces), `FreeTextField` (editable; `MemoryNote` beside it: "learned {date} from {who} during {context}", inspect/correct inline), `ConfidenceNote` (low-confidence sources), `SignatureBlockPreview` (locked; "signed at certification").
- `NarrativeEditor`: prose editor whose numerals are `LiveRef` nodes — uneditable in place; parts: `LiveRef` (renders the engine value at view time; caret/selection treats it as an atom; attempting to type inside selects it and offers "Edit the owning value → {surface}"), edit-diff capture (tone/structure/content-fact classes → memory, per agent brief), version history with undo, Approve action (typed click, recorded).
- `AttachmentChecklist`: checklist-item rows (expected doc type ← requirement cite), `VersionPin` (docId + version chip; stale badge on replacement; re-pin action), refusal row for resident-level classes (law cited), "request missing" link → Holding.
- `ReadinessRail`: `ReadinessRow` × 5 — named reason, state (green check / red with count), blocker link with return path; `WhatChangedSummary` (field-level rows from tie-out deltas, each → Review compare); `CertifyCTA` (disabled-with-reasons; enabled state uses the accent family, never the certified-sheet hexes — those are the Certificate's paper); quiet-log foot.
- `ExportNameControl`: proposed name from lender-scope memory (evidence pattern: "{Property} {Statement} {MM.DD.YY}", SLOT-4 filenames); confirm-once; per-file names listed under the bundle name; F4-pending note where render is absent.
- `PackageRevisionChip`: rev + content-hash short form (full on click), frozen/void/seal banners.

Template-required anatomy accounted for:

- Table/data grid: the rent-schedule and statement transcription tables (uniform row heights, open-not-boxed; virtualized past ~150 rows) — reuse the register grid primitives.
- Filters/saved views: deliberately minimal — the status-chip filter on the section list only; no saved views (a package is one object, not a register).
- Chart/legend/readout: none (doctrine — absent by design, §7).
- Covenant strip / headroom readout: verdict + basis-badge rows reuse the TestResult row grammar from Actuals/Review; rendered read-only on covenant-relevant sections.
- Document viewer: DocView renderer inside the Evidence slot for attachment preview and prior-submission readback (Recreated-searchable, labeled derivative).
- Source inspector / evidence panel: the shared Evidence pane + lit-row contract (reuse; cross-cutting §2).
- Timeline/activity: quiet-log slice at the rail foot (filtered ActivityEvent view — one store, 07 §2).
- Gate/finding/review object: `ReadinessRow` is this surface's gate object; findings render as links to Review stops, never dispositionable here.
- Command action: palette entries ("Composer — {Loan} {Period}", "Proceed to certify" when enabled) via `CommandPalette` (reuse).
- Form/request: `FormTranscription` + waive-request routing to Holding.
- Period/package selector: breadcrumb period switcher (08 §2 — the period crumb is a selector with status dots; reuse) + `PackageRevisionChip` for revision identity.
- Certify control: `CertifyCTA` — label "Proceed to certify", enabled state on the accent family, disabled state renders the red reason count and never a bare disabled button; the typed ceremony itself is the Certificate's (R4 ceremony-budget: zero heavy ceremony here).
- Send control: absent by design (Send & Record brief).
- Empty/error/recovery object: skeleton section list (empty), fail-closed template card (blocked), render-pending card (F4), stale/void banners with cause and next action (recovery).

## 12. Interaction specification

- Selection: single section active; chips and refs focusable/selectable as atoms.
- Hover: chip → lineage summary card (source doc, region, confirmer); memory note → provenance detail; section row → deliverable cite.
- Focus: visible focus ring (ruled tokens); focus order = list → workspace → rail.
- Keyboard: `J/K` section next/prev · `N/P` next/prev needs-input item · `Enter` open/edit field · `A` approve narrative (when focused) · `Esc` dismiss Evidence/focus mode · `⌘K` palette · `G P` here, `G V` Review, `G F` Certificate (08 §5) · `⌘↵` Proceed to certify when enabled.
- Editing and validation: free-text saves on blur with draft autosave; template validation (required form fields) feeds "sections present"; locked chips are never editable — the edit affordance routes to the owning value with a return breadcrumb ("Back to Composer · {section}").
- Narrative editing mechanics: the LiveRef is an atomic inline node — arrow keys skip over it, backspace/delete select it (second press does nothing but show the routing card), copy carries the rendered value + a provenance footnote, paste of external numerals into a draft is allowed as plain prose but lints in CI if it parses as a financial figure without a `value_id` (cross-cutting §3.6). Every human edit to a draft captures a diff event classed tone / structure / content-fact; content-fact edits additionally prompt "should the analyst remember this?" — accepted ones write MemoryEntries with the edit event as provenance (agent brief, LEARNING).
- Approval mechanics: Approve is per-section, recorded with actor + draft version; a figure re-render after approval (upstream change) automatically demotes the section to drafted-awaiting-approval with the change named — approval binds to content, not to the section object (R4: GitHub's diff-state approval dismissal).
- Bulk action: approve-all is deliberately absent for narratives (each approval is a per-section act); attachment pins confirm individually.
- Undo/recovery: narrative versions with undo; pin changes reversible pre-freeze; no undo past certify/send by design (§5).
- Sorting/filtering: status-chip filter on the section list; no other sorting (form order governs).
- Drill-down and return: every readiness/changed-field/lineage link carries a return path; Esc closes summoned panes before navigating (07 §3).
- Source-linked selection: click any chip/LiveRef → `traceToEvidence` lights the exact source line, which stays lit (lit-row law); wrong-row defect fixed upstream (U1-F1, cross-cutting §2.4).
- Save/persistence: continuous draft persistence per revision; revision increments on content change; the hash chip updates live.
- Collaboration: none in v1 beyond roles; the quiet log is the shared record.

## 13. Visual craft direction

- Typography: UI face per ruled system; all financial figures Geist Mono `tabular-nums slashed-zero` (Review-9 colors-only ruling keeps Geist Mono). Form labels render verbatim from the template at body size; section titles one step up; deliverable cites at caption size in a mid-rung gray.
- Locked chips: mono figure on a rung-2 ground with a hairline (rung-4), lock glyph at 12px, three-actor grammar per 06 §7 (inferred-confirmed = provenance underline; certified state only after freeze). No new colors — chip states use the gray ladder + accent family.
- LiveRefs in prose: same mono treatment inline, dotted provenance underline; hover raises the lineage card; never bolded like editorial emphasis — the treatment says "instrument, not prose."
- Readiness rows: green-check rows recede (mid-gray text, small check); blocking rows lead (full-contrast text + red-family severity per the ruled status vocabulary); counts in `CountBadge`.
- Spacing rhythm: 8px base grid; section workspace uses the paper-adjacent 880px column with generous margins at wide viewports; density otherwise matches the register standard (uniform rows, open-not-boxed).
- Dividers/elevation: hairline dividers on the ladder; the rails sit on the frame ground, workspace one rung lighter; no boxed cards.
- Semantic color: accent #7189FF family for links, live-ref affordances, the enabled CTA (hover #8EA1FF, active #6078F4, tint #A9B5FF for the lit band); severity colors only from the ruled status vocabulary. The certified-sheet blue-violet hexes do **not** paint here — they are the Certificate's paper; Composer signals "certified/frozen" with the banner + hash chip on standard tokens.
- Focus/selected/hover: ruled selected-state tokens (Ruling-J boundary: Covenant's own control layer).
- Charts: none (doctrine: nothing here out-encodes text).
- Motion: rail collapse and Evidence summon at 150–200ms ease; reduced-motion swaps to instant with state announced; the lit-row band never animates its persistence.
- Long-session ergonomics: this surface targets short sessions; the needs-input walk (`N`) makes them shorter.

The three-actor grammar applied to Composer atoms (06 §7, consumed not restyled):

| Atom | source | inferred (proposed) | inferred (confirmed) | certified/frozen |
|---|---|---|---|---|
| LockedChip | n/a — chips are engine outputs over confirmed inputs | dotted provenance underline + confidence (an unconfirmed input upstream) — blocks "confirmations current" | solid provenance underline; confirmer stamp on hover | rendered inside the frozen banner context; hash chip in header |
| LiveRef (in prose) | n/a | a draft never renders an unconfirmed value — the draft flags the gap instead | dotted-underline mono atom, hover lineage card | same atom inside the frozen read-only draft; trace still works |
| FreeTextField | — | memory pre-fill with MemoryNote (provenance aged) | answered/approved this period | read-only text |
| Attachment pin | links to Original bytes + hash (Documents) | proposed-pin (agent match, unconfirmed) | pinned (human-confirmed; version chip) | pinned + frozen; any re-pin is the void path |

## 14. Benchmark research and synthesis

| Product | Limited role | Official evidence | Exact pattern to take | Covenant adaptation | What to reject | Why this beats alternatives |
|---|---|---|---|---|---|---|
| GitHub Releases | draft→publish→immutable state machine | docs.github.com/en/repositories/releasing-projects-on-github/about-releases · …/managing-releases-in-a-repository · docs.github.com/en/code-security/concepts/supply-chain-security/immutable-releases (R4, checked 2026-08-08) | "Create the release as a draft. Attach all associated assets… Publish." — assemble fully mutable, one act freezes; lock badge on the artifact | Package draft mutable through composition; certify freezes content; hash = the attestation a lender could verify | "Title/notes still editable after publish" — after certify, even narrative edits void (lender packages are stricter than release notes) | The only mainstream product whose assembly-then-freeze ceremony matches package composition exactly |
| GitHub branch protection | readiness as required checks | docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches · docs.github.com/en/pull-requests/reference/status-checks (R4) | Every blocked state explains itself: named checks, granular states, each linking to detail; even privileged users stay gated | The five readiness reasons as named linked rows; disabled CTA never bare; no admin bypass | Bypass lists and admin exemptions | The merge-box is the most battle-tested "computed gate with named reasons" UI in existence |
| Vanta | evidence package assembly for an external reviewer | help.vanta.com/en/articles/11345844-how-will-your-auditor-use-vanta · …/11345427-audit-evidence · …/11345529-automated-test-evidence (R3) | Evidence freshness drives state ("Needs Update"); approved documents flip related tests; exports are an "immutable copy… with full metadata" | Attachment staleness (version-pinned, flagged on replacement) feeds "confirmations current"; the frozen package carries its evidence links | Framework/control indirection — Covenant's spine is the credit agreement, one layer | Closest live product for "assemble a package an outside party will scrutinize" |
| Ironclad | record = document + properties; verification views | support.ironcladapp.com/hc/en-us/articles/12277809086615-Smart-Import-and-AI-Suggestions-Overview · …/12277440413207-Manage-Records (R2) | "Needs verification" as a persistent view; AI fills usable-but-flagged until verified; verification teaches the system | Section statuses (needs-input / drafted-awaiting-approval) as the composer's standing lens; free-text edits teach lender-scope memory | Verification detached from the source page (emailed-CSV review) | The strongest records-repository precedent for proposed-vs-verified state on assembled artifacts |
| DataSnipper | self-contained evidence pack | knowledge.datasnipper.com/how-to-extract-data-from-a-document · …/en/articles/714290-how-to-overview-all-snips-in-a-document (R3) | Cross-references and referenced files stored **in** the workbook — the deliverable travels with its provenance | The composed package carries its lineage refs; every transcribed figure traces without the workspace | Excel as host; free-form snipping | Proves the "deliverable = figures + their traces, one object" contract Composer's chips implement |
| Numeric | AI-drafted prose with human sign-off | numeric.io/product/variance-analysis · numeric.io/product/close (R3) | AI drafts variance explanations from underlying detail; the accountant edits and approves; sign-off stays on the record | Narrative sections: agent drafts around live engine refs; approval is a recorded act; materiality logic decides which changes demand narrative | AI-drafted numbers as content — figures interpolate by reference only, never generation (04 §2.1) | The closest financial-close precedent for draft-approve narrative flow beside deterministic figures |
| NN/g (anti-pattern) | ceremony budget | nngroup.com/articles/confirmation-dialog/ (R4) | Nonstandard confirmations only for rare, dangerous acts | Zero dialogs in Composer — approvals are inline acts; the two heavy ceremonies live on Certificate/Send | Routine confirm dialogs | Keeps Composer frictionless so the certify ceremony retains meaning |

Anti-pattern (ruled in DIRECTIONS): free-form document editors (Google-Docs-class) as package composers — anywhere prose and numerals are equally editable, numbers drift from engine truth and the trust hierarchy dies. Composer's editor makes the numeral a different species from the word.

Synthesis: Composer is original because no benchmark composes a **lender-shaped regulatory deliverable from deterministic engine values under a computed gate**: Releases contributes the draft→freeze spine, branch protection the named-reasons gate, Vanta/Ironclad the evidence-freshness and verification grammar, DataSnipper the self-contained provenance, Numeric the draft-approve narrative rhythm — and the domain shape (the SLOT-4 exemplar: statements + rent schedule + questionnaire + certification + attachments) comes from the real submitted package, not from any of them.

## 15. Domain references

Terminology and expected-deliverable semantics: the JLL servicer forms themselves (SLOT-4/5 — questionnaire, annual certification; the primary domain authority for what a servicer package actually asks), Fannie Mae Form 6001.NR §8.02(b) deliverable language (SLOT-3 — the contractual source of section names, cadences, and the rent-schedule field list), Finley-class deliverables tabs (docs.finleycms.com/core-capabilities/deliverables, R3) for "what's owed to lenders on cadence" vocabulary, Avalara Managed Returns (help.avalara.com, R4) for the review-artifact-before-approval rhythm on regulatory submissions, and the BankStride/nCino tickler model as the named anti-pattern (upload-chute compliance with no computation, R3). Domain authority does not equal visual authority: none of these products' UI is copied, and covenant semantics — every threshold, cadence, field list, and definition — come from the loan documents and Terry, never from a referenced product. Where this brief names form content (the 11 questions, the 3 certification items, the rent-schedule fields), it quotes the evidence kit; any other lender's form enters the product only through its own template registry entry, never by analogy to JLL's.

## 16. Accessibility, performance, and safety

- WCAG contrast on all text/chips per the ruled ladder; focus visible on every interactive atom including LockedChip and LiveRef.
- Keyboard completeness: the entire compose-approve-navigate loop works without a pointer (§12); traces are keyboard-operable (focus chip → Enter → Evidence focus on lit row → Esc back).
- Screen-reader semantics: LockedChip announces "locked value, {value}, from {source}, {state}"; LiveRef announces "live figure, {value}, edit routes to {surface}"; readiness rows are a list with per-row state announced; the disabled CTA reads its reasons.
- Large data: statement transcription virtualizes past ~150 rows (the T-12 scale, evidence); section switching keeps scroll state.
- Loading/latency: per-section skeletons; trace-to-light under 200ms with document open (cross-cutting §6).
- Destructive confirmation: nothing destructive lives here; unpin/unapprove are reversible pre-freeze.
- Certify/send safety: this surface can never mutate a frozen package silently — edits against frozen state surface the void path explicitly; approved-bytes and the ceremonies belong to Certificate/Send; readiness is engine-computed with no manual override.
- Source immutability: attachments are links to Documents originals (immutable bytes + hash); Composer stores pins, never copies.
- Auditability: every approval, answer, pin, reorder, and naming confirmation is an ActivityEvent with actor + refs; package revisions and hashes are reconstructable per rev; a sealed period's Composer view re-renders any historical rev read-only with its traces intact (seal-not-wipe).
- Honest failure surfaces: engine unavailable → "readiness unavailable" (never fixture-green); template missing → fail-closed authoring card; resolver missing (occupancy, gap 7) → blocked card naming the missing reader. Fixture-era plausible placeholders are the retired anti-pattern (04 §2.5).

## 17. Acceptance tests and fixtures

Fixtures: **CAL-FYE2018** (Calloway Park annual: SLOT-1 T-12, SLOT-2 roll derivative, SLOT-3 agreement, SLOT-4 package shape); **CAL-Q2/Q3-2018** (quarterly: SLOT-5 questionnaires); **BEX-Q** (Bexley canon: monitored 90% floor, 268/301 = 89.04% shortfall).

1. Section derivation (CAL-FYE2018): confirmed RequirementRecords + JLL template derive exactly: statements group, rent schedule, questionnaire, annual certification, ENERGY STAR rider section, attachments (trial balance, vendor aging, loan/interest/escrow, ownership structure). Removing the Form 6241 rider from the fixture removes the ENERGY STAR section.
2. Quarterly derivation (CAL-Q2-2018): YTD income statement + rent schedule + questionnaire only; no annual-only sections leak.
3. Transcription lock: every engine-fillable questionnaire field renders a LockedChip; DOM-level edit attempts cannot alter the value; the edit affordance routes to the owning surface and returns.
4. Lineage: clicking the occupancy chip lights the roll-derivative aggregate row and the chain shows the 322-rows→301-units dedup step (06 chain C); clicking a statement line lights its T-12 source cell (06 chain A: Total OpEx $1,686,050, NOI $1,218,877 tie).
5. Narrative safety: a draft cannot render a numeral without a `value_id` (interpolation API unit test, cross-cutting §6.5); editing prose around a LiveRef leaves the figure atomic; upstream value change re-renders the figure inside the approved draft AND flips the draft to re-approval (content changed).
6. Readiness honesty (BEX-Q): with an undispositioned shortfall finding, "tests verdicted" renders red linking to the Review stop; disposition flips it green without touching Composer state. Each of the five reasons has a fixture that blocks it and a linked row that reaches the blocker in one click.
7. No decorative readiness: with the engine stubbed out, the rail renders "readiness unavailable" — never a green state from fixture strings (the gap-4 regression test).
8. Draft→freeze: certify on Certificate freezes this package; every edit affordance disappears; a subsequent COA-mapping correction fires the void event, returns the package to draft, and the void reason names the changed input (03 §2).
9. Attachment pinning: replacing the trial balance in Documents flags the pin stale with a diff link; re-pin pre-certify updates rev + hash; post-certify it voids. Resident-level class (deposit audit) attachment attempt renders the refusal with the rule text.
10. Memory: the management-fee explanation answered in Q2 pre-fills Q3 with "learned {date} from {who}"; zero re-asks across CAL-Q2→Q3 with unchanged facts (ask-once acceptance).
11. Export naming: first export proposes "{Property} {Statement} {MM.DD.YY}" from the lender-scope entry; user correction versions the memory; next period proposes the corrected form. Render-pending (F4) states appear wherever a PDF/XLSX artifact would — no fake download.
12. Viewports: 1440 (both rail modes), 1728, 2048 (Evidence split, both windows ≥560, readiness chip-strip), <1280 tabs — no silent compression at any breakpoint.
13. Keyboard: full loop — `G P` → `N` to first needs-input → answer → `A` approve narrative → `⌘↵` at readiness true lands on Certificate; screen-reader announcements per §16.
14. Vocabulary: BEX-Q renders "shortfall" with the monitored basis badge everywhere on this surface; the string "breach" is unreachable for monitored fixtures (structural test).
15. Workflow fixture, full period (CAL-Q2-2018, arrival to sealed): documents arrive → recognition/holding advance → extraction/normalization/computation complete → Composer derives sections and fills chips → human answers the needs-input walk → readiness all-green → certify on Certificate freezes rev 1 → send seals → the sealed Composer view renders read-only with every trace still working (seal-not-wipe). The whole pass through this surface takes under 5 minutes for the fixture operator.
16. Data integrity: re-rendering the same rev produces a byte-identical content hash (hash stability); any content change (a re-pin, an edited answer, an approved narrative edit) increments rev and changes the hash; the hash chip always matches the engine's current computation.
17. What-changed honesty: with a zero-delta fixture the summary renders the one-line clean sentence; with the CAL-FYE2018 fixture modified on one normalized line, exactly that field appears as a changed row with cause link.
18. Resident-data law: the rent-schedule section renders only anonymized labels; a fixture attachment of class deposit-audit is refused with the rule text; no outbound bundle manifest can include a resident-level class (structural test on the bundle builder).
19. Accessibility: axe-clean at all breakpoints; LockedChip/LiveRef announcements per §16 verified with a screen reader script; the readiness rail reads as a list with states; keyboard-only operator completes test 15's Composer segment.
20. Section-status rollup: answering the last needs-input field in the questionnaire flips its SectionRow to complete, decrements the list-header CountBadge, and flips "sections present" green in the same render pass — one source of truth, three projections.
21. Reorder constraint: drag handles render only on the template's reorderable groups; a form-fixed section (the questionnaire's field order) exposes no reorder affordance anywhere, including keyboard.
22. F4 honesty: with rendering absent, the export control lists render-pending rows for form/narrative sections and real files for pinned attachments; no code path produces a placeholder PDF (negative test on the bundle builder).
23. Deadline provenance: the header readout renders "due {date} · FYE+120d per §8.02(b)(2)" from the requirement's due-rule for CAL-FYE2018 — computed, not hand-labeled; clicking opens the Calendar row.
24. Benchmark challenger review: walk the surface against the §14 take/reject rows; any regression to a bare disabled button, an editable numeral, a manual readiness override, or a copied document list fails.

## 18. Build plan

- Dependencies: F2 persistence (packages, sections, pins, revisions — today's five migrations are mostly contract-only, snapshot); the orchestration spine (V-series) for period status; cross-cutting provenance contract (Figure/traceToEvidence) landed first; lender-form template registry (NEW — seeded from the JLL evidence forms); memory store (agent brief); **F4 PDF/XLSX rendering** (gap 4) for the outbound artifact — until it lands, the composed package renders on-screen and export controls state "render pending (F4)" honestly; the send transport meanwhile delivers filed source attachments only.

The F4 dependency, stated honestly on-surface and in sequencing: Composer's transcription, narratives, readiness, and pins are all buildable and testable before F4 — but the outbound artifact for form/narrative sections does not exist until F4 renders them. The interim contract: (a) the export control shows the bundle manifest with render-pending rows for unrendered sections, (b) readiness stays a composition gate (its five reasons do not include "artifact rendered" — that is a send-side concern the Send brief owns), (c) no placeholder PDF is ever generated (fail-closed honesty; 04 §2.5). This ordering is deliberate: computed readiness is the gap-4 item this brief retires; rendering is the gap-4 item F4 retires.

- Foundation work: template registry schema + the CAL/JLL seed; section-derivation function (template ∪ deliverables) in `src/lib/covenant/` with the rider fixture test; readiness reasons[] API on Package (engine); field-map validator.
- Components first: ReadinessRail (kills decorative readiness — the highest-leverage fix), then FormTranscription with LockedChip (wraps `<Figure>`), then NarrativeEditor/LiveRef, then AttachmentChecklist/VersionPin, then SectionList reorder, then ExportNameControl.
- Build order rationale: ReadinessRail first because it converts the surface's central lie (decorative readiness) into the product's central promise (computed gate) with the smallest component; transcription second because it is where the SLOT-4 evidence shape becomes visible product; narratives third because LiveRef depends on the interpolation API landing in the agent lane.
- Vertical slice (send-vertical pattern): one route `/covenant/[loanId]/[period]/composer`, CAL-Q2-2018 quarterly fixture end to end — derived sections, engine-filled questionnaire chips with working traces, computed readiness gating the CTA, into the existing certificate route and the REAL send gate.
- Migration from fixture data: the current Composer view's fixture arrays retire against `book.ts` decommission; CI counts ref-less `<Figure>`s toward the burndown (cross-cutting §5).
- Rollout/feature flag: `composer-computed-readiness` flag; decorative readiness path deleted (not flagged off) once the slice passes — the honest state must not be optional.
- Proof artifacts: recorded walkthrough of test 4's lineage chain (chip → lit source cell on the real T-12); the readiness fixture matrix (test 6 — one screenshot per blocked reason with its link target); the void-on-change capture (test 8); the derived-section diff for the rider fixture (test 1); the CI burndown count of ref-less figures at slice completion.
- Rollout: behind `composer-computed-readiness` for the slice period only; the deep route ships with the `(covenant)` route-group wave; `/review-room`'s Composer view continues mounting the same component (one component, two mounts) until the route wave completes.
- Final gate: `ADJUST` confirmed — chassis and framing kept; readiness, locking, live refs, pins, and naming memory are the adjustments; re-verdict after the vertical slice against tests 1–24.
