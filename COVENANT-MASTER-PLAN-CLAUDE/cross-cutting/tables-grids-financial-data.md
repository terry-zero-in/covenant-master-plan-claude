# CROSS-CUTTING — TABLES, GRIDS, AND FINANCIAL DATA

Ruled law this file applies: **uniform row heights, open-not-boxed** (snapshot §4, locked design law) · Geist Mono `tabular-nums slashed-zero` for financial figures (Review-9 + fourth-switch COLORS-ONLY ruling: Geist Mono stays) · Reports = the Terry-named gold-standard list page (snapshot §3) · the lit-row provenance contract (`cross-cutting/provenance-lit-row-trace.md`) · Lucide-only, no emoji. Current state: every register and grid in the build is hand-rolled per surface against the `book.ts` fixture (snapshot §3); this file is the one data-grid law and the one primitive that replaces them — the CountBadge consolidation (37 hand-rolled atoms across 21 files → ONE shared component, snapshot §5) is the named precedent and the method.

## 1. Scope — what is a CovenantTable and what is not

**Is:** every register (Loans, Reports, Inbox, Documents, Calendar agenda rows, Intake queue + holding checklist, Loan Detail PERIODS/SCHEDULE/DOCUMENTS/ACTIVITY tabs, correspondence thread list, Settings memory inspector + COA mapping library + roles matrix, Rent Roll unit + floorplan-mix tables) and every data grid (Actuals exceptions table, normalized statement grid, rent-roll normalized view, tie-out delta module) (07 §1). One primitive, per-surface columns via config (§14–15).

**Is not:** the certified sheet (paper artifact under the ruled blue-violet hexes — a document, not a grid), DocView's structured-reading tables (they mirror the source document's own layout — document content, not app furniture), the review-room package draft canvas, card stacks, and outline lists. These still obey uniform row heights and mono-figure typography where they render tabular figures, but they are not CovenantTable instances and must not fake its interaction grammar.

**Trust boundary, restated for grids:** every figure a table renders is deterministic engine output or labeled source/inferred content (three-actor grammar, 06 §7). A table is a renderer. It never computes, rounds differently, or re-derives a shipping number (locked trust hierarchy).

## 2. The two ruled visual laws

### 2.1 Uniform row heights (ruled)

- Every row in a given table renders at exactly one height — the density token (§8). No row ever grows: text truncates with a title tooltip; multi-value cells summarize ("3 properties") and expand elsewhere (row open / summoned pane), never inline.
- Wrapping is structurally impossible: cell content is single-line by construction. A column whose content cannot survive truncation is a wrong column (move the content to the row's opened object).
- Group headers, section dividers, and the bulk bar have their own fixed heights — also uniform per kind. Uniform heights are what make virtualization exact (§9) and skeletons truthful (§11).

### 2.2 Open-not-boxed (ruled) — earned boundaries only

- **Zero cell borders. Zero vertical rules. Zero zebra striping. No boxed table frame.** Alignment does the separating: columns are separated by whitespace and by the column type's alignment discipline (§3), not by lines.
- The only permitted horizontal boundaries: a hairline under the column header row, and hairline **group dividers** where the register is grouped (due-window groups on Calendar, taxonomy groups on the extraction inspector, COA group subtotals on the statement grid). A boundary must mark a real semantic seam — that is what "earned" means.
- All hairlines and resting text consume the ten-rung gray ladder (adopted 2026-08-07, 55 re-point sites; snapshot §4): hairline rung for dividers, secondary-text rung for metadata cells, primary-text rung for content. Zero local grays — verified by canvas readback, never eyeball (oklch caution, snapshot §4).
- Row hover = one gray-ladder rung shift on the row ground; row focus = accent-family focus treatment (§4). No shadows, no elevation inside tables (elevation is for the canvas panel and menus, 08 §4).

### 2.3 Row anatomy (authoring defaults; [VERIFY against shipped metrics, measurement-gated])

- Horizontal cell padding 16px; first/last cell padding 20px to the table edge; the leading gutter (checkbox/star zone, when present) 40px fixed.
- Cell content vertically centered in the density row height (§8); chips/badges vertically centered at fixed heights (20px chip at both densities — chips do not scale with density).
- Group header rows: 32px, group label in secondary-text rung + optional CountBadge; group subtotal rows on grids (statement grid COA groups): density row height, figures in mono, bold-weight per the type scale, hairline above only (the earned seam).
- Column headers: 32px, secondary-text rung, sortable headers render the sort affordance on hover/focus only (quiet at rest).

## 3. The column type system

Every column in every config declares exactly one type. The type fixes alignment, face, truncation, sort semantics, and interaction — surfaces choose columns, never per-cell styling.

| Type | Alignment | Face | Contract |
|---|---|---|---|
| `text` | left | app sans | Single line, truncate + title tooltip. Never carries figures |
| `entity-link` | left | app sans | Outward names only (names law: Bexley, Westbrook Flats, Calloway Park — real names never print). Click/Enter navigates to the owning object; carries loan/period identity where ambiguous (palette-chip grammar, 08 §5) |
| `date` | left | app sans | Absolute date canonical; relative qualifier secondary ("due Mar 17 · in 12d"). Due dates carry due-rule provenance on hover ("FYE+120d per §8.02(b)(2)" — evidence-class cite, per calendar direction). Deadline urgency renders via the row's status/verdict chips, never by coloring the date text (severity color belongs to severity carriers only) |
| `figure` | **right** | **Geist Mono `tabular-nums slashed-zero`** | Financial figures and ratios. Right-aligned so magnitudes scan vertically; tabular numerals so columns don't shimmer. Signed values print an explicit sign (signed headroom is dual-denominated, 05 §3); sign is never color-coded. Material figures wrap in `<Figure>` → the provenance-carrying cell (below) |
| `figure-provenance` | right | Geist Mono, as above | A `figure` cell whose value carries a `ProvenanceRef` per the lit-row contract (provenance §2.1–2.2): dotted provenance underline per three-actor grammar (06 §7), click/Enter at cell level → `traceToEvidence(ref)` lights the exact source line beside the claim. Legacy fixture values render the "no source" affordance and count toward the migration burndown (provenance §5) |
| `mono-id` | left | Geist Mono | Identity strings: hashes, form numbers, COA codes. Reports' hash chip is the exemplar: short hash, click = full + verify (reports direction). Not right-aligned — these are identifiers, not magnitudes |
| `status-chip` | left | chip | Period lifecycle vocabulary only: open → in-review → ready → certified → sent/sealed (03; the deterministic state machine's states, snapshot §3). Chips consume ruled tokens; no per-surface synonyms |
| `basis-badge` | left | badge | `covenanted` \| `monitored` — structural, rendered from `basis_echo`, never inferred by the renderer (05 §3). Always legible wherever a verdict renders |
| `verdict-chip` | left | chip | pass / watch / shortfall / breach. **Breach is renderable only when `basis_echo = covenanted`** — the fail-closed dispatcher makes the canon sentence (268/301 = 89.04% is a SHORTFALL vs a monitored 90% floor, never a breach) impossible to violate (05 §3). A verdict chip never renders without its basis badge in the same row |
| `count` | right | CountBadge | The ONE shared CountBadge component (37→1 consolidation, snapshot §5). Counts are computed queries, never unread (C-8 fix, 08 §8) |
| `star` | center | icon | The one pinning mechanism (08 §3); Lucide star, toggles inline (borrower-owned, §5) |
| `actions` | right | icon | Row overflow menu (vertical dots) for row-scoped secondary acts (R5 research: Mercury — actions live on the row, not a toolbar). The row's PRIMARY act is always row-open (Enter); overflow never hides a gate act |

Column order convention: identity (entity-link) leftmost → descriptive text/dates → figures right-of-center → chips/badges → count/star/actions rightmost. Figures cluster so the mono column block reads as a statement.

### 3.1 The Figure cell contract (the lit-row law inside a grid)

The `figure-provenance` cell is where the table law and the provenance law meet, so its contract is explicit:

- The cell mounts the one `<Figure>` component (provenance §2.2) — the table never re-implements provenance rendering. Props: `{ ref: ProvenanceRef, format }`; the cell supplies only layout (right-align, mono, density padding).
- Three-actor grammar at rest (06 §7): inferred-proposed = confidence + dotted provenance underline, never bold-final; inferred-confirmed = provenance underline + confirmer stamp on hover; certified = certified badge (the paper treatment itself stays on the sheet — in a register the certified class renders as badge grammar, not the blue-violet hexes, which are ruled for the light document surface only).
- Activation (cell-level Enter or click) dispatches `trace(ref)`; the Evidence context opens **beside** the register (summoned pane per frame law 08 §4, positioned to not cover the clicked cell) and the exact source line lights and stays lit (the ruled mechanic). The lit target derives only from `ref.chain` — never from row index or render order (the U1-F1 invariant, provenance §2.4).
- A figure with no ref (legacy fixture) renders the visibly-weaker "no source" affordance and increments the CI fixture-migration burndown (provenance §5). A register full of unprovenanced figures is a measured debt, never a hidden one.
- Aggregate figures (occupancy on a rent-roll summary row, portfolio UPB totals) carry refs whose chains include the aggregation step — the rent-roll teaser's "322 rows → 301 units" dedup must be visible inside the chain (06 chain C), and portfolio aggregates carry the property-dedup rule (02 §1).

### 3.2 Chip and badge anatomy

- **Status chip:** label + the lifecycle state's token treatment; sealed adds the seal glyph (Lucide). One vocabulary app-wide (03) — a config supplying a synonym string is a type error, not a style choice.
- **Basis badge:** text-only badge (`covenanted` / `monitored`), gray-ladder treatment — basis is structure, not severity, so it never takes severity color. It exists so no reader ever has to guess which failure vocabulary applies (05 §3).
- **Verdict chip:** verdict word + severity treatment. `pass` renders quiet (gray ladder — color absent at rest; severity color appears only when severity does, mirroring the chart doctrine's severity-only rule). `watch` / `shortfall` / `breach` consume the ruled severity roles from the Review-9 token set [VERIFY exact token names against the 43-token package; if a needed role is absent that is a token-authority question for Terry under the COLOR LAW — this file mints no value]. Signed headroom, where the column shows it, renders in the adjacent `figure` cell in both denominations (ratio/pp + dollars, 05 §3), never inside the chip.
- Chips are instruments: a verdict chip click traces to its TestResult inputs (provenance adoption item 3); a status chip click opens the period; a hash chip click expands short → full + verify (reports direction).

### 3.3 Sort semantics per type

| Type | First sort direction | Rule |
|---|---|---|
| `figure` | descending | Numeric on the engine value, never the formatted string; null/blocked sorts last with its state chip visible |
| `date` | ascending (soonest due first) | Chronological; undated rows last |
| `text` / `entity-link` | ascending | Locale alpha on the outward name |
| `status-chip` | lifecycle order | open → in-review → ready → certified → sent/sealed (03), never alpha |
| `verdict-chip` | severity order | breach → shortfall → watch → pass (worst first — triage order); ties break by signed headroom ascending (closest to the line first) |
| `count` | descending | Numeric |

## 4. Selection model

Three levels, strictly separated:

1. **Row focus** — always available, single, keyboard-driven (§10). Focus is not selection: it renders as the accent-family focus treatment (`--acc` family; focus ring per app law) and drives Enter/Space.
2. **Range selection** — Shift+click / Shift+↑↓ extends from the focused row. Exists only where a bulk act exists.
3. **Checkbox bulk** — a leading checkbox column renders **only where a real bulk act exists**. Checkboxes are earned by acts, never decorative. Named bulk acts in v1: Inbox bulk snooze-with-reason; Intake bulk dismiss-not-relevant (arrival decisions otherwise stay per-item — the decision set is per-arrival, intake direction); Documents bulk download; Settings COA-library bulk export. Registers with no bulk act (Reports' sealed register, the Loans book) render **no checkboxes**.
4. **Gate acts are never bulk.** Certify and Send are typed acts with legal weight, one loan+period at a time (locked trust hierarchy; 04 gates). No bulk bar may ever carry them — structurally excluded from the BulkBar act registry, not by convention (§17 test 10).
5. `Cmd/Ctrl+A` selects the **filtered scope** — the rows the active view currently admits — never the whole store (§6). The header restates it: "12 selected of 12 shown · 43 total". Selected state renders as `--accT`-tinted row ground (accent tint token; no new colors).
6. Selection survives sort changes, dies on filter changes (the scope changed under it — silent survival would select invisible rows), and is never carried in the URL.

## 5. Inline edit law

- **Only borrower-owned fields are inline-editable** (R5 research: Attio — spreadsheet-style inline editing, adopted narrowly). Borrower-owned in v1: star, internal notes, delegation, snooze, internal prep dates (the Karbon dual-date's internal side, R5), recipient contact fields (with provenance, send-record direction).
- **Engine outputs are never inline-editable**: metrics, verdicts, headroom, statuses, hashes, computed due dates, occupancy aggregates. An edit gesture on a computed cell answers with its ownership: "Computed — trace to source" → the lit-row trace. Correction happens at the owning surface (COA mapping, confirmation, extraction) where source sits beside the decision (05 §4 co-visibility) — never in a register where the evidence isn't co-visible.
- Extracted/inferred values likewise never edit in a register: Correct routes to the confirmation surface with the source region lit (extraction direction). Ask-once law holds: a confirmed mapping edited upstream propagates; the register only ever displays.
- Every inline edit is an act: quiet-logged (ActivityEvent), attributable, and — where the field feeds a certified package — preceded by a downstream-impact warning, because the change fires void-on-change (06 §6). No silent mutation past a gate, ever.
- Edit affordance appears on hover/focus of editable cells only; commit on Enter, cancel on Esc, and the cell never changes height (uniform rows).

## 6. Sorting, filtering, and saved views — ONE shared mechanism app-wide

One implementation (`ViewBar` + view store), mounted by every CovenantTable. No surface builds its own filter UI.

- **Filter cluster:** 4–6 curated structured chips per surface (loan, lender, period range, status, due window…), pinned top-left of the register (R5 research: Stripe Dashboard filter placement, https://docs.stripe.com/dashboard/basics — take the pinned structured filters, reject the open-ended query-builder surface area: a 2–50-loan book is navigated by structure, not free text).
- **Sort:** column headers are focusable controls; Enter/click cycles asc → desc → off; one secondary sort via Shift. Sort state is part of the view.
- **Saved views:** any filtered/sorted register state saves as a named view (R5 research: Linear custom views, https://linear.app/docs/custom-views — take view scoping wholesale). Scope **personal → shared (workspace)**; a shared view's filter/sort state is common to all members (R5 research: Attio, https://attio.com/help/reference/managing-your-data/views/filter-and-sort-views), with **ephemeral filtering** for exploration that never mutates the shared view (Attio's changelog mechanic, adopted).
- **Star-to-rail:** frequently used views star into the rail's one Starred group (08 §3 — one pinning mechanism; Linear's star-to-sidebar, adopted).
- **URL-carried state:** the full view state (view id, or ad-hoc filters + sort) round-trips through the URL, so any register state is a shareable deep link (Linear's Cmd/Ctrl+Shift+C mechanic, adopted; the addressability rule, 08 §1). "Q3 packages — Fannie loans" is a link any teammate opens to the identical register (R5 synthesis d).
- **Default landing:** the top-ordered view is the surface's default (Attio, adopted); reordering views is a shared-view admin act.
- Filters double as audit scopes on Reports ("everything sent to {lender} in 2026", reports direction) — same mechanism, no special case.
- **Header stats recompute against the active filter** (R5 research: Mercury, https://support.mercury.com/hc/en-us/articles/38790547830036 — adopted as numbers, not graphs: charts stay under the chart doctrine and Reports is ruled chartless; `charts-under-doctrine.md` §3).

Ownership note (no-double-homing, 07 §2): a saved view is a **query**, not a copy — "everything due in 30 days" on Home, Calendar, and the rail badge all read the same computed rows. The view mechanism must therefore sit on the shared query layer, not per-surface caches; two surfaces disagreeing on a count is the U3-F2-class contradiction this architecture makes impossible.

### 6.1 Filter chip grammar

- Chips are structured (field + operator + value), rendered in the ViewBar in application order; each chip removable individually; the set clears with one act ("clear filters" in the filtered-empty state, §12).
- Chip values come from the object model's enums and entities (lender list, period range picker, status vocabulary) — never free-text contains-matching on figures. Free text belongs to `/`-register search, which matches identity fields (names, form numbers, hashes) and is itself not a filter chip.
- Segment controls (Live/Pipeline on Loans, register modes on Documents) are top-level view axes rendered as tabs above the ViewBar — they scope which config renders, and saved views record their segment.

## 7. The count+CTA header — Reports is the gold standard (ruled)

Anatomy, in order: **count** ("{n} packages · this cycle {m}", CountBadge grammar) → **filter cluster** → **primary CTA** (right-aligned; only where the register owns a creating/primary act — Reports' register of sealed records has export, not create).

- **Count honesty law:** the count always equals the rows the active view admits, and the register renders them all (or virtualizes them all, §9). A register never shows fewer rows than it claims without stating the filter — this kills the U3-C1 class of defect (3 of 7 outstanding docs rendered) and the U3-F7 badge lie (unread counted, review-count labeled) structurally: one computed query feeds both count and rows.
- With selection active, the header appends "k selected" and the BulkBar's acts (§4).
- The header is part of the CovenantTable primitive — surfaces get it by config, so the gold standard propagates by construction, not by imitation.

## 8. Density modes

- Two densities, app-wide, per-user, set in Settings → Appearance (settings direction): **comfortable** (default) and **compact**.
- Authoring defaults: comfortable row 40px, compact row 32px — recorded as tokens in `src/styles/covenant-tokens.css` and [VERIFY against the shipped row metrics; the re-point executes measurement-gated, per the grey-ladder and CountBadge method — no visual change unmeasured].
- Density changes row height and vertical cell padding **only**. Type scale, column widths, chip sizes, and hairlines do not change — a density switch may never reflow columns or resize text.
- Density is uniform per table and per app; no per-surface overrides (one law, or it isn't one).

## 9. Virtualization — thresholds and the three sizing fixtures

The sizing fixtures (evidence + canon; these are the perf test beds, §17):

| Fixture | Shape | Verdict |
|---|---|---|
| The loan book | ≤50 loans (user scale 2–50; demo book 7 loans) | **Never virtualizes** — render all rows; virtualization overhead exceeds its value below ~120 rows |
| T-12 statement grid | ~150 rows × 14 columns, group subtotals (evidence: SLOT-1) | **Virtualizes** |
| Rent roll unit table | 322 lease rows / 301 units, 24 columns (evidence: SLOT-2; unit dedup renders "322 rows → 301 units") | **Virtualizes** |

- **Threshold:** fixed-height windowing engages above ~120 rows (roughly two viewport-heights at compact density). Below it, render everything — simplicity is a feature at loan-book scale.
- Uniform row heights (§2.1) make windowing **exact**: offset = index × rowHeight, no measurement pass, no scroll jitter. This is a load-bearing consequence of the ruled law, not a coincidence.
- Overscan ~10 rows; the scrollbar reflects the true extent; group headers render sticky and the window respects group boundaries.
- **Search and filter run over data, not DOM**: `/`-focused register search and filter chips hit virtualized rows identically (08 §5).
- Keyboard focus (§10) scrolls the window; `Cmd+A` selects the filtered scope regardless of what is mounted.
- The quiet-log register (append-only ActivityEvent) virtualizes with date-paged fetch; its count header still states the true total (count honesty, §7).
- No infinite scroll masquerading as completeness: registers are bounded and say their bounds.

## 10. Keyboard grammar for grids

Two focus levels — **row level** (registers) and **cell level** (grids and any row whose cells are instruments) — so Enter can be both "open the object" and "activate the instrument" without conflict:

| Key | Row level | Cell level |
|---|---|---|
| ↑ / ↓ | Move row focus | Move cell focus vertically |
| → | Enter cell level (first cell) | Next cell |
| ← | — | Previous cell; at first cell, exit to row level |
| Enter | **Open the row's object** (Loan Detail, sealed record, arrival…) | **Activate the cell's instrument**: trace a `figure-provenance` cell (lights the source line), follow an `entity-link`, expand a hash chip |
| Space | Toggle selection (where selection exists, §4) | — |
| Shift+↑/↓ | Extend range selection | — |
| Cmd/Ctrl+A | Select filtered scope | — |
| Esc | Clear selection → then clear focus → then the frame's Esc ladder (close summoned panes before navigating, 07 §3) | Exit to row level |
| Home/End · PgUp/PgDn | First/last row · page the window | Row start/end |
| `/` | Focus the register search (08 §5) | same |
| J / K | Row-focus synonyms on queue surfaces where ruled (Intake, Inbox directions) | — |
| Tab | Leaves the table to the next frame region; headers are in the Tab order (sort via Enter, §6) | — |

- G-chords always pass through: a focused table never swallows `G H`/`G I`/… (08 acceptance test 5).
- Focus is always visible (accent-family ring) and restored on return navigation (the register remembers its focused row per view).

## 11. Loading skeletons

- Skeleton rows render at the **exact density row height** with column-shaped placeholders (text-length blocks per column type; figure columns skeleton right-aligned). Data arrival replaces content with **zero layout shift** — uniform heights make this testable to the pixel (§17 test 7).
- The count header skeletons as a placeholder chip — it never shows `0` while loading (a false empty is a lie).
- Skeleton shimmer is subdued and disabled under reduced-motion (static blocks; Settings → Appearance + `prefers-reduced-motion`).
- Partial hydration: rows stream in order with the skeleton tail shrinking; sort/filter controls disable until the scope is complete (acting on a partial scope would produce dishonest counts).
- A provenance trace targeting a lazy-loading document keeps the target region skeleton-marked (provenance §4).

## 12. States

| State | Behavior |
|---|---|
| Empty (true) | Domain-real empty state + the constructive next step ("No packages sent yet — your first certified package registers here"; "Send documents to {intake addr}"). Never marketing copy, never a fake chart |
| Filtered-to-empty | Distinct from true empty: "No rows match · clear filters" with the active chips shown. The two must never share copy |
| Awaiting documents | Rows that depend on arrivals render the holding-checklist link (Intake owns the checklist; the register renders links, not copies — no double-homing, 07 §2) |
| Loading | §11 skeletons |
| Partial | Blocked/low-confidence rows render **with their state chips** — never silently dropped (the U3-C1 defect class is structurally dead per §7) |
| Extracted-unconfirmed | `figure-provenance` cells render inferred grammar (dotted underline + confidence, 06 §7); confirm/correct routes to the owning surface (§5) |
| Stale | Source replaced upstream → affected figure cells flag stale + re-confirm path (actuals direction); the row never pretends currency |
| Low-confidence | Confidence renders beside the value per three-actor grammar; sort-by-confidence available where the surface triages (extraction, intake) |
| Conflict / failed tie-out | The delta renders beside both sources (50/50 compare summon, 05 §4); the register row carries the exception chip until dispositioned |
| Watch / shortfall / breach | Verdict chips per §3; basis badge always co-rendered; breach structurally unreachable for monitored rows |
| Permission-denied | Excluded rows are **stated**: "3 rows hidden by role" in the header — never a silently shorter register; a restricted row's trace states the restriction (provenance §5) |
| Read-only | Role-scoped: edit affordances absent (not disabled-and-teasing); bulk acts absent |
| Blocked/gated | Rows blocked by a gate render the gate reason as a link to the blocking item (readiness grammar, composer direction) |
| Certified (void-on-change) | Rows feeding a certified package carry the certified badge; an upstream change flips them to the VOID treatment with reason (06 §6) — visible in the register, never only on the sheet |
| Sent/sealed | Seal badge; row opens the sealed read-only record; traces work forever (seal-not-wipe); no edit affordances of any kind |
| Error | Load failure states the failure + retry; never stale rows presented as fresh, never an empty state masquerading as "no data" |
| Recovery/undo | Inline edits undo within the quiet-log window (Cmd+Z on the register); gate acts and sealed records have **no undo by design** — void and re-certify is the only path (06 §6) |

## 13. Responsive and minimum widths

- Columns declare priority; below minimum width, lowest-priority columns **collapse into the opened row** (labeled, discoverable), never squeeze — the frame's no-silent-compression law applied to columns (08 §9).
- True grids (statement grid, rent roll) scroll horizontally with the identity column(s) frozen left; registers never horizontal-scroll — they shed columns instead.
- Minimum column widths per type: figures never truncate digits (a truncated figure is a wrong number — the column holds its width or the column set sheds a lower-priority column first).
- At the 1280–1439px band the register keeps single-pane layout; summoned preview panes follow frame law (08 §9). Registers remain usable at the 1152×720 minimum frame.

## 14. Per-surface column configs — the consolidation map

The census targets (§15's migration order runs through them). Columns restate each surface's blueprint direction in the §3 type system — the config files are these rows, verbatim:

| Register (config file) | Columns (type in parentheses) | Bulk acts | Notes |
|---|---|---|---|
| Reports `reports.columns.ts` — **gold standard, migrates first** | Period (entity-link) · Loan (entity-link) · Lender (text) · Certified who/when (status-chip + date) · Sent when/transport (date + text) · Hash (mono-id chip) · Status (status-chip, sealed) · → sealed record (row-open) | none (no checkboxes) | Chartless, ruled; filters double as audit scopes |
| Loans `loans.columns.ts` | Loan (entity-link) · Property/ies (entity-link, pledge-aware summary) · Lender/Servicer (text) · Program+Form (mono-id) · UPB (figure-provenance) · Rate (figure) · Maturity (date) · Current period (status-chip) · Next deadline (date, due-rule provenance) · Verdicts (verdict-chip + basis-badge, basis-aware) · ★ (star) | none | Live/Pipeline is a filter segment, not a rail place (08 §3) |
| Inbox `inbox.columns.ts` | Item (text, act-type lead) · Loan+Period (entity-link) · Act type (status-chip-class) · Deadline proximity (date) · State (status-chip: new/seen/snoozed) | bulk snooze-with-reason | "Done" only via the owning surface's real act |
| Calendar agenda `calendar.columns.ts` | Obligation (text) · Loan (entity-link) · Lender (text) · Due (date + due-rule provenance cite) · Period (entity-link) · Status (status-chip: on-track/at-risk/blocked/complete) · Escalation (status-chip) | none | Grouped by due window (Overdue / This week / Next 30 days / Later); groups auto-promote (R5 research: Karbon My Week, https://help.karbonhq.com/en/articles/5724366-best-practices-for-managing-your-work-in-my-week — adopted) |
| Documents FILED `documents.columns.ts` | Document (entity-link) · Type (text) · Loan(s) (entity-link) · Period (entity-link) · Version (mono-id) · Arrival channel (text) · Filed (date) | bulk download | The other four register modes (VERSIONS / EXTRACTED DATA / CONFLICTS / AUDIT-USAGE) are sibling configs on the same primitive |
| Intake queue `intake.columns.ts` | Arrival (text: sender/subject) · Received (date) · Recognition (status-chip + confidence) · Candidate loan+period (entity-link) | bulk dismiss-not-relevant | Grouped: Needs your decision / Processing / Recently filed |
| Actuals exceptions `actuals-exceptions.columns.ts` | Exception (text: class) · Source line (mono-id COA / roll row) · Value (figure-provenance) · Expected (figure) · Δ (figure) · State (status-chip) | none | Rows expand source-beside-decision (co-visibility, 05 §4) — the expansion is a summoned pane, not a taller row |
| Statement grid `statement-grid.columns.ts` | COA line (mono-id + text) · 12 month columns + total (figure-provenance ×13) · tie marker (status-chip) | none | The one true grid: frozen identity column, horizontal scroll, group subtotals (evidence: SLOT-1, ~150 rows × 14 cols) |
| Rent roll units `rentroll.columns.ts` | Unit (mono-id) · Floorplan (text) · SQFT (figure) · Status (status-chip: the roll's own taxonomy) · Lease dates (date ×2) · Market rent (figure) · Lease rent (figure-provenance) · Charges (figure) | none | 322 rows/301 units fixture; resident identity never travels outward (names law) |
| Loan Detail PERIODS `periods.columns.ts` | Period (entity-link) · Status (status-chip) · Certified (date + text) · Sent (date + text) · Sealed (status-chip) | none | Row opens the sealed or in-flight period |
| Tie-out module `tieout.columns.ts` | Field (text) · This period (figure-provenance) · Prior sealed (figure-provenance) · Δ (figure) · State (status-chip: clean/failed/provisional) | none | Failed rows open the 50/50 peer compare; `provisional` per the early-data state (R3 research: Modern Treasury `tentatively_reconciled`, https://docs.moderntreasury.com/v1.0/docs/processing-tentative-reconciliation — adapted) |
| Settings registers (memory inspector, COA library, roles) | per settings direction | COA bulk export | Same primitive; memory rows carry "learned {date} from {who}" provenance text |

Registers not listed (correspondence threads, quiet log, holding checklist, floorplan mix, Home mini-agenda) follow the same pattern; every one is a config file, and the census (§15) counts what each replaces.

## 15. Component registry entry — one primitive, config-only variation

**`CovenantTable`** (NEW, `src/components/covenant/table/CovenantTable.tsx`; build target per the `(covenant)` route group law). Parts:

- `ViewBar` (count+CTA header §7 + filter cluster + saved views §6 — one implementation)
- `ColumnDef` config types (§3 column type system; per-surface files like `loans.columns.ts`, `reports.columns.ts` — **columns via config, never forked grid code**)
- Cell renderers per type: `FigureCell` (wraps the provenance `<Figure>`, provenance §2.2 — reuse, not reimplement), `EntityLinkCell`, `DateCell`, `StatusChipCell`, `BasisBadgeCell`, `VerdictChipCell` (fail-closed on basis, §3), `MonoIdCell`/hash chip, `CountBadge` (REUSE — the existing consolidated atom, branch `build/covenant-count-badge-atom`), `StarCell`, `ActionsCell`
- `SelectionModel` + `BulkBar` (§4; gate acts structurally excluded)
- `GroupHeader` (sticky, hairline-earned §2.2), `SkeletonRows` (§11), `EmptyState`/`FilteredEmptyState` (§12), `VirtualWindow` (§9), keyboard controller (§10)

**Consolidation, the CountBadge way (37 atoms → 1, snapshot §5):** first step is a **census, not a rewrite** — count every hand-rolled table/list-row implementation across `src/components/covenant/` (214 files; the §1 "Is" list names at least 20 table-shaped renders across 14 surfaces) and record the number in the tracker exactly as the CountBadge sitting recorded 37-across-21. Then migrate register-by-register in the blueprint order (Reports first — it IS the gold standard, so the primitive is proven against the ruled exemplar; then Loans, Inbox, Documents, the rest), measurement-gated, **no visual change intended** where a surface already renders the ruled grammar. CI lint from migration start: new table markup outside `src/components/covenant/table/` fails the build (the fixture-burndown pattern, provenance §5).

A surface needing behavior the primitive lacks files a primitive extension PR — never a local fork. The moment two tables disagree on row height, keyboard, or count honesty, this file has been violated.

## 16. Benchmark register (limited roles; from the research notes)

| Product | Limited role | Mechanic | Verdict | Source |
|---|---|---|---|---|
| Attio | Object-table + record-page donor | Spreadsheet-style table views, inline edit in the grid, saved views with shared filter/sort state + ephemeral filtering; top-ordered view = default landing | **Take** view scoping and shared-state semantics; **adapt** inline edit to borrower-owned fields only; **reject** user-editable schema (Covenant's schema is confirmed from documents) | https://attio.com/help/reference/managing-your-data/views/filter-and-sort-views (R5 research: Attio) |
| Linear | Saved-view + keyboard-rhythm donor | Any filtered list saves as a custom view; personal/team/workspace scope; star to sidebar; URL-shareable filtered views | **Take** wholesale as the one saved-view mechanism (§6) | https://linear.app/docs/custom-views (R5 research: Linear) |
| Stripe Dashboard | Industrial register donor | Structured filters pinned top-left; detail = facts + append-only event timeline | **Take** pinned structured filters; **adapt** to 4–6 curated chips; **reject** the open query-builder surface area at 2–50-loan scale | https://docs.stripe.com/dashboard/basics (R5 research: Stripe) |
| Mercury | Calm-register donor | Quick vs advanced filter tiers; header graphs re-render against active table filters; "Needs review" paired state + filter; per-row overflow menu | **Take** stats-bound-to-filters (as numbers, §6) and row-scoped overflow acts; **adapt** needs-review pairing to exception states; **reject** keyword-first navigation | https://support.mercury.com/hc/en-us/articles/38790547830036 (R5 research: Mercury) |
| Ramp | Bulk-act + readiness donor | Checkbox bulk approve; items enter the queue only when actionable | **Take** checkbox-earned-by-act (§4); **adapt** readiness gating to your-move admission; **reject** approval-hierarchy routing | https://support.ramp.com/hc/en-us/articles/4417421399699-Transaction-Reviews (R5 research: Ramp) |
| Modern Treasury | Match-state grammar donor | Expected-vs-actual as enum'd states incl. named provisional | **Adapt** as the tie-out module's state column (§14) | https://docs.moderntreasury.com/reconciliation/docs/overview (R3 research: Modern Treasury) |
| DataSnipper | Figure-to-region provenance donor | Value + cross-reference created as one object; V/X tickmarks | **Adapt** tickmark vocabulary to the statement grid's tie markers; the lit-row law itself lives in the provenance file | https://knowledge.datasnipper.com/how-to-validate-data-on-a-document (R3 research: DataSnipper) |
| Numeric | Exception-first close donor | Autosubmit-when-matched: clean lines consume no attention; only exceptions persist as work | **Take** for the Actuals exceptions register: zero-exception groups collapse to one summary row | https://www.numeric.io/product/reconcile (R3 research: Numeric) |

## 17. Acceptance tests (ticket-ready; fixtures named)

1. **Uniform rows:** every rendered row in the rent-roll fixture (SLOT-2, 322 rows) measures exactly the density token at both densities; zero wrapped cells (DOM assert).
2. **Open-not-boxed readback:** computed styles on all cells of the Reports register show zero borders; hairlines exist only under the header and at group seams; all grays resolve to ladder tokens (canvas readback, not eyeball).
3. **Figure provenance:** on the Actuals statement grid, the Calloway Park FYE-2018 NOI cell ($1,218,877, evidence SLOT-1) traces to its T-12 source rows and the exact sheet cells light (chain A, 06 §2); Total OpEx $1,686,050 control-total tie renders its tie marker.
4. **Vocabulary law, structurally:** the Bexley canon row (occupancy 268/301 = 89.04%, monitored 90% floor) renders basis `monitored` + verdict `shortfall`; a unit test proves `VerdictChipCell` cannot render breach with `basis_echo=monitored`. Westbrook Flats' 1.20x DSCR row renders `covenanted` (canon contrast).
5. **Saved views shared mechanism:** create "sent to {lender} · 2026" on Reports (filters: lender + period range), share to workspace, star to rail; a second account lands on the identical register via the URL; ephemeral filter changes do not mutate the shared view.
6. **Selection scope:** with the 7-loan demo book filtered to 2 rows, Cmd+A selects 2; header reads "2 selected of 2 shown · 7 total".
7. **Virtualization + skeleton:** the 322-row roll and a 150-row T-12 scroll without dropped frames at compact density; `/`-search matches unmounted rows; data arrival after skeleton produces zero layout shift (pixel-diff assert).
8. **Count honesty (U3-C1/U3-F7 regression):** no register can render a count that differs from its admitted row set — the count and the rows read the same query (unit test on the shared query hook); permission-hidden rows always emit the "hidden by role" statement.
9. **Keyboard-only walk:** from rail → register → row focus → cell level → trace a figure (Evidence lights, provenance test 4 chain) → Esc ladder back — no pointer; G-chords fire with table focus.
10. **No bulk gates:** the BulkBar act registry type excludes certify/send (compile-time); no register config can mount them.
11. **Density isolation:** switching comfortable↔compact changes row height and cell padding only (computed-style diff on the Loans register).
12. **Consolidation census:** the census number is recorded in the tracker before the first migration PR; after Reports migrates, the lint forbidding out-of-primitive table markup is on; the burndown count decreases monotonically.
13. **Sort semantics:** on the Loans register, sorting the verdict column orders breach → shortfall → watch → pass with ties broken by signed headroom ascending (fixture: the 7-loan book with Westbrook covenanted and Bexley monitored rows); sorting UPB sorts on the engine value, not the formatted string (fixture: $15,232,500 vs a $9,800,000 peer).
14. **Due-rule provenance:** the Calendar agenda's Calloway Park annual-statement row shows "FYE+120d" and its hover cite resolves to §8.02(b)(2) (evidence: SLOT-3); the date cell itself never takes severity color (computed-style assert).
15. **Rent-roll dedup visibility:** the unit table's occupancy summary figure (evidence: 279/301 = 92.69% physical, 04/30/2018 roll) opens a chain that shows the 322-rows→301-units dedup step and the tie to the roll's own summary sheet (06 chain C); a summary/row disagreement fixture renders the discrepancy flag, never a silent pick.
16. **Inline-edit boundary:** attempting to edit a computed cell (Bexley DSCR) yields the "Computed — trace to source" response and no editor; editing a recipient contact field commits, quiet-logs, and — when the field feeds a certified package — shows the void-on-change warning before commit (06 §6).
