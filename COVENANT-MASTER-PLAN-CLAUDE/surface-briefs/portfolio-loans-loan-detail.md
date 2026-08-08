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
- Completion/advancement: none — this floor never advances a period; it exposes the period's own advancement state and links to the surface that can.

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

No-double-homing boundary: this surface owns the loan/pledge/property identity graph and nothing that moves a period. Everything period-, test-, document-, or thread-shaped is a **summary with a link** to its owner (02 §3 rule).

## 4. Data and semantic model

- Source facts: loan documents (SLOT-3-class: executed agreement + riders), servicer statements where present; the immutable Original artifacts behind every term.
- Extracted values awaiting confirmation: proposed loan terms and requirement records from loan-doc extraction (first-time setup, or CSV-imported loans in "setup needed" state) — rendered with confidence + dotted provenance underline, never bold-final (06 §7).
- Confirmed values: the loan master record's term fields (confirmed at setup, Gate 1); the confirmed RequirementRecord set; COA/occupancy definition pins referenced, not owned.
- Deterministic outputs: TestResult rows (verdict, signed headroom, `basis_echo` — 05 §3); period status; readiness echoes; trend series assembled by `assembleTrendSeries` from sealed-period engine outputs (05 §5: series points link to their sealed periods); register aggregates.
- Agent proposals/drafts: findings with drafted mitigant narratives (summarized here, dispositioned elsewhere); chase status echoes on awaiting-documents periods.
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

| Lane | On this surface |
|---|---|
| ENGINE | Register row projection (status, verdicts, deadlines) from persisted state; TestResult rows with `basis_echo` + signed headroom; trend series via `assembleTrendSeries` from sealed periods; property-dedupe aggregation; period event history assembly; provenance-chain resolution. All figures deterministic (04 §2.1) |
| AGENT | None resident. Echoes only: findings summarized on OVERVIEW (raised elsewhere), chase status on awaiting-documents chips, remembered answers beside fields they filled ("learned {date} from {who}"). No chat box (ABSOLUTE LAW 10); Ask reachable via canvas panel + highlight-to-ask |
| HUMAN | Star; create/edit/share saved views; navigate; open provenance; route out to typed acts. No typed act with legal weight lives here |
| GATE | None owned. Gates render as chips/banners linking to their owning surface (blocked test → setup authoring; failed tie-out → Review; certify/send → spine routes) |
| QUIET LOG | ACTIVITY tab renders the one ActivityEvent store filtered to this loan (actor, what, evidence refs, period identity, outcome); loan lifecycle-stage changes and view-sharing events append here. Nothing on this surface pushes |

## 7. Information hierarchy

Register (`/loans`), scan order:
1. Count+CTA header — "{n} loans · {m} periods open · {k} need you" (Reports gold-standard pattern, snapshot §3), counts via shared `CountBadge`;
2. Segment + filter cluster — Live / Pipeline segment control, then loan/lender/property/verdict/deadline-window filter chips + saved-view switcher;
3. The table itself (primary work region) — worst-verdict and next-deadline columns carry the urgency;
4. Aggregate strip — total UPB (loan-sum), properties and units (property-deduped, labeled "deduped by property"), recomputing against the active filter (R5 research: Mercury);
5. Evidence: none resident — provenance opens from the record;
6. Actions: row-level (open, star), header (new loan → import/setup, save view);
7. Activity: none — the register is stateless inventory.
Absent by design: charts (verdict chips + deadline column out-encode any chart at this grain — chart doctrine gate failed deliberately); unread badges (C-8: unread is not a concept); any period-mutation affordance.

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
  - min width 1240px for the full column set; resize: columns collapse per §10 priority into a labeled second row line — never silent truncation of figures;
  - why a single table pane: the decision made here (which loan next) is a scan decision — co-visibility is row-internal.
- **Aggregate strip**
  - purpose: the filtered book's totals; content: total UPB (loan-sum) · properties · units (both property-deduped, labeled "deduped by property") — recomputed against active filters (R5 research: Mercury);
  - persistence: pinned footer, 36px; collapses into the header count below 720px viewport height (labeled, not hidden);
  - why pinned: totals must ride with the filter state they describe.

`/loans/[loanId]` — one work window + summonable Evidence (split max 2; canvas panel separate):
- **Record header** (persistent, 56px): identity, stage, star, supplemental linkage chip when present ("Supplemental of {loan}" / "Has supplemental: {loan}" — Loan↔Loan relation, 02 §1).
- **Highlight widgets** (persistent, 72px): the six above; each a provenance-carrying figure (mono, tabular); worst-verdict widget shows verdict + basis badge + signed headroom compact form.
- **Tab strip** (persistent, 40px): seven tabs; tab = view-switch of the one big pane (no per-tab panes).
- **OVERVIEW** tab body: (a) current-period card — period label, status, deadline, your-move line rendering the one computed query scoped to this loan (03 §4) with a single CTA into the owning spine surface; (b) findings strip — per-loan Finding summaries with headroom evidence + link to disposition surface (transient: renders only when findings exist); (c) **covenant strip** — one row per RequirementRecord carrying a test or monitored threshold (anatomy §11); (d) **trends** — the extended runtime wire (anatomy §11); (e) **terms block** — titled section (R5 research: Attio sections), every field with provenance chip (§11).
- **SCHEDULE**: read-only confirmed RequirementRecords grouped by kind (05 §1.1 taxonomy): title, basis badge, cadence + due-rule ("Q+45d", "FYE+120d" — evidence: SLOT-3 §8.02(b)), deliverable named fields, definition source region chip. Header action: "Amend schedule" → `/loans/[loanId]/setup` (extraction flow; amendment re-opens affected records only, 02 §5).
- **PERIODS**: the period-history register + per-row expandable event timeline (anatomy §11).
- **PROPERTIES**: the pledge graph (anatomy §11).
- **DOCUMENTS**: per-loan filed-document list — type, period, version, filed date — every row a link into `/documents/[docId]`; no upload here (Intake/Documents own arrival and filing).
- **CORRESPONDENCE**: open/recent thread summaries (subject, direction, state, last activity) linking to `/loans/[loanId]/correspondence`.
- **ACTIVITY**: quiet-log view filtered to this loan; filter by actor (engine/agent/human) and type.
- **Evidence panel** (summoned work window 2, ≥560px or stacked tab): opens beside on any provenance click; lit-row stays lit (ABSOLUTE LAW 6). Modal use on this surface: none — no typed acts with legal weight live here.
- **Canvas panel** (floating, summonable): Ask with selection provenance; never load-bearing.

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

No pane exists merely because information exists: SCHEDULE/DOCUMENTS/CORRESPONDENCE need no co-visibility and are tabs, not panes.

## 10. Layouts and viewport behavior

- **1440px**: rail expanded 240px → work area ~1200px. Register: full column set at minimum comfortable widths (Loan 200 · Properties 160 · Lender 140 · Program 130 · UPB 110 · Rate 80 · Maturity 100 · Status 110 · Deadline 110 · Verdicts 140 · ★ 40). Record: single window; Evidence opens as overlay-leaning split only if both windows hold ≥560px (1200 → 640/560 split, allowed at the floor); otherwise Evidence stacks as a labeled tab (frame law, 08 §9 — no silent compression).
- **1728px**: rail 260px → work ~1468px. Register gains column air (no new columns — density, not data). Record: Evidence split 880/560 comfortable; trends chart renders at full 12-mark width with readout rail beside.
- **2048px**: rail 280px → work ~1768px. Record: Evidence split 1100/650; the covenant strip and trends sit side-by-side ONLY if both regions keep full legibility — default remains stacked (the split-max-2 law counts the Evidence window; no third pane ever).
- **Narrow (<1280px)**: rail auto-collapses to 48px icons; register drops columns by priority (keep Loan · Status · Deadline · Verdicts; fold Lender/Program/UPB/Rate/Maturity into the row's expandable second line — labeled, not hidden); record tabs remain; Evidence becomes a full-height overlay sheet with explicit close.
- Focus behavior: `F` on a covenant-strip row expands it to the full definition + inputs view within the pane (view-switch, not a new pane).
- Compare behavior: none on this surface (period-vs-period compare belongs to Review's tie-out; trend chart is the record's longitudinal view).
- Proof/source behavior: provenance always opens BESIDE (Evidence window or overlay sheet) — never a modal (ABSOLUTE LAW 6).
- Minimum viable: registers usable at 1152×720 (frame law); the record is fully operable there with Evidence as overlay.
- Tab stacking: Evidence-below-1280 renders as a stacked labeled tab pair ("Record | Evidence") with the lit row preserved across switches.

## 11. Components and exact anatomy

- **LoanRegisterTable** — ADJUST existing Loans register view (`src/components/covenant/` Loans views, fixture): uniform 44px rows; column set per §8; sortable headers; row hover raises rung-1 ground tint (gray ladder), selection = accent-tint left edge (ruled tokens, no new values). Verdict-chip cell: up to three chips (worst first) + "+n" overflow; each chip = verdict word + basis badge glyph.
- **BasisBadge** — NEW, tiny: `COV` (filled, gray-rung-9 ink on rung-3 ground) vs `MON` (outlined, rung-7 ink) — covenanted and monitored rendered distinctly by *shape and fill*, never by inventing color (ABSOLUTE LAW 3/4); tooltip spells the word + "per {source region}".
- **VerdictChip** — NEW shared (replaces the independent fixture strings — 07 §2): verdict word set exactly `pass / watch / shortfall / breach`; consumes the ruled status tokens from the Review-9 set; `breach` render path type-guarded on `basis_echo=covenanted` (05 §3).
- **CovenantStrip** — ADJUST existing Loan Detail strip to render engine TestResult rows. Row anatomy (one line, uniform height): requirement title → BasisBadge → threshold per the loan's own definition (text from the RequirementRecord, never invented) → current value (mono, `tabular-nums slashed-zero`) → VerdictChip → **signed headroom in both denominations** (e.g. canon: Bexley occupancy "−0.96pp · 3 units below the monitored floor"; canon phrasing class: "$240K of NOI from a DSCR shortfall") → provenance chip. Expanded (F): definition verbatim + source region, inputs with lineage refs, watch-band ref, verdict history sparkrow (≤12 discrete marks). Reporting-only loans (Bexley/Calloway shape — 02 §1) render the strip with monitored rows only, plus the labeled note "No recurring ratio covenants — reporting-only agreement (per the loan documents)".
- **TrendPanel** — EXTEND the existing runtime wire: keep `assembleTrendSeries` (the real assembler; snapshot §3), migrate its input from demo readings to sealed-period engine outputs (05 §5). Series: NOI, occupancy, and DSCR only where the loan's own definition pins a basis (engine refuses otherwise — renders the blocked state, 05 §7.1). Chart doctrine compliant: discrete marks (last ≤12 sealed periods — under the ~20 cap); severity-only color (marks in ink; only verdict-crossing marks carry status tint); **every mark an instrument** — click → readout (period, value, verdict, headroom) + "Open sealed period" link → the sealed record; threshold/floor reference line labeled with its basis ("monitored floor 90%" — canon). The chart out-encodes the adjacent text (trajectory + crossings) or the tab ships table-only.
- **TermsBlock** — NEW titled section: field rows (label · value · **ProvenanceChip**). Fields: UPB, rate structure, IO/am schedule (evidence shape: "2-yr IO → 30-yr am"), maturity (evidence: 2027-07-07), program+form+riders (evidence: 6001.NR + 6220 + 6241), lender/servicer, reporting-only flag, `supplemental_of`. Each ProvenanceChip opens the chain to the exact loan-document region (06 §1); proposed/confirmed/stale treatments per 06 §7.
- **PeriodTimeline** — NEW: period-history register (rows: period label · status chip · certified chip (who/when + hash short-chip) · sent chip (when/transport) · seal state · → links) + per-row expandable **append-only event timeline** (arrival → recognition → … → certified → sent → sealed, each entry actor-stamped from the ActivityEvent store and the period ledger; a void event renders in-line with its reason). Pattern source: R5 research: Stripe (detail = facts + append-only event history; state derived from events).
- **PledgeGraphPanel** — NEW: property cards (outward name, units, PMS identity, pledge role: primary / pool member, link to Rent Roll teaser where wired) + relation edges rendered as labeled rows, not a freeform canvas: "secures this loan (primary)"; cross-collateral note when a property also secures another loan ("also secures {loan} — supplemental", the Loan↔Loan `supplemental_of` relation, 02 §1). Footer states the aggregation law: "Portfolio totals count each property once" (02 §1).
- **SavedViewSwitcher / filter chips** — REUSE the shared saved-view mechanism (02 §2); scopes personal → workspace; top view = landing default; URL-shareable (R5 research: Linear/Attio).
- **CountBadge** — REUSE the ruled shared component (snapshot §4) for header counts and tab counts.
- **Evidence panel + lit-row** — REUSE the review-room Evidence panel under the cross-cutting contract (fix U1-F1 first — 06 §1).
- **CommandPalette / AskPanel / HighlightToAsk / CovenantShell** — REUSE as frame components (08 §5–6).
- **Empty/error/recovery objects** — NEW shared: empty register (CTA), per-tab empties, blocked-computation card (names the missing definition), honest 404 for out-of-scope loans.

## 12. Interaction specification

- Selection: single row selection (click/Enter); multi-select limited to star and view-scoped bulk (no bulk mutations exist here).
- Hover: row ground tint; provenance chips reveal at row hover but remain keyboard-reachable always; chip tooltips (basis word, hash full value) on 300ms.
- Focus: visible 2px accent focus ring (ruled accent family) on rows, chips, tabs, marks; focus order = scan order (§7).
- Keyboard: `G L` → `/loans`; `↑/↓` rows; `Enter` open; `S` star; `/` focus filter; `⌘K` palette (object + computed-answer rows per 08 §5 — "dscr westbrook" returns the TestResult with a lit-row link); tab strip arrow-navigable, every tab keyboard-reachable; `F` expand covenant-strip row; `Esc` closes Evidence/canvas before it ever navigates (07 §3). Final chord map defers to `cross-cutting/search-command-keyboard.md`.
- Editing/validation: none on figures (read surface). Saved-view name editing inline with dirty-state "Update view / Save as new" (shared-view mutation always explicit).
- Bulk action: star/unstar selection; export register CSV (metadata only, mirrors Reports).
- Undo/recovery: star and view edits undo via toast; nothing else mutates.
- Sorting/filtering: any column sorts; filters compose AND; segment (Live/Pipeline/All) composes with filters; aggregate strip recomputes on every change.
- Drill-down and return: row → record → tab → spine route; breadcrumb altitude gate returns (record shows book crumb; spine shows loan+period crumbs — 08 §2); back/forward traverse tabs (addressable state).
- Source-linked selection: clicking any figure (term, strip value, trend mark, widget) opens Evidence beside with the exact source line lit, and it **stays lit** until dismissed (ABSOLUTE LAW 6); the lit ref survives tab switches within the record.
- Save/persistence: view state (tab, sort, filters) in URL; saved views persisted server-side; star per user.
- Collaboration/commenting: none in v1 by design — discussion happens on findings and review stops in their owning surfaces.

## 13. Visual craft direction

- Typography: page/record titles in the UI face at 18/24 semibold; column headers 12/16 uppercase tracked, gray rung-7 ink; row text 13/20; all financial figures **Geist Mono, `tabular-nums slashed-zero`** (ruled), right-aligned in numeric columns.
- Financial numbers: UPB in full dollars ($15,232,500 — canon fixture), rates to 2dp ("4.17% fixed"), headroom always signed (+/−) in both denominations; negative headroom never colored alone — verdict chip carries the severity, the figure stays ink (severity-only color law).
- Spacing rhythm: 8px base grid; 44px register rows; 12px intra-row chip gaps; 24px between OVERVIEW sections; titled sections open with a 13px semibold label + hairline, no boxes.
- Density: register is the dense end (44px rows); the record breathes (72px widget band, 24px section gaps) — reference density, not task density.
- Open ground vs earned boundaries: open-not-boxed tables (row hairlines rung-3 only); the ONLY carded elements are the current-period card and property cards (earned: they are objects, not layout).
- Dividers/elevation: ten-rung gray ladder exclusively (adopted 2026-08-07); elevation only on the summoned Evidence sheet and canvas panel (hairline + shadow per the ruled seam behavior).
- Semantic color: accent family #7189FF for interactive/selected states (hover #8EA1FF, active #6078F4, tint #A9B5FF); verdict severity via the ruled status tokens; basis distinction via shape/fill (§11), never a new hue. Zero local grays (readback-checked, not eyeballed — oklch caution, snapshot §4).
- Certified-sheet treatment: this surface never paints the certified sheet; certified chips reference it only as links. The sealed-record views opened from PERIODS render under the Certificate brief's ruled paper hexes — not restyled here.
- Focus/selected/hover: hover = rung-1 ground; selected = accent-tint edge + rung-2 ground; focus = accent ring; lit evidence row = the ruled lit-trace treatment (the one live paper-accent render today — snapshot §4).
- Chart style: marks-as-instruments (12 discrete points, 6px targets with 24px hit areas), reference lines hairline rung-4 with end labels, no gridlines beyond the hairline baseline, readout in mono; severity tint only at verdict-crossing marks.
- Motion: 120ms ease-out on Evidence summon and row expansion; mark-click readout snaps (no tween on data); `prefers-reduced-motion` collapses all to instant.
- Long-session ergonomics: register scan relies on chip position (fixed columns) not color memory; sticky header row; the record's tab strip sticks under the header on scroll.

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
| BankStride/nCino | Anti-pattern | https://www.bankstride.com/tickler-tracking-management (R3) | Nothing structural | — | Covenant-as-tickler: due dates + upload chutes with no computation, no headroom, no provenance | The named void this record exists to replace (R3 research: BankStride) |

Synthesis: the register is an Attio-projection table wearing Reports' gold-standard header, with Mercury's filter-bound aggregates constrained by the ruled property-dedupe law; the record is Attio's composition (widgets + titled sections) fused with Stripe's append-only event history and Modern Treasury's typed-relation grammar for the pledge graph — but what makes it Covenant rather than a CRM skin is that every figure is a deterministic engine output carrying a lit-row chain into the loan's own documents, every verdict is basis-typed so monitored misses can never borrow breach language, and the record's "activity" is a sealed, append-only period history rather than a mutable feed. No referenced product has provenance-bearing terms, basis-typed verdicts, or seal-not-wipe periods; those come from Covenant's own laws (02, 05, 06).

## 15. Domain references

Finley and LoanBoss (above) plus the servicer-side world the packages feed — JLL-class servicer questionnaires and Form 6001.NR reporting structures (evidence: SLOT-3/4) — supply terminology (deliverable, critical date, lender-adjusted definition, notice window) and expected-data shape only. **Domain authority ≠ visual authority:** none of these products contributes a pixel. Covenant semantics — thresholds, test definitions, cadences, basis — come exclusively from each loan's own documents and Terry's rulings, never from any referenced product (ABSOLUTE LAW 8).

## 16. Accessibility, performance, and safety

- WCAG: all chip/badge text ≥4.5:1 against its ground (ruled tokens verified by readback); verdict and basis never encoded by color alone (word + shape + fill); focus ring visible on every interactive element including trend marks.
- Keyboard completeness: full register and record operation without a pointer (§12); trend marks arrow-navigable with readout announced.
- Screen-reader semantics: register = `table` with column headers; verdict cell reads "shortfall, monitored basis, headroom minus 0.96 percentage points" (basis always spoken); tabs = `tablist`; period timeline = list with time semantics; Evidence panel announces the lit source line on open.
- Virtualization: 50-loan books need none; virtualize the ACTIVITY tab and event timelines (append-only, unbounded).
- Loading/latency: skeletons at final row height; aggregates render only when their input set is complete (no flickering partial sums); trend panel renders series only from sealed periods — never provisional points.
- Destructive confirmation: nothing destructive exists here; archive (lifecycle) is typed-confirm and reversible.
- Certify/send safety: not present by design — this surface renders certification/send state, links to sealed records, and can never mint or mutate either (approved-bytes and void-on-change enforced at their gates, 02 §6).
- Source immutability: Evidence opens Original artifacts read-only with hash visible; derivatives labeled (ABSOLUTE LAW 7).
- Auditability: every render of certified/sent chips traces to CertificationRecord/SendRecord rows; the ACTIVITY tab is the one append-only store filtered — no surface-local history that can contradict it (07 §2).

## 17. Acceptance tests and fixtures

Fixtures: **Bexley** (canon: $15,232,500 UPB · 4.17% fixed · 301 units · Fannie 6001.NR reporting-only · occupancy 268/301 = 89.04% vs monitored 90% floor) · **Westbrook Flats** (canon: floating-rate, covenanted 1.20x DSCR) · **Calloway Park (CAL)** (evidence spine: FYE-2018 NOI $1,218,877; rent roll 322 rows → 301 units; maturity 2027-07-07; 2-yr IO → 30-yr am; riders 6220/6241).

1. **Vocabulary law (blocking):** Bexley's occupancy row renders `shortfall` + `MON` badge + "−0.96pp · 3 units below the monitored floor"; the string "breach" appears nowhere on Bexley's record; Westbrook's failing DSCR fixture renders `breach` + `COV` badge. Type-level test: `VerdictChip` refuses `breach` with `basis_echo=monitored`.
2. **Basis rendering distinct:** automated snapshot asserts `COV` (filled) vs `MON` (outlined) are visually distinct at 100% zoom and both tooltips cite their source region.
3. **Terms provenance (gap 11):** every TermsBlock field on Calloway Park opens Evidence with the exact loan-document region lit (maturity 2027-07-07 → its source page); the lit row persists across a SCHEDULE→OVERVIEW tab switch; U1-F1-class wrong-row regression asserted against the fixture region map.
4. **Trend wire preserved-and-extended:** `assembleTrendSeries` remains the assembler (no fork); occupancy series over ≥6 sealed Calloway periods renders ≤12 marks; clicking the 89.04%-class mark opens readout (period, value, verdict, headroom) and "Open sealed period" navigates to the sealed read-only record; a DSCR series on a loan with no pinned debt-service basis renders the blocked card naming the missing definition — never a plausible line.
5. **Chart doctrine audit:** trends panel — marks <20, severity tint only at verdict-crossing marks, every mark clickable, and the panel demonstrably out-encodes its adjacent text (crossing visible that the text doesn't state) or ships table-only.
6. **Register gold-standard header:** `/loans` renders "{n} loans · {m} periods open · {k} need you" via shared CountBadge; `{k}` equals the computed your-move query filtered to loans (03 §4) — one number, everywhere.
7. **Live/Pipeline as filter:** the segment control filters the same table (URL param `?stage=pipeline`); "Pipeline" appears nowhere in the rail (08 §3); Westbrook (pipeline fixture) appears only under Pipeline/All.
8. **Property-dedupe aggregation:** fixture with senior + supplemental loans on one property — aggregate strip counts the property and its 301 units once while UPB sums both loans; footer label "deduped by property" present (02 §1).
9. **Pledge graph:** the same fixture renders two loan cards linked by `supplemental_of` ("Supplemental of {senior}"), one shared property card with both pledge roles; the supplemental is a separate row on `/loans` (never merged).
10. **Period timeline append-only:** certify → void (corrected value) → re-certify → send on a fixture period renders four ordered events including the void with its reason; sealed rows expose no mutating affordance; deep links resolve to sealed read-only views.
11. **Saved views shared mechanism:** create "Fannie loans — open periods", share to workspace, open as second user → identical filter/sort; ephemeral filter changes don't mutate the shared view without explicit update.
12. **States:** empty org shows onboarding CTA; CSV-imported loan renders "setup needed" with tests refusing to run; a replaced T-12 flags stale on affected strip figures with diff link; foreign-org deep link → honest 404-shape; reviewer role sees no star/view-edit affordances.
13. **Viewport fixtures:** 1440/1728/2048 render per §10 ratios; at 1250px Evidence stacks as labeled tab (no silent compression); register at 1152×720 fully operable.
14. **Keyboard fixture:** `G L` → register → `↓↓ Enter` → Bexley → arrow to covenant strip → `F` expands → provenance chip → Evidence opens with focus inside → `Esc` closes panel without navigating.
15. **Accessibility:** axe-clean on both routes; verdict cell screen-reader string includes the basis word; trend marks reachable and announced.
16. **Benchmark challenger review:** a reviewer armed with the R5/R3 notes confirms the shipped surface takes only the named patterns and rejects the named rejects (no Attio schema editing, no Stripe filter sprawl, no tickler-model period rows).

## 18. Build plan

- Dependencies: **F2 persistence** (org/client/property/pledge/loan/period tables, tenancy FKs, `supplemental_of` — gap 3); `covenant_test_state` (the one wired writer today) as the TestResult read path; sealed-period store for trends (05 §5); due-rule engine **F5** for the next-deadline column (until wired: render "—" with "deadline engine pending" title — honest absence, never a fixture date); loan-doc extraction (V2) for term provenance refs; the lit-row contract fix (U1-F1) from `cross-cutting/provenance-lit-row-trace.md`.
- Foundation work: define the register-row projection query (loan + pledge join + current period + worst verdict + next deadline) in `src/lib/covenant/`; type `basis_echo` end-to-end so the vocabulary law is compiler-enforced.
- Components first: `VerdictChip` + `BasisBadge` (shared, unblocks Review/Home too), `TermsBlock` + `ProvenanceChip`, `PeriodTimeline`, then `PledgeGraphPanel`.
- Vertical slice (send-vertical pattern): `/loans/[loanId]` for **Bexley**, one loan end-to-end — persisted loan row, engine TestResult from `covenant_test_state`, `assembleTrendSeries` over sealed fixture periods, terms with real provenance refs — one route, engine data, zero `book.ts` reads. Then widen to `/loans` with the projection query.
- Migration from fixture: `book.ts` (574 lines, 7 loans) becomes the seed for the persisted book; each surface region flips per-region behind the flag; the trend view's demo-readings input is the LAST fixture removed (it exercises the real assembler until sealed periods exist).
- Rollout/feature flag: `covenant.loans.engine` per-region (register / record tabs); fixture and engine renders never mix within one region (a region is engine-backed or labeled fixture, never blended).
- Proof artifacts: screenshots at the three viewports; canvas-readback token audit (zero local grays); the vocabulary-law test run; a recorded lit-row trace on Calloway terms; the trend-mark → sealed-period navigation capture.
- Final gate: **ADJUST** confirmed — IA kept, trend wire preserved and extended, every figure re-sourced from the engine; re-benchmark against R5's Attio/Stripe mechanics before widening past the Bexley slice.
