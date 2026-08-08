# CROSS-CUTTING — ACCESSIBILITY AND LONG-SESSION ERGONOMICS

Commitment: **WCAG 2.1 AA on every Covenant surface**, verified by measurement, never by eyeball — plus the endurance contract the monthly close demands. The close is hours of table work, evidence reading, and two high-stakes ceremonies (03 §4's book rhythm); a product that is technically accessible but exhausting fails the same user. This file is one contract because the two concerns share every mechanism: the quiet ground that meets contrast law is the ground that prevents fatigue; the keyboard model that serves a screen-reader user is the speed layer for a preparer on loan 14 of 30.

Consumes: the ruled token system (settled law — zero new colors, ever), the pane model (08 §4, §9), the lit-row contract (`cross-cutting/provenance-lit-row-trace.md`), the chord map (`cross-cutting/search-command-keyboard.md`), the quiet-log law (04 §2.3), and the Settings brief's Appearance section (density; reduced motion).

## 1. The commitment, mapped to mechanisms

| WCAG 2.1 success criterion | Where it bites in Covenant | Owning section |
|---|---|---|
| 1.3.1 Info and relationships | Registers as semantic tables; three-actor grammar in ARIA, not styling alone | §4 |
| 1.4.1 Use of color | Verdict chips carry words; severity color is reinforcement, never the datum | §4 |
| 1.4.3 Contrast (minimum) | Every text-role token on every ground it may sit on — measured by readback | §2 |
| 1.4.11 Non-text contrast | Focus indicators, chip boundaries, chart marks ≥3:1 independent of hairlines | §2.3 |
| 2.1.1 Keyboard / 2.1.2 No trap | Every workflow keyboard-complete incl. both ceremonies and lit-row tracing | §3 |
| 2.2.1 Timing adjustable | Auth expiry preserves work; nothing times out into data loss | §6 |
| 2.3.1 Three flashes | Nothing flashes; the ground is quiet by law | §5 |
| 2.4.3 Focus order / 2.4.7 Focus visible | Trace handoff, ceremony traps, pane discipline; visible focus on every rung | §5, §2.3 |
| 1.4.4 Resize text / 1.4.10 Reflow | 200% text scale without loss; reflow posture stated honestly for review work | §3.1 |
| 2.5.1–2.5.4 Input modalities | No path gestures, pointer cancellation, label-in-name, no motion actuation | §3.2 |
| 3.3.1–3.3.3 Error identification/labels | Typed-act forms restate, label, and name errors inline; gates explain themselves | §9.1 |
| 4.1.2 Name, role, value / 4.1.3 Status messages | Figure announcement grammar; polite live regions under the quiet-log law | §4 |

## 2. Contrast on the ruled token system — measured, never eyeballed

### 2.1 The readback rule (the oklch trap)

The design-system packages author colors in oklch that Lightning CSS compiles **~2 shades darker** than authored; `src/styles/covenant-tokens.css` therefore pins the DS-labeled hex values, and the standing verification method for any Covenant color claim is **canvas readback of the rendered surface** (snapshot §4; memory `covenant-ds-oklch-renders-dark-pin-hex.md`; 08 test 7 applies it to the frame). This contract makes readback the *only* admissible evidence for a contrast claim:

- A contrast assertion in any brief, PR, or review is invalid unless produced by the readback harness (§11) against rendered pixels.
- The arithmetic table in §2.2 is **expected values** computed from the authored hexes — an anchor for the harness, never a substitute for it. If readback disagrees with the authored hex, that is the oklch trap firing: the build fails on the delta itself, before any contrast question is even asked.
- The full verification matrix is enumerated from `covenant-tokens.css` mechanically: every text-role token × every surface token it may sit on (the ten-rung gray ladder rungs, the periwinkle accent family, the certified-sheet hexes), plus every non-text meaning-bearing pair (focus indicators, chip boundaries, chart marks) against SC 1.4.11's 3:1. Rung values are not restated here — the token file is the authority; the harness reads it, this file rules on the results. The gray ladder was adopted with the raised-rung usage *measured* before ruling (snapshot §4) — the same measurement culture governs here.

### 2.2 Expected values on the ruled pairs (computed 2026-08-08 from the pinned hexes; binding number = readback)

| Pair (role) | Nominal ratio | AA at normal text (4.5:1) | Ruling |
|---|---|---|---|
| Certified-sheet accent sentences #33419D on panel #E7EBF8 | 7.40:1 | pass | The certification statement lines are AA-clean as ruled |
| #33419D on stronger tint #DDE4FB (key-figure band) | 6.95:1 | pass | AA-clean as ruled |
| Pressed #121459 on panel #E7EBF8 | 13.89:1 | pass | — |
| White label on the Certify control #33419D | 8.81:1 | pass | — |
| White label on hover #5265C5 | 5.22:1 | pass | The hover state carries its label safely |
| Hover #5265C5 *as text* on panel #E7EBF8 | 4.38:1 | **fail** | Usage rule: #5265C5 is a control-fill state, never a normal-size text color on the panel |
| Hairline #B3BCDA on panel #E7EBF8 | 1.59:1 | n/a (decorative) | Hairlines are ornament; no meaning-bearing boundary may rely on them alone (§2.3) |
| On-accent #0B1020 on accent #7189FF | 6.06:1 | pass | Accent-filled controls pair with on-accent, always |
| Accent #7189FF *as text* on white/light grounds | 3.12:1 | **fail** (passes 3:1 large-text/non-text) | Usage rule: accent on light grounds is large-text, non-text (focus rings, provenance underlines, lit-row bars), or paired-ground use only — never normal-size body/label text |
| Active #6078F4 as text on white | 3.82:1 | **fail** at normal size | Same usage rule as accent |

The certified sheet is a **light document surface always** — even inside the dark frame (Certificate brief) — so the sheet pairs above are verified on the light paper ground only; there is no dark-mode variant of the sheet to verify.

### 2.3 The resolution law

- Where a **ruled combination as ruled** fails AA under readback, the resolution is an **amendment request to Terry — NEVER a local color change** (COLOR LAW D-50: no session changes a color value without Terry naming it). The readback harness emits the amendment-request artifact (pair, measured ratio, surface, screenshot) instead of a patch. A "helpful" hex nudge is the named automatic failure of this file.
- Where the failure is a **usage** (a token placed on a ground the rules above forbid), the fix is the usage rule — re-pair, resize, or re-role the text — which changes no color value and needs no amendment. §2.2's two usage rules exist precisely so accent-family text never ships at normal size on light grounds.
- SC 1.4.11 (non-text 3:1): focus indicators must meet 3:1 against adjacent colors independently of hairlines (accent #7189FF on white = 3.12:1 nominal — passes as a focus ring); verdict-chip boundaries never carry meaning that the chip's **word** does not (§4), so chip tint contrast is not load-bearing.
- Figures set in Geist Mono `tabular-nums slashed-zero` (settled law) — the slashed zero is itself a legibility control (0/O disambiguation in hashes and figures) and is non-negotiable in ceremony hash chips.

## 3. Keyboard completeness

Every workflow is completable keyboard-only (SC 2.1.1), including the two ceremonies and the money-shot interaction:

| Surface / mechanism | Keyboard contract |
|---|---|
| Registers and queues | J/K row movement, Enter opens, `/` focuses search; per-surface act keys (Inbox: S snooze, Shift+D delegate — Inbox brief); sort/filter controls tabbable |
| Global navigation | G-chords + ⌘K per `cross-cutting/search-command-keyboard.md`; chords work with the rail collapsed and with focus inside a table (08 test 5) |
| Breadcrumb switchers | Loan and period switchers keyboard-reachable and operable as menus (08 test 2) |
| Lit-row tracing | Every `<Figure>` focusable; Enter traces; Evidence receives focus on the lit row; Esc returns focus to the figure (provenance §2.2, test 4). The review's core act can never be pointer-only |
| Intake decisions | The decision set (file / replace / new period / merge / request / hold / dismiss) reachable as focusable actions on the selected arrival |
| The certify ceremony | Reachable, operable, cancelable entirely by keyboard — Tab through the restated identity block (loan, period, revision, hash), the typed attestation is a native text field, Esc cancels safely before the attestation submits. The typed act is keyboard-native by design (R4: typed confirmation for rare, dangerous acts) |
| The send ceremony | Same, over its distinct rhythm (recipient + hash-equality review, then typed SEND confirmation — deliberately non-identical to certify per R4's habituation guard) |
| Hover-revealed information | Per-field lineage (Composer), confirmer stamps, chip detail — all have focus-visible equivalents; row hover actions render on row focus |
| Drag interactions | Rail resize and Composer section ordering have keyboard commands (move up/down); drag is an accelerant, never the only path |

No keyboard trap (SC 2.1.2) except the two ceremony modals' intentional trap-with-escape (§5); focus never lands on `body` (sentinel assertion in the E2E).

### 3.1 Text resize and reflow — the honest posture

- **SC 1.4.4 (resize text, AA): full commitment.** 200% text scale loses no content or function on any surface: the type system is rem-based; panes obey their minimum-width laws under scaled text exactly as under narrow viewports (08 §9's no-silent-compression — a pane that can't hold its minimum at 200% collapses to a labeled tab, never clips); registers keep uniform rows at the scaled height; the certified sheet's ~880px paper column reflows its text without truncating figures.
- **SC 1.4.10 (reflow, AA): committed with the frame plan's stated boundary.** Registers, Settings, Documents, Calendar, and read-views reflow to narrow widths (evidence panes become overlay sheets, splits become stacked tabs — 08 §9). The spine's *review work* below the 1152×720 minimum renders the explicit "larger window required for review work" state (08 §9) rather than a broken layout — an honest, announced boundary, and 1.4.10's own exemptions (data tables, evidence renders requiring 2D layout) cover the working panes. Wide content always scrolls inside its own container, never the page body.
- Long-session corollary at the other extreme: at 2048+ ultra-wide, text measures stay bounded (the sheet's max-width, register max column widths) — unbounded line length is a fatigue defect, not a feature.

### 3.2 Input modalities beyond keyboard (SC 2.5.1–2.5.4)

- **No path-based gestures anywhere** — no swipe-to-act on rows, no drag-only affordances (§3's drag rule); everything is a click/Enter target.
- **Pointer cancellation:** acts fire on up-event; dragging off a control cancels; the two ceremonies fire nothing on press alone (the typed attestation is the trigger, by design).
- **Label in name:** every icon-labeled control's accessible name contains its visible label text (palette verbs, row actions, DocView mode tabs) — voice-control users can speak what they see.
- **No motion actuation.** Nothing shakes, tilts, or listens to device motion.

### 3.3 The two load-bearing keystroke sequences (specified key-by-key)

**The lit-row trace** (the money-shot interaction, keyboard form — provenance §2.2):

```text
Tab / arrow …      focus reaches a <Figure> (focus ring per §2.3; announcement per §4)
Enter              trace fires; Evidence opens/focuses; focus lands ON the lit row
Tab                walks the chain breadcrumb (figure → formula → inputs → region)
Enter (on a link)  re-lights at that chain level
Esc                dismisses the trace; focus returns to the originating figure, exactly
```

**The certify ceremony** (keyboard form — Certificate brief anatomy):

```text
Tab → Certify      reachable only when readiness is true (else the reasons list is the tab order)
Enter              ceremony modal opens; focus on the summary HEADING (never the confirm control)
Tab                walks the restated identity block: loan → period → revision → hash (full hash
                   reachable, announced via its chip) → capacity select (only if >1 capacity applies)
Tab                the typed-attestation text field (a native input; label visible and associated)
type…              the deliberate typed confirmation (R4's rare-and-dangerous exception)
Tab → Enter        the Certify commit control (enabled only when the attestation validates)
                   focus lands on the certification statement in the sheet when the record exists
Esc (any time pre-commit)  cancels; focus returns to the invoking control; nothing written
```

The send ceremony follows the same skeleton over its own rhythm (recipient review → hash-equality statement → typed SEND), asserted non-identical in §12.6.

## 4. Screen-reader semantics — and the color-blind safety rule

- **Tables are tables:** every register renders semantic `<table>` with `<th scope>` headers and a caption carrying the register's identity ("Reports — {n} packages"); spine-surface tables carry loan + period in the caption (the breadcrumb altitude gate, made audible). Uniform row heights (law) keep virtualization semantics exact (§8).
- **The Figure component announces value + provenance availability:** accessible name = the formatted value + its three-actor class + the affordance — "…$1,218,877 — certified — provenance available, press Enter to trace" (classes per 06 §7). The dotted provenance underline and paper treatment are visual grammar; the ARIA name carries the same information as text (SC 1.3.1, 4.1.2). A figure with no ref (legacy fixture) announces "no source available" — the honesty rule reaches the accessibility tree.
- **Verdict chips always render the word** — `pass` / `watch` / `shortfall` / `breach` — never color alone (SC 1.4.1). This is simultaneously the color-blind safety rule: severity tint is reinforcement, the word is the datum. A monitored miss reads "shortfall" (canon: Bexley occupancy 268/301 = 89.04% against the monitored 90% floor is a SHORTFALL — the word "breach" is unreachable for `basis=monitored`, structurally). Basis badges (`covenanted` / `monitored`) are likewise text.
- **Component semantics for the named chrome:**

| Component | Semantics |
|---|---|
| `CountBadge` (your-move) | Announced as "your move: {n} items" on its rail item; never a bare number; zero renders no badge (no empty announcements) |
| Breadcrumb period selector | Menu button + menu roles; period options carry status in text ("Q2 2018 — ready"), not dot color alone |
| DocView mode strip | Tabs (`tablist`/`tab`/`tabpanel`); the derivative label "Recreated · searchable — derivative" is programmatically associated with the panel so a derivative can never be mistaken for original bytes (document-artifact law, made audible) |
| Certified sheet | A labeled document region; the hash chip's accessible name includes the full hash; certification statement lines are normal text (they *are* the record) |
| Quiet log | A plain list, newest-first, each entry naming actor + act + object; no live region — the quiet-log law means the log never announces itself |
| Charts under the doctrine | Every mark an instrument: focusable, with accessible name (loan/period/value); severity-only color means meaning survives grayscale by construction |
| Icons | Lucide-only: decorative icons `aria-hidden`; functional icons labeled. No emoji anywhere (law) |

- **Status messages** (SC 4.1.3): async completions announce via `aria-live="polite"` sparingly — the quiet-log law applies to live regions too: completed autonomous work does not announce; only state changes of the surface the user is operating do (readiness flipping, the void-on-change banner, gate results).

## 5. Focus management

- **Trace handoff (the lit-row contract):** Enter on a Figure moves focus to the lit row in Evidence; the chain breadcrumb is next in the tab order; Esc returns focus to the originating figure exactly (provenance test 4). Where the trace summons the Evidence overlay pane (08 §4), the summon receives focus; dismissal restores it.
- **The two ceremony modals are focus-trapped** (the only intentional traps): `aria-modal`, initial focus on the ceremony's *summary heading* — never on the confirm control (no reflex-arming; R4's NN/g guard rendered in focus order); Tab cycles inside; Esc cancels pre-attestation and returns focus to the invoking control; after the act completes, focus lands on the resulting record (the certification statement / the SendRecord receipt).
- **Void-on-change never steals focus:** the VOID banner announces via live region and becomes the first tab stop of the sheet; it does not yank focus from whatever the user is doing (a focus steal during data entry is its own error class).
- **Pane discipline:** summoned panes (work window 2, canvas panel) take focus only on explicit summon; Esc dismisses the canvas panel (08 §4); closing a split returns focus to the surviving window's last position. Below 1280px, evidence panes become overlay sheets (08 §9) — the overlay follows the same summon/restore rules, and a pane that collapses to a labeled tab moves focus to that tab, never into the void.
- **View switching** moves focus to the new view's heading; back/forward restore both scroll and focus context (§6 session-restore).
- Focus is always visible (SC 2.4.7) — the indicator meets 3:1 independently (§2.3) on every rung it can appear on, verified by the harness.

## 6. Long-session ergonomics — the monthly close is hours

- **Density modes** (Appearance: comfortable / compact): compact tightens row height and spacing for the 30-loan close; **uniform row heights are preserved in both modes** (law — and the virtualization contract §8 depends on it). Density changes spacing tokens only; zero color changes; both modes pass the full a11y suite (§12.9).
- **Quiet visual ground:** open-not-boxed tables on the ten-rung ladder, severity-only color, no decorative chrome — hours of exposure without alarm fatigue. Verdict tints appear only where verdicts are; the ground never shouts. The paper surfaces (DocView, the certified sheet) stay light even in the dark frame (ruled) — document reading happens on paper, the instrument chrome around it stays quiet.
- **No interruptions:** the quiet-log law is an ergonomic law — completed autonomous work never toasts, never pushes, never badges (unread is not a concept, C-8); the one push class is deadline escalation per org policy (04 §2.3). A preparer deep in the close is structurally uninterruptible; what the analyst did while you were away is *discoverable* (Home §4, the quiet feed), never announced.
- **Session-restore on every deep route:** the URL carries loan + period on every spine surface (08 §1); reload restores shell state; back/forward traverse views (08 test 1). Standing up from the desk costs nothing; the close resumes at Home's your-move, which is always the "where was I" answer (03 §4 — the computed query is the resume point, no separate "continue" feature needed).
- **Work survives authentication:** Clerk session expiry mid-act re-authenticates and returns to the interrupted surface with state intact — including a partially-completed ceremony re-arming at its summary step (never auto-submitting; the restored session re-resolves scope first, per `roles-permissions-tenancy.md` §8). SC 2.2.1 is satisfied by preservation, not by never expiring. Draft narrative edits and half-entered corrections autosave as drafts (drafts are cheap; typed acts are not).
- **Repetition ergonomics:** exception-first ordering (Review brief), the J/K rhythm, ask-once memory (nothing re-asked, ever — the agent law doubles as fatigue law), and the ~60-second review target mean the close's cost scales with exceptions, not with loans. The two ceremonies stay heavy *because* everything else is frictionless (R4's ceremony budget — two heavy acts, zero routine dialogs).

### 6.1 The endurance walkthrough (a close day, mechanisms named)

1. **09:00 — arrival.** Home's your-move is the resume point: three certify-ready periods, one arrival decision, one failed tie-out. No feed to triage, no unread to clear (C-8) — the list *is* the day.
2. **09:05 — the tie-out.** `G L` → loan → period → review; the exception renders first; a lit-row trace (Enter) checks the delta's source; the disposition is typed; focus returns; J to the next stop. Density: compact (set once in Appearance, remembered).
3. **10:30 — deep table work.** The 322-row roll scrolls at 60fps with uniform rows; the ground stays quiet (no boxes, no severity tint outside verdicts); the mono figures with slashed zeros stay legible at compact density.
4. **12:00 — lunch.** The browser closes. Nothing decays: every route is deep-addressable, the period is a durable state, drafts are autosaved.
5. **13:00 — resume.** Reload → shell state restores (08 test 1); Home's your-move recomputes to the same truth. Clerk re-auth on a stale session returns to the interrupted surface, not to a landing page.
6. **15:00 — ceremonies.** Certify then send, each its own rhythm (§3.3); no confirmation dialogs were encountered anywhere else all day, so the two typed acts still register as acts (NN/g's habituation constraint, §10.5). Focus lands on each record when written.
7. **All day — zero interruptions.** The agent's completed work sits in the quiet log for whenever curiosity strikes; the only thing that could have pushed was a deadline escalation, and none fired.

The test of this section is subtractive: nothing in the walkthrough required a pointer, produced a toast, or asked a question the product had asked before.

## 7. Reduced motion

- **No motion is required for meaning, anywhere.** The lit-row highlight is a static tinted band + left accent bar (provenance §4) — it works with zero animation; any settle transition is garnish over an already-legible state.
- `prefers-reduced-motion` is honored globally, and the Appearance setting (Settings brief) mirrors it in-product: non-essential transitions drop to none, scroll-to-lit-region becomes instant scroll, skeleton pulse becomes a static placeholder.
- The two ceremonies contain no celebratory motion by design — the record *is* the payoff (trust posture, not confetti).
- Nothing auto-plays, nothing flashes (SC 2.3.1), nothing parallaxes. The quiet ground is also the vestibular-safe ground.

## 8. Table virtualization performance

- Registers virtualize above ~200 rows via NEW `VirtualizedRegister` (`src/components/covenant/register/`), wrapping the uniform-row open-not-boxed table. Uniform row heights make virtualization *exact* — no measurement pass, no scroll jitter (the design law pays a performance dividend).
- Canonical stress fixture: the Calloway Park rent roll — **322 lease rows over 301 units** (evidence) — plus a 150-row statement in the Evidence pane (provenance test 6 pairs with it). The 7-loan demo book is trivial; Documents and Reports grow without bound.
- A11y under virtualization: `aria-rowcount`/`aria-rowindex` reflect the full set; J/K and Tab reach virtualized rows (scrolling them into existence); `/` search operates on the full dataset; the register is complete or says what's filtered (the Inbox brief's no-silent-truncation rule).
- Budgets: 60fps scroll on the 322-row fixture at 1440 and 2048 viewports; zero layout shift from row mounting; wide tables scroll inside their own container, never the page.

## 9. Loading and latency feedback standards

- **Skeletons keep geometry:** loading states reserve exact final dimensions (row-level skeletons; the lit-row target stays marked while document pages lazy-load — provenance §4). No spinner-only full-page states; registers render their count header first, rows stream beneath it.
- **Async acts are honest:** every act control shows a working state and stays disabled until server truth returns; failures render exactly as the API returns them (the send vertical's honest grammar). **No optimistic UI on typed acts** — a period never renders certified or sent before its record exists (trust hierarchy: the record is the fact).

| Situation | Rendering |
|---|---|
| Engine read in flight | Prior value + explicit "recomputing…" marker (recompute-on-view never blanks the surface) |
| Long extraction/OCR job | Never blocks a surface; runs as quiet-log work, surfaces when done |
| Gate refusal | The API's own reason, verbatim category (503 config → admin-facing fix path; 403 → why; 404 → scope), per the send vertical |
| Blocked computation | The fail-closed state naming what is missing (04 §2.5) — never a plausible placeholder |
| Document page lazy-load | Row-level skeleton with the trace target held marked |

- **Latency budgets by interaction class:**

| Interaction class | Budget | Over-budget behavior |
|---|---|---|
| Keystroke/focus feedback | <100ms | — (hard budget) |
| Trace-to-light, document open | <200ms | Skeleton-marked target region (provenance test 6) |
| Register render (count header + first rows) | <300ms | Count header first, rows stream |
| Engine read (verdicts, readiness, tie-out) | <500ms | Named progress ("recomputing tie-out…"), prior value held visible |
| Document page render | lazy | Row-level skeleton holds the trace target marked |
| Extraction/OCR jobs | unbounded | Never blocks; quiet-log completion; the waiting checklist item states what it awaits |
| Typed-act round trip (certify/send) | server-truth | Working state on the control; **no optimistic render, ever** |

### 9.1 Forms and error prevention (SC 3.3.1–3.3.3)

- Every input in a typed-act flow carries a visible, programmatically associated label; placeholder text is never the label.
- Errors are identified in text, inline, beside the field ("the attestation must match your name as shown"), and announced politely; error color rides on the text, never replaces it (§4's rule applied to forms).
- The two ceremonies are themselves the SC 3.3.4-class safeguard (reversible/checked/confirmed for legal commitments): each restates exactly what is being acted on (loan, period, revision, hash, recipient) before the typed confirmation — review-before-commit is the ceremony's anatomy, not an extra dialog (R4).
- Gate refusals are error prevention, not error punishment: a disabled Certify lists each unmet requirement as its own linked row (readiness reasons), so the "error" is fixed at its source, never guessed at.

## 10. Accessibility-relevant states (every surface, one grammar)

| State | A11y behavior |
|---|---|
| Loading | Skeletons with reserved geometry; no aria-busy storms; count header renders first |
| Blocked / gated | The gate names its reason as text; the blocked control is absent or labeled-disabled *with the reason in its accessible description* (gates are the one legitimate disabled-with-reason case — permission chrome is invisible-not-disabled per `roles-permissions-tenancy.md` §5) |
| Stale / low-confidence / conflict | Badge text + description, never tint alone; suspect-word styling in DocView pairs with a text marker |
| Watch / shortfall / breach | The word, always (§4); signed headroom as a figure with provenance |
| Certified / void-on-change | Certified state announced on the sheet; the VOID banner is a live-region announcement + first tab stop (§5) |
| Sealed / sent | Sealed banner text explains the projection; everything remains traceable read-only |
| Permission-denied / read-only | Named restriction text (roles contract §13); zero mutation affordances rather than disabled ones |
| Reduced-motion | All states above fully legible with zero animation (§7) |
| Compact density | All states above at compact spacing with uniform rows preserved (§6) |

## 11. Build target (basis-v2)

- `src/styles/covenant-tokens.css` — consumed as-is (never edited by this workstream; COLOR LAW).
- NEW `scripts/a11y/contrast-readback.ts` — renders surface fixtures headless, canvas-reads token pairs, compares against authored hexes + §2.2 expecteds; emits pass/fail + amendment-request artifacts. Wired into CI beside the existing engine tests.
- NEW `src/components/covenant/register/VirtualizedRegister.tsx` (§8); the `<Figure>` a11y behavior lands with the provenance contract's component (`src/components/covenant/provenance/Figure.tsx`).
- axe integration (`@axe-core/playwright`) in the E2E suite; reduced-motion + density fixtures in the same suite.
- Ceremony focus-trap behavior lands in the certify/send modal components per their briefs (`/covenant/[loanId]/[period]/certificate` route); `CountBadge`, breadcrumb switcher, and DocView mode-strip semantics land in their owning components (existing `CovenantShell` chrome).

## 12. Acceptance tests

1. **axe-clean on every surface fixture:** `/home`, `/inbox`, `/intake`, `/loans`, `/loans/[loanId]` (all tabs), `/calendar`, `/reports`, `/documents`, `/documents/[docId]` (all four DocView modes), `/settings/*`, `/rent-roll`, and the spine `/covenant/[loanId]/[period]/{review|composer|certificate|actuals}` — zero violations at the AA ruleset, per fixture, in CI.
2. **`covenant-keyboard-only-close` (named E2E):** on the Calloway Park FYE-2018 evidence spine — arrival via intake → recognition decision → checklist → confirm schedule → confirm mapping → review with at least one lit-row trace (Enter/Esc round-trip) → certify (typed attestation) → send → sealed record opened from Reports. **Zero pointer events for the entire run.** This is the release-gating walkthrough from arrival to sealed record.
3. **Contrast readback:** the harness passes on all enumerated pairs at 1440/1728/2048; any rendered-vs-authored hex delta fails the build (the oklch detector); a failing *ruled* pair produces an amendment-request artifact and fails CI with no color patch present (the patch's absence is itself asserted — the fix path is Terry, not the build).
4. **Verdict words:** all four verdict chips render their word in every context (register chips, covenant strip, review stops, Home summaries); the Bexley fixture's monitored occupancy miss (268/301 = 89.04% vs the monitored 90% floor) renders "shortfall" — and the string "breach" is unreachable for `basis=monitored` (unit + render test).
5. **Trace focus handoff:** provenance test 4 passes on every adopting renderer (focus figure → Enter → lit row focused → Esc → figure refocused), including the overlay-pane variant below 1280px.
6. **Ceremony traps:** both ceremonies trap focus, open on the summary heading (never the confirm control), Esc-cancel safely pre-attestation, and land focus on the resulting record after the act; the two ceremonies' interaction rhythms are asserted non-identical (R4 habituation guard).
7. **Reduced-motion suite:** with `prefers-reduced-motion` (and with the Appearance setting), zero non-essential animations run, scroll is instant, and the lit-row highlight is fully legible as a static state (screenshot assertion).
8. **Virtualization:** the 322-row rent-roll fixture — `aria-rowcount` = 322, J/K reaches row 300, 60fps scroll budget met, zero CLS from row mounting; the 322-rows→301-units dedup remains visible in the traced chain (provenance test 3).
9. **Density modes:** comfortable and compact both pass tests 1 and 3; row-height uniformity asserted in both; zero color-token differences between modes.
10. **Session restore:** reload on every deep route restores view + pane state; simulated auth expiry mid-certify returns to the ceremony summary with state intact and un-submitted; Home's your-move equals the computed query after restore (08 test 6).
11. **Latency:** trace-to-light <200ms on the 150-row statement (provenance test 6); typed-act controls verified non-optimistic (record-before-render assertion against the real send gate); skeleton geometry stability (CLS ≈ 0) on register load.
12. **SR smoke:** the Figure announcement grammar (value + class + trace affordance, and the "no source available" case), table captions with loan/period identity on spine surfaces, the derivative-label association in DocView, and the `CountBadge` "your move" phrasing — verified with an assistive-technology script over the review fixture.
13. **Text resize and reflow:** at 200% text scale, every surface fixture retains all content and function (no clipping; panes collapse to labeled tabs per the no-silent-compression law); registers reflow at narrow widths; the spine's sub-minimum state renders its named "larger window required" message rather than a broken layout; ultra-wide (2048+) fixtures show bounded text measures.
14. **Input modality:** no act in the E2E map requires a path gesture or drag; acts fire on up-event and cancel on drag-off; every icon control's accessible name contains its visible label (automated 2.5.3 scan across fixtures).
15. **Form errors:** invalid attestation input in each ceremony yields an inline text error, associated with the field and announced politely; the gate refusal cases render their reasons as linked rows (readiness), asserted as text content.
