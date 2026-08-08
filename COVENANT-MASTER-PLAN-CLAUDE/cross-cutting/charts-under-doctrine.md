# CROSS-CUTTING — CHARTS UNDER THE CHART DOCTRINE

Ruled law this file applies: the chart doctrine, locked 2026-07-07 (`docs/doctrine/chart-doctrine.md`; memory `covenant-chart-doctrine-locked.md`; snapshot §4/§5) · Reports = the gold-standard list page, **chartless by rule** (snapshot §3) · the lit-row provenance contract (`cross-cutting/provenance-lit-row-trace.md`) · Review-9 tokens + the ten-rung gray ladder, zero new colors (COLOR LAW). Current state: exactly ONE runtime engine wire exists in the whole UI — `assembleTrendSeries` on the Loans surface, feeding demo readings through the real trend assembler (snapshot §3, gap 6). This file states what charts may exist in Covenant v1, what every mark must be, and why almost everything else is forbidden.

## 1. The four ruled rules, verbatim

1. **Discrete marks under ~20.**
2. **A chart must out-encode adjacent text or not ship.**
3. **Severity-only color.**
4. **Every mark an instrument** (clickable to its object).

(Source: `docs/doctrine/chart-doctrine.md`, the four-rule formulation recorded in snapshot §4 and locked per memory `covenant-chart-doctrine-locked.md`.)

Read together they define what a Covenant chart is: a small set of discrete, addressable, clickable readings whose *arrangement* carries information that prose and figures cannot — never a texture, never a mood, never proof that the product "has data." Covenant is a reporting instrument; a chart in it is an instrument panel, or it is banned.

## 2. What the doctrine PERMITS in v1 (the complete list)

Three charts exist in Covenant v1. Nothing else renders as a chart without amending this file.

### 2.1 The Loans trend series (Loan Detail → OVERVIEW → trends)

The one existing runtime wire, preserved and extended (07 §1 B3; portfolio-loans direction).

- **Series:** NOI, DSCR, occupancy — per loan, over **sealed periods only** (05 §5 Trends row: grain loan, rolling, source = sealed periods). One metric per chart; no dual-axis composites (§3).
- **Marks:** one mark per sealed period. **Each mark is clickable to its period** — the sealed period route `/covenant/[loanId]/[period]/...` (every mark an instrument; the URL-carries-loan+period rule, 08 §1).
- **Mark budget:** a quarterly loan shows 4/yr — three years = 12 marks, comfortably ruled. Monthly reporting crosses ~20 within two years, so the series **windows to the trailing ≤18 sealed periods** with an explicit range pager (older windows page, marks never densify, and the chart never silently aggregates months into quarters — aggregation would invent a series the engine didn't emit).
- **Out-encode statement (rule 2, discharged in writing):** the trend chart encodes *direction, rate, and inflection of a metric across periods* — the shape of decay or recovery — which adjacent text (current value + signed headroom, always present beside it) cannot carry without becoming a table of every period. Below 3 sealed periods there is no shape; the chart does not render and a signed text delta takes its place (§4.6).

### 2.2 The headroom trend beside a test

Agent-presence law: headroom renders *beside the test* (kit law: agent presence is contextual; 04). Where a TestResult row (Loan Detail covenant strip, Actuals tests block, Review stop) has ≥3 sealed periods of history, a small headroom trend renders beside it.

- **Series:** `signed_headroom` from TestResult (05 §3) — the ratio/pp denomination plots; the dollar denomination rides in the readout (both denominations always available, because the mitigant conversation happens in dollars — canon phrasing target: "$240K of NOI from a DSCR shortfall").
- **The zero line is the threshold** — a labeled hairline. Position above/below zero *is* the verdict geometry; marks below zero carry the severity of their sealed verdict (§4.3).
- **Basis travels with the series:** the chart consumes `basis_echo` per point, so a monitored series can never paint or announce breach (vocabulary law, structurally held — same fail-closed contract as the verdict chip in `tables-grids-financial-data.md` §3.2).
- **Out-encode statement:** encodes *approach toward the line* — whether headroom is eroding, stable, or recovering across periods. The adjacent text states this period's headroom; only the chart shows the trajectory.

### 2.3 The Calendar heat-strip (PROPOSE, decision D-3)

Per the calendar direction: default stays the task-first agenda; the heat-strip is the PROPOSE-grade companion, presented for Terry with the existing-law alternative (no grid at all).

- **Form:** a compact 7×5 mini-grid, **density dots only** — dot presence/size encodes how many obligations fall due that day; no event titles, no duration bars (R5 research: TaxDome's own "show due date only" concession, https://help.taxdome.com/article/785-workflow-basic-calendar-view — durations pollute a deadline surface; Google Calendar month grid is the named anti-pattern: day cells that HIDE workload behind "+N more", R5).
- **Every dot-day is an instrument:** click → the agenda scrolls to that day. The strip is an index into the agenda, never a workspace.
- **Severity:** days containing overdue items take the severity role; all other dots are gray-ladder quiet. No other color.
- **Doctrine reconciliation, stated honestly:** a month holds up to 31 day-cells, which brushes rule 1's ~20 budget. The resolution: the strip's dots are *density pips*, not value-encoding marks in a series — position encodes date, size encodes count, and every pip is a click-through. It ships only under the D-3 ruling; if Terry declines, v1 has two charts.

**Why the deadline cluster out-encodes text:** "45 days after quarter end" stacks most of the book onto the same fortnight (R5 synthesis b — 2–50 loans × 1–4 obligations/quarter, clustered by construction). The *shape* of that cluster — how hard the crunch fortnight is — is invisible in a list and is exactly what a density strip encodes.

## 3. What the doctrine FORBIDS (named, so nobody relitigates)

| Forbidden | Why (which rule kills it) |
|---|---|
| Decorative KPI tiles | Home direction: "absent by design: decorative charts, KPI tiles without decisions." A number in a big font is text — render it as text with provenance, not as a chart costume (rule 2) |
| Pie/donut charts | No part-of-whole question in this product survives rule 2 — portfolio composition is a table with figures; a 7-slice pie of a 7-loan book out-encodes nothing |
| Portfolio-wide vanity dashboards | A wall of charts nobody clicks violates rules 2 and 4 at once; Home's job is your-move, not surveillance décor (Home direction) |
| **Any chart on Reports** | Ruled chartless (snapshot §3; reports direction: "NO charts (ruled for this page)"). Reports is the audit register — the gold standard is the *list* |
| Duration/Gantt bars on deadline surfaces | The deadline is a point; bars plot process where only the due moment matters (R5 research: TaxDome/Financial Cents both demote the grid) |
| Smoothed/interpolated lines | Data exists only at sealed periods; interpolation renders values the engine never computed — a fabricated number (trust hierarchy). Missing periods render as a gap |
| Area fills, gradients, drop shadows on marks | Decoration encodes nothing (rule 2); severity-only color (rule 3) leaves no channel for fills |
| Dual-axis charts | Two scales sharing a frame un-encode each other; render two charts or one table |
| In-table sparklines | Marks too small to click violate rule 4; the register renders a signed delta figure instead (`tables-grids-financial-data.md` §3) |
| Occupancy gauges/donuts | 268/301 = 89.04% is a figure with a verdict chip and a chain (canon) — a gauge adds pixels, not encoding |
| Animated build-ins/count-ups | Motion that carries no information; charts appear complete (§6) |
| Charts of unsealed periods | The trend substrate is sealed periods only (05 §5); charting in-flight values would plot numbers still subject to void-on-change |

The default state of any new surface is **chartless**. A chart is added by exception, through the §8 acceptance gate, never by habit.

## 4. Mark anatomy

### 4.1 Position encodes value

- x = sealed period, evenly spaced by period index (periods are discrete events, not continuous time); period labels beneath, elided to first/last/inflection under width pressure.
- y = the metric value (or signed headroom). Scale bounds derive from the series + threshold with honest zero handling: ratio scales need not start at zero, but the threshold line must be inside the plotted range whenever a threshold exists.
- A missing sealed period is a **gap** — no line segment bridges it, no phantom mark (§3, no interpolation).

### 4.2 The mark and the line

- The mark (a ~6px dot; hit target ≥24px) carries the data and the interaction. The connecting line is a **hairline in a gray-ladder rung** — grouping chrome only, it encodes nothing and never takes severity color (a red line segment would color-encode an interval where no data exists).
- Threshold line: labeled hairline (gray rung) at the threshold value, label carrying the basis badge (`covenanted` | `monitored`) and the definition ref — the threshold itself comes from the RequirementRecord, read from the loan's own documents, never invented (domain firewall; 05 §1.2). Watch band: a second labeled hairline at the band edge (authoring default floor × 1.05, surfaced as an authoring default per 05 §7.4 [VERIFY library value]); no band fill unless the ruled token set carries a tint role for it — this file mints no color.

### 4.3 Severity-only color (rule 3, mapped to existing token roles — zero new colors)

- **pass → no color.** Pass marks paint a gray-ladder rung (quiet). Severity color appears only when severity does — that is what "severity-only" means in practice.
- **watch / shortfall / breach → the ruled severity roles in the Review-9 token set**, mapped one-to-one to the vocabulary law's ladder [VERIFY the exact token names against the 43-token package; if a needed role is absent, that is a token-authority question for Terry under the COLOR LAW — no session mints a value].
- **breach color is structurally unreachable for monitored series** (`basis_echo` gates the mapping — same contract as the verdict chip). The canon Bexley occupancy series can paint at most the shortfall role.
- Interaction states are chrome, not encoding: the focused/selected mark takes the accent-family focus treatment (`--acc` family) and the traced mark the `--accT` lit convention (provenance §4) — accent never encodes a data value, so rule 3 is not violated.
- All of it verified by canvas readback, never eyeball (oklch caution, snapshot §4).

### 4.4 Axes and frame

Open-not-boxed applies to charts too: no plot border, no gridline lattice. Y labels at min/max + the threshold; hairline gridlines only at labeled values (earned boundaries). Axis labels in the app sans at secondary-text rung; all values in Geist Mono `tabular-nums slashed-zero`.

### 4.5 Adjacent text is mandatory

Every chart renders beside a text block stating the current value, the verdict + basis, and signed headroom in both denominations — engine text, provenance-carrying. **The chart never replaces the number** (§5); it earns its place only for the shape.

### 4.6 The minimum-marks rule

Fewer than 3 sealed periods = no shape = no chart. The slot renders the signed text delta ("+2.1pp vs Q1, sealed") instead. Empty (zero sealed periods) renders the domain-real empty state ("trends begin after your first sealed period"), never an empty axis frame.

## 5. Readout behavior (hover/focus)

- Hover or keyboard-focus on a mark opens a readout **beside** the mark (positioned to never cover the mark, the threshold line, or the adjacent text — the proof-beside-claim discipline at chart scale):
  - exact figure, Geist Mono, full precision as the engine emitted it (fixture: NOI $1,218,877 — evidence SLOT-1, chain A);
  - period identity (the period label is also a link);
  - verdict chip + basis badge for that period (test series);
  - signed headroom, both denominations (test series);
  - the provenance affordance: **trace** — dispatches the lit-row mechanic; the Evidence context opens beside and the exact source line lights and stays lit (provenance adoption item 5: trend points trace to their sealed-period values; the chain ends in the sealed package, seal-not-wipe, so traces work forever).
- Click on the mark itself navigates to the sealed period (rule 4 — the mark's object); trace is the readout's explicit affordance, so navigation and provenance never fight over one gesture.
- **The chart never replaces the number.** The readout supplements the adjacent text; nothing is discoverable only by hover (hover-only truth fails keyboard and fails honesty). Everything in the readout exists as text or trace elsewhere on the surface.
- Readout latency: synchronous from the assembled series (no fetch on hover); document lazy-load on trace follows the provenance skeleton rule (provenance §4).

## 6. Keyboard and reduced motion

- The chart is one Tab stop. Inside: ←/→ walk marks (readout follows focus — hover parity); Enter opens the focused mark's sealed period; the readout's trace affordance is reachable (↓ into the readout, Enter traces); Esc exits the chart, then the frame's Esc ladder (08). G-chords pass through.
- Focus ring on the active mark = accent-family treatment, always visible.
- **Motion law:** charts appear complete — no build-in, draw-on, or count-up animation, ever (motion that carries no information is forbidden at baseline, not just under reduced motion). Residual micro-transitions (readout fade, focus shift ≤150ms) are stripped entirely under `prefers-reduced-motion` or the Settings → Appearance reduced-motion preference (settings direction): state changes become instant, and the lit/selected states are static styles. Nothing in any chart conveys meaning through motion, so reduced-motion loses zero information.

## 6.1 Placement and sizing (pane-model compliance)

Charts are surface-interior content — they never occupy a work window of their own, never summon the canvas panel, and never justify a split (the pane law's co-visibility test: a chart is co-visible *with* the figures it contextualizes, on the same surface).

| Instance | Placement | Size (authoring defaults; [VERIFY against shipped trends tab metrics]) |
|---|---|---|
| Loans trend series (§2.1) | Loan Detail → OVERVIEW → trends block; one chart per metric, stacked | Width: content column up to 720px; height 200px fixed per chart. Below 480px available width → the text-delta fallback (§4.6 mechanism, width-triggered) |
| Headroom trend (§2.2) | Beside its TestResult row (covenant strip / tests block / review stop) | Mini: 280×64px, threshold-as-zero line + marks only, no axis labels (the readout carries exactness); opens nothing on its own — its marks click to periods like any other |
| Calendar heat-strip (§2.3, PROPOSE) | Calendar right rail, above the cycle summary | 7 columns × 5 rows at 16px cells (~128×96px including labels); fixed |

Viewport behavior (frame plan 08 §9): at ≥1728px the trends block may render two charts side by side (still one work window); 1440–1727px stacks them; 1280–1439px unchanged (charts are narrow-safe by design); below the 480px content threshold any chart yields to its text delta — the no-silent-compression law applied to marks (a chart whose hit targets fall under 24px is compressed into dishonesty).

Density modes (Settings → Appearance) do not affect charts — mark size and hit targets are accessibility floors, not density preferences.

## 7. The trend assembler wire — the substrate to extend

`assembleTrendSeries` is **the one existing runtime wire in the UI** (snapshot §3: demo readings through the real trend assembler, on Loans). The plan preserves and extends it rather than building a chart data layer beside it (portfolio-loans direction: "preserve and extend the wire").

- **Landing place:** assembler stays in `src/lib/covenant/` (pure, test-covered, per the engine-library pattern); chart components are NEW in `src/components/covenant/charts/`: `TrendChart.tsx` (serves §2.1 and §2.2 — the headroom trend is a TrendChart with a threshold-as-zero configuration, not a second primitive) and `HeatStrip.tsx` (§2.3, ships only if D-3 rules for it). One chart primitive, config-varied — the CovenantTable consolidation law applied to charts before forks ever exist.
- **Extended contract (per 05 §5 Trends: series points link to their sealed periods):**

```ts
// assembleTrendSeries — extended output shape [UNVERIFIED-BY-SESSION exact current symbol; capability attested by snapshot §3]
type TrendSeries = {
  loanId: string
  metric: 'noi' | 'dscr' | 'occupancy' | 'headroom'
  basis_echo?: 'covenanted' | 'monitored'   // test-derived series only; gates severity mapping (§4.3)
  threshold_ref?: string                     // RequirementRecord ref — never a literal here
  points: Array<{
    period_id: string                        // the sealed period; mark click target (rule 4)
    value: number                            // engine output, verbatim
    verdict?: 'pass'|'watch'|'shortfall'|'breach'
    signed_headroom?: { dollars: number; ratio_or_pp: number }
    ref: ProvenanceRef                       // provenance §2.1 — the mark IS a Figure
  }>
}
```

- **Migration:** step 1, the existing wire's demo readings swap for sealed-period reads (the fixture-to-engine seam, roadmap V-wave; the wire's architecture already proves the path — that is why it is the substrate). Step 2, headroom series derive from TestResult history. Step 3, marks adopt `ProvenanceRef` (provenance adoption item 5). No step forks the assembler.
- **The renderer never computes.** `TrendChart` receives assembled series and draws them; scale math is layout, not data. Any derived figure a chart wants (deltas, windows) is emitted by the assembler, tested in the library (trust hierarchy: every shipping number is deterministic engine output).

## 8. States

| State | Behavior |
|---|---|
| Empty (no sealed periods) | Domain-real empty text (§4.6); no axis skeleton pretending data |
| 1–2 sealed periods | Signed text delta, no chart (minimum-marks rule) |
| Loading | Fixed-size chart skeleton (exact final dimensions — no layout shift); static under reduced motion |
| Partial (a period unsealed mid-window) | Gap in the series, labeled on focus ("Q3 not yet sealed") — never interpolated |
| Stale/void upstream | Charts read sealed data only, so void-on-change cannot repaint history; a period re-sealed after void gets a fresh point and the readout shows the re-certification in its chain |
| Low-confidence input | Cannot occur: sealed series are certified-class by construction; confidence states live upstream on the surfaces that confirm |
| Watch/shortfall/breach | Severity roles per §4.3; monitored series structurally capped at shortfall |
| Permission-denied | The trends tab states the restriction; no ghost axes (provenance §5 discipline) |
| Read-only/sealed | Charts are inherently read-only; traces work forever (seal-not-wipe) |
| Blocked (occupancy resolver pending, gap 7) | The occupancy series renders the honest block ("occupancy trends unlock with the rent-roll reader"), consistent with the Actuals BLOCKED state — never a fixture curve |

## 9. Benchmark register (limited roles; from the research notes)

| Product | Limited role | Mechanic | Verdict | Source |
|---|---|---|---|---|
| Sigma | Drill-from-mark donor | "Drill anywhere": click a mark → underlying records; "view underlying data" renders the record set at exactly the granularity that fed the mark (ungrouped-to-match); drill is view state, never mutation | **Take** the aggregate-plus-underlying contract as the mark→trace behavior; **adapt** free-form drill paths down to Covenant's one contractual path (mark → sealed value → chain → source); **reject** ad-hoc re-slicing beside the certify gate | https://help.sigmacomputing.com/docs/drill-into-data · https://help.sigmacomputing.com/docs/view-underlying-data (R3 research: Sigma) |
| Puzzle | Figure-as-door donor | "Click any category to drill into individual transactions" — every statement figure is a live door, not a dead number | **Take** as confirmation of rule 4 generalized: in Covenant even chart marks are doors; **adapt** the drill target to the read-only Evidence panel, not an editable tab | https://help.puzzle.io/en/articles/11385709-financial-statements-in-puzzle (R3 research: Puzzle) |
| Mercury | Register-adjacent graphs (boundary case) | Two real-time graphs above the transactions register re-render against active table filters | **Reject for Covenant registers** (Reports is ruled chartless; the mechanic survives as filter-bound header *numbers*, `tables-grids-financial-data.md` §6) — recorded here so nobody re-imports the graphs half | https://support.mercury.com/hc/en-us/articles/38790547830036 (R5 research: Mercury) |
| Vanta | Chartless-compliance proof | Compliance deadlines rendered as a status list with deadline-in-the-row urgency (orange attention / red overdue) — no calendar, no charts, at audit-grade stakes | **Take** as the existence proof that obligation surfaces need no charts; urgency is a row property (the agenda's model) | https://help.vanta.com/en/articles/11345374-the-tests-page (R5 research: Vanta) |
| TaxDome / Financial Cents | Grid-concession donors | TaxDome ships a "show due date only" toggle admitting duration bars pollute deadline views; Financial Cents plots due-date-only and treats the grid as a secondary lens over the list | **Adapt** into the heat-strip's constraints (due-date-only, secondary, index-into-agenda); **reject** Day/Week/Month grids as workflow surfaces | https://help.taxdome.com/article/785-workflow-basic-calendar-view · https://help.financial-cents.com/en/articles/4610145-calendar-view (R5 research: TaxDome) |
| Google Calendar month grid | Anti-pattern | ~2–3 items per day cell before "+N more"; cell size fixed by month geometry so workload is invisible exactly when heaviest; no urgency dimension | **Reject entirely**; the heat-strip exists to encode the density a month grid hides (R5 research: the documented failure mode) | https://support.google.com/calendar/thread/1706707 (R5 research: Google Calendar) |
| LoanBoss | Domain forward-projection (boundary case) | Projects next year's DSCR/DY with lender-adjusted math | **Reject for v1 charts**: Covenant charts plot sealed history only (§3, charts of unsealed periods). Forward-looking headroom belongs to the findings service as engine-computed *text* with provenance (agent brief), not as a projected curve that would draw numbers no period sealed | https://www.loanboss.com/blog/automated-lender-adjusted-dscr-dy (R5 research: LoanBoss) |

## 10. Acceptance tests (ticket-ready; fixtures named)

1. **The out-encode gate (rule 2, made procedural):** a chart ships only with a **written out-encode statement** in its PR — what the chart encodes that the adjacent text cannot — reviewed against §2's statements; absence fails review. CI: the Reports surface cannot import from `src/components/covenant/charts/` (lint), keeping Reports chartless by build, not by memory.
2. **Mark budget (rule 1):** fixture of 24 monthly sealed periods → the window clamps to ≤18 with the range pager; no silent aggregation (assert the rendered series' period_ids are a contiguous window of the input).
3. **Every mark an instrument (rule 4):** on a Bexley fixture with 8 sealed quarters, clicking each mark navigates to `/covenant/bexley/[that period]/...`; keyboard walk (←/→ + Enter) reaches identical targets; hit targets ≥24px.
4. **Severity readback (rule 3):** canvas readback (never eyeball) — pass marks paint a gray rung; the Bexley occupancy series (canon: 268/301 = 89.04% vs monitored 90% floor) paints at most the shortfall role and a unit test proves the breach role is unreachable with `basis_echo=monitored`; the Westbrook Flats covenanted 1.20x DSCR series may reach the breach role (canon contrast); zero non-token colors on the canvas.
5. **Readout exactness:** the readout figure equals the engine value verbatim (fixture: Calloway Park FYE-2018 NOI $1,218,877); trace lights the exact T-12 source rows (chain A, 06 §2) and the light persists per the lit-row law.
6. **Minimum marks:** a 2-period fixture renders the text delta, not a chart; a 3-period fixture renders the chart.
7. **No interpolation:** a 7-period fixture with period 5 unsealed renders a gap with no bridging segment (DOM assert on path segments).
8. **Reduced motion:** with `prefers-reduced-motion`, zero animation frames on mount, focus, and readout transitions (recorded-frames assert).
9. **Heat-strip (if D-3 ships):** dot counts tie to the due-rule engine's rows for each day (the Calloway Park Q+45d and FYE+120d rules materialize the fixture deadlines — evidence SLOT-3 §8.02(b)); clicking a dot scrolls the agenda to that day; overdue-day dots paint the severity role, all others a gray rung.
10. **Chartless-by-default sweep:** a repo grep in CI lists every import of the charts directory; the allowlist is exactly the surfaces in §2 — any new consumer fails until this file is amended (the doctrine's "not ship" default, enforced).
