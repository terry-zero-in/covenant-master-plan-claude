# CROSS-CUTTING — SEARCH, COMMAND PALETTE, AND KEYBOARD SYSTEM

Completes 08 §5 (palette: navigation-only → compute-and-return) and 08 §10 frame tests 4–5. Current state: `CommandPalette`/`SearchPalette` are FIXTURE — ⌘K commands, G-chords, and type-to-filter exist but navigate only and return no computed values (snapshot §5; `PALETTE_V2_SPEC.md` is the gap doc this file supersedes); the keyboard model is FIXTURE with chords present (inventory 07 §1 A4/A8). This file is the one contract for palette grammar, the reserved keymap, focus order, and the dangerous-verb boundary.

## 1. Why this is a system, not a feature

One keymap registry, one palette grammar, one focus law — or else three failure modes: chord collisions that differ per surface, palette rows whose provenance is decorative, and an invisible line between what the deterministic engine answered and what the agent might say. The last one is the trust hierarchy itself: **the engine/agent boundary must be visible in the very grammar of the palette** (08 §5.3).

## 2. The three-class palette grammar (one input, ⌘K)

Every result belongs to exactly one class; classes render as labeled sections in a fixed order (context actions → places → objects → computed answers → Ask routing, subject to the ranking law in §3).

### 2.1 Class P — Places (exists today; kept)

Routes and views. Row anatomy (uniform 40px, gray-ladder tokens, Lucide icon 16px):

```text
[icon]  Place name            [context chips]              [chord hint]
  ▸ "Review — Bexley Q2"      [ready ● status dot]         G V
  ▸ "Calendar"                                             G C
```

- Spine places carry loan+period identity chips and the period's status dot (status vocabulary tokens; a monitored shortfall never renders breach styling).
- Right-aligned chord hint teaches the G-chord for that place (redundancy law, §10).

### 2.2 Class O — Objects

Loans, documents, periods, requirement records, sends (requires the persisted object store — roadmap F2; 08 §5.2). Row anatomy:

```text
[type icon]  Object title                    [identity chips]           [state chip]
  ▸ "Bexley loan agreement v2"               [Bexley] [filed 03-12]     [confirmed]
  ▸ "Q1 package — sent"                      [Bexley] [Q1-2026]         [sealed]
```

- Enter opens the owning surface; ⌘Enter opens as the second work window where the pane law permits (max-2 split, 08 §4); the palette never spawns a third pane.
- Identity chips are mandatory — an object row that cannot state its loan/period identity does not render (altitude gate discipline, 08 §2).

### 2.3 Class A — Computed answers (the compute-and-return upgrade)

Typed queries the engine can answer deterministically: `dscr {loan}`, `occupancy {loan}`, `headroom {loan}`, `next deadline`, `next deadline {loan}`, `upb {loan}`, `status {loan} {period}`. Row anatomy (two-line, 56px — the one taller row class):

```text
[metric icon]  DSCR — Westbrook Flats · Q2-2026            [verdict chip]
               1.[figure]x · headroom [figure]              ⌘↵ evidence   ← mono line
```

- **The mono figure treatment**: the answer line renders in Geist Mono, `tabular-nums slashed-zero`, one type-size step above the label — the visual signature of deterministic engine output, used for engine rows and nothing else.
- **Provenance refs are structural**: every answer row is a `<Figure>`-bearing render carrying a `ProvenanceRef` (`cross-cutting/provenance-lit-row-trace.md` §2.1, adoption item 7). Enter opens the owning surface with the figure's exact source line lit and staying lit; the row shows the as-of period and the value's three-actor class (source/inferred/certified) so a not-yet-certified figure is legible as such.
- Answers come **only** from engine reads over persisted state (TestResult, Metric, Deadline, ReportingPeriod rows). The grammar is a registry of answerable query shapes (`answerGrammar.ts`, §11); a query that parses but has no computed value renders the honest blocked state ("occupancy — blocked: resolver pending reader"), never a plausible number (04 §2.5).
- Precedent for answers-in-the-input: Raycast's calculator returns computed results inline in Root Search before any navigation (R6 research: Raycast, https://manual.raycast.com/search-bar) — take the mechanic, replace math with engine reads.

### 2.4 Class K — Ask routing (explicit, visually distinct)

The palette **never free-generates**. Any free-text query that doesn't parse as P/O/A gets exactly one agent affordance — a pinned final section, one row:

```text
── Ask the analyst ─────────────────────────────────────────
[message icon]  Ask the analyst: "{query}"     grounded — cites sources, or refuses
```

- Distinct styling: accent-family tinted left border (#7189FF family tokens; no new colors), regular proportional type — deliberately NOT the mono figure treatment. Engine rows look like instruments; the Ask row looks like a colleague.
- Selecting it opens AskPanel in the canvas panel (existing component; 08 §6) with the query as grounding context; the palette closes. The answer's citation behavior is the agent brief's contract (grounded or explicit refusal — `agent-ask-watch-memory.md`).
- The boundary is thereby visible in the grammar itself: engine answers live IN the palette with mono figures and lit-row links; the agent is reachable FROM the palette through one labeled door. No intermixing, ever.

## 3. Context-first ranking

Ranking law (R6 research: Linear — the command menu surfaces actions applicable to the current view or selection first, https://linear.app/docs/select-issues):

1. **Grammar match pins top**: a query that parses as a computed answer or parameterized command renders that row first.
2. **Current selection/loan/period actions** rank above global places: on `/covenant/bexley/q2/review`, typing "cert" ranks "Certificate — Bexley Q2" above "Reports". With grid rows selected, ⌘K scopes to selection-applicable actions (safe verbs only, §9).
3. **Places** (frecency-ordered), then **objects** (frecency + recency; Raycast's learned ranking, per-user, never shared — R6), then remaining answers.
4. **Ask routing** is always last and always present for free text.

Empty query state: recent places/objects + the current loan/period's context actions — never a marketing tips carousel.

## 4. Parameterized commands (Raycast quicklinks pattern)

`go to {loan} {period}` — placeholders resolve inline: typing "go to bex q2" type-ahead-completes each slot against the object store; Tab accepts a slot, Enter navigates. Also parameterized: `review {loan} {period}`, `actuals {loan} {period}`, `certificate {loan} {period}` (navigation to the ceremony, §9), `documents {loan}`, `deadlines {loan}`. Precedent: Raycast Quicklinks with Dynamic Placeholders resolved at run time (R6 research: https://manual.raycast.com/quicklinks) — take placeholders; reject user-authored quicklink sprawl (curated command set only). Slot resolution respects role scope: loans outside the viewer's scope never complete.

## 5. The complete G-chord map (reserved; single registry)

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
- G H/I/L/C/R/D are ruled in 08 §5; G V/P/F reserved there for the spine; this file completes the map with G A/U/S/T and freezes it.

**Collision rules:**

1. One keymap registry (`src/lib/covenant/keymap.ts`, §11) holds every binding with its scope (global / loan-context / surface / grid / palette-internal / overlay). Registering a duplicate within any reachable scope chain is a **build-time error** (CI test, §12.6); the "?" overlay renders from the registry, so an unlisted binding cannot exist.
2. `G` is a reserved global prefix; no surface binds bare `G` or any `G x` outside this table.
3. Single-key surface verbs (Intake: J/K/1–9/F/R/H/X/U; Inbox: J/K/S/Shift+D; Review: N/P — per their briefs) are surface-scoped and may not shadow global bindings; the registry enforces it.
4. All chords and single-key verbs are **suspended while focus is in an editable field** (typing "gh" in a note must not navigate); ⌘K and Esc are always live.
5. Browser-owned combos are never bound: ⌘L, ⌘T, ⌘W, ⌘N, ⌘R.
6. `/` focuses the register search input on register surfaces (08 §5); `?` opens the shortcut overlay (§8); both are global, registry-held.

## 6. Focus order law

- **Region order: rail → header → work window 1 → work window 2 (if split) → pinned panes → canvas panel.** Tab traverses within a region; F6 / Shift+F6 cycles regions (long-session accessibility; the `accessibility-long-session` cross-cutting file owns the full a11y contract).
- Summoned panes receive focus on summon and return focus to the summoner on close (the provenance contract already requires this for traces: Enter traces, Evidence receives focus on the lit row, Esc returns to the figure — `provenance-lit-row-trace.md` §6.4).
- **The Esc ladder** (deterministic, one step per press): (1) close open menu/popover; (2) clear pending chord or close the palette; (3) dismiss the lit trace; (4) close the canvas panel; (5) close the summoned second work window / overlay pane; (6) only when nothing remains to dismiss may Esc step up one breadcrumb altitude (spine surface → its floor). **Esc closes summoned panes before it ever navigates** (07 §3). Inside typed-act modals (certify/send/void — the legal-weight class, 08 §4), focus is trapped and Esc means "cancel this act" only — it never skips rung 6 out of a ceremony.

## 7. Grid keyboard grammar (reference)

Grids share one grammar — J/K row nav, Enter open, X/Shift-click select, ⌘K acts on selection (safe verbs only, §9), `/` focus filter — with cell-level navigation, sticky-header behavior, and uniform-row law owned by `cross-cutting/tables-grids.md`. This file reserves the keys (registry scope `grid`); that file specifies the interaction detail. No grid re-invents its own bindings.

## 8. The "?" shortcut overlay

`?` opens `KeyboardMapOverlay` (NEW) as an overlay sheet (canvas-panel slot; Esc closes, rung 2): the full map, grouped — Global chords / This surface / Grids / Palette — **rendered from the keymap registry** (never a hand-maintained list; a binding missing from the overlay is a failing test), searchable, role-aware (bindings whose target the role cannot see are absent, not grayed — the GitHub visibility rule, R6). Linear precedent: `?` lists all shortcuts (R6 research: https://linear.app/now/invisible-details). Lucide icons; key caps render in the mono face.

## 9. The dangerous-verb rule

**Certify and send exist in the palette as navigation to their ceremonies ONLY — never as palette-executable verbs.** There is no "certify now", no "send package" execution row, no keyboard path that performs a typed act with legal weight outside its owning ceremony (the certificate sheet's typed-act modal; the send panel's typed SEND confirmation — R4 synthesis 12: the gates accept only an authenticated human session performing a typed act; no API path around either).

- Palette-executable verb classes (closed list): navigation; view/filter composition (rendered as visible, editable filter chips on arrival — Trigger.dev's NL→structured-filter pattern, R6); star/pin; snooze-with-reason (an Inbox item-state, not a domain act); copy link. Everything that mutates domain ground truth — certify, send, confirm an extraction, waive a checklist item, disposition a finding, void acknowledgment — is **navigate-to-the-affordance**: the palette row reads "Certificate — Bexley Q2" and lands you at the gate; the ceremony does the rest.
- **Role-gated visibility**: ceremony rows render only for roles holding the right (Settings Team & Roles matrix; certify/send rights explicitly assignable). Gating hides rather than disables (R6 research: GitHub org settings — visibility, not just editability, is role-gated, https://docs.github.com/en/organizations/managing-organization-settings). A preparer typing "certify" gets no certificate-ceremony row; they get the Review place row, which is their actual next act.
- The agent's verbs in the palette stop where they stop everywhere: propose and draft, never commit (04 §1 lanes; Digits' commit boundary moved one notch safer — R6).

## 10. Deliberate redundancy law

**Every palette action also exists as a visible affordance** — a button, menu item, link, or breadcrumb on the owning surface (R6 research: Linear — every action reachable via button, shortcut, contextual menu, or palette). The palette accelerates; it never gates. Enforcement is structural: every registry-registered palette command declares its visible-affordance anchor (component id + surface); CI walks the registry and fails on an anchorless command (§12.7). Corollary: a feature reachable ONLY through the palette is a spec violation, not a power-user delight.

## 11. Build target (basis-v2)

| Piece | Address | Status |
|---|---|---|
| One palette (merge `CommandPalette`/`SearchPalette`) | `src/components/covenant/palette/CommandPalette.tsx` | ADJUST — two fixture components become one, fed by the three registries below |
| Places registry (routes/views + chord hints) | `src/lib/covenant/palette/places.ts` | NEW (derives from the 08 §1 route table) |
| Answer grammar (query shapes → engine reads) | `src/lib/covenant/palette/answerGrammar.ts` | NEW; pure; imports engine read APIs ONLY — importing any agent/LLM client is a lint error (§12.2) |
| Object search | over the persisted object store | F2 dependency — honest degradation until then (§12 states) |
| Keymap registry + chord listener | `src/lib/covenant/keymap.ts` + shell-level listener in `CovenantShell.tsx` | NEW / ADJUST (chords exist as fixture; re-home them into the registry) |
| `KeyboardMapOverlay` ("?") | `src/components/covenant/palette/KeyboardMapOverlay.tsx` | NEW |
| Answer-row figure rendering | reuses `<Figure>` + `traceToEvidence` | REUSE (provenance contract §2) |
| Settings search | settings sections/items indexed as places with deep anchors (`/settings/notifications#escalation-policy`) | NEW; admin-gated items invisible to non-admins in results; every setting one palette jump away (R6: reject multi-hop-only settings access) |

Tokens: gray ladder + accent family only; mono figures in Geist Mono `tabular-nums slashed-zero`; Lucide icons; zero new colors.

## 12. States and acceptance tests

States: **empty query** (recents + context actions); **no match** (Ask routing row + "no places or objects match" — never an empty void); **F2 absent** (places + computed answers work; object section renders "object search arrives with the persisted store" — honest, not broken-looking); **answer blocked** (named blocked state, e.g. occupancy pending the rent-roll reader — gap 7 — never a stale or invented figure); **stale value** (answer carries as-of period + staleness flag per its store state); **permission-denied** (rows absent, not disabled); **narrow viewport** (<1280px: palette full-width overlay; chord hints hidden, chords still live — frame test 08 §10.5); **reduced motion** (no palette animation; instant open/close).

Acceptance tests (ticket-ready; fixtures: Bexley canon, Westbrook Flats canon, Calloway Park FYE-2018 spine):

1. **Compute-and-return with provenance.** `dscr westbrook` returns Westbrook Flats' latest TestResult from engine reads — verdict chip + signed headroom against its covenanted 1.20x DSCR (canon), mono figure treatment — and Enter opens the owning surface with the source line lit and staying lit (frame test 08 §10.4). The row's ProvenanceRef chain resolves through `traceToEvidence` with zero renderer-local resolution.
2. **Zero free-generation in the palette.** Static assertion: `answerGrammar.ts` and the palette component import no agent/LLM client (CI lint). Behavioral assertion: every rendered answer row's value equals the engine store value for that key at render time; an unparseable query yields ONLY the Ask routing row.
3. **Vocabulary under the grammar.** `occupancy bexley` renders 268/301 = 89.04% with a **shortfall** chip against the monitored 90% floor (canon) and the monitored basis badge — the word "breach" appears nowhere in monitored rows (C-9 test).
4. **Boundary visibility.** Snapshot test: engine answer rows render mono figures + evidence affordance; the Ask row renders the agent affordance + accent-tinted border; no row class borrows the other's anatomy.
5. **Chord map.** With the rail collapsed AND focus inside a Loans grid row, G C lands on `/calendar` (frame test 08 §10.5); G V at book altitude opens the palette pre-filled with `review {loan} {period}`; "gh" typed inside an editable field navigates nowhere.
6. **Collision enforcement.** Registering a duplicate binding in any reachable scope chain fails the build; the "?" overlay lists exactly the registry contents (walked programmatically, not hand-asserted).
7. **Redundancy law.** CI walks every registered palette command and resolves its declared visible-affordance anchor to a mounted component on the owning surface; anchorless commands fail.
8. **Dangerous verbs.** Typing "certify" as an owner yields only navigation rows (certificate place/ceremony), whose action is `navigate` — asserted at the command-descriptor level: no palette command carries an `execute` handler that writes domain state. As a preparer without certify rights, the ceremony row is absent entirely (visibility, not disablement).
9. **Esc ladder.** With a pending chord, a lit trace, and an open canvas panel stacked: three Esc presses clear them in order (chord → trace → panel), the fourth closes the summoned pane, the fifth steps up one breadcrumb altitude; inside the certify modal, Esc only cancels the act.
10. **Parameterized command.** `go to bex q2` slot-completes to Bexley Q2-2026 and lands on the period spine with loan+period crumbs (altitude gate); a loan outside the viewer's role scope never appears in slot completion.
11. **Settings search.** `escalation` returns the deep anchor into `/settings/notifications` for an admin and returns nothing for a non-admin (invisible, not grayed).
12. **Answer honesty.** With the occupancy resolver blocked (gap 7 fixture), `occupancy calloway` renders the named blocked state and no figure; unblocking the resolver flips the same query to the computed 279/301 = 92.69% (evidence: SLOT-2, 04/30/2018) with a working lit-row link to the roll's contributing rows including the 322-rows→301-units dedup step.
