# RENT ROLL TEASER — PLACEMENT AND V1 SURFACE

RULED: a working Rent Roll v1 lives FREE inside the Covenant shell; v1+v2 are charged together at v2 launch (snapshot §5, memory `rr-teaser-v2-monetization.md`, 2026-07-02). This brief sites it, specifies the v1 surface completely, and draws the identity guard. It is a UTILITY inside Covenant — a quiet place in the rail, not a product identity (DIRECTION). It is powered by the rent-roll canonical reader (roadmap F3) — the same reader that unblocks the engine's occupancy resolver (gap 7, snapshot §6; 05 §6 row 7), so building this teaser and unblocking the flagship proactive-occupancy story are one investment.

## 1. Identity and verdict

- Product layer: portfolio floor (inventory B9, 07 §1).
- Route/address in the basis-v2 repository: `/rent-roll` — proposed, already named in the shell plan's deep-route list and rail proposal (08 §1, §3). Mounts `CovenantShell` like every route in the `(covenant)` group.
- Current build state: `ABSENT` (07 B9: NOT STARTED; gap 13).
- Existing-surface verdict: `UNBUILT`.
- Research tier: A/B (DIRECTION — shorter brief, all 18 sections, no truncation).
- Primary users/roles: owner and PMC preparer (daily operators of the book); reviewer role read-only. In PMC mode the view is Client-scoped like every projection (02 §1).
- Frequency and session duration: short utility sessions — 1–5 minutes, whenever a fresh roll arrives (monthly rhythm for most PMS exports) or when someone wants a clean answer ("what's occupancy at {property} right now?") without opening the raw export.
- Error cost: no outbound act exists here (nothing certifies, nothing sends), so the terminal error is unreachable on this surface by construction. The real risk is downstream: this reader's output feeds the occupancy resolver and questionnaire fields on covenant surfaces — so the reader's tie-out discipline (aggregates must tie to the roll's own summary; unit dedup mandatory) is enforced at the reader, not at this view (05 §5 occupancy row).
- Success criterion: any filed roll renders normalized within the pipeline's processing time; every aggregate ties to the roll's own summary sheet or is flagged; the user stops opening the raw xlsx. Acceptance spine: the Calloway Park roll renders 322 rows → 301 units, six statuses, physical occupancy 279/301 = 92.69% as of 04/30/2018 (evidence: SLOT-2), with zero covenant verdicts anywhere on the page.

## 2. User job and decisions

- Primary job-to-be-done: "show me the current roll for this property, clean and trustworthy, with the totals proven."
- Decisions made here: which property, which roll (as-of date) — navigation decisions only. No covenant-weight decision lives here.
- Questions the surface must answer in scan order:
  1. Which roll am I looking at? (property, as-of date, source doc, version)
  2. Is it clean? (does it tie to its own summary sheet; how many rows collapsed in dedup)
  3. What is occupancy — physically, and economically per the reader's definition?
  4. What is the status mix across the six states?
  5. What does any single unit look like?
  6. Where did any number come from? (the lit-row answer, always one click away)

**The identity guard, restated as rules (DIRECTION; enforced by tests T6/T7):**
1. No covenant verdict, floor, threshold, or verdict vocabulary renders on `/rent-roll` — occupancy-vs-floor comparison is covenant-surface work only.
2. The only bridge to covenant context is the neutral cross-reference chip ("this roll feeds {loan}'s occupancy →").
3. Resident identifiers never reach this surface's payload, render, or export path (v1 has no export path at all).
4. No pricing, upgrade, or product-identity chrome — the teaser is a quiet utility inside Covenant's shell, monetized only at v2 (RULED).
- What the user should not have to decide here: status-label mapping (a pipeline confirmation, remembered per property/PMS — 05 §2.1); COA anything; whether occupancy passes any floor (that judgment belongs to covenant surfaces only — identity guard, §below); which roll version is canonical (Documents owns versions; this view renders the current filed version and links its chain).
- Entry paths: rail item `Rent Roll` (08 §3 proposal — quiet, no badge); command palette Places result ("Rent Roll — {property}"); Documents register row for a filed roll ("view normalized"); the cross-reference chip on covenant surfaces ("occupancy source: {roll as-of}" links here); Loan Detail PROPERTIES tab property card.
- Exit paths: source-doc link → DocView (`/documents/[docId]`); "this roll feeds {loan}'s occupancy" cross-reference → that loan's covenant surface (`/covenant/[loanId]/[period]/actuals`); breadcrumb → book floors.
- Completion/advancement conditions: none — this surface never advances a period. Its reader output advances periods indirectly (occupancy fields on the questionnaire section, checklist item "rent schedule — arrived"), all owned elsewhere.

## 3. Object and ownership model

| Object/action/status | Owned here? | Summary/link elsewhere | Address/identity | Notes |
|---|---:|---|---|---|
| Normalized roll view (rendered projection of reader output) | YES | Actuals REGION 3 renders the same reader output inside the period workflow | `(propertyId, rollAsOfDate)` | Two renders, one reader, one output — never two normalizations (no-double-homing honored at the data layer) |
| Property/roll picker state, saved views | YES | — | per user | Shared SavedView mechanism (02 §2) |
| Roll document (bytes, versions, artifacts) | NO | Header links "source: {doc} · Recreated from {PMS} export" → DocView | `docId` | Documents owns the canon (02 §3) |
| Arrival/filing decisions | NO | "arrived via intake {date}" line links to the intake record | — | Intake owns arrivals; NO separate intake exists for rolls (DIRECTION) — rolls travel the one pipeline |
| Status-label mapping confirmations | NO | Unmapped-status flags here deep-link to the owning exception card | MemoryEntry (property/PMS scope) | The typed confirm is the normalization pipeline's act (05 §2.1); ask-once, remembered |
| Occupancy aggregates (engine output) | NO (rendered) | Actuals/Loan Detail/questionnaire consume the same figures | `(propertyId, date)` | Engine-owned (05 §5); this surface renders with lit-row provenance |
| Occupancy-vs-floor verdicts | NEVER | Covenant surfaces only | TestResult | The identity guard — see §5 and §17 |
| Unit dedup ledger (322→301 collapse record) | YES (render) | — | per roll | The reader computes it; this is its one first-class display home |

No-double-homing boundary, stated: Intake owns arrival decisions; Documents owns the filed roll and its versions; the normalization pipeline (Actuals & Computation machinery) owns mapping confirmations; the engine owns aggregates; **this surface owns only the standalone presentation** — the register of properties-with-rolls and the normalized roll view. Everything mutable it shows is a link to its owner.

## 4. Data and semantic model

- Source facts: the PMS detail export — 24 columns, 322 lease rows over 301 units, per-row unit status, plus the roll's own summary sheets (floorplan mix, status totals) (evidence: SLOT-2; 05 §2.1). Original bytes immutable in Documents; grain: document.
- Extracted values awaiting confirmation: unknown status labels the first time a PMS's vocabulary appears; missing SQFT/rent cells; duplicate-unit suspicion (05 §2.1 exception classes). Rendered here as flags; confirmed in the pipeline's exception card.
- Confirmed values: status mappings (property/PMS-scope MemoryEntry, versioned); grain: property.
- Deterministic outputs (the reader, F3), in pipeline order:
  1. Row parse of the 24-column detail export (row-class: lease row vs summary vs header).
  2. Unit dedup: 322 rows → 301 units; future-lease dual rows collapse onto their unit with both leases retained as children (the 301 ≠ 322 rule, 06 chain C). The collapse is a ledger, not a deletion — every collapsed row stays addressable.
  3. Status normalization onto the roll's own six-state taxonomy: Occupied-no-NTV / Occupied-NTV / Occupied-NTV-Leased / Vacant-Leased / Admin-Down / Vacant-Not-Leased (evidence: SLOT-2).
  4. Aggregates: status counts (259/18/2/13/2/7 on the evidence roll); physical occupancy 279/301 = 92.69% as of 04/30/2018; economic aggregates per the reader's documented definition (definition text summonable beside the figure; the definition belongs to the reader spec and the loan's own documents — never this brief's invention); floorplan mix rollups.
  5. Summary tie-out: row-derived totals cross-checked against the roll's own summary sheets (301 ✓ on the evidence roll; any disagreement flags and blocks occupancy consumers fail-closed).
- Agent proposals/drafts: proposed status mappings with confidence; anomaly notes vs the prior roll (unit-count change, status-mix jump) — quiet observations, not findings with dispositions (findings live on covenant surfaces).
- Human decisions: none owned here (see §3).
- Certified values: none originate here. Where a roll feeds a certified package, the certified linkage renders as a downstream-use note ("feeds {loan} {period} — certified {date}"), and replacing that roll triggers the void machinery on the owning surfaces (03 §2), never silently.
- Versions/periods/packages: the view pins to a specific roll (docId+version+as-of). A newer filed version shows a "newer roll available" bar; the old version stays addressable (seal-not-wipe posture at the document layer).
- Evidence/provenance: every aggregate opens the full chain of 06 §1 — aggregate → reader function → input rows → source sheet cells → docId/version/artifact → processing version → confirmation history. Every unit row lights its exact source row region.
- Permissions/read-only projections: reviewer sees everything read-only; PMC mode scopes by Client; resident identifiers never render on this surface at all (§8 — the strongest permission is omission).

Grain map: organization → client → property (the pivot grain here) → document (roll) → normalized unit rows → aggregates. Loan/period/package appear only as cross-reference chips.

## 5. State machine and exceptions

| State | Meaning here | Trigger → resolution | Actor | Reversible? | Audit |
|---|---|---|---|---|---|
| empty | No property has a filed roll | First roll arrives via the one intake pipeline → view populates | — | — | — |
| awaiting-documents | Property known, no roll filed (or checklist expects a newer one) | Render "no roll on file — rolls arrive through intake or upload to Documents" + the intake address line (read-only, managed in Settings) | Agent chases per the holding checklist where a loan expects it | — | Chases quiet-logged (owned by Intake) |
| loading/processing | Roll filed, reader running | Progress row on the property card; no fake numbers | Engine | — | ActivityEvent |
| partial/incomplete | Reader parsed but some rows unreadable/missing cells | Render parsed portion + a named-gap flag ("14 rows unreadable — view in pipeline") | Engine flags; pipeline resolves | Yes (re-run after fix) | ActivityEvent |
| extracted/unconfirmed | Unknown status labels pending mapping confirmation | Affected units render in an "unmapped" group, excluded from aggregates; aggregates show "excludes n unmapped units" — never a guessed count | Human confirms in pipeline (once per PMS) | Yes (correction versions) | Confirmation history |
| stale/superseded | A newer roll version filed | "Newer roll available {as-of}" bar; one click switches; downstream consumers flagged by the owning surfaces | Engine | Yes | Version chain in Documents |
| low confidence | Cell-level extraction confidence below threshold | Dotted provenance underline + confidence at point of use; suspect cells never render bold-final (06 §7) | Human corrects in pipeline | Yes | Correction history |
| conflict | Duplicate unit rows beyond the dual-row pattern | Conflict flag on the unit group; aggregates blocked for that unit until resolved | Human (pipeline) | Yes | ActivityEvent |
| failed tie-out | Row-derived totals ≠ the roll's own summary sheet | The tie-out chip goes to failed state, shows both numbers + delta; occupancy consumers (resolver) are blocked fail-closed — the block renders honestly here AND on the covenant surfaces | Human dispositions in pipeline | Yes | ActivityEvent + disposition record |
| watch/shortfall/breach | **Never rendered here.** Occupancy-vs-floor comparison renders ONLY on covenant surfaces (DIRECTION identity guard). The word "shortfall"/"breach"/"pass"/"watch" never appears on `/rent-roll` | — | — | — | Acceptance test §17-T6 |
| permission-denied | Client-scope or role denies | Standard denied state; no data shape leaks | — | — | — |
| read-only | Reviewer role; also every historical roll version | Full render, zero mutation affordances | — | — | — |
| blocked/gated | Reader cannot process the PMS format at all | Fail-closed block naming exactly what is missing ("unrecognized roll format — the reader's format registry does not match these columns"); never a guessed table; manual authoring path offered per the fail-closed law | Engine blocks; pipeline authoring resolves | Yes | ActivityEvent |
| unrecognized property | A roll arrives that matches no known property | Never reaches this surface — it sits in the Intake queue as an arrival decision; this surface links "1 roll awaiting routing" only as a quiet note when the org has one | Human (Intake decision) | Yes | Intake decision record |
| duplicate arrival | The same roll bytes re-filed | Suppressed upstream by content hash (04 §1 arrival row); a changed-bytes re-send becomes a version, not a duplicate | Engine | — | Suppression quiet-logged |
| certified (void-on-change) | N/A as an origin state; downstream-use note renders where a roll feeds a certified package | Replacing that roll → void event on the owning package surface; this view shows "superseded — fed a certified package, see void notice" | Engine | Void is not reversible; re-certify is a fresh act | Void event |
| sent/sealed | N/A here; sealed packages that consumed this roll list under downstream uses (link to Reports) | — | — | — | — |
| recovery/undo | Switching roll versions is always reversible; nothing destructive exists on this surface by design — there is nothing to undo because there is nothing to do | — | — | — | — |

## 6. Engine / Agent / Human / Gate / Quiet Log

| Lane | On this surface |
|---|---|
| ENGINE | The canonical reader (F3): 24-column parse, unit dedup (322→301), status normalization, aggregate computation, summary-sheet tie-out; fail-closed on unrecognized formats; all figures deterministic (04 §1 actuals row) |
| AGENT | Proposes status mappings first-time-per-PMS (confidence + source region); notes anomalies vs the prior roll; never computes an aggregate, never fills a gap with a guess |
| HUMAN | Confirms status mappings once (in the pipeline's exception card, deep-linked from here); picks property/roll; nothing else — this is the point of the surface |
| GATE | Failed summary tie-out or blocked format → occupancy consumers blocked fail-closed; the gate renders on this view AND on the covenant surfaces that depend on it (04 §2.5 honesty invariant) |
| QUIET LOG | Every roll normalized, every clean tie-out, every mapping reuse ("pre-filled from memory {ref}"), every version supersession — ActivityEvents, no push ever from this surface |

## 7. Information hierarchy

1. Page/frame header: shell chrome; breadcrumb `Covenant / Rent Roll` (book altitude — no loan/period crumbs, per the altitude gate, 08 §2).
2. Decision/status summary: the roll identity band — property picker, as-of date, source-doc link, "Recreated from {PMS} export" provenance label, tie-out chip (clean/failed), dedup chip ("322 rows → 301 units").
3. Primary work region: the occupancy summary block, then the unit table.
4. Secondary context: floorplan mix table (below, or tab at narrow widths).
5. Evidence/proof: Evidence panel, summonable right — lit-row into the source sheet.
6. Actions: version switch, saved views, cross-reference chips. No certify, no send, no export in v1.
7. Activity/history: collapsed "roll history" strip (versions, normalizations) at the bottom.

Absent by design: covenant verdicts and floors (identity guard); charts (a status-mix bar would not out-encode the counts table — chart doctrine says it does not ship; snapshot §4); pricing/upsell UI (RULED: no pricing UI in v1); resident identifiers (below); any export/share affordance in v1 (which also makes the resident-data law trivially unbreakable from this surface).

## 8. Page anatomy and regions

- **Region A — roll identity band** (persistent, full width, ~56px): property picker (type-ahead over properties with filed rolls), roll/as-of selector (versions), provenance line ("Recreated from {PMS} export · filed {date} via intake · source ↗"), tie-out chip, dedup chip. Interaction: chips expand inline popovers (tie-out shows both totals + delta; dedup lists the 21 collapsed dual rows with their unit pairs). Why not a pane: identity is orientation, not work.
- **Region B — occupancy summary** (pinned, ~120px, open ground): six status-count figures in a single row (each count clickable → filters the unit table to that status, and lights its source aggregation in Evidence), then physical occupancy ("279 / 301 = 92.69% · as of 04/30/2018" on the evidence fixture) and the economic aggregate, each with the dotted provenance underline and a summonable definition note. A quiet cross-reference chip when loans consume this roll: "feeds {Bexley}'s occupancy →" — a link, never a verdict (DIRECTION).
- **Region C — unit table** (primary work window, fills remaining height): uniform-row grid, one row per deduped unit. The rendered column set is a fixed projection of the 24 source columns (DIRECTION anatomy):

| Rendered column | Content | Treatment |
|---|---|---|
| Unit | Unit identifier | Mono; the row anchor; dual-row units carry a "2 rows" marker that expands the pre-dedup source rows |
| Floorplan | Plan code | Links its floorplan-mix row (Region D) |
| SQFT | Square footage | Mono, right-aligned; missing → em-dash + low-confidence flag, never 0 |
| Status | Normalized six-state label | Text label always; source PMS label + mapping provenance on hover |
| Lease dates | Current-lease start / end | Future lease renders as the child row under its unit, never a second unit |
| Market rent | Per the roll's market-rent column | Mono, right-aligned |
| Lease rent | Per the roll's lease-rent column | Mono, right-aligned; the market-vs-lease pair drives the economic aggregates |
| Charges | Rollup of the roll's charge columns | Single rolled figure; expands inline to the source charge breakdown |

  **Resident-identifier columns are omitted by design** — they exist in the Structured reading artifact in Documents for those with rights, but the teaser never renders them (resident-data law: resident-level data never travels outward; this surface removes even the inward temptation, and no column picker can summon them).
- **Region D — floorplan mix table** (secondary, below C; becomes a tab below 1728px): floorplan, unit count, avg SQFT, avg market rent, avg lease rent, occupancy by plan — tied to the roll's own mix summary sheet, discrepancies flagged like Region B.
- **Region E — Evidence panel** (summonable, right, 360–420px): the lit-row trace (06 §1). Click any figure/row → exact source sheet region lights and stays lit. Opens beside, never a modal (lit-row law). Esc dismisses.

Pane-model compliance: one big pane (C) + view switching (D as tab at narrow widths); Evidence is the surface-interior inspector pane per the frame law (08 §4); no split of work windows exists here — no decision on this surface needs two work windows. Modals: none.

## 9. Co-visibility matrix

| Datum/surface A | Datum/surface B | Must be simultaneous? | User decision enabled | Default topology | Fallback |
|---|---|---:|---|---|---|
| Aggregate figure (e.g. 92.69%) | Its source rows/cells | On demand | Trust ("is this number real?") | Evidence panel summoned beside, row lit | Overlay sheet <1440px |
| Status counts | The roll's own summary sheet totals | On demand | Trust the tie-out | Tie-out chip popover (both totals + delta) | Same |
| Unit row | Its pre-dedup source rows | On demand | Understand the 322→301 collapse | Inline expansion under the row | Same |
| Normalized roll | Original document bytes | No | Deeper audit | Link to DocView (navigation, with return path) | — |
| This roll | The prior roll | No in v1 | Roll-over-roll change (v2 scope) | Not built in v1; version switch only | — |
| Unit table row | Its floorplan-mix row | No | Plan-level context | Floorplan cell links to Region D (scroll/tab focus) | — |
| Occupancy figure | Any covenant floor | **NEVER on this surface** | — (identity guard) | Cross-reference chip navigates to the covenant surface | — |

No pane exists merely because information exists: the floorplan mix demotes to a tab rather than forcing a second pane.

## 10. Layouts and viewport behavior

| Viewport | Rail | Work area | Topology (ratios) | Evidence panel |
|---|---|---|---|---|
| 1440px | 240px (or user-collapsed 48px) | ~1200px | A/B full width; C full width; D below C | Overlays right at 400px; C does not reflow; lit row auto-scrolls into view |
| 1728px | 240–280px | ~1488px | C 1028px + E 420px + gutters (69/28 split); D below | Docked side pane |
| 2048px | 280px | ~1808px | C 1200px + E 480px; D may pin beside C's lower half (interior inspector, not a third work window) | Docked, wider trace text |
| <1280px | icon rail 48px | remainder | A compresses to two lines; B wraps 2×3 + occupancy line; C horizontal-scrolls inside its own container (body never scrolls sideways) | Labeled tab / overlay sheet — no silent compression (08 §9) |

- Minimums honored at every step: C ≥ 720px (primary-window law), E ≥ 360px, or the pane demotes to a labeled tab.
- Focus behavior: selecting a unit row pins it; summoning Evidence keeps the selection lit.
- Compare behavior: none in v1 (roll-over-roll compare is v2 — stated, not stubbed).
- Proof/source behavior: lit-row per §8-E at every width; below 1440 the Evidence sheet overlays from the right and the lit region auto-scrolls.
- Minimum viable dimensions: 1152×720 frame minimum (08 §9); this surface remains fully usable there (registers-class surface).
- Tab stacking: D → tab below 1728px; E → overlay below 1728px.

## 11. Components and exact anatomy

- `CovenantShell` — REUSE (mounts the route; rail item added per 08 §3).
- `RollIdentityBand` — NEW. Parts:
  - `PropertyPicker` — type-ahead over properties with filed rolls; reuses the palette's object-row rendering (loan/period identity chips per 08 §5).
  - `RollVersionSelector` — reuses the period-selector popover pattern from the breadcrumb spec (08 §2): version list with filed dates and status dots.
  - `ProvenanceLine` — "Recreated from {PMS} export · filed {date} via intake · source ↗"; the derivative label is mandatory (document-artifacts law).
  - `TieOutChip` — states clean / failed / running; popover shows row-derived total, summary-sheet total, delta.
  - `DedupChip` — "322 rows → 301 units"; popover lists the collapsed-row ledger (the 21 dual rows on the evidence fixture) with unit links.
- `OccupancySummaryBlock` — NEW. Parts:
  - six `StatusCountFigure` atoms — count + normalized label; click filters Region C to that status and lights the aggregation in Evidence.
  - `OccupancyFigure` — numerator / denominator / percent in Geist Mono `tabular-nums slashed-zero`; dotted provenance underline (inferred-class grammar, 06 §7).
  - `DefinitionNote` — summonable; quotes the reader's definition text verbatim with its source ref; never paraphrases.
- Unit table — REUSE the app-wide uniform-row grid primitives (tables/grids cross-cutting system, 07 §1E): uniform row heights, open-not-boxed, virtualized. NEW column set as §8-C. `StatusCell` shows normalized label; hover reveals "source label: {PMS string} · mapped {date}" (memory provenance, 03 §3 grammar).
- `FloorplanMixTable` — NEW, same grid primitives; each aggregate cell carries provenance refs.
- Evidence panel — REUSE the review-room Evidence panel component with the repaired lit-row contract (`cross-cutting/provenance-lit-row-trace.md`; fix U1-F1 before this surface adopts it).
- `UnmappedStatusFlag` — NEW: names the unknown label, links to the pipeline's exception card; renders the excluded-unit count beside affected aggregates.
- `NewerRollBar` — NEW: supersession notice + one-click switch.
- `CrossReferenceChip` — NEW: "feeds {loan}'s occupancy →"; navigates with return path; never carries verdict color or verdict text.
- `DownstreamUseNote` — NEW: certified/sealed package references (links to Reports rows).
- Empty/error/recovery objects — REUSE the app-wide empty-state object; the awaiting-roll state includes the read-only intake-address line (rendered by the same component Intake uses, display-only).
- Saved views — REUSE the shared SavedView mechanism.
- CountBadge — NOT used here (the rail item is quiet, badge-less by design).

## 12. Interaction specification

- Selection: single row select (click or ↑/↓); selection drives Evidence.
- Hover: status cells reveal source label + mapping provenance; figures reveal confidence class per the three-actor grammar (06 §7).
- Focus: full keyboard focus order A → B → C → D → E; visible focus ring from the ruled accent family tokens.
- Keyboard map:

| Key | Action |
|---|---|
| `/` | Focus the property picker |
| ↑/↓ | Row navigation in the unit table |
| Enter | Open Evidence for the selected row (lit-row) |
| `E` | Toggle the Evidence panel |
| `1–6` | Filter to a status (taxonomy render order); `0` clears |
| Esc | Close Evidence → clear selection → nothing (never navigates as a side effect) |
| `⌘K` / G-chords | Palette and global places per 08 §5 — no new G-chord is claimed for `/rent-roll` in v1; palette + rail suffice for a quiet utility |
- Editing and validation: none — read-only surface; the only mutating affordance is a deep link out to the pipeline's exception card.
- Bulk action: none in v1.
- Undo/recovery: nothing destructive exists; version switching is freely reversible.
- Sorting/filtering: sort any column; filter by status/floorplan/lease-expiry window; filters compose with saved views; the aggregate band always shows whole-roll numbers with a "filtered view — aggregates are whole-roll" note (no silently filtered denominators, ever).
- Drill-down and return path: Evidence → "open in DocView" → DocView with a return crumb; cross-reference chip → covenant surface with return.
- Source-linked selection: the lit-row behavior per the frame contract (08 §7) — click a figure, the exact source line lights and stays lit until dismissed.
- Save/persistence: saved views + last-selected property per user.
- Collaboration/commenting: none in v1 (utility surface).

## 13. Visual craft direction

- Typography: page title and property name in the app sans at the register-header size; all counts, percentages, rents, SQFT in Geist Mono with `tabular-nums slashed-zero` (ruled setting). Provenance lines at the small quiet size on gray rung ~6 of the ten-rung ladder.
- Financial-number treatment: right-aligned mono columns; negative/credit charges rendered with a minus sign, never color-only.
- Spacing rhythm: the register rhythm — 8px base grid, 12px cell padding, 40px section gaps between B, C, D.
- Density: default comfortable; a compact toggle obeys the app-wide density setting (Settings → Appearance), uniform row heights preserved in both.
- Open ground vs earned boundaries: open-not-boxed tables (locked law); the occupancy summary sits on open ground separated by whitespace, not cards; the only hairlines are the table header rule and the Evidence panel seam, from the gray ladder.
- Dividers/elevation: Evidence panel earns the one elevation step (same treatment as the review room's panel); popovers use the standard raised surface from the adopted ladder.
- Semantic color: status counts are neutral (gray-ladder text) — status is taxonomy, not severity; the ONLY accent-family use is interaction (selected row #7189FF family: hover #8EA1FF, active #6078F4, focus/lit-row highlight per the Evidence panel's existing treatment); tie-out failed state uses the app's standard exception treatment, not a new color. Zero new colors proposed (settled law).
- Three-actor badge grammar at point of use (06 §7): reader outputs render as inferred (dotted provenance underline; confirmer stamp on hover once mappings are confirmed); source values render document-native inside Evidence; nothing on this surface ever wears the certified paper treatment.
- Certified sheet treatment: not touched here — this surface never renders the certified paper; downstream-use notes are plain links.
- Focus/selected/hover: consume the existing Covenant control-layer tokens (Ruling-J measurement: the control layer exists; consume, never restyle).
- Chart style: no charts in v1 (doctrine: the mix table out-encodes any bar of six counts; a chart that cannot out-encode adjacent text does not ship).
- Motion: filter transitions and Evidence summon at the app's standard short duration; reduced-motion setting collapses them to instant.
- Long-session ergonomics: n/a-short surface, but the table honors the app-wide virtualization + contrast rules.

## 14. Benchmark research and synthesis

| Product | Limited role | Official evidence | Exact pattern to take | Covenant adaptation | What to reject | Why this reference beats alternatives |
|---|---|---|---|---|---|---|
| ABBYY | Derivative honesty | https://support.abbyy.com/hc/en-us/articles/19098939370515 (R2 research) | "Text under the page image" — original pixels on top, recognized layer beneath | The "Recreated from {PMS} export" label on the identity band mirrors the Documents artifact grammar; the normalized view is always labeled a derivative | OCR-proofreading loops | It is the canonical honest-derivative model, already adopted app-wide (kit law: document artifacts) |
| Instabase | Cell-level grounding | https://docs.instabase.com/automate/validating-documents (R2 research) | Field↔source-region provenance; their documented table limitation (whole-table lighting only) | Covenant lights individual source cells/rows — deliberately exceeding the documented limitation for the unit table and aggregates | Reviewer-workforce apparatus | Names the exact table-provenance bar this surface must clear |
| Hebbia | Citation click-through | https://www.hebbia.com/blog/introducing-matrix-the-interface-to-agi (R2 research) | Every derived cell citation-linked to the exact source location; peek-before-jump previews | Aggregate figures peek their source region in the Evidence panel before any DocView navigation | Free-prose cells as stored records | Citation-first posture at grid scale is exactly the aggregate-to-source contract |
| Box | Version mechanics | https://support.box.com/hc/en-us/articles/360043697054-Accessing-Version-History (R2 research) | Version stack under one identity; promote/inspect without forks | `NewerRollBar` + version selector over the Documents version chain | Filename-collision version inference | The simplest proven version-stack UX; Covenant adds the explicit typed filing question upstream (Documents brief) |
| Attio | Register/record projection | https://attio.com/help/reference/managing-your-data/views/create-and-manage-table-views (R5 research) | Saved table views; table and record page as two projections of one object | Property/roll register + normalized roll view are two projections of the reader's one output; shared SavedView mechanism | User-editable schema | Cleanest object→view model among register donors |
| Mercury | Calm register | https://support.mercury.com/hc/en-us/articles/38790547830036 (R5 research) | Header stats bound to the active filter; quick/advanced filter split | Whole-roll aggregates stay pinned with an explicit "filtered view" note — the deliberate inverse, chosen so denominators never silently shrink | Keyword-first navigation | Its stat-filter binding is the pattern we must consciously invert for occupancy denominators, and naming that choice prevents a subtle integrity bug |
| Rossum | Proposal-state grammar | https://rossum.ai/help/faq/bounding-box/ (R2 research) | The proposed-vs-human-validated visual state machine with hoverable provenance | The status cell's hover provenance ("source label → normalized label, mapped {date}") reuses the same proposed/confirmed grammar the pipeline surfaces use | Auto-export past humans | Keeps the teaser's provenance grammar identical to the confirmation surfaces' — one language app-wide |

Synthesis: the composed surface is a provenance-first normalized register — ABBYY's derivative honesty on the label, Instabase/Hebbia's grounding on every figure, Box's version stack, Attio's projection discipline — pointed at a domain object none of these products has: the multifamily rent roll with its dual-row future leases and six-state taxonomy. Originality and domain correctness come from the evidence kit itself: the surface is designed around the exact failure modes of the real export (322 ≠ 301; roll-vs-summary disagreement; alien status labels), and around the borrower-side law that this data feeds lender reporting but never renders a lender verdict here.

## 15. Domain references

Domain semantics (terminology, expected columns, status vocabularies) come from PMS detail exports of the evidence kit's shape and from the servicer's own occupancy asks (the JLL questionnaire's month-end occupancy fields — evidence: SLOT-5), plus Finley-class deliverable-tracking products (R5 research: Finley) for how borrower-side reporting products name things. Status vocabularies differ per PMS — the six-state taxonomy in this brief is the evidence roll's own, and the reader's mapping layer (ask-once per property/PMS) is the mechanism that absorbs other vendors' vocabularies without this brief pretending to enumerate them (domain-content firewall). Domain authority does not equal visual authority: none of these dictates a pixel. Occupancy definitions (denominator, Admin/Down treatment, physical vs economic) are per-loan questions pinned on RequirementRecords (05 §7.3) — covenant semantics come from the loan documents and Terry, never from any referenced product.

## 16. Accessibility, performance, and safety

- WCAG contrast: all text on the ten-rung ladder meets AA at the sizes used; status is never conveyed by color alone (labels always print).
- Focus: visible ring on every interactive element; the lit-row highlight pairs with an aria-live announcement ("source row {n} highlighted").
- Keyboard completeness: every action in §12 keyboard-reachable; Evidence panel focus-trapped while open, Esc releases.
- Screen-reader semantics: the unit table is a real table with column headers; aggregate figures carry aria-describedby linking their provenance summary; the dedup chip reads "322 source rows collapsed to 301 units, 21 future-lease rows merged."
- Table virtualization: virtualized rows above ~200 units; the evidence fixture (301) and large properties (1,000+) both smooth at 60fps targets.
- Loading/latency: skeleton rows only for the table body; aggregates never render as placeholders — they appear when computed or show the processing state (fail-closed honesty).
- Destructive confirmation: n/a — nothing destructive exists here.
- Certify/send safety: n/a here by design; the identity guard keeps this surface outside the certify/send blast radius.
- Source immutability: originals untouched; the view reads reader output only.
- Auditability: every normalization, tie-out, and mapping reuse is an ActivityEvent; the view can always answer "which roll version, which reader version, produced this number."
- Resident-data safety: resident-identifier columns are structurally absent from this surface's queries, not merely hidden — a renderer cannot leak what it never receives.

## 17. Acceptance tests and fixtures

Fixtures: `CAL-roll-2018-04-30` (the Calloway Park SLOT-2 evidence roll: 24 columns, 322 rows, 301 units, statuses 259/18/2/13/2/7, summary sheet totals, floorplan mix sheet); `BEX-canon` (Bexley demo book, canon occupancy 268/301 = 89.04%).

- T1 (reader spine): filing `CAL-roll-2018-04-30` through the intake pipeline renders `/rent-roll` with dedup chip "322 rows → 301 units", six status counts exactly 259/18/2/13/2/7, physical occupancy "279 / 301 = 92.69% · as of 04/30/2018", tie-out chip clean (summary sheet 301 ✓).
- T2 (dedup ledger): clicking the dedup chip lists exactly 21 collapsed rows, each expandable to its unit's row pair; each pair's source rows light in Evidence.
- T3 (lit-row): clicking the 92.69% figure opens Evidence with the aggregation's input rows lit, and the lit rows remain lit through scroll; clicking a unit's lease rent lights that exact source cell region (regression-guarded against the U1-F1 wrong-row defect — this surface may not ship on the buggy trace).
- T4 (tie-out failure): a mutated fixture whose summary sheet says 302 renders the failed tie-out chip with "rows 301 · summary 302 · Δ1", and the occupancy resolver consumers render blocked on their covenant surfaces.
- T5 (unmapped status): a fixture with a seventh alien status label renders those units in the unmapped group, aggregates annotated "excludes n unmapped units", flag deep-links to the pipeline exception card; after confirmation, return path lands back here with aggregates updated and the mapping provenance visible on hover.
- T6 (identity guard): with `BEX-canon` loaded, `/rent-roll` renders 268/301 = 89.04% as a plain figure; automated copy-scan asserts the strings "shortfall", "breach", "pass", "watch", any floor value, and any verdict chip component are absent from the route; the cross-reference chip navigates to the covenant surface where the SHORTFALL verdict renders correctly (canon).
- T7 (resident-data law): the route's data payload contains zero resident-identifier fields (schema-level test), and no column configuration can summon one.
- T8 (viewports): 1440 (Evidence overlays), 1728 (Evidence docks 1028/420), 2048 (1200/480), 1152×720 minimum — no horizontal body scroll at any width; the unit table scrolls inside its own container; D demotes to a tab below 1728.
- T9 (keyboard): full pass of §12 with rail collapsed; `1–6` filters match taxonomy order; Esc order (Evidence → selection → nothing).
- T10 (versions): filing a second roll version shows `NewerRollBar`; switching is instant and reversible; the superseded view stays addressable; downstream certified-package note renders where wired.
- T11 (no pricing UI): route-level assertion that no upgrade/pricing/upsell component renders (RULED v1 boundary).
- T12 (empty and awaiting states): an org with zero filed rolls renders the empty state with the read-only intake-address line; a property whose checklist expects a roll renders awaiting-documents with the chase status summarized from Intake (link, not ownership).
- T13 (permission and scope): a PMC preparer sees only their Client's properties; a reviewer sees the full render with zero mutation affordances; a denied scope renders the standard denied state with no data-shape leak.
- T14 (accessibility): axe-clean at all four viewports; the T6 figure and T1 aggregates readable by screen reader with provenance descriptions; status never color-only.
- T15 (benchmark challenger): reviewer walks the Instabase cell-grounding bar and the Hebbia peek pattern against T3; failure on either returns the build to ADJUST.

## 18. Build plan

- Dependencies, named: **F3 rent-roll canonical reader** (the load-bearing dependency — dedup, taxonomy, aggregates, tie-out; its acceptance tests come from the SLOT-2 evidence per 06 §4); F1/V2 intake pipeline (arrival path — no separate intake is built for rolls); F2 persistence and tenancy (property/document/version tables, Client scoping); Documents filing (canon home); the repaired lit-row trace (`cross-cutting/provenance-lit-row-trace.md` — U1-F1 fix is a hard predecessor).
- Foundation work, in order:
  1. Reader output schema + the read API this view and the Actuals REGION 3 both consume (one reader, two renders — the structural guarantee behind §3's no-double-homing claim).
  2. Route scaffold in the `(covenant)` group mounting `CovenantShell` (components land in `src/components/covenant/rent-roll/`; reader in `src/lib/covenant/`; tokens consumed from `src/styles/covenant-tokens.css` — build-target law).
  3. Rail item behind the flag (08 §3).
- Components first: `RollIdentityBand` (with `TieOutChip`/`DedupChip`), `OccupancySummaryBlock`, the unit-table column set on the shared grid primitives — in that order, because the identity band's tie-out/dedup chips are the surface's trust spine.
- Vertical slice (the send-vertical pattern): one route, one property, engine data end to end — file `CAL-roll-2018-04-30` → reader → `/rent-roll` renders T1 with live lit-row Evidence. No fixture strings anywhere in the slice.
- Migration from fixture data: none to migrate (surface is net-new); the Bexley canon occupancy remains a book.ts fixture only until the reader supersedes it on covenant surfaces.
- Rollout/feature flag: `rentRollTeaser` flag gates the rail item and route; ships when T1–T7 pass; the v2 monetization boundary is a plan note, not product chrome (RULED).
- Proof artifacts required: T1 screenshot set at all four viewports; the T3 lit-row screen recording; the T6 copy-scan report; the T7 schema-level payload assertion in CI; the reader's SLOT-2 acceptance-test run output (322→301, 259/18/2/13/2/7, 92.69%, tie 301 ✓).
- Occupancy-resolver handshake: when this slice ships, the engine's occupancy resolver unblocks on the same reader output (gap 7); the covenant surfaces' occupancy figures and this surface's figures must be byte-identical reads of one output — a shared integration test pins it.
- Final gate: `PASS` when T1–T15 green; any tie-out or identity-guard failure is an automatic `ADJUST` return.
