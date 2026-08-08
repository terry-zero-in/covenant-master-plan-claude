# CROSS-CUTTING — ACCESSIBILITY AND LONG-SESSION ERGONOMICS

Commitment: **WCAG 2.1 AA on every Covenant surface**, verified by measurement, never by eyeball — plus the endurance contract the monthly close demands. The close is hours of table work, evidence reading, and two high-stakes ceremonies (03 §4's book rhythm); a product that is technically accessible but exhausting fails the same user. This file is one contract because the two concerns share every mechanism: the quiet ground that meets contrast law is the ground that prevents fatigue; the keyboard model that serves a screen-reader user is the speed layer for a preparer on loan 14 of 30.

Consumes: the ruled token system (settled law — zero new colors, ever), the pane model (08 §4, §9), the lit-row contract (`cross-cutting/provenance-lit-row-trace.md`), the chord map (`cross-cutting/search-command-keyboard.md`), the quiet-log law (04 §2.3), and the Settings brief's Appearance section (density; reduced motion).

## 1. Contrast on the ruled token system — measured, never eyeballed

### 1.1 The readback rule (the oklch trap)

The design-system packages author colors in oklch that Lightning CSS compiles **~2 shades darker** than authored; `src/styles/covenant-tokens.css` therefore pins the DS-labeled hex values, and the standing verification method for any Covenant color claim is **canvas readback of the rendered surface** (snapshot §4; memory `covenant-ds-oklch-renders-dark-pin-hex.md`; 08 test 7 applies it to the frame). This contract makes readback the *only* admissible evidence for a contrast claim:

- A contrast assertion in any brief, PR, or review is invalid unless produced by the readback harness (§10) against rendered pixels.
- The arithmetic table in §1.2 is **expected values** computed from the authored hexes — an anchor for the harness, never a substitute for it. If readback disagrees with the authored hex, that is the oklch trap firing: the build fails on the delta itself, before any contrast question.
- The full verification matrix is enumerated from `covenant-tokens.css` mechanically: every text-role token × every surface token it may sit on (the ten-rung gray ladder rungs, the periwinkle accent family, the certified-sheet hexes), plus every non-text meaning-bearing pair (focus indicators, chip boundaries, chart marks) against SC 1.4.11's 3:1. Rung values are not restated here — the token file is the authority; the harness reads it, this file rules on the results.

### 1.2 Expected values on the ruled pairs (computed 2026-08-08 from the pinned hexes; binding number = readback)

| Pair (role) | Nominal ratio | AA verdict at normal text (4.5:1) | Ruling |
|---|---|---|---|
| Certified-sheet accent sentences #33419D on panel #E7EBF8 | 7.40:1 | pass | The certification statement lines are AA-clean as ruled |
| #33419D on stronger tint #DDE4FB (key-figure band) | 6.95:1 | pass | AA-clean as ruled |
| Pressed #121459 on panel #E7EBF8 | 13.89:1 | pass | — |
| White label on Certify control #33419D | 8.81:1 | pass | — |
| White label on hover #5265C5 | 5.22:1 | pass | Hover state carries its label safely |
| Hover #5265C5 *as text* on panel #E7EBF8 | 4.38:1 | **fail** | Usage rule: #5265C5 is a control-fill state, never a normal-size text color on the panel |
| Hairline #B3BCDA on panel #E7EBF8 | 1.59:1 | n/a (decorative) | Hairlines are ornament; no meaning-bearing boundary may rely on them alone (§1.3) |
| On-accent #0B1020 on accent #7189FF | 6.06:1 | pass | Accent-filled controls pair with on-accent, always |
| Accent #7189FF *as text* on white/light grounds | 3.12:1 | **fail** (passes 3:1 large-text/non-text) | Usage rule: accent on light grounds is large-text, non-text (focus rings, provenance underlines, lit-row bars), or paired grounds only — never normal-size body/label text |
| Active #6078F4 as text on white | 3.82:1 | **fail** at normal size | Same usage rule as accent |

### 1.3 The resolution law

- Where a **ruled combination as ruled** fails AA under readback, the resolution is an **amendment request to Terry — NEVER a local color change** (COLOR LAW D-50: no session changes a color value without Terry naming it). The readback harness emits the amendment-request artifact (pair, measured ratio, surface, screenshot) instead of a patch.
- Where the failure is a **usage** (a token placed on a ground the rules above forbid), the fix is the usage rule — re-pair, resize, or re-role the text — which changes no color value and needs no amendment. §1.2's two usage rules exist precisely so accent-family text never ships at normal size on light grounds.
- SC 1.4.11 (non-text 3:1): focus indicators must meet 3:1 against adjacent colors independently of hairlines (accent #7189FF on white = 3.12:1 nominal — passes as a focus ring); verdict-chip boundaries never carry meaning that the chip's **word** does not (§3), so chip tint contrast is not load-bearing.
- Figures set in Geist Mono `tabular-nums slashed-zero` (settled law) — the slashed zero is itself a legibility control (0/O disambiguation in hashes and figures) and is non-negotiable in ceremony hash chips.

## 2. Keyboard completeness

Every workflow is completable keyboard-only (SC 2.1.1), including the two ceremonies and the money-shot interaction:

- **Registers and queues:** J/K row movement, Enter opens, `/` focuses search, per-surface act keys (Inbox: S snooze, Shift+D delegate — Inbox brief); G-chords + ⌘K per `cross-cutting/search-command-keyboard.md`; breadcrumb loan/period switchers keyboard-reachable (08 test 2).
- **Lit-row tracing:** every `<Figure>` is focusable; Enter traces; Evidence receives focus on the lit row; Esc returns focus to the figure (provenance contract §2.2 and test 4). Tracing is the review's core act — it can never be pointer-only.
- **The certify ceremony:** reachable, operable, and cancelable entirely by keyboard — Tab through the restated identity block (loan, period, revision, hash), the typed attestation input is a native text field, Esc cancels safely before the attestation submits. The typed act is keyboard-native by design (R4: typed confirmation for rare, dangerous acts).
- **The send ceremony:** same, over its distinct rhythm (recipient + hash-equality review, then typed SEND confirmation — deliberately non-identical to certify per R4's habituation guard).
- **No pointer-only affordances anywhere:** hover-revealed lineage (Composer per-field lineage, confirmer stamps) has a focus-visible equivalent; drag interactions (rail resize, Composer section order) have keyboard commands (move up/down); row hover actions render on row focus.
- **No keyboard trap** (SC 2.1.2) except the two ceremony modals' intentional trap-with-escape (§4); focus never lands on `body` (sentinel assertion in the E2E).

## 3. Screen-reader semantics — and the color-blind safety rule

- **Tables are tables:** every register renders semantic `<table>` with `<th scope>` headers and a caption carrying the register's identity ("Reports — {n} packages"); spine-surface tables carry loan + period in the caption (altitude gate, made audible). Uniform row heights (law) keep virtualization semantics exact (§9).
- **The Figure component announces value + provenance availability:** accessible name = the formatted value + its three-actor class + the affordance — "…$1,218,877 — certified — provenance available, press Enter to trace" (classes per 06 §7). The dotted provenance underline and paper treatment are visual grammar; the ARIA name carries the same information as text (SC 1.3.1, 4.1.2).
- **Verdict chips always render the word** — `pass` / `watch` / `shortfall` / `breach` — never color alone (SC 1.4.1). This is simultaneously the color-blind safety rule: severity tint is reinforcement, the word is the datum. A monitored miss reads "shortfall" (canon: Bexley occupancy 268/301 = 89.04% against the monitored 90% floor is a SHORTFALL — the word "breach" is unreachable for monitored basis, structurally). Basis badges (`covenanted` / `monitored`) are likewise text.
- **Charts under the doctrine are already color-safe by construction:** severity-only color plus every-mark-an-instrument means each mark has an accessible name (loan/period/value) and its meaning survives grayscale; marks are focusable instruments (chart doctrine + SC 2.1.1).
- **Artifact honesty is announced:** the "Recreated · searchable — derivative" label on DocView is programmatically associated with the document content, so a screen-reader user can never mistake a derivative for original bytes (document-artifact law, made audible).
- **Status messages** (SC 4.1.3): async completions announce via `aria-live="polite"` sparingly — the quiet-log law applies to live regions too: completed autonomous work does not announce; only state changes of the surface the user is operating do (readiness flipping, void-on-change banner, gate results).
- Icons are Lucide-only: decorative icons `aria-hidden`; functional icons carry labels. No emoji anywhere (law).

## 4. Focus management

- **Trace handoff (the lit-row contract):** Enter on a Figure moves focus to the lit row in Evidence; the chain breadcrumb is next in the tab order; Esc returns focus to the originating figure exactly (provenance test 4). Where the trace summons the Evidence overlay pane (08 §4), the summon receives focus; dismissal restores it.
- **The two ceremony modals are focus-trapped** (the only intentional traps): `aria-modal`, initial focus on the ceremony's *summary heading* — never on the confirm control (no reflex-arming; R4's NN/g guard rendered in focus order); Tab cycles inside; Esc cancels pre-attestation and returns focus to the invoking control; after the act completes, focus lands on the resulting record (the certification statement / the SendRecord receipt).
- **Void-on-change never steals focus:** the VOID banner announces via live region and becomes the first tab stop of the sheet; it does not yank focus from whatever the user is doing (a focus steal during data entry is its own error class).
- **Pane discipline:** summoned panes (split window 2, canvas panel) take focus only on explicit summon; Esc dismisses the canvas panel (08 §4); view-switching moves focus to the new view's heading, never to `body`.
- Focus is always visible (SC 2.4.7) — the focus indicator meets 3:1 independently (§1.3) on every rung it can appear on, verified by the harness.

## 5. Reduced motion

- **No motion is required for meaning, anywhere.** The lit-row highlight is a static tinted band + left accent bar (provenance §4) — it works with zero animation; motion (a brief settle transition) is garnish over an already-legible state.
- `prefers-reduced-motion` is honored globally, and the Appearance setting (Settings brief) mirrors it in-product: non-essential transitions drop to none, scroll-to-lit-region becomes instant scroll, skeleton pulse becomes a static placeholder.
- The two ceremonies contain no celebratory motion by design — the record *is* the payoff (trust posture, not confetti).
- Nothing auto-plays, nothing flashes (SC 2.3.1), nothing parallaxes. The quiet ground is also the vestibular-safe ground.

## 6. Long-session ergonomics — the monthly close is hours

- **Density modes** (Appearance: comfortable / compact): compact tightens row height and spacing for the 30-loan close; **uniform row heights are preserved in both modes** (law — and the virtualization contract §9 depends on it). Density changes spacing tokens only; zero color changes; both modes pass the full a11y suite (§10.9).
- **Quiet visual ground:** open-not-boxed tables on the ten-rung ladder, severity-only color, no decorative chrome — hours of exposure without alarm fatigue. Verdict tints appear only where verdicts are (the ground never shouts).
- **No interruptions:** the quiet-log law is an ergonomic law — completed autonomous work never toasts, never pushes, never badges (unread is not a concept, C-8); the one push class is deadline escalation per org policy (04 §2.3). A preparer deep in the close is structurally uninterruptible.
- **Session-restore on every deep route:** the URL carries loan + period on every spine surface (08 §1); reload restores shell state; back/forward traverse views (08 test 1). Standing up from the desk costs nothing.
- **Work survives authentication:** Clerk session expiry mid-act re-authenticates and returns to the interrupted surface with state intact — including a partially-completed ceremony re-arming at its summary step (never auto-submitting). SC 2.2.1 (timing adjustable) is satisfied by preservation, not by never expiring. (Scope resolution on return: `roles-permissions-tenancy.md` §8.)
- **Repetition ergonomics:** exception-first ordering (Review brief), J/K rhythm, ask-once memory (nothing re-asked), and the ~60-second review target mean the close's cost scales with exceptions, not with loans.

## 7. Table virtualization performance

- Registers virtualize above ~200 rows via NEW `VirtualizedRegister` (`src/components/covenant/register/`), wrapping the uniform-row open-not-boxed table. Uniform row heights make virtualization *exact* — no measurement pass, no scroll jitter (the design law pays a performance dividend).
- Canonical stress fixture: the Calloway Park rent roll — **322 lease rows over 301 units** (evidence) — plus a 150-row statement in Evidence (provenance test 6 pairs with it).
- A11y under virtualization: `aria-rowcount`/`aria-rowindex` reflect the full set; J/K and Tab reach virtualized rows (scrolling them into existence); `/` search operates on the full dataset, and the register is complete or says what's filtered (Inbox brief's no-truncation rule).
- Budgets: 60fps scroll on the 322-row fixture at 1440 and 2048 viewports; zero layout shift from row mounting.

## 8. Loading and latency feedback standards

- **Skeletons keep geometry:** loading states reserve exact final dimensions (row-level skeletons; the lit-row target stays marked while document pages lazy-load — provenance §4). No spinner-only full-page states; registers render their count header first.
- **Async acts are honest:** every act control shows a working state and stays disabled until server truth returns; failures render exactly as the API returns them (the send vertical's honest 503/404/403 grammar). **No optimistic UI on typed acts** — a period never renders certified or sent before its record exists (trust hierarchy: the record is the fact).
- Budgets: input feedback <100ms; trace-to-light <200ms with the document open (provenance test 6); engine reads render <500ms or show named progress ("recomputing tie-out…"); long extraction/OCR work never blocks a surface — it is quiet-log work that surfaces when done.
- Recompute-on-view (04 §1, Review) may not blank the surface: stale-while-revalidate rendering with an explicit "recomputing" marker, never a flash to skeleton.

## 9. Build target (basis-v2)

- `src/styles/covenant-tokens.css` — consumed as-is (never edited by this workstream; COLOR LAW).
- NEW `scripts/a11y/contrast-readback.ts` — renders surface fixtures headless, canvas-reads token pairs, compares to authored hexes + §1.2 expecteds; emits pass/fail + amendment-request artifacts. Wired into CI beside the existing engine tests.
- NEW `src/components/covenant/register/VirtualizedRegister.tsx` (§7); `<Figure>` a11y behavior lands with the provenance contract's component (`src/components/covenant/provenance/Figure.tsx`).
- axe integration (`@axe-core/playwright`) in the E2E suite; reduced-motion + density fixtures in the same suite.
- Ceremony focus-trap behavior lands in the certify/send modal components per their briefs (`/covenant/[loanId]/[period]/certificate` route).

## 10. Acceptance tests

1. **axe-clean on every surface fixture:** `/home`, `/inbox`, `/intake`, `/loans`, `/loans/[loanId]` (all tabs), `/calendar`, `/reports`, `/documents`, `/documents/[docId]` (all DocView modes), `/settings/*`, `/rent-roll`, and the spine `/covenant/[loanId]/[period]/{review|composer|certificate|actuals}` — zero violations at the AA ruleset, per fixture, in CI.
2. **`covenant-keyboard-only-close` (named E2E):** on the Calloway Park FYE-2018 evidence spine — arrival via intake → recognition decision → checklist → confirm schedule → confirm mapping → review with at least one lit-row trace (Enter/Esc round-trip) → certify (typed attestation) → send → sealed record opened from Reports. **Zero pointer events for the entire run.** This is the release-gating walkthrough from arrival to sealed record.
3. **Contrast readback:** the harness passes on all enumerated pairs at 1440/1728/2048; any rendered-vs-authored hex delta fails the build (oklch detector); a failing *ruled* pair produces an amendment-request artifact and fails CI without any color patch present.
4. **Verdict words:** all four verdict chips render their word; the Bexley fixture's monitored occupancy miss (268/301 = 89.04% vs the monitored 90% floor) renders "shortfall" — the string "breach" is unreachable for `basis=monitored` (unit + render test).
5. **Trace focus handoff:** provenance contract test 4 passes on every adopting renderer (focus figure → Enter → lit row focused → Esc → figure refocused).
6. **Ceremony traps:** both ceremonies trap focus, open on the summary heading (never the confirm control), Esc-cancel safely pre-attestation, and land focus on the resulting record after the act; the two ceremonies' interaction rhythms are asserted non-identical (R4 habituation guard).
7. **Reduced-motion suite:** with `prefers-reduced-motion`, zero non-essential animations run, scroll is instant, and the lit-row highlight is fully legible as a static state (screenshot assertion).
8. **Virtualization:** 322-row rent-roll fixture — `aria-rowcount` = 322, J/K reaches row 300, 60fps scroll budget met, zero CLS from row mounting; the 322-rows→301-units dedup remains visible in the traced chain (provenance test 3).
9. **Density modes:** comfortable and compact both pass tests 1 and 3; row-height uniformity asserted in both; zero color-token differences between modes.
10. **Session restore:** reload on every deep route restores view + pane state; simulated auth expiry mid-certify returns to the ceremony summary with state intact and un-submitted.
11. **Latency:** trace-to-light <200ms on the 150-row statement (provenance test 6); typed-act controls verified non-optimistic (record-before-render assertion against the real send gate).
12. **SR smoke:** the Figure announcement grammar (value + class + trace affordance), table captions with loan/period identity on spine surfaces, and the derivative label association — verified with an assistive-tech script over the review fixture.
