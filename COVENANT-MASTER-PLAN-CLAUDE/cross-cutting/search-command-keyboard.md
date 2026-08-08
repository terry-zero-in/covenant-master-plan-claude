# CROSS-CUTTING — SEARCH, COMMAND PALETTE, AND KEYBOARD SYSTEM

Completes 08 §5 (palette: navigation-only → compute-and-return) and 08 §10 frame tests 4–5. Current state: `CommandPalette`/`SearchPalette` are FIXTURE — ⌘K commands, G-chords, and type-to-filter exist but navigate only and return no computed values (snapshot §5; `PALETTE_V2_SPEC.md` is the gap doc this file supersedes); the keyboard model is FIXTURE with chords present (inventory 07 §1 A4/A8). This file is the one contract for palette grammar, the reserved keymap, focus order, and the dangerous-verb boundary.

## 1. Why this is a system, not a feature

One keymap registry, one palette grammar, one focus law — or else three failure modes: chord collisions that differ per surface, palette rows whose provenance is decorative, and an invisible line between what the deterministic engine answered and what the agent might say. The last one is the trust hierarchy itself: **the engine/agent boundary must be visible in the very grammar of the palette** (08 §5.3). "The Analyst prepares. Covenant calculates." — the palette is where a user most often meets both in one input, so the grammar is where the distinction must be unmissable.

## 2. The palette container (⌘K)

- Opens centered over the work area as an overlay (canvas-slot family; never a modal in the legal-weight sense — Esc dismisses, rung 2 of the ladder, §7). Width 640px at ≥1440px; full-width sheet below 1280px (frame responsive rules, 08 §9).
- One input; results in labeled sections with a fixed section order (subject to the ranking law, §4): **Context actions → Places → Objects → Computed answers → Ask the analyst**. Sections render only when populated — except Ask, which always renders for free text (§3.4).
- Maximum visible rows ~10 before scroll; ↑↓ navigate, Enter opens, ⌘Enter opens as second work window where the pane law permits (max-2 split, 08 §4; the palette never spawns a third pane), Tab accepts a parameter slot (§5), Esc closes.
- Uniform row height 40px (gray-ladder tokens; Lucide icons 16px); the ONE taller row class is the computed answer (56px, two-line, §3.3). Selection state uses the accent-family tint tokens; no new colors.
- `/` is NOT the palette: on register surfaces it focuses that register's own filter/search input (08 §5) — local filtering of visible rows, no computed answers, no navigation. The two inputs never merge; the palette is global, `/` is local.

### 2.1 The three invocation tiers (fixed; no user-authored tiers)

| Tier | Invocation | Serves | Speed contract |
|---|---|---|---|
| Chords | `G x` with the palette closed | The thirteen reserved places (§6) | Zero UI before arrival — Raycast's hotkey lesson (fire without opening the search), scoped inside the app only |
| Palette | `⌘K`, then type | Everything: places, objects, answers, parameterized commands, Ask routing | One input, ranked results (§4) |
| Surface keys | Single keys on the active surface (J/K, N/P, S…) | The surface's own work rhythm | No prefix, no palette — the hands never leave the queue |

A capability may live in several tiers (Review is G V, a palette place, and a breadcrumb) but each tier's grammar is fixed — users configure nothing, so every seat's muscle memory transfers to every other seat (deliberate anti-Raycast constraint: no alias sprawl, R6).

## 3. The three-class result grammar

Every result belongs to exactly one class; a row's class is legible from its anatomy alone.

### 3.1 Class P — Places (exists today; kept)

Routes and views. Row anatomy:

```text
[icon]  Place name            [context chips]              [chord hint]
  ▸ "Review — Bexley Q2"      [ready ● status dot]         G V
  ▸ "Calendar"                                             G C
```

- Spine places carry loan+period identity chips and the period's status dot (status vocabulary tokens; a monitored shortfall never renders breach styling — C-9).
- Right-aligned chord hint teaches the G-chord for that place (redundancy law, §11).
- Settings places index individual settings, not just sections (§10).

### 3.2 Class O — Objects

Loans, documents, periods, requirement records, sends (requires the persisted object store — roadmap F2; 08 §5.2). Row anatomy:

```text
[type icon]  Object title                    [identity chips]           [state chip]
  ▸ "Bexley loan agreement v2"               [Bexley] [filed 03-12]     [confirmed]
  ▸ "Q1 package — sent"                      [Bexley] [Q1-2026]         [sealed]
```

- Enter opens the owning surface; identity chips are mandatory — an object row that cannot state its loan/period identity does not render (altitude gate discipline, 08 §2).
- Version-bearing objects (documents) surface the CURRENT version with a version chip; superseded versions are reachable from DocView, not from search (no accidental stale-version opens).
- Document search matches the Recreated-searchable artifact's text layer where one exists (document-artifact law: the derivative is labeled; opening lands on DocView's Readable mode with the label visible).

### 3.3 Class A — Computed answers (the compute-and-return upgrade)

Typed queries the engine can answer deterministically. Row anatomy (two-line, 56px):

```text
[metric icon]  DSCR — Westbrook Flats · Q2-2026            [verdict chip]
               1.[figure]x · headroom [figure]              ⌘↵ evidence   ← mono line
```

- **The mono figure treatment**: the answer line renders in Geist Mono, `tabular-nums slashed-zero`, one type-size step above the label — the visual signature of deterministic engine output, used for engine rows and nothing else.
- **Provenance refs are structural**: every answer row is a `<Figure>`-bearing render carrying a `ProvenanceRef` (`cross-cutting/provenance-lit-row-trace.md` §2.1; adoption item 7 of that contract). Enter opens the owning surface with the figure's exact source line lit and staying lit; the row shows the as-of period and the value's three-actor class (source/inferred/certified) so a not-yet-certified figure is legible as such.
- Answers come **only** from engine reads over persisted state (TestResult, Metric, Deadline, ReportingPeriod rows). A query that parses but has no computed value renders the honest blocked state ("occupancy — blocked: resolver pending reader"), never a plausible number (04 §2.5).
- The answer grammar is a closed registry of query shapes:

```ts
// src/lib/covenant/palette/answerGrammar.ts (NEW; pure; imports engine read APIs ONLY)
type AnswerQuery =
  | { kind: 'test';     metric: 'dscr'|'debt-yield'|'occupancy'|'liquidity'; loanId: string; period?: string }  // → TestResult (verdict + signed headroom + basis badge)
  | { kind: 'metric';   metric: 'noi'|'upb'|'rate'|'maturity'; loanId: string; period?: string }                // → Metric / loan term (with provenance ref)
  | { kind: 'deadline'; loanId?: string }                                                                        // → next Deadline row(s) + due-rule citation
  | { kind: 'status';   loanId: string; period: string }                                                         // → ReportingPeriod state + readiness reasons
// parse(query, context) → AnswerQuery | null   — null means "not answerable"; there is no fuzzy fallback
```

- Verdict vocabulary flows through untouched: pass / watch / shortfall / breach, with the `basis` badge (covenanted | monitored) on every test answer — a monitored miss says **shortfall**, never breach (C-9; canon: Bexley occupancy 268/301 = 89.04% vs monitored 90% floor).
- Precedent for answers-in-the-input: Raycast's calculator returns computed results inline in Root Search before any navigation (R6 research: Raycast, https://manual.raycast.com/search-bar) — take the mechanic, replace math with engine reads.

### 3.4 Class K — Ask routing (explicit, visually distinct)

The palette **never free-generates**. Any free-text query that doesn't parse as P/O/A gets exactly one Ask affordance — a pinned final section, one row:

```text
── Ask the analyst ─────────────────────────────────────────
[message icon]  Ask the analyst: "{query}"     grounded — cites sources, or refuses
```

- Distinct styling: accent-family tinted left border (#7189FF family tokens; no new colors), regular proportional type — deliberately NOT the mono figure treatment. Engine rows look like instruments; the Ask row looks like a colleague.
- Selecting it opens AskPanel in the canvas panel (existing component; 08 §6) with the query as grounding context; the palette closes. The answer's grounding and refusal behavior is the agent brief's contract (grounded in engine reads + sealed records + filed documents + confirmed values, or an explicit refusal naming what's missing — `agent-ask-watch-memory.md`).
- The boundary is thereby visible in the grammar itself: engine answers live IN the palette with mono figures and lit-row links; the agent is reachable FROM the palette through one labeled door. No intermixing, ever — an Ask result never renders inside the palette's result list, and an engine answer never renders inside AskPanel without its refs.

## 4. Context-first ranking

Ranking law (R6 research: Linear — the command menu surfaces actions applicable to the current view or selection first, https://linear.app/docs/select-issues):

1. **Grammar match pins top**: a query that parses as a computed answer or parameterized command renders that row first.
2. **Current selection/loan/period actions** rank above global places: on `/covenant/bexley/q2/review`, typing "cert" ranks "Certificate — Bexley Q2" above "Reports". With grid rows selected, ⌘K scopes to selection-applicable actions (safe verbs only, §9).
3. **Places** (frecency-ordered), then **objects** (frecency + recency — Raycast's learned ranking: results rank by how often and how recently the user chose them for a given query; per-user, never shared — R6 research: https://manual.raycast.com/search-bar), then remaining answers.
4. **Ask routing** is always last and always present for free text.

Empty query state: recent places/objects + the current loan/period's context actions — never a marketing tips carousel. Frecency data is personal-scope state; it never syncs across users and never reorders shared saved views.

## 5. Parameterized commands (Raycast quicklinks pattern)

`go to {loan} {period}` — placeholders resolve inline: typing "go to bex q2" type-ahead-completes each slot against the object store; Tab accepts a slot, Enter navigates. The curated parameterized set:

| Command | Resolves to |
|---|---|
| `go to {loan} {period}` | The period spine (Review by default) |
| `review {loan} {period}` · `actuals {loan} {period}` · `composer {loan} {period}` | The named spine surface |
| `certificate {loan} {period}` | The certify ceremony's surface — navigation only (§9) |
| `documents {loan}` | Documents register filtered to the loan |
| `deadlines {loan}` | Calendar scrolled to the loan's next Deadline row |
| `filter: {natural language}` on a register | Composes the structured filter set, rendered as visible editable chips — Trigger.dev's NL→structured-filter pattern (R6 research: https://trigger.dev/changelog/ai-run-filtering); the chips are the result, inspectable and correctable; no hidden query state |

Precedent: Raycast Quicklinks with Dynamic Placeholders resolved at run time (R6 research: https://manual.raycast.com/quicklinks) — take placeholders; reject user-authored quicklink sprawl (curated command set only). Slot resolution respects role scope: loans outside the viewer's scope never complete. Period slots complete against real ReportingPeriods only (no speculative future periods).

## 6. The complete G-chord map (reserved; single registry)

Two-key chords: press `G`, then the letter, inside the timeout (1000ms; a pending-chord hint chip renders in the header). The FULL reserved map — no surface may bind outside it:

| Chord | Target | Scope |
|---|---|---|
| G H | Home `/home` | global |
| G I | Inbox `/inbox` | global |
| G L | Loans `/loans` | global |
| G C | Calendar `/calendar` | global |
| G R | Reports `/reports` | global |
| G D | Documents `/documents` | global |
| G A | Intake (Arrivals) `/intake` | global |
| G U | Rent Roll (Units) `/rent-roll` | global |
| G S | Settings `/settings` | global |
| G V | Review — current loan+period | loan-context |
| G P | Composer (Package) — current loan+period | loan-context |
| G F | Certificate — current loan+period | loan-context |
| G T | Actuals (Tests/numbers) — current loan+period | loan-context |

- **Loan-context chords at book altitude do not dead-end**: G V outside a loan context opens the palette pre-filled with the parameterized command (`review {loan} {period}`, cursor in the loan slot). A chord never silently no-ops.
- G H/I/L/C/R/D are ruled in 08 §5; G V/P/F are reserved there for the spine; this file completes the map with G A/U/S/T and **freezes it** — new chords require amending this file, not a surface brief.

**Collision rules:**

1. One keymap registry (`src/lib/covenant/keymap.ts`, §15) holds every binding with its scope (global / loan-context / surface / grid / palette-internal / overlay). Registering a duplicate within any reachable scope chain is a **build-time error** (CI test, §17.6); the "?" overlay renders from the registry, so an unlisted binding cannot exist.
2. `G` is a reserved global prefix; no surface binds bare `G` or any `G x` outside this table.
3. Single-key surface verbs (Intake: J/K, 1–9 pick candidate, F file, R replace, H hold, X dismiss, U undo; Inbox: J/K, S snooze, Shift+D delegate; Review: N/P stop walk — per their briefs) are surface-scoped and may not shadow global bindings; the registry enforces it.
4. All chords and single-key verbs are **suspended while focus is in an editable field** (typing "gh" in a note must not navigate); ⌘K and Esc are always live.
5. Browser-owned combos are never bound: ⌘L, ⌘T, ⌘W, ⌘N, ⌘R.
6. `/` focuses the register search input on register surfaces; `?` opens the shortcut overlay (§8); both are global, registry-held.
7. Chord semantics are identical with the rail collapsed, inside a grid, and inside a summoned pane (frame test 08 §10.5) — the only suppressor is editable-field focus.

```ts
// src/lib/covenant/keymap.ts (NEW; single source for every binding)
type Binding = {
  keys: string                    // "g h", "cmd+k", "/", "?", "j" …
  scope: 'global'|'loan-context'|'surface:<id>'|'grid'|'palette'|'overlay'
  action: { kind: 'navigate'|'focus'|'open-overlay'|'surface-verb'; target: string }
  label: string                   // renders in the "?" overlay and palette hints
  visible_affordance: string      // component anchor id (redundancy law, §11) — required for palette/surface verbs
  roles?: RoleId[]                // omitted = all; role-gating hides (§9)
}
```

## 7. Focus order law

```text
 ┌─ rail ─┐ ┌───────────── header (crumbs · view switcher · your-move chip) ─────────────┐
 │   1    │ │                                    2                                       │
 │        │ ├─ work window 1 ─────────────┬─ work window 2 (split) ─┬─ pinned panes ─────┤
 │        │ │            3                │           4             │        5           │
 └────────┘ └──────────────────────────────┴────────────────────────┴────────────────────┘
                                              ┌─ canvas panel (floating) ─┐
                                              │            6              │
```

- **Region order: rail → header → work window 1 → work window 2 (if split) → pinned panes → canvas panel.** Tab traverses within a region; F6 / Shift+F6 cycles regions (long-session accessibility; the `accessibility-long-session` cross-cutting file owns the full a11y contract).
- Summoned panes receive focus on summon and return focus to the summoner on close (the provenance contract already requires this for traces: Enter traces, Evidence receives focus on the lit row, Esc returns to the figure — `provenance-lit-row-trace.md` §6.4).
- **The Esc ladder** (deterministic, one step per press): (1) close open menu/popover; (2) clear pending chord or close the palette; (3) dismiss the lit trace; (4) close the canvas panel; (5) close the summoned second work window / overlay pane; (6) only when nothing remains to dismiss may Esc step up one breadcrumb altitude (spine surface → its floor). **Esc closes summoned panes before it ever navigates** (07 §3). Inside typed-act modals (certify/send/void acknowledgment — the legal-weight class, 08 §4), focus is trapped and Esc means "cancel this act" only — it never skips rung 6 out of a ceremony.
- Breadcrumb switchers (loan switcher, period selector popover — 08 §2) are keyboard-reachable from the header region: Enter opens the popover, type-to-filter inside it, Esc closes it (rung 1).

## 8. Grid keyboard grammar (reference)

Grids share one grammar — J/K row nav, Enter open, X/Shift-click select, ⌘K acts on selection (safe verbs only, §9), `/` focus filter — with cell-level navigation, sticky-header behavior, and the uniform-row law owned by `cross-cutting/tables-grids.md`. This file reserves the keys (registry scope `grid`); that file specifies the interaction detail. No grid re-invents its own bindings, and no grid binding may collide with the reserved map (§6).

## 9. The dangerous-verb rule

**Certify and send exist in the palette as navigation to their ceremonies ONLY — never as palette-executable verbs.** There is no "certify now", no "send package" execution row, no keyboard path that performs a typed act with legal weight outside its owning ceremony (the certificate sheet's typed-act modal; the send panel's typed SEND confirmation — R4 synthesis 12: the gates accept only an authenticated human session performing a typed act; no API path around either).

- Palette-executable verb classes (closed list): navigation; view/filter composition (rendered as visible, editable filter chips on arrival, §5); star/pin; snooze-with-reason (an Inbox item-state, not a domain act); copy link. Everything that mutates domain ground truth — certify, send, confirm an extraction, waive a checklist item, disposition a finding, void acknowledgment — is **navigate-to-the-affordance**: the palette row reads "Certificate — Bexley Q2" and lands you at the gate; the ceremony does the rest.
- Structural enforcement: the command descriptor has no `execute` handler kind that writes domain state (§6 `Binding.action.kind` admits `navigate`/`focus`/`open-overlay`/`surface-verb`; `surface-verb` dispatches to the owning surface's own affordance handler, which applies its own gates — the palette cannot reach a store directly).
- **Role-gated visibility**: ceremony rows render only for roles holding the right (Settings Team & Roles matrix; certify/send rights explicitly assignable — the gap-4 unfix). Gating hides rather than disables (R6 research: GitHub org settings — visibility, not just editability, is role-gated, https://docs.github.com/en/organizations/managing-organization-settings). A preparer typing "certify" gets no certificate-ceremony row; they get the Review place row, which is their actual next act.
- The agent's verbs in the palette stop where they stop everywhere: propose and draft, never commit (04 §1 lanes; Digits' commit boundary moved one notch safer — R6). There is no palette verb that asks the agent to alter a shipping number, because no such verb exists anywhere.

## 10. Settings search

Settings sections AND individual settings index as places with deep anchors: "escalation" → `/settings/notifications#escalation-policy`; "intake address" → `/settings/intake#address`; "agent memory" → `/settings/agent#memory-inspector`. Admin-gated items are invisible to non-admins in results (GitHub visibility rule, §9). Every setting is one palette jump away — R6's explicit rejection of multi-hop-only settings access (Stripe/GitHub multi-step navigation is the control case). The settings index derives from the settings.md section map; a setting missing from the index is a failing test, not a content gap.

## 11. The "?" shortcut overlay and the deliberate-redundancy law

`?` opens `KeyboardMapOverlay` (NEW) as an overlay sheet (canvas-slot family; Esc closes, rung 2): the full map, grouped — Global chords / This surface / Grids / Palette — **rendered from the keymap registry** (never a hand-maintained list; a binding missing from the overlay is a failing test), searchable, role-aware (bindings whose target the role cannot see are absent, not grayed). Linear precedent: `?` lists all shortcuts (R6 research: https://linear.app/now/invisible-details). Lucide icons; key caps render in the mono face.

**Deliberate redundancy law**: every palette action also exists as a visible affordance — a button, menu item, link, or breadcrumb on the owning surface (R6 research: Linear — every action reachable via button, shortcut, contextual menu, or palette). The palette accelerates; it never gates. Enforcement is structural: every registered command declares its `visible_affordance` anchor (§6 Binding); CI walks the registry and fails on an anchorless command (§17.7). Corollary: a feature reachable ONLY through the palette is a spec violation, not a power-user delight.

## 12. Index scope and tenancy

- The search index covers **the org's book only**: its loans, periods, documents, requirement records, sends, settings. Nothing global, nothing cross-org, ever.
- **PMC mode**: object results and slot completion scope to the clients the seat may see (Settings → Organization client scoping); a preparer scoped to two clients cannot complete a loan slot from a third. Scoping is enforced in the query layer, not by hiding rows post-hoc.
- **Resident-level data is not indexed**: tenant names, unit-level rent rolls, deposit and delinquency detail never appear in palette results or search snippets (names law: resident data never travels outward — and a search snippet is a travel path). Rent-roll documents surface as objects by title/type/loan; their row contents do not.
- Sealed periods stay fully searchable forever (seal-not-wipe); the seal badge renders on their rows.
- Index freshness is store-driven (no crawler): an object is searchable the moment its store row commits, and a replaced document version drops out of default results the same moment its successor files.

## 13. Ownership boundaries (what this file does NOT own)

- **Ask grounding, refusal behavior, capability prompts** — `surface-briefs/agent-ask-watch-memory.md`. This file owns only the routing door (§3.4).
- **Grid interior keyboard detail** (cell navigation, edit-in-place, selection model) — `cross-cutting/tables-grids.md`. This file reserves the keys.
- **Register filter clusters and saved views** — each register's brief + the shared SavedView mechanism (02 §2). The palette composes filters (§5); the register owns them.
- **The ceremonies themselves** (typed-act anatomy, readiness) — `surface-briefs/certificate-certify-gate.md`, `send-record.md`. The palette only navigates there (§9).
- **Frame pane topology and responsive rules** — 08 §4/§9. The palette obeys them.

## 14. Benchmarks (limited roles; exact mechanics; official URLs from R6 research)

| Product | Limited role | Mechanic taken | Take/adapt/reject |
|---|---|---|---|
| Linear (https://linear.app/docs/select-issues) | Context-first palette + redundancy | ⌘K surfaces actions applicable to the current view/selection first; every action also exists as button/menu/shortcut; "?" lists all | TAKE context ranking + redundancy law + "?" overlay; ADAPT verbs to domain acts; REJECT engineer-grade shortcut density (fewer, mnemonic bindings) |
| Raycast (https://manual.raycast.com/quicklinks) | Palette input grammar | Computed results inline in the search field; Quicklinks with Dynamic Placeholders; frequency+recency ranking | TAKE computed answers + parameterized commands + per-user frecency; REJECT global OS hotkeys and user-authored alias sprawl |
| Trigger.dev (https://trigger.dev/changelog/ai-run-filtering) | NL→filter composition | Natural-language description composes the structured filter set | ADAPT: result renders as visible, editable chips — never hidden query state |
| GitHub (https://docs.github.com/en/organizations/managing-organization-settings) | Role-gated visibility | Org settings are invisible, not disabled, to non-owners | TAKE hide-don't-disable for ceremony rows and admin settings in results |
| Stripe (https://docs.stripe.com/dashboard/search) | Search grammar control case | Multi-term AND, negation, metadata search, loose dates | ADAPT scaled down into register `/` filters (R5); REJECT open-ended query builder as primary UI for a 2–50 loan book |
| Digits (https://digits.com) | Agent commit boundary | Queue-not-chatbox agent surface; confidence-gated routing | ADAPT one notch safer: palette agent verbs stop at propose/draft; commit paths don't exist |

## 15. Build target (basis-v2)

| Piece | Address | Status |
|---|---|---|
| One palette (merge `CommandPalette`/`SearchPalette`) | `src/components/covenant/palette/CommandPalette.tsx` | ADJUST — two fixture components become one, fed by the three registries below |
| Places registry (routes/views + chord hints) | `src/lib/covenant/palette/places.ts` | NEW (derives from the 08 §1 route table) |
| Answer grammar (query shapes → engine reads) | `src/lib/covenant/palette/answerGrammar.ts` | NEW; pure; imports engine read APIs ONLY — importing any agent/LLM client is a lint error (§17.2) |
| Object search | over the persisted object store | F2 dependency — honest degradation until then (§16 states) |
| Keymap registry + chord listener | `src/lib/covenant/keymap.ts` + shell-level listener in `CovenantShell.tsx` | NEW / ADJUST (chords exist as fixture; re-home them into the registry) |
| `KeyboardMapOverlay` ("?") | `src/components/covenant/palette/KeyboardMapOverlay.tsx` | NEW |
| Answer-row figure rendering | reuses `<Figure>` + `traceToEvidence` | REUSE (provenance contract §2) |
| Ask routing | reuses `AskPanel` (existing) in the canvas panel | REUSE — the palette hands off, never embeds |
| Settings search index | derived from settings section map | NEW |

Tokens: gray ladder + accent family only; mono figures in Geist Mono `tabular-nums slashed-zero`; Lucide icons; zero new colors. Reduced-motion preference (Settings → Appearance) disables palette open/close animation.

## 16. States

| State | Behavior |
|---|---|
| Empty query | Recents + current loan/period context actions; no tips carousel |
| No match | Ask routing row + "no places or objects match" — never an empty void |
| Object store absent (pre-F2) | Places + computed answers work; object section renders "object search arrives with the persisted store" — honest, not broken-looking |
| Answer blocked | Named blocked state (e.g. occupancy pending the rent-roll reader — gap 7); no figure, no placeholder |
| Answer stale | As-of period + staleness flag per the store's state; a stale figure never renders unflagged |
| Low-confidence source behind an answer | The three-actor class renders on the row (inferred + confidence); the trace shows the suspect region per DocView |
| Permission-denied | Rows absent (visibility gating, §9), never disabled rows advertising what the role lacks |
| Sealed-period answers | Answer rows against sealed periods render the seal badge; traces work forever (seal-not-wipe) |
| Narrow viewport (<1280px) | Palette becomes a full-width sheet; chord hints hidden; chords still live (frame test 08 §10.5) |
| Reduced motion | Instant open/close; no result-list animation |
| Registry failure (a binding fails to resolve at runtime) | The binding is dropped from the active map AND the "?" overlay together (they share the registry); a silent dead key is impossible |

## 17. Acceptance tests (ticket-ready; fixtures: Bexley canon, Westbrook Flats canon, Calloway Park FYE-2018 spine)

1. **Compute-and-return with provenance.** `dscr westbrook` returns Westbrook Flats' latest TestResult from engine reads — verdict chip + signed headroom against its covenanted 1.20x DSCR (canon), mono figure treatment — and Enter opens the owning surface with the source line lit and staying lit (frame test 08 §10.4). The row's ProvenanceRef chain resolves through `traceToEvidence` with zero renderer-local resolution.
2. **Zero free-generation in the palette.** Static assertion: `answerGrammar.ts` and the palette component import no agent/LLM client (CI lint). Behavioral assertion: every rendered answer row's value equals the engine store value for that key at render time; an unparseable query yields ONLY the Ask routing row; `parse()` returns null (never a fuzzy guess) for out-of-grammar queries.
3. **Vocabulary under the grammar.** `occupancy bexley` renders 268/301 = 89.04% with a **shortfall** chip against the monitored 90% floor (canon) and the monitored `basis` badge — the word "breach" appears nowhere in monitored rows (C-9 test).
4. **Boundary visibility.** Snapshot test: engine answer rows render mono figures + evidence affordance; the Ask row renders the agent affordance + accent-tinted border; no row class borrows the other's anatomy; the Ask section renders below all engine sections in every fixture.
5. **Chord map.** With the rail collapsed AND focus inside a Loans grid row, G C lands on `/calendar` (frame test 08 §10.5); G V at book altitude opens the palette pre-filled with `review {loan} {period}`; "gh" typed inside an editable field navigates nowhere; the pending-chord hint chip appears and clears on timeout.
6. **Collision enforcement.** Registering a duplicate binding in any reachable scope chain fails the build; the "?" overlay lists exactly the registry contents (walked programmatically, not hand-asserted); every G-chord in §6's table is present and no others.
7. **Redundancy law.** CI walks every registered palette command and resolves its declared `visible_affordance` anchor to a mounted component on the owning surface; anchorless commands fail.
8. **Dangerous verbs.** Typing "certify" as an owner yields only navigation rows (certificate place/ceremony) whose action kind is `navigate` — asserted at the descriptor level: no palette command carries a handler that writes domain state. As a preparer without certify rights, the ceremony row is absent entirely (visibility, not disablement). Same assertions for "send".
9. **Esc ladder.** With a pending chord, a lit trace, and an open canvas panel stacked: successive Esc presses clear them in ladder order, then close the summoned pane, then step up one breadcrumb altitude; inside the certify modal, Esc only cancels the act and never navigates.
10. **Parameterized command.** `go to bex q2` slot-completes to Bexley Q2-2026 and lands on the period spine with loan+period crumbs (altitude gate); a loan outside the viewer's role scope never appears in slot completion; a period slot offers only real ReportingPeriods.
11. **NL filter composition.** `filter: sent to fannie this year` on the Reports register produces visible, editable filter chips matching the structured filters, and the register's count header updates; clearing the chips restores the unfiltered register (no hidden residue).
12. **Settings search.** `escalation` returns the deep anchor into `/settings/notifications#escalation-policy` for an admin and returns nothing for a non-admin (invisible, not grayed); every settings.md section resolves through the index (walked test).
13. **Answer honesty.** With the occupancy resolver blocked (gap 7 fixture), `occupancy calloway` renders the named blocked state and no figure; unblocking the resolver flips the same query to the computed 279/301 = 92.69% (evidence: SLOT-2, 04/30/2018) with a working lit-row link to the roll's contributing rows including the 322-rows→301-units dedup step.
14. **Focus round-trip.** Keyboard-only: ⌘K → type `dscr westbrook` → Enter → the owning surface opens with Evidence focused on the lit row → Esc returns focus per the provenance contract; F6 cycles regions in the §7 order on every surface fixture.
