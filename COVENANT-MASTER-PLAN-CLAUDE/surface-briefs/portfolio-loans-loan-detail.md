# PORTFOLIO / LOANS / LOAN DETAIL — the loan book register and the per-loan record

## 1. Identity and verdict

- Product layer: **portfolio floor** (inventory B3).
- Route/address in basis-v2: `/loans` (register) and `/loans/[loanId]` (record), new deep routes in the `(covenant)` group mounting `CovenantShell` (08-GLOBAL-FRAME-SHELL-PLAN §1); today both live as Loans views inside `/review-room` (snapshot §3).
- Current build state: `FIXTURE` — with the ONE runtime engine wire in the whole UI: `assembleTrendSeries` feeding demo readings through the real trend assembler on the Loans trends view (snapshot §3). Every other figure is a `book.ts` demo string.
- Existing-surface verdict: `ADJUST` — preserve the register/record IA and **preserve-and-extend the trend wire**; rewire everything else from fixture to engine (DIRECTIONS: portfolio-loans-loan-detail).
- Research tier: **C** (deepest).
- Primary users/roles: owner (2–50 loans) scanning the book and drilling one loan; PMC preparer working a client-scoped subset; reviewer (read-only) inspecting a loan's history.
- Frequency/session: several times weekly at mid-cycle ("where does the book stand?"); daily near deadlines; the record is the reference surface opened whenever a loan is discussed. Sessions 1–10 minutes; not a deadline-pressure surface itself — it routes to the spine surfaces that are.
- Error cost: indirect but real — a misrendered verdict basis here (a monitored miss dressed as "breach", or the reverse) misinforms the conversation that precedes certification. The terminal error (wrong number certified and sent) is guarded downstream; this surface's own terminal error is **misstating the book's risk posture** (vocabulary law, ABSOLUTE LAW 3).
- Success criterion: any loan's terms, obligations, verdicts, trend, collateral graph, and period history are reachable in ≤2 clicks from `/loans`, every figure provenance-carrying, with zero fixture strings and zero basis-vocabulary violations.

## 2. User job and decisions

- Primary job-to-be-done: know the state of every loan in the book, and hold the complete, provable record of any single loan — terms, schedule, verdicts, history — without opening a filing cabinet.
- Decisions made here:
  - which loan needs attention next (register scan → verdict chips + next deadline);
  - whether a verdict warrants action (covenant strip: signed headroom in both denominations → open Review or acknowledge the finding);
  - whether a term/schedule entry is trusted (provenance click → source region; if wrong → route to amend flow);
  - saved-view curation (what the team's default lens on the book is);
  - star/unstar (personal pinning to the rail Starred group).
- Questions the register must answer in scan order:
  1. How many loans, and how many need me right now?
  2. Which rows carry shortfall/breach verdicts or deadline pressure?
  3. What is next due across the filtered book, and when?
  4. Which loans are live vs pipeline (segment answer, not a separate place)?
  5. Reference facts per row (lender, program, UPB, rate, maturity) — present but never competing with 1–3.
- Questions the record must answer in scan order:
  1. What is this loan (identity, terms, who services it)?
  2. Where does the current period stand, and is anything my move?
  3. Are its tests and monitored thresholds passing, by how much, and on what basis (covenanted vs monitored)?
  4. How has that trended across sealed periods?
  5. What collateral secures it, and does it share collateral with another loan?
  6. What is the paper trail (schedule, periods, documents, correspondence, activity)?
- What the user should NOT have to decide here: no dispositions (Review Room owns them), no confirmations (Extraction & Confirmation owns them), no certify/send (gates own them), no period-status mutation (the orchestration spine owns transitions — 02 §3). This surface reads and routes.
- Entry paths: rail `Loans` (`G L`), Home your-move item → loan context, palette object result ("Bexley"), Calendar deadline → period → loan crumb, Reports row → loan link, trend mark or finding elsewhere citing a loan.
- Exit paths: register row → record; record period row → `/covenant/[loanId]/[period]/{review|composer|certificate|actuals}`; schedule row → `/loans/[loanId]/setup` (amend); document row → `/documents/[docId]`; correspondence summary → `/loans/[loanId]/correspondence`; any figure → Evidence (lit-row) → DocView.
- Completion/advancement conditions: none owned — this floor never advances a period; it exposes the period's own advancement state (chips reading the spine's ledger) and links to the surface that can. The two acts that do complete here are personal: a saved view exists and is shared; a loan is activated (pipeline → live), which lets the engine begin scheduling its cadence occurrences (03 §5).

## 3. Object and ownership model

| Object/action/status | Owned here? | Summary/link elsewhere | Address/identity | Notes |
|---|---:|---|---|---|
| Loan (master record: lender/servicer, program+form+riders, UPB, rate structure, IO/am schedule, maturity, reporting-only flag, `supplemental_of?`) | **YES** | Actuals reads terms for debt service; Certificate prints identity | `loanId` | Every term field carries a source-region ref to the loan documents (gap 11; 02 §2) |
| Pledge (collateral relation, role: primary / pool member) | **YES** (Loan Detail PROPERTIES tab) | Aggregation surfaces consume for dedupe | `(loanId, propertyId)` | The ONLY home of the collateral relation (02 §1) |
| Property (outward name, address, units, PMS identity, COA mapping ref) | **YES** (via PROPERTIES tab) | Rent Roll teaser links; Actuals normalizes per property | `propertyId` | Real names never print (ABSOLUTE LAW 9) |
| Sponsor, Portfolio (human grouping) | **YES** (portfolio floor) | Filters/saved views consume | `sponsorId`, `portfolioId` | Portfolio is non-exclusive; never a security boundary (02 §1) |
| SavedView instances for `/loans` | **YES** (each register owns its instances) | Shared mechanism app-wide | `viewId` | One mechanism, no reinvention (02 §2) |
| Star/pin state | **YES** (personal) | Rail Starred group renders | per user | One pinning mechanism (08 §3) |
| RequirementRecord (confirmed schedule) | NO — Extraction & Confirmation | SCHEDULE tab renders read-only; "Amend" routes out | `reqId` | 02 §3 |
| TestResult / verdicts / headroom | NO — Actuals & Computation (engine) | Covenant strip + register chips render engine rows | `(loanId, period, reqId)` | Single source kills the U-audit duplication (07 §2) |
| ReportingPeriod status + lifecycle | NO — orchestration spine | PERIODS tab renders; period chips everywhere | `(loanId, period)` | No surface mutates status (02 §3) |
| CertificationRecord / SendRecord | NO — Certificate / Send & Record | PERIODS tab shows chips + links; "last sent" summary | `certId`, `sendId` | Reports owns the book-wide audit register |
| Document register | NO — Documents | DOCUMENTS tab lists per-loan links only | `docId` | No double-homing |
| CorrespondenceThread | NO — Lender Q&A | CORRESPONDENCE tab: summaries + links | `threadId` | |
| Deadlines/escalation | NO — Calendar/Obligations | Next-deadline column + record header chip | `noteId` | |
| Finding | NO — agent layer | Per-loan findings render on OVERVIEW (02 §2) | `findingId` | Disposition happens where raised (Review/Home) |
| ActivityEvent | NO — one cross-cutting store | ACTIVITY tab = filtered view of the one store | `eventId` | Kills the contradicting-feeds defect (07 §2) |

No-double-homing boundary, stated:
- this surface owns the **loan/pledge/property identity graph** (plus sponsor/portfolio groupings, register saved views, stars) and nothing that moves a period;
- everything period-, test-, requirement-, document-, or thread-shaped renders here as a **summary with a link** to its sole owner (02 §3 rule) — same content, never a second editable home;
- the reverse boundary holds too: other surfaces (Actuals, Certificate, Composer) read loan terms from the record owned here and never re-author them;
- the ACTIVITY tab and PERIODS event timelines are projections of the one ActivityEvent store — this surface stores no history of its own.

## 4. Data and semantic model

- Source facts: loan documents (SLOT-3-class: executed agreement + riders — evidence: 6001.NR + Forms 6220/6241), servicer statements where present; the immutable Original artifacts (bytes + hash) behind every term field.
- Extracted values awaiting confirmation: proposed loan terms and requirement records from loan-doc extraction (first-time setup, or CSV-imported loans in "setup needed" state) — rendered with confidence + dotted provenance underline, never bold-final (06 §7); they may not feed tests, aggregates, or the register's verdict chips until confirmed.
- Confirmed values: the loan master record's term fields (confirmed at setup, Gate 1); the confirmed RequirementRecord set (rendered on SCHEDULE); COA/occupancy definition pins referenced, not owned (05 §7.3).
- Deterministic outputs (the only figures this surface prints):
  - TestResult rows — verdict, signed headroom in both denominations, `basis_echo` (05 §3);
  - period status projections from the spine's ledger;
  - trend series assembled by `assembleTrendSeries` from sealed-period engine outputs (05 §5: series points link to their sealed periods);
  - register aggregates under the property-dedupe rule;
  - the computed your-move count scoped to loan/register (03 §4).
- Agent proposals/drafts: findings with drafted mitigant narratives (summarized here, dispositioned elsewhere); chase status echoes on awaiting-documents periods; remembered answers rendered beside the fields they fill ("learned {date} from {who}" — inspect/correct inline, one store, 03 §3). Drafted narratives interpolate engine values by reference, never by generation (04 §2.1) — even in summary form here.
- Human decisions captured here: star, saved-view create/edit/share, tab/view preferences. Nothing else — all typed acts route out.
- Certified values: never minted here; certified/sent chips render CertificationRecord/SendRecord state with hash chips linking to the sealed record.
- Versions/periods/packages: PERIODS tab renders the full per-loan set of ReportingPeriods; a period row expands to its append-only event history (R5 research: Stripe — state derived from events, never overwritten).
- Evidence/provenance: every term field, every TestResult figure, every trend mark carries a provenance ref opening the full chain (06 §1): formula → inputs → normalized row → source region → doc/loan/period identity → processing version → confirmation history.
- Permissions/read-only projections: PMC preparers see client-scoped subsets (Client FK on every object — 02 §1); reviewers get the whole surface read-only (no star-for-others, no view mutation of shared views); "Client" column renders ONLY in PMC mode, never for single-owner orgs.
- Field groups and grain, per template list:
  - organization/client: scoping FKs only (PMC mode adds a Client column and client filter; owner mode renders neither — 02 §1);
  - sponsor/portfolio: filter dimensions and grouping chips on the register; never security boundaries;
  - property: pledge-aware property cells and PROPERTIES tab cards (outward names only);
  - loan: the master record — the grain of both routes;
  - pledge: `(loanId, propertyId)` relation rows on the PROPERTIES tab;
  - reporting period: PERIODS rows + current-period card + register status chip;
  - document/requirement/test/metric: per-tab joins (links, read-only renders, engine rows);
  - package section / certification record / send record: chips within period rows, linking to owners.
- **Aggregation rule (RULED):** portfolio-level aggregates at property grain (property count, unit count, property-grain financial rollups) de-duplicate by Property — a property pledged to two loans (senior + supplemental) or appearing in two Portfolios counts once; loan-grain sums (UPB, loan count) sum loans directly; the aggregate strip labels which rule each figure used (02 §1).

## 5. State machine and exceptions

The surface renders states; it owns only view state plus the loan lifecycle stage. Everything period-shaped is read from the orchestration spine (03 §2) and rendered without mutation rights.

**Loan lifecycle (owned here — loan state, not a rail place, 08 §3):**

| Transition | Trigger | Actor | Prerequisites | Reversible | Audit event | Failure behavior |
|---|---|---|---|---|---|---|
| — → pipeline | loan created (import/setup) | human | org scope | n/a | quiet log: loan created (source: CSV row / doc extraction) | validation errors render row-level, never partial-create |
| pipeline → live | typed "activate" act | human | terms confirmed; ≥1 confirmed RequirementRecord (else the engine cannot schedule periods) | yes (deactivate, typed, reasoned) | quiet log: stage change, who/when | blocked with named missing prerequisites |
| live → archived (paid off / transferred) | typed archive act | human | no open periods (or explicit proceed-with-open acknowledgment) | yes (un-archive) | quiet log: archive + reason | sealed periods remain addressable forever — archive hides from default register segment only |
| any → deleted | — | — | — | — | — | **does not exist** — loans are never deleted (seal-not-wipe posture extends to the book) |

**Period-status chip legend (read-only projections of the spine states, 03 §2):** `open` (rung-ground chip; carries an `awaiting documents` overlay marker while the checklist is incomplete) → `in-review` → `ready` (accent-tint — a your-move state) → `certified` (chip carries who/when on hover) → `packaged/sent` (settled; sealed rows render the sealed treatment). A voided certification renders the period back at `in-review` with the void event visible in its timeline — the chip never pretends the certification still stands.

**Figure-level and view-level exception states:**

| State | Where it renders | Trigger | Actor | Behavior / exit |
|---|---|---|---|---|
| empty (no loans) | register | new org | — | Empty object: "No loans yet" + CTA to onboarding import/upload (never a fake row); reversible by adding loans |
| loading | both | route fetch | engine | Skeleton rows at uniform height; no spinners-over-numbers; figures never render before provenance refs attach |
| setup needed (extracted-unconfirmed) | register chip + record banner | CSV import or doc-extraction proposals unconfirmed | human resolves | Banner links to `/loans/[loanId]/setup`; term fields show proposed treatment; tests refuse to run (fail-closed) until confirmation |
| low confidence | term field / schedule row | extractor confidence below threshold | human | Confidence badge beside the field; click → source region; confirm/correct routes to setup flow |
| conflict | term field | two documents disagree (e.g. amendment vs original) | human | Both values render with their source refs; field blocked from downstream use until resolved in setup flow |
| partial/incomplete | record tabs | some tabs have no data yet (no periods, no docs) | — | Honest per-tab empty states naming what's missing; never hidden tabs |
| awaiting-documents | period chip (register + PERIODS + current-period card) | checklist incomplete (03 §2) | agent chases | Chip links to Holding; chase status echoed ("chased {date}") from quiet log |
| stale | term field / covenant strip | source doc replaced after confirmation (03 §2) | engine flags, human re-confirms | Stale badge with diff link everywhere the affected figure renders |
| failed tie-out | period chip | prior-package reconciliation broke | human (in Review) | Chip renders blocked state + link to Review Room; never summarized as a bare number |
| watch / shortfall / breach | verdict chips + covenant strip | TestResult verdicts | human dispositions via Review | `breach` renderable ONLY when `basis_echo=covenanted` (05 §3 — structurally enforced); monitored rows cap at shortfall. Canon: Bexley 268/301 = 89.04% vs monitored 90% floor renders SHORTFALL; Westbrook Flats' covenanted 1.20x DSCR may render breach |
| blocked/gated | covenant strip row | missing definition (e.g. DSCR basis unpinned — 05 §7.1) | human authors definition | Renders the gate ("blocked: debt-service basis undefined — define in setup"), never a plausible number (04 §2.5) |
| permission denied | register/record | out-of-scope client (PMC) or org | — | Object never renders; deep link → honest 404-shape (matches the send vertical's 404-on-foreign-org) |
| read-only | whole surface | reviewer role | — | All routes work; star/view-edit affordances absent, not disabled-gray mystery |
| certified (void-on-change) | period row | CertificationRecord | — | Chip: who/when + hash; a void event renders as its own timeline entry with reason — the void is loud in history, quiet in notifications |
| sent/sealed | period row | SendRecord + seal | — | Sealed rows are read-only forever (seal-not-wipe, 02 §5); links open the sealed record view |
| recovery/undo | view state | — | human | Star/saved-view edits undoable; term corrections version (never delete); **no undo by design** on anything sealed, certified, or sent — history is append-only |

## 6. Engine / Agent / Human / Gate / Quiet Log

The five-lane table (04 §1 format), per region of this surface:

| Region | ENGINE (deterministic) | AGENT (prepares/proposes) | HUMAN (typed decisions) | GATE (interrupts) | QUIET LOG |
|---|---|---|---|---|---|
| Register | Row projection (status, verdicts, deadlines) from persisted state; header counts = the one your-move query; filter-responsive, property-deduped aggregates | Nothing resident | Star; saved-view create/edit/share; navigate | None owned — gate states render as chips linking out | View-sharing events |
| Highlight widgets + terms | Term values from the confirmed loan record; provenance-chain resolution per field | Remembered answers render beside fields they filled ("learned {date} from {who}" — ask-once) | Open provenance; route to setup to correct | Unconfirmed terms block downstream tests (rendered, resolved in setup) | Term-correction versions echoed |
| Covenant strip | TestResult rows verbatim: verdict, `basis_echo`, signed headroom in both denominations; fail-closed blocked rows | Headroom framing beside the test; finding summaries with drafted-narrative links (ABSOLUTE LAW 10 — beside, never chrome) | Click through to Review to disposition; never disposition here | A `breach` on a covenanted test renders its gate marker linking to Review (04 §1) | Pass/watch verdicts append silently |
| Trends | `assembleTrendSeries` over sealed periods — the one preserved runtime wire, extended | Nothing — the chart is engine-only | Mark click → sealed period | Blocked series (missing definition) renders the gate card | — |
| Periods timeline | Event-history assembly from the period ledger + ActivityEvent store; hash chips | Chase-status echoes on awaiting-documents rows | Navigate to spine surfaces | Certify/send gates live on the spine — linked, never mirrored | The timeline IS a quiet-log projection |
| Pledge graph | Pledge joins; supplemental linkage; dedupe arithmetic | Nothing | Navigate to properties/loans | None | Pledge/lifecycle changes append |

Lane invariants checked: no shipping number outside ENGINE (every figure here is an engine read); the agent never appears as chrome (no chat box; Ask is the canvas panel + highlight-to-ask only); nothing on this surface pushes — the one push-eligible class (deadline escalation) belongs to Calendar/Obligations (04 §2.3).

## 7. Information hierarchy

Register (`/loans`), scan order:
1. Count+CTA header — "{n} loans · {m} periods open · {k} need you" (Reports gold-standard pattern, snapshot §3), counts via shared `CountBadge`;
2. Segment + filter cluster — Live / Pipeline segment control, then loan/lender/property/verdict/deadline-window filter chips + saved-view switcher;
3. The table itself (primary work region) — worst-verdict and next-deadline columns carry the urgency;
4. Aggregate strip — total UPB (loan-sum), properties and units (property-deduped, labeled "deduped by property"), recomputing against the active filter (R5 research: Mercury);
5. Evidence: none resident — provenance opens from the record;
6. Actions: row-level (open, star), header (new loan → import/setup, save view);
7. Activity: none — the register is stateless inventory.
Absent by design: charts (verdict chips + deadline column out-encode any chart at this grain — the chart-doctrine ship gate failed deliberately); unread badges (C-8: unread is not a concept); any period-mutation affordance; inline cell editing (Attio's spreadsheet-style grid editing is rejected — loan facts are confirmed in setup flows with provenance, never typed into a grid cell).

Record (`/loans/[loanId]`), scan order:
1. Header: outward name crumb (book altitude: `Covenant / Loans / Bexley` — no period crumb; the record is loan-altitude, 08 §2), lifecycle stage, star;
2. Highlight-widget row (≤6 — R5 research: Attio): UPB · rate · maturity · current-period status chip · next deadline · worst-verdict chip with basis badge;
3. Tab strip (header view switcher role): OVERVIEW · SCHEDULE · PERIODS · PROPERTIES · DOCUMENTS · CORRESPONDENCE · ACTIVITY;
4. Tab body (primary work region) — OVERVIEW order: current-period card (status + your-move) → findings strip (when any) → covenant strip → trends → terms block;
5. Evidence: summoned beside (Evidence panel as work window 2) on any provenance click;
6. Actions: per-row routes out; nothing destructive;
7. Activity: its own tab (quiet log), last.
Absent by design: certify/send controls (gates live on the spine); requirement editing (owner: Extraction & Confirmation); generic agent chrome.

## 8. Page anatomy and regions

`/loans` — one work window (the pane model's one big pane; nothing here requires side-by-side decision, so no split exists):

- **Header band**
  - purpose: orientation + the two register-level actions; content: count+CTA ("{n} loans · {m} periods open · {k} need you") left, "New loan" (routes to import/setup) + "Save view" right;
  - persistence: persistent; min height 48px; no collapse;
  - why: the Reports gold-standard header pattern, ruled consistent across registers (snapshot §3).
- **Segment/filter band**
  - purpose: scope the table; content: `Live | Pipeline | All` segment control (filter semantics — a loan's stage is loan state, not a place; 08 §3), filter chips (lender, property, portfolio, verdict, deadline window; PMC adds client), saved-view switcher (top-ordered view = the landing default — R5 research: Attio);
  - persistence: persistent, 40px; interaction: chips toggle popovers; ephemeral filtering never mutates a shared view without explicit "Update view" (R5 research: Attio ephemeral filtering);
  - why in-pane (not a rail): filters are view state of this register, addressable in the URL.
- **Register table** (primary work region)
  - content: columns in order — Loan (outward name) · Property/ies (pledge-aware: primary property + "+n pool" overflow chip) · Lender/Servicer · Program+Form ("Fannie 6001.NR + 6220/6241", evidence-shaped) · UPB · Rate · Maturity · Period status chip · Next deadline · Verdict chips (basis-aware) · ★;
  - persistence: persistent, fills remaining height; uniform row height 44px, open-not-boxed (ABSOLUTE LAW 11);
  - interaction: row click/Enter → record; chip clicks deep-link (period chip → that period's spine surface, verdict chip → record with covenant strip anchored); column sort; header row sticky;
  - default sort: next deadline ascending, worst verdict as secondary — urgency reads top-down before any filter is touched (R5 research: Vanta's deadline-in-the-row register); saved views may override;
  - min width 1240px for the full column set; resize: columns collapse per §10 priority into a labeled second row line — never silent truncation of figures;
  - why a single table pane: the decision made here (which loan next) is a scan decision — co-visibility is row-internal.
- **Aggregate strip**
  - purpose: the filtered book's totals; content: total UPB (loan-sum) · properties · units (both property-deduped, labeled "deduped by property") — recomputed against active filters (R5 research: Mercury);
  - persistence: pinned footer, 36px; collapses into the header count below 720px viewport height (labeled, not hidden);
  - why pinned: totals must ride with the filter state they describe.

`/loans/[loanId]` — one work window + summonable Evidence (split max 2; the canvas slot separate and uncounted):

- **Record header**
  - content: outward-name identity, lifecycle stage chip, star, supplemental linkage chip when present ("Supplemental of {loan}" / "Has supplemental: {loan}" — Loan↔Loan relation, 02 §1);
  - persistence: persistent, 56px; book-altitude crumb only (no period crumb — 08 §2).
- **Highlight widgets** (R5 research: Attio — ≤6, pinned at-a-glance values; the cap is law here)
  - content: UPB · rate · maturity · current-period status chip · next deadline · worst-verdict chip (verdict + basis badge + compact signed headroom);
  - persistence: persistent, 72px band; each widget a provenance-carrying figure (mono, tabular); widget click scrolls/anchors to its owning section or routes to its owner;
  - the set is product-fixed, not user-configurable (Attio adaptation: layout tuning is not schema editing).
- **Tab strip**
  - content: OVERVIEW · SCHEDULE · PERIODS · PROPERTIES · DOCUMENTS · CORRESPONDENCE · ACTIVITY (tab counts via shared CountBadge where meaningful: periods, documents, open threads);
  - persistence: persistent, 40px, sticky under header on scroll; a tab is a view-switch of the one big pane (no per-tab panes) — the ruled pane model honored by construction.
- **OVERVIEW tab body** (scroll column, 24px section rhythm; sections in decision order):
  - (a) current-period card — period label, status, deadline, your-move line rendering the one computed query scoped to this loan (03 §4), one CTA into the owning spine surface; transient by content (collapses to "no open period" line between cycles);
  - (b) findings strip — per-loan Finding summaries (trigger, computed headroom evidence, drafted-narrative link) routing to their disposition surface; renders only when findings exist (agent presence beside the thing it concerns, ABSOLUTE LAW 10);
  - (c) covenant strip — one row per RequirementRecord carrying a test or monitored threshold (anatomy §11);
  - (d) trends — the extended runtime wire (anatomy §11); min height 280px, collapsible with state remembered;
  - (e) terms block — titled section (R5 research: Attio sections), every field with a provenance chip (§11).
- **SCHEDULE tab**: read-only confirmed RequirementRecords grouped by kind (05 §1.1 taxonomy): title, basis badge, cadence + due-rule ("Q+45d", "FYE+120d" — evidence: SLOT-3 §8.02(b)), deliverable named fields (e.g. the §8.02(b)(3) rent-schedule field list — evidence), definition source-region chip, version marker on amended records. Header action: "Amend schedule" → `/loans/[loanId]/setup` (extraction flow; amendment re-opens only affected records, 02 §5). Why read-only: Extraction & Confirmation owns authoring (no double-homing).
- **PERIODS tab**: the period-history register + per-row expandable append-only event timeline (anatomy §11); newest first; sealed rows visually settled (rung-1 ground), open period elevated to top with its status chip.
- **PROPERTIES tab**: the pledge graph (anatomy §11) + the aggregation-law footnote.
- **DOCUMENTS tab**: per-loan filed-document list — type, period link, version, arrival channel, filed date — every row a link into `/documents/[docId]`; no upload affordance here (Intake owns arrivals, Documents owns filing — ABSOLUTE LAW 7).
- **CORRESPONDENCE tab**: open/recent thread summaries (subject, direction, draft/approved state, last activity) linking to `/loans/[loanId]/correspondence`; a draft-awaiting-approval summary carries a your-move marker consistent with the one computed query.
- **ACTIVITY tab**: the quiet log filtered to this loan (one ActivityEvent store); filterable by actor (engine/agent/human) and event type; virtualized; append-only, no dismiss/clear affordances.
- **Evidence panel** (summoned work window 2): opens beside on any provenance click; ≥560px or it renders as a stacked labeled tab (frame law); lit source line stays lit (ABSOLUTE LAW 6); why a pane not a modal: proof must sit beside the claim while the user keeps reading.
- **Canvas panel** (floating, summonable, uncounted slot): Ask with selection provenance (highlight-to-ask); never load-bearing for any decision on this surface; Esc dismisses.
- **Modals**: none — no typed act with legal weight lives on this surface; archive confirmation is the single destructive-confirm dialog and it is reversible.

## 9. Co-visibility matrix

| Datum/surface A | Datum/surface B | Must be simultaneous? | User decision enabled | Default topology | Fallback |
|---|---|---:|---|---|---|
| Register row set | Aggregate totals | YES | "Is the filtered book healthy?" | Table + pinned footer strip | Footer collapses to header count at <720px height |
| Term/test figure | Its source region | YES on demand | "Do I trust this figure?" | Record + Evidence split (65/35) | Evidence becomes labeled overlay sheet <1280px |
| Verdict chip | Signed headroom + basis badge | YES | "How bad, and is it contractual?" | Same covenant-strip row (one line) | Never separated |
| Trend mark | Its sealed period identity | YES on click | "What produced this point?" | Readout beside chart + link to sealed period | Readout below chart on narrow |
| Covenant strip (this period) | Trend history | NO (sequential) | trajectory after level | Stacked in OVERVIEW scroll | — |
| Current-period card | Spine surface it routes to | NO | routing only | Link out | — |
| Pledge graph | Loan terms | NO | separate questions | Separate tabs | — |
| Period row | Its event timeline | YES on expand | "What happened, in order?" | Inline expansion under the row | Same at all widths |
| Register row | Register aggregate strip | YES | "Is this row typical of the filtered book?" | Table + pinned footer | Footer folds into header count |
| Supplemental loan | Its senior loan | NO (linked) | "What shares this collateral?" | Linkage chips both directions | — |
| Finding summary | The headroom evidence it cites | YES | "Is this finding real?" | Same strip row (evidence inline) | Never split from its finding |

No pane exists merely because information exists: SCHEDULE/DOCUMENTS/CORRESPONDENCE/ACTIVITY need no co-visibility with anything and are tabs, not panes; the only second work window on the whole surface is Evidence, and it exists solely for the figure⇄source decision.

## 10. Layouts and viewport behavior

Default pane topology and ratios:

| Route | Default topology | With Evidence summoned | Canvas panel |
|---|---|---|---|
| `/loans` | one work window, 100% | n/a (provenance lives on the record) | floats free (Ask) |
| `/loans/[loanId]` | one work window, 100% | 65/35 split where both ≥560px; stacked labeled tabs otherwise | floats; docks right below 1440px |

- **1440px**: rail expanded 240px → work area ~1200px.
  - Register: full column set at minimum comfortable widths (Loan 200 · Properties 160 · Lender 140 · Program 130 · UPB 110 · Rate 80 · Maturity 100 · Status 110 · Deadline 110 · Verdicts 140 · ★ 40 = 1220 — one px-tier of breathing room; below this the collapse rules engage).
  - Record: single work window; Evidence opens as a split only because both windows can hold the floor (1200 → 640/560, exactly at the ≥560px law); the split is offered, with the stacked-tab alternative one click away.
- **1728px**: rail 260px → work ~1468px.
  - Register gains column air only (no new columns — added width buys density relief, not data).
  - Record: Evidence split 880/560 comfortable; TrendPanel renders full 12-mark width with the readout rail beside the chart.
- **2048px**: rail 280px → work ~1768px.
  - Record: Evidence split 1100/650; OVERVIEW sections stay stacked by default — covenant strip and trends may render side-by-side ONLY as a user-chosen layout and only while both keep full legibility; the split-max-2 law counts the Evidence window, so no third pane can ever exist.
- **Narrow (<1280px)**: rail auto-collapses to the 48px icon rail; register drops columns by priority — keep Loan · Status · Deadline · Verdicts; fold Lender/Program/UPB/Rate/Maturity into an expandable, labeled second row line (never hidden data); record tabs persist; Evidence becomes a full-height overlay sheet with explicit close and preserved lit row.
- Focus behavior: `F` on a covenant-strip row expands it to the full definition + inputs view within the pane (view-switch, not a new pane).
- Compare behavior: none on this surface (period-vs-period compare belongs to Review's tie-out; trend chart is the record's longitudinal view).
- Proof/source behavior: provenance always opens BESIDE (Evidence window or overlay sheet) — never a modal (ABSOLUTE LAW 6).
- Minimum viable: registers usable at 1152×720 (frame law); the record is fully operable there with Evidence as overlay.
- Tab stacking: Evidence-below-1280 renders as a stacked labeled tab pair ("Record | Evidence") with the lit row preserved across switches.

## 11. Components and exact anatomy

- **LoanRegisterTable** — ADJUST the existing Loans register view (`src/components/covenant/` Loans views, fixture today). Parts:
  - header row (sticky, sortable, sort-state announced) · body rows (uniform 44px) · aggregate footer;
  - row cells per the §8 column set; property cell is pledge-aware (primary + "+n pool" chip opening a popover of pool members with roles);
  - verdict cell: up to three VerdictChips (worst first) + "+n" overflow; each chip = verdict word + BasisBadge glyph;
  - row hover = rung-1 ground tint; selection = accent-tint left edge (ruled tokens, no new values);
  - second-line collapse mode (narrow): labeled key-value pairs, same row identity.
- **BasisBadge** — NEW, tiny. Parts: `COV` variant (filled — gray-rung-9 ink on rung-3 ground) vs `MON` variant (outlined — rung-7 ink, transparent ground) — covenanted and monitored rendered distinctly by *shape and fill*, never by inventing color (ABSOLUTE LAWS 3/4); tooltip spells the full word + "per {source region}"; the badge is part of the accessible name.
- **VerdictChip** — NEW shared (replaces the independent fixture strings on Loan Detail vs Review — 07 §2). Parts: verdict word (exactly `pass / watch / shortfall / breach`), severity ground from the ruled status tokens in the Review-9 set (no local values), optional compact headroom suffix; the `breach` render path is type-guarded on `basis_echo=covenanted` (05 §3) — the guard is the component's contract, not a caller convention.
- **CovenantStrip** — ADJUST the existing Loan Detail strip to render engine TestResult rows (kills the independent fixture strings, 07 §2). Parts:
  - row (one line, uniform height 40px): requirement title → BasisBadge → threshold text per the loan's own definition (verbatim from the RequirementRecord, never invented — ABSOLUTE LAW 8) → current value (mono, `tabular-nums slashed-zero`) → VerdictChip → **signed headroom in both denominations** → ProvenanceChip;
  - headroom rendering, canon-anchored: Bexley occupancy "−0.96pp · 3 units below the monitored floor" (canon: 268/301 = 89.04% vs monitored 90%); DSCR-class phrasing "$240K of NOI from a DSCR shortfall" (canon phrasing class — dollars because the mitigant conversation happens in dollars, 05 §3);
  - expanded state (`F`): definition verbatim + source region, inputs with lineage refs, watch-band ref (authoring default floor×1.05 — surfaced, never silent, 05 §7.4), verdict-history sparkrow (≤12 discrete marks, each clickable to its period);
  - reporting-only loans (Bexley/Calloway shape — 02 §1) render monitored rows only plus the labeled note "No recurring ratio covenants — reporting-only agreement (per the loan documents)";
  - blocked row: the gate card naming the missing definition (05 §7.1) in place of any number.
- **TrendPanel** — EXTEND the one existing runtime wire (snapshot §3). Parts:
  - the assembler: keep `assembleTrendSeries` in `src/lib/covenant/` as the single series source — no fork, no surface-local recomputation; migrate its input from demo readings to sealed-period engine outputs (05 §5: trends read sealed periods; series points link to their sealed periods);
  - series switcher: NOI · occupancy · DSCR — DSCR only where the loan's own definition pins a debt-service basis (engine refuses otherwise and the panel renders the blocked card, 05 §7.1); Bexley/Calloway default to occupancy + NOI;
  - the chart, doctrine-compliant (ABSOLUTE LAW 11): discrete marks over the last ≤12 sealed periods (under the ~20 cap); severity-only color (marks in ink; only verdict-crossing marks carry the ruled status tint); **every mark an instrument** — click/Enter opens the readout (period, value, verdict, basis, signed headroom) with an "Open sealed period" link to the sealed read-only record;
  - reference line: the threshold/floor with its basis in the label ("monitored floor 90%" — canon; "covenant 1.20x" — Westbrook canon), hairline weight;
  - legend/readout: mono figures, right rail at ≥1728px, below-chart at narrower widths;
  - ship gate: the chart must out-encode the adjacent text (show a crossing or trajectory the text doesn't state) or the tab ships table-only.
- **TermsBlock** — NEW titled section (gap 11 discharged). Parts:
  - field rows (label · value · ProvenanceChip): UPB, rate structure, IO/am schedule (evidence shape: "2-yr IO → 30-yr am"), maturity (evidence: 2027-07-07), program+form+riders (evidence: 6001.NR + 6220 + 6241), lender/servicer, reporting-only flag, `supplemental_of`;
  - ProvenanceChip: opens the full chain to the exact loan-document region (06 §1) in the Evidence panel;
  - state treatments: proposed (dotted underline + confidence), confirmed (underline + confirmer stamp on hover), stale (badge + diff link), conflict (both values + both refs) — per 06 §7.
- **PeriodTimeline** — NEW (R5 research: Stripe — detail = facts + append-only event history; state derived from events, never overwritten). Parts:
  - period register rows: period label · status chip · certified chip (who/when + hash short-chip; click = full hash + link to the certification record) · sent chip (when/transport) · seal state · → links to `/covenant/[loanId]/[period]/{review|certificate}`;
  - expandable event timeline per row: arrival → recognition → holding → extraction → normalization → computation → tie-out → review → certified → sent → sealed; each entry actor-stamped (engine/agent/human) from the ActivityEvent store + period ledger;
  - void events render in-line with their reason ("certification voided — {field} corrected {date}"), never elided;
  - empty state: "No periods yet — periods appear when the loan goes live and the engine schedules its cadence" (03 §5).
- **PledgeGraphPanel** — NEW. Parts:
  - property cards: outward name, address, unit count, PMS identity, pledge role (primary / pool member — 02 §2), link to the Rent Roll teaser where wired ("this roll feeds {loan}'s occupancy" cross-reference only);
  - relation rows, not a freeform canvas: "secures this loan (primary)"; cross-collateral note when a property also secures another loan ("also secures {loan} — supplemental", the Loan↔Loan `supplemental_of` relation, 02 §1);
  - supplemental banner on supplemental loans: "This is a supplemental loan of {senior loan} — separate reporting obligations, shared collateral";
  - footer: the aggregation law stated in words — "Portfolio totals count each property once" (02 §1).
- **LoanRecordHeader + HighlightWidgetRow** — NEW composition over existing shell header patterns. Parts:
  - identity block (outward name, lender/servicer subline, lifecycle stage chip, star toggle);
  - supplemental linkage chip (renders only when `supplemental_of` or an inverse relation exists);
  - widget row: six fixed widgets (label 11px rung-7 · value in mono · optional chip), each keyboard-focusable, each an anchor or route;
  - period/deadline widgets re-read the spine and deadline stores on focus (recompute-on-view — never a cached stale chip).
- **SavedViewSwitcher / filter chips** — REUSE the shared saved-view mechanism (02 §2: one mechanism app-wide). Parts: view menu (personal → workspace scoping, star-to-rail, reorder — top view is the landing default), dirty-state bar ("Update view / Save as new"), URL-share action (R5 research: Linear/Attio); filter chips are structured (entity pickers), not free text.
- **ProvenanceChip** — NEW shared atom (also consumed by Review/Certificate renderers): dotted-underline affordance on the figure + a chip glyph; states: proposed (confidence shown) / confirmed (confirmer on hover) / stale / conflict; click contract: open Evidence beside, light the exact source line, keep it lit (the cross-cutting lit-row component contract).
- **CountBadge** — REUSE the ruled shared component (one component, 37 atoms consolidated — snapshot §4) for header counts and tab counts.
- **Evidence panel + lit-row** — REUSE the review-room Evidence panel under the cross-cutting contract (U1-F1 wrong-row fix is a prerequisite — 06 §1).
- **CommandPalette / SearchPalette / AskPanel / HighlightToAsk / CovenantShell** — REUSE as frame components (08 §5–6); no surface-local variants.
- **Empty/error/recovery objects** — NEW shared set: empty register (CTA to onboarding), per-tab empties (each naming what will fill it and from where), blocked-computation card (names the missing definition + the authoring route), honest 404-shape for out-of-scope loans (mirrors the send vertical's honest failures).

## 12. Interaction specification

- Selection: single row selection (click/Enter) on register and all record tables; `X`/Shift-click multi-select limited to star and CSV-export scoping — no bulk mutations exist on this surface by design.
- Hover: row ground tint (rung-1); provenance chips reveal at row hover but remain in the accessibility tree and keyboard-reachable always; tooltips (basis word spelled out, full hash, due-rule derivation) on 300ms delay; trend-mark hover shows the compact readout without committing selection.
- Focus: visible 2px accent-family focus ring on rows, chips, tabs, widgets, and chart marks; focus order = the scan order of §7; focus is never trapped by the Evidence panel (it is a sibling region, entered explicitly).
- Keyboard:
  - `G L` → `/loans`; `↑/↓` row navigation; `Enter` open; `S` star; `/` focus the filter cluster; `Esc` clears filter focus before anything else;
  - `⌘K` palette: place rows ("Loans"), object rows ("Bexley — loan"), and computed-answer rows ("dscr westbrook" → the latest TestResult with verdict + headroom + a lit-row citation link — 08 §5; engine rows get the mono figure treatment, zero free-generation);
  - record: tab strip arrow-navigable, every tab keyboard-reachable in stable order; `F` expands the focused covenant-strip row; `[`/`]` previous/next period row on PERIODS;
  - `Esc` ladder: closes canvas panel → closes Evidence → only then navigates up (07 §3);
  - final chord map defers to `cross-cutting/search-command-keyboard.md`; this brief requires only that no interaction here is pointer-only.
- Editing and validation: no figure is editable here (read surface; corrections route to their owning flows). Saved-view naming edits inline with explicit dirty-state resolution: "Update view" (mutates the shared view for everyone — labeled as such) vs "Save as new" (personal copy).
- Bulk action: star/unstar selected; CSV export of the register (row metadata only — no package contents; mirrors the Reports register export).
- Undo/recovery: star and personal-view edits undo via toast (5s); shared-view updates undo via version restore in the view menu; nothing else on the surface mutates, so nothing else needs undo.
- Sorting/filtering: every column sorts (stable sort, sort state in URL); filters compose AND; the Live/Pipeline/All segment composes with filters; aggregate strip and header counts recompute on every change (R5 research: Mercury).
- Drill-down and return: register row → record → tab → spine route; the breadcrumb altitude gate governs return (record shows the book crumb only; spine surfaces show loan+period crumbs — 08 §2); browser back/forward traverse tabs and filter states (all view state is addressable).
- Source-linked selection (the lit-row behavior): clicking any figure — term field, strip value, widget, trend mark, period-row hash — opens Evidence beside with the exact source line lit, and it **stays lit** until explicitly dismissed (ABSOLUTE LAW 6); the lit ref survives tab switches within the record; a second figure click moves the light, never stacks it.
- Save/persistence: tab, sort, filter, segment, and expanded-row state in the URL; saved views persisted server-side with scope; star per user; trend-panel collapsed state per user.
- Collaboration/commenting: none in v1 by design — discussion happens on findings and review stops in their owning surfaces; the record stays a reference, not a channel.

## 13. Visual craft direction

- Typography roles: page/record titles in the UI face at 18/24 semibold; section titles 13/20 semibold; column headers 12/16 uppercase tracked, gray rung-7 ink; row text 13/20 regular; chip text 11/16 medium; all financial figures **Geist Mono, `tabular-nums slashed-zero`** (ruled — fonts are not colors; the mono face stands per the colors-only meta-ruling, snapshot §4), right-aligned in numeric columns.
- Financial-number treatment: UPB in full dollars ($15,232,500 — canon fixture), rates to 2dp ("4.17% fixed"), ratios to 2dp ("1.20x"), percentages to 2dp ("89.04%"), headroom always signed (+/−) and dual-denominated; negative headroom is never colored alone — the verdict chip carries the severity, the figure stays ink (severity-only color law); figures never wrap or truncate (column collapse rules exist so they never have to).
- Spacing rhythm: 8px base grid; 44px register rows; 12px intra-row chip gaps; 24px between OVERVIEW sections; titled sections open with a 13px semibold label + hairline, no boxes.
- Density: register is the dense end (44px rows); the record breathes (72px widget band, 24px section gaps) — reference density, not task density.
- Open ground vs earned boundaries: open-not-boxed tables (row hairlines rung-3 only); the ONLY carded elements are the current-period card and property cards (earned: they are objects, not layout).
- Dividers/elevation: ten-rung gray ladder exclusively (adopted 2026-08-07); elevation only on the summoned Evidence sheet and canvas panel (hairline + shadow per the ruled seam behavior).
- Semantic color: accent family #7189FF for interactive/selected states (hover #8EA1FF, active #6078F4, tint #A9B5FF); verdict severity via the ruled status tokens; basis distinction via shape/fill (§11), never a new hue. Zero local grays (readback-checked, not eyeballed — oklch caution, snapshot §4).
- Certified-sheet treatment: this surface never paints the certified sheet; certified chips reference it only as links. The sealed-record views opened from PERIODS render under the Certificate brief's ruled paper hexes — not restyled here.
- Focus/selected/hover: hover = rung-1 ground; selected = accent-tint edge + rung-2 ground; focus = accent ring; lit evidence row = the ruled lit-trace treatment (the one live paper-accent render today — snapshot §4).
- Chart style: marks-as-instruments (12 discrete points, 6px targets with 24px hit areas), reference lines hairline rung-4 with end labels, no gridlines beyond the hairline baseline, readout in mono; severity tint only at verdict-crossing marks.
- Motion: 120ms ease-out on Evidence summon and row expansion; mark-click readout snaps (no tween on data); `prefers-reduced-motion` collapses all to instant.
- Chip grammar (one system across register and record): status chips = rounded 16px height, rung ground + rung-9 ink; verdict chips = the ruled status tokens; basis badges = shape/fill distinction (§11); hash chips = mono, 8-char short form + copy affordance; every chip that names an object is a link, every chip that names a state is inert — the distinction is visible (link chips underline on hover).
- Icons: Lucide-only, no emoji (locked law); icons never carry meaning alone (always paired with a word or accessible name).
- Long-session ergonomics: register scan relies on chip position (fixed columns) not color memory; sticky header row; the record's tab strip sticks under the header on scroll; density is constant across sessions (no responsive font scaling that would break figure alignment).

## 14. Benchmark research and synthesis

| Product | Limited role | Official evidence | Exact pattern to take | Covenant adaptation | What to reject | Why this beats alternatives |
|---|---|---|---|---|---|---|
| Attio | Object table + record-page composition donor | https://attio.com/help/reference/managing-your-data/records/configure-record-pages ; https://attio.com/help/reference/attio-101/attios-data-model/define-your-data-model-objects-lists-and-views (R5) | Table and record page as two projections of one object; ≤6 highlight widgets; admin-titled sections; activity tab | Widgets fixed by the product (UPB/rate/maturity/status/deadline/verdict), not user-schema; sections = terms/schedule | Freeform user schema editing — the loan schema is confirmed from documents | The cleanest documented object→record projection in CRM-class software (R5 research: Attio) |
| Stripe Dashboard | Object detail + event-log donor | https://docs.stripe.com/payments/payment-records ; https://docs.stripe.com/radar/transaction-reviews (R5) | Detail = header facts + append-only event timeline + related objects; state derived from events, never overwritten | PERIODS tab: period history as event log (submitted → certified → sent → sealed, void events in-line) | Stripe's open-ended filter surface — 2–50 loans need 4–6 curated chips | Industrial proof that append-only event history is the trustworthy record shape (R5 research: Stripe) |
| Linear | Saved-view + keyboard donor | https://linear.app/docs/custom-views (R5) | Views scoped personal→workspace, starred to sidebar, URL-shareable | The one shared saved-view mechanism on this register | Triage accept/decline verbs — obligations can't be declined | Best-documented view scoping + sharing grammar (R5 research: Linear) |
| Mercury | Calm-register donor | https://support.mercury.com/hc/en-us/articles/38790547830036-Viewing-cashflow-and-transactions-data-on-your-Transactions-page (R5) | Header stats recompute against active filters | Aggregate strip = filter-responsive mini-dashboard with the dedupe label | Keyword-first navigation — books navigate by structure | Proves a small operator's register can carry live aggregates without noise (R5 research: Mercury) |
| Modern Treasury | Linked-object state grammar | https://docs.moderntreasury.com/reconciliation/docs/expected-payments ; https://docs.moderntreasury.com/platform/reference/expected-payment-object (R3) | Expected-vs-actual as separate objects joined by typed, enum'd relations — never overwrites | Loan⇄Pledge⇄Property and period⇄certification⇄send rendered as typed relations with named states, mirrored in chips | Payment-grade streaming — Covenant's cadence is periodic | The reference for relations-as-typed-objects, which the pledge graph and period chips both are (R3 research: Modern Treasury) |
| LoanBoss | Domain critical-date taxonomy (terminology) | https://www.loanboss.com/notifications ; https://www.loanboss.com/blog/automated-lender-adjusted-dscr-dy (R5) | Critical-date types: covenant tests, extension notice windows, maturity events; lender-adjusted metric definitions stored beside values | Maturity + next-deadline columns; the loan's own definition stored on the requirement and echoed in the strip | Rates/hedging/valuation breadth | Domain-native evidence of what a multifamily borrower's loan record must surface (R5 research: LoanBoss) |
| Finley | Domain agreement→deliverables decomposition (terminology) | https://www.finleycms.com/blog/product-deep-dive-credit-facility-deliverable-tracking ; https://docs.finleycms.com/core-capabilities/deliverables (R5/R3) | Agreement decomposed into deliverable schedule + computed covenants, separately stated | SCHEDULE tab (deliverables) vs covenant strip (computed tests) — two renders, one requirement store | Lender-shared workspace framing | Closest existing borrower-side obligation model (R5 research: Finley) |
| Vanta | Deadline-in-row donor | https://help.vanta.com/en/articles/11345374-the-tests-page (R5) | Deadlines inline beside color-coded statuses — urgency is a property of the row, not a position on a grid | The next-deadline column + deadline chip on the record header; no calendar rendering on this surface | SLA/control indirection | Proves compliance deadlines read best as row properties in a register (R5 research: Vanta) |
| BankStride/nCino | Anti-pattern | https://www.bankstride.com/tickler-tracking-management (R3) | Nothing structural | — | Covenant-as-tickler: due dates + upload chutes with no computation, no headroom, no provenance | The named void this record exists to replace (R3 research: BankStride) |

Synthesis: the register is an Attio-projection table wearing Reports' gold-standard header, with Mercury's filter-bound aggregates constrained by the ruled property-dedupe law and Vanta's deadline-in-the-row urgency; the record is Attio's composition (≤6 widgets + titled sections) fused with Stripe's append-only event history and Modern Treasury's typed-relation grammar for the pledge graph. What makes the result original and domain-correct for borrower-side lender reporting, rather than a CRM skin:
- every figure is a deterministic engine output carrying a lit-row chain into the loan's own documents (no referenced product has provenance-bearing terms);
- every verdict is basis-typed at the data layer, so a monitored miss can never borrow breach language — a distinction none of the referenced products models;
- the record's "activity" is a sealed, append-only period history (seal-not-wipe), not a mutable CRM feed;
- the collateral model is the ruled many-to-many Pledge graph with supplemental loans as first-class separate loans — a structure no CRM object model carries.
Those four properties come from Covenant's own laws (02, 05, 06), not from any benchmark.

## 15. Domain references

Finley-class covenant/deliverable trackers and LoanBoss-class CRE debt managers (above, per the DIRECTION: domain truth, not visuals) plus the servicer-side world the packages feed — JLL-class servicer questionnaires and Form 6001.NR reporting structures (evidence: SLOT-3/4) — supply terminology (deliverable, critical date, lender-adjusted definition, notice window), expected-data shape, and workflow semantics only. **Domain authority ≠ visual authority:** none of these products contributes a pixel, a layout, or an interaction. Covenant semantics — thresholds, test definitions, cadences, basis, due-rules — come exclusively from each loan's own documents and Terry's rulings, never from any referenced product (ABSOLUTE LAW 8).

## 16. Accessibility, performance, and safety

- WCAG contrast and focus: all chip/badge text ≥4.5:1 against its ground (ruled tokens, verified by canvas readback per the oklch caution — never by eyeball); verdict and basis are never encoded by color alone (word + shape + fill carry the meaning); the focus ring is visible on every interactive element including chart marks and provenance chips.
- Keyboard completeness: full register and record operation without a pointer (§12); trend marks arrow-navigable with the readout announced on focus; the Evidence panel reachable and dismissible from the keyboard.
- Screen-reader semantics: register = `table` with proper column headers and sortable-state announcements; verdict cell reads "shortfall, monitored basis, headroom minus zero point nine six percentage points" (the basis word always spoken — the vocabulary law is an accessibility requirement too); tabs = `tablist`/`tabpanel`; the period timeline = ordered list with time semantics; the Evidence panel announces the lit source line and its document identity on open.
- Table virtualization/large data: a 50-loan register needs no virtualization; virtualize the ACTIVITY tab and per-period event timelines (append-only, unbounded); PERIODS paginates beyond 24 periods (6 years of quarterlies) with sealed history always reachable.
- Loading and latency feedback: skeletons at final row height (no layout shift); aggregates render only when their input set is complete — no flickering partial sums; the trend panel renders series only from sealed periods, never provisional points; slow provenance resolution shows a labeled resolving state inside the Evidence panel, never a spinner over the figure.
- Destructive action confirmation: nothing irreversible exists here; archive (lifecycle) is a typed confirm and reversible; shared-view update warns that it changes the view for everyone.
- Certify and external-send safety: not present by design — this surface renders certification/send state and links to sealed records; it can never mint, repeat, or void either act (approved-bytes and void-on-change are enforced at their owning gates, 02 §6); the hash chip is display + verify only.
- Source immutability: Evidence opens Original artifacts read-only with the hash visible; Recreated-searchable derivatives are labeled as derivatives, never represented as originals (ABSOLUTE LAW 7).
- Auditability: every certified/sent chip traces to a CertificationRecord/SendRecord row; the ACTIVITY tab is a filter over the one append-only store — no surface-local history that can contradict it (the U3-F2 class of defect made structurally impossible, 07 §2); register CSV exports carry an export timestamp and the active filter description.

## 17. Acceptance tests and fixtures

Fixtures: **Bexley** (canon: $15,232,500 UPB · 4.17% fixed · 301 units · Fannie 6001.NR reporting-only · occupancy 268/301 = 89.04% vs monitored 90% floor) · **Westbrook Flats** (canon: floating-rate, covenanted 1.20x DSCR) · **Calloway Park (CAL)** (evidence spine: FYE-2018 NOI $1,218,877 · Total OpEx $1,686,050 · Net Income $460,159; rent roll 322 rows → 301 units, physical occupancy 279/301 = 92.69% as of 04/30/2018; maturity 2027-07-07; 2-yr IO → 30-yr am; riders 6220/6241).

0. **Workflow fixture (a full period, arrival to sealed, as seen from this surface):** run the Calloway FYE-2018 spine through the pipeline (arrival → recognition → extraction → normalization → computation → tie-out → review → certify → send → seal). Assert, on this surface only: the register period chip advances through `open → in-review → ready → certified → sent` without any affordance here mutating it; the PERIODS row accretes its event timeline append-only; the covenant strip's monitored occupancy row re-renders from the new TestResult; the trends panel gains exactly one new mark linked to the newly sealed period; the ACTIVITY tab shows the same events as the period timeline (one store, two projections).
1. **Vocabulary law (blocking):**
   - Bexley's occupancy row renders `shortfall` + `MON` badge + "−0.96pp · 3 units below the monitored floor";
   - the string "breach" appears nowhere on Bexley's register row, record, chips, tooltips, or screen-reader strings (automated text sweep of the rendered DOM);
   - Westbrook Flats' failing covenanted 1.20x DSCR fixture renders `breach` + `COV` badge;
   - type-level: `VerdictChip` refuses (compile error or thrown contract violation) `breach` with `basis_echo=monitored`.
2. **Basis rendering distinct:** automated snapshot asserts `COV` (filled) vs `MON` (outlined) are visually distinct at 100% zoom and both tooltips cite their source region.
3. **Terms provenance (gap 11):** every TermsBlock field on Calloway Park opens Evidence with the exact loan-document region lit (maturity 2027-07-07 → its source page); the lit row persists across a SCHEDULE→OVERVIEW tab switch; U1-F1-class wrong-row regression asserted against the fixture region map.
4. **Trend wire preserved-and-extended:**
   - `assembleTrendSeries` remains the single assembler (no fork; import-graph assertion);
   - occupancy series over ≥6 sealed Calloway periods renders ≤12 discrete marks with the monitored-floor reference line labeled "monitored floor 90%" (canon);
   - clicking the 89.04%-class mark opens the readout (period, value, verdict, basis, signed headroom) and "Open sealed period" navigates to the sealed read-only record;
   - a DSCR series on a loan with no pinned debt-service basis renders the blocked card naming the missing definition — never a plausible line (05 §7.1);
   - regression: the current demo-readings render keeps working until the sealed-period input lands (the wire may extend, never break).
5. **Chart doctrine audit:** trends panel — marks <20, severity tint only at verdict-crossing marks, every mark clickable, and the panel demonstrably out-encodes its adjacent text (crossing visible that the text doesn't state) or ships table-only.
6. **Register gold-standard header:** `/loans` renders "{n} loans · {m} periods open · {k} need you" via shared CountBadge; `{k}` equals the computed your-move query filtered to loans (03 §4) — one number, everywhere.
7. **Live/Pipeline as filter:** the segment control filters the same table (URL param `?stage=pipeline`); "Pipeline" appears nowhere in the rail (08 §3); Westbrook (pipeline fixture) appears only under Pipeline/All.
8. **Property-dedupe aggregation:**
    - fixture with senior + supplemental loans pledged to one 301-unit property: the aggregate strip counts the property once and its 301 units once while UPB sums both loans;
    - the same property placed in two Portfolios changes no aggregate (never sum across Portfolios — 02 §1);
    - the footer label "deduped by property" renders whenever a property-grain figure is shown.
9. **Pledge graph:** the shared-collateral fixture renders two loan cards linked by `supplemental_of` ("Supplemental of {senior}" / "Has supplemental"), one shared property card carrying both pledge roles; the supplemental loan is a separate row on `/loans` with its own periods and obligations (never merged — 02 §1).
10. **Period timeline append-only:**
    - certify → void (corrected value) → re-certify → send on a fixture period renders four ordered events including the void with its stated reason;
    - the sealed row exposes no mutating affordance anywhere in its expanded timeline;
    - the certified chip's hash short-form expands to the full hash and matches the SendRecord's bytes hash (approved-bytes identity, 02 §6);
    - deep links from timeline entries resolve to sealed read-only views.
11. **Saved views shared mechanism:**
    - create "Fannie loans — open periods", share to workspace, open as a second user → identical filter/sort/columns;
    - ephemeral filter changes do not mutate the shared view without explicit "Update view";
    - the top-ordered view is the landing default for `/loans` (R5 research: Attio);
    - the view URL opened cold reproduces the exact register state.
12. **States:**
    - empty org shows the onboarding CTA (no fake rows);
    - a CSV-imported loan renders "setup needed" with proposed-treatment terms and tests refusing to run (fail-closed card, not a blank);
    - a replaced T-12 flags stale on every affected strip figure with a working diff link;
    - a foreign-org deep link returns the honest 404-shape (parity with the send vertical's 404);
    - reviewer role sees no star/view-edit affordances (absent, not disabled);
    - PMC preparer sees only their client's loans, with the Client column present; a single-owner org never renders a Client column (02 §1).
13. **Viewport fixtures:** 1440/1728/2048 render per §10 ratios; at 1250px total width the Evidence window stacks as a labeled tab instead of squeezing below 560px (no silent compression — frame law); the register at 1152×720 remains fully operable with the narrow column-fold engaged; financial figures never truncate at any fixture width.
14. **Keyboard fixture:** `G L` → register → `↓↓ Enter` → Bexley → arrow to covenant strip → `F` expands → provenance chip → Evidence opens with focus inside → `Esc` closes panel without navigating.
15. **Accessibility:** axe-clean on both routes; verdict cell screen-reader string includes the basis word; trend marks reachable and announced.
16. **Benchmark challenger review:** a reviewer armed with the R5/R3 notes confirms the shipped surface takes only the named patterns and rejects the named rejects (no Attio schema editing, no Stripe filter sprawl, no tickler-model period rows).

## 18. Build plan

- Dependencies (named):
  - **F2 persistence** — org/client/property/pledge/loan/period tables, tenancy FKs, the `supplemental_of` flag (gap 3); nothing on this surface can leave fixture without it;
  - `covenant_test_state` (the one wired writer today, snapshot §3) as the TestResult read path;
  - sealed-period store for trends (05 §5 — series points must link to sealed periods);
  - due-rule engine **F5** for the next-deadline column — until wired the column renders "—" with a "deadline engine pending" title (honest absence, never a fixture date; 04 §2.5);
  - loan-doc extraction (V2) for term provenance refs (gap 11);
  - the lit-row contract fix (U1-F1) from `cross-cutting/provenance-lit-row-trace.md` — landing provenance on a defective trace would ship wrong evidence;
  - the orchestration spine (V2–V5) for live period status; before it, status chips read the period ledger directly.
- Foundation work: the register-row projection query (loan + pledge join + current period + worst verdict + next deadline) in `src/lib/covenant/`; `basis_echo` typed end-to-end so the vocabulary law is compiler-enforced; the dedupe-aware aggregate function with unit tests against the shared-collateral fixture.
- Components to build first: `VerdictChip` + `BasisBadge` (shared — unblocks Review and Home renders of the same rows), then `TermsBlock` + `ProvenanceChip`, then `PeriodTimeline`, then `PledgeGraphPanel`; `TrendPanel` last (it extends working code and must not regress the one live wire).
- Vertical slice (the send-vertical pattern): `/loans/[loanId]` for **Bexley** — one route, one loan, engine data end to end: persisted loan row, TestResult from `covenant_test_state`, `assembleTrendSeries` over sealed fixture periods, terms with real provenance refs, zero `book.ts` reads on the route. Then widen to `/loans` with the projection query over the persisted book.
- Migration from fixture data: `book.ts` (574 lines, 7 loans — snapshot §3) becomes the seed migration for the persisted book (canon values preserved verbatim); each region flips per-region behind the flag; the trend view's demo-readings input is the LAST fixture removed, because it currently exercises the real assembler and proves the wire.
- Rollout/feature flag: `covenant.loans.engine` scoped per region (register / each record tab); a region is engine-backed or labeled fixture, never blended — no fixture string may sit beside an engine figure unlabeled.
- Proof artifacts required: screenshots at 1440/1728/2048 + narrow; canvas-readback token audit (zero local grays, ruled tokens only); the vocabulary-law test run green; a recorded lit-row trace on Calloway terms (correct region, stays lit); the trend-mark → sealed-period navigation capture; the dedupe unit-test output on the shared-collateral fixture.
- Roadmap slot: this brief discharges gap 11 (multi-loan handling fixture-only) in wave **W3** with the property-dedupe aggregation rule from 02 §1 (05 §6 gap table).
- Final gate: **ADJUST** confirmed — IA kept, the trend wire preserved and extended, every figure re-sourced from the engine; re-run the benchmark challenger review (test 16) against R5's Attio/Stripe mechanics before widening past the Bexley slice.
