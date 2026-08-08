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

### The questionnaire as transcription target (evidence: SLOT-4/5)

The JLL Property Questionnaire's 11 questions split structurally: **engine-fillable** (occupancy by month-end dates — the occupancy chain's package terminus, 06 §4; down units from the roll status taxonomy; other-income breakdown and non-recurring expenses from normalized lines; management fee % from the mapped expense line, with the 4–6% band explanation as a free-text follow-up; 12-months-of-operations check from period coverage) and **human-answerable** (contact changes, sub-market job losses, casualty/crime, capital improvements across the form's 15 categories with expense-line separation, subordinate financing) — the latter pre-filled from lender-scope memory where previously answered, else `needs-input`. The signed certification block renders as a locked preview: signature happens at the Certificate, never here. Executed questionnaires in evidence are flattened scans with zero text layer — the Recreated-searchable artifact exists for reading them back (kit evidence; cross-cutting/documents law).

### Resident-data law, enforced at composition

Rent schedule sections build only from the anonymized roll derivative; the attachment picker structurally refuses resident-level document classes (deposit audits, delinquency lists) for outbound sections, rendering the refusal with the rule ("resident info NEVER travels outward" — Terry ruling 2026-08-07) — not a silent absence, an explained one.

## 5. State machine and exceptions

Package draft lifecycle (GitHub-Releases shape — assemble fully in a mutable draft; one act freezes; R4 research):

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

| Lane | On this surface |
|---|---|
| ENGINE | Readiness computation (sections present, tests verdicted, tie-out clean, confirmations current, checklist complete/waived) with reasons[]; field-map validation against the template; live-ref re-render of every interpolated figure at view time (no stale numeral can render — 04 §2.1); content-hash per revision; void-on-change enforcement |
| AGENT | Assemble sections; transcribe engine values onto the lender form per field map; draft narratives interpolating engine refs; propose free-text answers from memory with provenance; match checklist items to filed documents and propose pins; propose the export name from lender convention |
| HUMAN | Approve/edit narratives; answer needs-input fields; include/exclude optional sections; confirm attachment pins and versions; confirm naming once per lender; final content decisions |
| GATE | Readiness false → certify CTA disabled-with-reasons, every reason a named linked row (this surface renders the gate; the certify act itself lives on Certificate) |
| QUIET LOG | Every assembly revision, transcription fill, draft update, pin, and naming reuse ("pre-filled from memory {ref}") — discoverable, never pushed |

Lane invariant honored: no shipping number is produced outside ENGINE; the agent transcribes and drafts around engine values, never generates them (04 §2.1).

## 7. Information hierarchy

1. Frame header: deal-scoped crumbs `Covenant / {Loan} / {Period} / Composer` (altitude gate, 08 §2) + period status chip.
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

No pane exists merely because information exists: compare lives in Review; the Composer links to it.

## 10. Layouts and viewport behavior

- 1440px (shell rail collapsed 48px → 1392 work): 280 list · 792 center · 320 rail. Shell rail expanded (240 → 1200 work): list collapses to 56px icon strip (labeled tooltip), 824 center, 320 rail — announced, not silent.
- 1728px (rail 240 → 1488 work): 280 · 888 center (880 paper column) · 320.
- 2048px (rail 240 → 1808 work): 280 · center grows; Evidence split fits fully: 280 list + 764 center + 764 evidence, readiness rail as chip strip until dismissed — both work windows ≥560.
- Narrow/compact (<1280): single work window; section list and readiness become labeled tabs above the workspace ("Sections · 2 need input" / "Readiness 3/5"); Evidence is an overlay sheet. Minimum viable: the frame's 1152×720 (08 §9); below, spine surfaces show the larger-window state.
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

Tables: uniform row heights, open-not-boxed. Charts: none. Icons: Lucide only.

## 12. Interaction specification

- Selection: single section active; chips and refs focusable/selectable as atoms.
- Hover: chip → lineage summary card (source doc, region, confirmer); memory note → provenance detail; section row → deliverable cite.
- Focus: visible focus ring (ruled tokens); focus order = list → workspace → rail.
- Keyboard: `J/K` section next/prev · `N/P` next/prev needs-input item · `Enter` open/edit field · `A` approve narrative (when focused) · `Esc` dismiss Evidence/focus mode · `⌘K` palette · `G P` here, `G V` Review, `G F` Certificate (08 §5) · `⌘↵` Proceed to certify when enabled.
- Editing and validation: free-text saves on blur with draft autosave; template validation (required form fields) feeds "sections present"; locked chips are never editable — the edit affordance routes to the owning value with a return breadcrumb ("Back to Composer · {section}").
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

Terminology and expected-deliverable semantics: the JLL servicer forms themselves (SLOT-4/5 — questionnaire, annual certification), Fannie Mae Form 6001.NR §8.02(b) deliverable language (SLOT-3), Finley-class deliverables tabs (docs.finleycms.com/core-capabilities/deliverables, R3) for "what's owed to lenders on cadence" vocabulary, and the BankStride/nCino tickler model as the named anti-pattern (upload-chute compliance with no computation, R3). Domain authority does not equal visual authority: none of these products' UI is copied, and covenant semantics — every threshold, cadence, field list, and definition — come from the loan documents and Terry, never from a referenced product.

## 16. Accessibility, performance, and safety

- WCAG contrast on all text/chips per the ruled ladder; focus visible on every interactive atom including LockedChip and LiveRef.
- Keyboard completeness: the entire compose-approve-navigate loop works without a pointer (§12); traces are keyboard-operable (focus chip → Enter → Evidence focus on lit row → Esc back).
- Screen-reader semantics: LockedChip announces "locked value, {value}, from {source}, {state}"; LiveRef announces "live figure, {value}, edit routes to {surface}"; readiness rows are a list with per-row state announced; the disabled CTA reads its reasons.
- Large data: statement transcription virtualizes past ~150 rows (the T-12 scale, evidence); section switching keeps scroll state.
- Loading/latency: per-section skeletons; trace-to-light under 200ms with document open (cross-cutting §6).
- Destructive confirmation: nothing destructive lives here; unpin/unapprove are reversible pre-freeze.
- Certify/send safety: this surface can never mutate a frozen package silently — edits against frozen state surface the void path explicitly; approved-bytes and the ceremonies belong to Certificate/Send; readiness is engine-computed with no manual override.
- Source immutability: attachments are links to Documents originals (immutable bytes + hash); Composer stores pins, never copies.
- Auditability: every approval, answer, pin, reorder, and naming confirmation is an ActivityEvent with actor + refs; package revisions and hashes are reconstructable per rev.

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
15. Benchmark challenger review: walk the surface against the §14 take/reject rows; any regression to a bare disabled button, an editable numeral, or a copied document list fails.

## 18. Build plan

- Dependencies: F2 persistence (packages, sections, pins, revisions — today's five migrations are mostly contract-only, snapshot); the orchestration spine (V-series) for period status; cross-cutting provenance contract (Figure/traceToEvidence) landed first; lender-form template registry (NEW — seeded from the JLL evidence forms); memory store (agent brief); **F4 PDF/XLSX rendering** (gap 4) for the outbound artifact — until it lands, the composed package renders on-screen and export controls state "render pending (F4)" honestly; the send transport meanwhile delivers filed source attachments only.
- Foundation work: template registry schema + the CAL/JLL seed; section-derivation function (template ∪ deliverables) in `src/lib/covenant/` with the rider fixture test; readiness reasons[] API on Package (engine).
- Components first: ReadinessRail (kills decorative readiness — the highest-leverage fix), then FormTranscription with LockedChip (wraps `<Figure>`), then NarrativeEditor/LiveRef, then AttachmentChecklist/VersionPin, then SectionList reorder, then ExportNameControl.
- Vertical slice (send-vertical pattern): one route `/covenant/[loanId]/[period]/composer`, CAL-Q2-2018 quarterly fixture end to end — derived sections, engine-filled questionnaire chips with working traces, computed readiness gating the CTA, into the existing certificate route and the REAL send gate.
- Migration from fixture data: the current Composer view's fixture arrays retire against `book.ts` decommission; CI counts ref-less `<Figure>`s toward the burndown (cross-cutting §5).
- Rollout/feature flag: `composer-computed-readiness` flag; decorative readiness path deleted (not flagged off) once the slice passes — the honest state must not be optional.
- Proof artifacts: recorded walkthrough of test 4's lineage chain; readiness fixture matrix (test 6); the void-on-change capture (test 8).
- Final gate: `ADJUST` confirmed — chassis and framing kept; readiness, locking, live refs, pins, and naming memory are the adjustments; re-verdict after the vertical slice against tests 1–15.
