# REVIEW ROOM
## The ~60-second review — the exception walk over the package draft, evidence beside every claim

## 1. Identity and verdict

- **Product layer:** reporting spine (per loan, per period). This is the flagship surface — the room where "the borrower gives a roughly 60-second review" happens (03 §1, step 10; gate 1 completes here).
- **Route/address in basis-v2:** `/covenant/[loanId]/[period]/review` (planned deep route, 08 §1), mounting `CovenantShell` with the existing `ReviewSpread` + `Workspace` components (`src/components/covenant/`); today reachable only as a view inside `/review-room` (snapshot §3). New interior components land in `src/components/covenant/review/`; stop derivation and disposition acts in `src/lib/covenant/review/`; the route resolver clones the certificate-route pattern (own route + resolver + tests).
- **Current build state:** `FIXTURE` — the ruled 3-column reconcile layout renders over the Bexley demo book; every figure is a demo string; the lit-row trace renders but lights the WRONG source row in 2 of 3 renderers (U1-F1, the demo's money-shot interaction) (snapshot §3).
- **Existing-surface verdict:** `ADJUST`. The 3-column reconcile layout is ruled KEPT (snapshot §5; frame-mirror spec #528); the layer above the spine changes: the walk becomes stop-ordered and keyboard-driven, dispositions become typed acts, the lit-row trace adopts the app-wide contract (`cross-cutting/provenance-lit-row-trace.md`), readiness becomes computed.
- **Research tier:** C (R3 primary; R4 for review-progress/readiness mechanics; R2 for citation click-through and keyboard-flow mechanics).
- **Primary users/roles:** owner (reviews and will certify), PMC preparer (walks exceptions before the owner's pass; per-client scope), reviewer (read-only) (02 §4).
- **Frequency and session duration:** monthly/quarterly per loan, under deadline pressure (due-rules like Q+45d — evidence: SLOT-3 §8.02(b)). Target session: ~60 seconds on a clean period; a few minutes when exceptions exist, because each stop arrives with its evidence already lit. Deadline week means many loans reviewed back-to-back — the room is built for serial passes.
- **Error cost:** terminal. A wrong number accepted here becomes a certified number and then a sent number — the exact failure the product exists to prevent. Second error class: a review so padded with non-exceptions that the human rubber-stamps (NN/g habituation, R4 research) — which is why confirmed clean figures consume zero stops.
- **Success criterion:** a zero-exception period is reviewed and advanced to certify in ~60 seconds; every exception is dispositioned with its evidence beside it; the lit row is the *right* row 100% of the time; nothing reaches the certify CTA with readiness false.

## 2. User job and decisions

- **Primary job-to-be-done:** "Confirm this package is right before I put my name on it — show me only what needs my judgment, with proof beside each item."
- **Decisions made here, by stop class:**
  - *Shortfall/breach findings:* accept the verdict with a typed disposition; approve/route the drafted mitigant narrative; escalate a covenanted breach (never auto-dispositioned — 04 §1).
  - *Failed tie-outs:* "which period is right?" (05 §4) — accept-with-reason, or correct at source.
  - *Stale confirmations:* re-confirm a value whose source document was replaced, or route to Extraction & Confirmation.
  - *Changed-vs-prior fields:* accept the change, annotate it, or correct at source; summon the prior package for side-by-side proof.
  - *Narrative approvals:* approve the agent's draft, or edit it in Composer (with return path).
  - *Void return:* acknowledge the void reason, then walk the re-opened stops.
  - *The advance:* decide to proceed to certify once readiness is true.
- **Questions the surface must answer in scan order:** (1) Which loan+period am I reviewing and what is its deadline? (2) How many stops remain and what kind? (3) What is this stop claiming and what is its evidence? (4) What changed vs the prior package? (5) Am I ready to certify — and if not, exactly why?
- **What the user should not have to decide here:** whether a figure is computed correctly (engine, deterministic — trust hierarchy, LOCKED); which figures deserve attention (the engine orders the stops); how to phrase a mitigant from scratch (the agent drafts; the human approves/edits); anything already confirmed and unchanged (ask-once law — no re-review of clean confirmed figures); nothing about pane arrangement (stable geometry between loans).
- **Entry paths:** Home your-move row ("Bexley Q2 — in review, 3 stops") → this route with stop 1 focused; Inbox item row; Calendar deadline → period header → Review; Loans → Loan Detail → PERIODS tab → period; command palette ("Review — {loan} {period}" place row; `G V` in loan context — 08 §5); post-void return link from the void notice; Reports → sealed period (read-only render).
- **Exit paths:** correct-at-source routing — Actuals for values/mappings, Extraction & Confirmation for stale/pending confirmations, Composer for narrative edits and section assembly, Documents/DocView for source artifacts — each carrying a return path back to the exact stop; "Proceed to certify" → `/covenant/[loanId]/[period]/certificate`; breadcrumb up to Loan Detail; Esc closes summoned panes before it ever navigates (07 §3).
- **Completion/advancement conditions:** all stops dispositioned + tie-out clean + confirmations current + engine readiness true → period flips `in-review → ready` (03 §2); the certify CTA enables. Review completion is gate 1's completion (04 §1); the certify act itself never happens here.

**The ~60-second budget (design target, decomposed).** Clean period (FIX-CLEAN class): land with the SummaryStop focused (~0s) → read the summary claim + expand one count if desired (~20s) → scan the readiness strip, all reasons green (~10s) → optionally spot-trace one figure, evidence lights <200ms (~15s) → Proceed to certify (~5s). Total ≈ 50s with margin. Exception period: each stop budgets ~15–25s because the evidence is already lit on arrival and the disposition is one keystroke — a 4-stop period stays under 3 minutes. Anything pushing a stop past that budget (a real judgment call — a failed tie-out, a mitigant edit) is exactly the work the product is supposed to surface; the budget bounds ceremony, never judgment.

## 3. Object and ownership model

| Object/action/status | Owned here? | Summary/link elsewhere | Address/identity | Notes |
|---|---:|---|---|---|
| ReviewStop (derived exception queue) | YES | Home/Inbox count open stops as your-move | `(loanId, period, pkgRev, seq)` | Engine-derived projection, never hand-curated; ordering is law (§5) |
| Stop disposition acts (accept/annotate/approve/reopen) | YES | Quiet log renders each act | append-only act records | Typed acts; reopening is a new act with reason, never deletion |
| TieOut (prior-package reconciliation + disposition) | YES | Actuals links to the same deltas | `(loanId, period)` | 02 §2: Review Room owns TieOut; failed tie-out blocks readiness |
| Review-complete state | YES | Period header everywhere | period status input | Contributes to engine readiness; never a manual checkbox |
| Package draft + sections | no — Composer | Center canvas renders read-only; "open in Composer" links | `pkgId = (loanId, period, rev)` | Review never edits package content (02 §3) |
| TestResult verdicts + headroom | no — Actuals & Computation (engine) | Rendered in covenant section + finding stops | `(loanId, period, reqId)` | `basis_echo` re-stated on every render (05 §3) |
| Finding (shortfall/breach) | no — agent layer | Blocking findings surface as stops; the disposition act writes to the Finding | `findingId` | Disposition is a typed act (02 §2) |
| Documents / artifacts | no — Documents | Evidence panel renders (never re-files) documents | `docId` + version | Lit-row targets live here |
| Readiness (boolean + reasons[]) | no — engine computes; Composer owns package assembly | Readiness strip renders the same computed reasons with links | per package rev | One computed readiness; three renderers (Review, Composer, Certificate) — projection, not double-homing |
| Requirement definitions | no — Extraction & Confirmation | Cited verbatim beside tests in the Evidence chain | `reqId` | Definition text + source region only; never re-authored here |
| CertificationRecord / SendRecord | no — Certificate / Send & Record | Void state returns here as stop #0; sealed state renders read-only | `certId` / `sendId` | — |

**No-double-homing statement:** the Review Room owns the walk — stops, dispositions, tie-out — and nothing else. Every figure, document, verdict, draft, and readiness reason rendered here is a projection of its owning surface's state with a link, per 02 §3. Nothing on this surface mutates package content; "correct" always routes to the owner.

## 4. Data and semantic model

- **Source facts:** rendered only inside the Evidence panel — document artifacts (Original / Recreated-searchable / Structured reading, labeled per the four-artifact law), e.g. the Calloway Park T-12 sheet rows and the executed 6001.NR pages (evidence: SLOT-1/SLOT-3).
- **Extracted values awaiting confirmation:** render with proposed-class grammar (dotted provenance underline + confidence, 06 §7); they block readiness and surface as confirmation stops (§5).
- **Confirmed values:** provenance underline + confirmer stamp on hover; consume zero stops when unchanged and clean (exception-first law).
- **Deterministic outputs:** every shipping figure on the canvas — normalized lines, metrics, TestResults with signed headroom, tie-out deltas, readiness — engine only (04 §2.1). Recompute-on-view: the room never renders a stale figure without a stale badge (04 §1, Review row).
- **Agent proposals/drafts:** narrative sections (drafted, awaiting approval); stop explanations ("why this changed"); proposed dispositions for known-benign tie-out classes (04 §1, Tie-out row) — always labeled, never auto-applied. Drafted narratives interpolate engine values by reference, never by generation (04 §2.1).
- **Human decisions:** disposition acts, narrative approvals, tie-out dispositions, void acknowledgment, the decision to proceed to certify.
- **Certified values:** appear here only in sealed/read-only renders and in the prior-package compare pane (hash-anchored, certified-class grammar).
- **Versions/periods/packages:** the room binds to one `(loanId, period)` and the current package rev; the compare pane binds to the prior *sealed* package. Dispositions bind to the subject value's content state, not the rev — an unchanged value's disposition survives a rev bump; a changed value's stop reopens (GitHub stale-dismissal semantics, R4).
- **Evidence/provenance:** every material figure carries a `ProvenanceRef` and renders through `<Figure>`; `traceToEvidence(ref)` is the only resolver (`cross-cutting/provenance-lit-row-trace.md` §2 — the U1-F1 fix).
- **Permissions/read-only projections:** reviewer role sees everything, dispositions nothing; PMC preparer dispositions within client scope (certify may be reserved to the owner — role-configurable, 02 §4); sealed periods render read-only for everyone (seal-not-wipe).

**Three-actor grammar at the point of use in this room (06 §7):**

| Class | On the canvas | On trace |
|---|---|---|
| source | never on the canvas — Evidence panel only, document-native | opens Original bytes; hash visible |
| inferred (proposed) | dotted provenance underline + confidence; never bold-final | lights source region; confirm/correct routes upstream |
| inferred (confirmed) | provenance underline; confirmer stamp on hover | lights source region; correction history in the chain |
| certified | only in compare (prior package) and sealed renders | opens the certification record + full chain |

**Field groups and grain:**

| Group | Grain | Rendered where |
|---|---|---|
| Organization / client scope | org, client (PMC mode) | route authorization only — never a visible "Client" column in owner mode (02 §1) |
| Loan terms | loan (slowly changing) | period header context; evidence chains for debt-service inputs |
| Reporting period | `(loanId, period)` | breadcrumb, deadline chip, status |
| Package + sections | `(loanId, period, rev)` + sectionId | center canvas blocks, in lender-form order |
| Requirement + test | `(loanId, period, reqId)` | covenant section rows, finding stops, definition citations |
| Normalized financial line | `(propertyId, period, coaCode)` | Evidence panel rows; tie-out inputs |
| Document | docId + version + artifact | Evidence panel; DocView links |
| Stop + disposition | `(loanId, period, pkgRev, seq)` + act records | outline, stop cards, quiet log |
| Certification / send records | certId / sendId | void stop, certified/sealed banners |

## 5. State machine and exceptions

### 5.1 The stop queue (the surface's core state)

Stops are derived by the engine in ruled order; confirmed clean figures generate none (exception-first law — R3: Numeric autosubmit-when-matched):

```text
ORDER   CLASS                      SOURCE                                   DISPOSITIONS
  0     void-reason (only after    CertificationRecord void event           acknowledge (typed) — then walk
        a post-certify change)     (what changed, when, by whom/what)       the re-opened stops below
  1     shortfall/breach findings  TestResult verdicts + Finding objects    accept-with-disposition / annotate /
                                   (watch is informational — no stop)       route-to-narrative (mitigant approval)
  2     failed tie-outs            TieOut deltas vs prior sealed package    accept-with-reason (reason required) /
                                                                            correct-at-source
  3     stale confirmations        replaced source doc after confirmation;  accept-reconfirm / correct →
                                   pending or low-confidence confirmations  Extraction & Confirmation, return path
  4     changed-vs-prior fields    field delta crossing the org's           accept / annotate / correct-at-source;
                                   variance band (band is visible policy,   compare summonable; under-band changes
                                   never hard-coded — 05 §7 discipline)     list collapsed, consume no stops
  5     narrative approvals        drafted sections awaiting approval       approve / edit-in-Composer (return path)
```

Within a class, stops follow package-section order, so the walk reads top-to-bottom through the draft.

### 5.2 Stop lifecycle

| Transition | Trigger | Actor | Audit event |
|---|---|---|---|
| — → open | engine derives the stop from an exception | engine | stop derived (quiet log) |
| open → dispositioned(accepted) | Accept act | human | act record: who/when/subject value state |
| open → dispositioned(annotated) | Annotate act (accept + note) | human | act record + note text |
| open → dispositioned(approved) | Approve act (narrative stops) | human | act record + draft rev approved |
| open → suspended(correcting) | Correct-at-source routing taken | human | routing act + destination |
| suspended → dispositioned / open | return + engine recompute (self-resolves if the exception cleared; else reopens with the new state) | engine | recompute outcome |
| dispositioned → reopened | subject value changed, source doc replaced, or tie-out recomputed dirty | engine | reopen event with reason; prior act kept in history |
| dispositioned → reopened (manual) | typed reopen-with-reason before certify | human | reopen act (append-only) |

All transitions are quiet-logged with actor + evidence refs + loan/period identity. There is no "mark all done": each stop is dispositioned singly, by design.

**Disposition act record shape** (append-only, `src/lib/covenant/review/dispositions.ts`):

```text
DispositionAct {
  actId, stopRef: (loanId, period, pkgRev, seq)
  kind: accept | annotate | approve | accept-with-reason | acknowledge-void | reopen
  subject_value_state: content hash of the figure/section at act time   ← the reopen binding
  who (identity + role-in-which-acting), when
  note? (annotate), reason? (required for tie-out accepts and reopens)
  evidence_refs: the ProvenanceRefs lit when the act was taken          ← what the human saw
}
```

`evidence_refs` makes every disposition auditable as "accepted while looking at exactly this" — the record a lender follow-up or auditor can replay from the sealed period.

### 5.3 The room at each period status

| Period status (03 §2) | The room renders |
|---|---|
| open (holding) | "Nothing to review yet" or partial draft + awaiting-documents banner; stops derive as sections land |
| in-review | The full walk — this brief's main mode |
| ready | All stops dispositioned; readiness true; the strip leads with the enabled CTA; the walk remains browsable |
| certified | Read-only + certified banner + certificate link; any input change → void → back to in-review with stop #0 |
| packaged/sent (sealed) | Read-only forever; sealed banner; traces resolve; entered from Reports |

### 5.4 Surface states (template-complete)

| State | Behavior here | Trigger/actor | Reversible? |
|---|---|---|---|
| empty (no package draft yet) | "Nothing to review yet" object: period status, checklist summary, links to Intake/Composer — never a blank canvas | period open, composition not run | n/a |
| awaiting-documents | Banner over the outline: holding checklist incomplete (n of m), link to Intake/Holding; drafted sections render; missing-input sections render as labeled gaps | checklist gaps (engine) | resolves on arrival/waiver |
| loading/processing | Recompute-on-view skeletons per section; the stop list renders as soon as derived; no figure renders from cache marked fresh | route entry | — |
| partial/incomplete | Some sections drafted, some absent — absent ones are labeled gaps with Composer links; readiness false with reasons | composition in progress | — |
| extracted/unconfirmed | Proposed-class grammar on affected figures; class-3 stop; readiness false | first-period fields pending gate 1 | confirm/correct upstream |
| stale/superseded | Stale badge + diff link on affected figures everywhere they render; their stops REOPEN even if previously dispositioned | source doc replaced (engine flags — 03 §2) | re-confirm clears |
| low confidence | Folded into class-3 stops; confidence rendered beside the extracted link (three-actor grammar) | sub-threshold extraction survives to review | confirm clears |
| conflict | Unresolved extraction/source conflict renders the affected section blocked with the conflict named + owner link (Documents/Extraction); readiness false | conflicting sources | resolve upstream |
| failed tie-out | Class-2 stop; the delta rendered beside both source chains; BLOCKS readiness until dispositioned (02 §2) | engine tie-out | disposition (typed) |
| watch / shortfall / breach | Watch: covenant-strip chip only, no stop. Shortfall/breach: class-1 stop with signed headroom + drafted mitigant beside. Vocabulary law enforced structurally: Bexley 268/301 = 89.04% renders SHORTFALL vs the monitored 90% floor — `breach` unreachable when `basis=monitored` (canon; 05 §3) | engine verdicts | disposition (typed) |
| permission denied | Foreign-org/foreign-client loan: honest 404/403-equivalent page (send-vertical honesty pattern); reviewer role: dispositions disabled with role note | auth scope | — |
| read-only | Reviewer role; sealed periods via Reports — full render, banner states why read-only; every trace still works | role / seal | — |
| blocked/gated | Fail-closed engine stop (missing definition, unknown form): the section renders the gate naming what is missing — never a plausible placeholder (04 §2.5) | engine | authoring upstream |
| certified (void-on-change) | Certified: read-only render + certified banner + link to certificate. ANY input change → VOID: the period returns to in-review **here**, with the void reason as stop #0; dispositions on untouched subjects persist; touched subjects reopen | engine void event | re-walk, re-certify |
| sent/sealed | Read-only forever; every trace still resolves (seal-not-wipe); sealed banner explains inspectability | send record | irreversible by design |
| recovery/undo | A disposition can be superseded pre-certify by typed reopen-with-reason (append-only; the old act stays in history). Post-send: no undo by design — corrections are next-period or correspondence, never edits to the sealed record | human | bounded as stated |

## 6. Engine / Agent / Human / Gate / Quiet Log

| Lane | In the Review Room |
|---|---|
| ENGINE | Derives and orders the stop queue; computes tie-out deltas, verdicts, signed headroom, variance-band crossings, readiness (boolean + linked reasons); recompute-on-view; enforces disposition typing and void-on-change; resolves every trace via the one `traceToEvidence` resolver |
| AGENT | Presents the exception-first walk; auto-lights each stop's evidence; explains deltas ("why this changed") beside changed-field stops; drafts mitigant narratives beside shortfall findings (canon phrasing class: "$240K of NOI from a DSCR shortfall"); proposes dispositions for known-benign tie-out classes — labeled proposals only; answers highlight-to-ask with the selection's provenance as grounding |
| HUMAN | The review itself: dispositions every stop, approves narratives, dispositions tie-outs, acknowledges void reasons, decides to proceed to certify |
| GATE | Failed tie-out and open stops block readiness (disabled-with-reasons on the certify CTA); a `breach` verdict on a covenanted test is never auto-dispositioned (04 §1); review completion is gate 1's completion — the certify and send gates live on their own surfaces |
| QUIET LOG | Every disposition, reopen, annotation, narrative approval, and reviewed-with-zero-exceptions event, with actor + evidence refs + loan/period identity; no push from this surface |

## 7. Information hierarchy

Scan order, top-left to bottom-right:

1. **Frame header (48px):** breadcrumb `Covenant / {Loan} / {Period} / Review` — the loan crumb is a loan switcher scoped to Review, the period crumb a period-switcher popover with status dots (08 §2); deadline chip ("due in {n}d", computed from the due-rule with its provenance, e.g. "Q+45d per §8.02(b)(1)" — evidence-class cite).
2. **Decision/status summary — readiness strip (top-right of the work area):** review progress "n of m stops" + computed readiness reasons, each linking to its blocker + the "Proceed to certify" CTA (disabled-with-reasons).
3. **Primary work region — center canvas:** the package draft, section under review, paper-adjacent rendering, current stop anchor highlighted.
4. **Secondary context — left outline:** the ordered stop walk (grouped by class, state dots) above the full package section list with per-section status (clean / n stops).
5. **Evidence/proof — right Evidence panel:** the lit-row trace with its chain breadcrumb; document artifact rendering.
6. **Actions:** the inline disposition bar on the focused stop; correct-at-source routing; compare summon on changed-field stops.
7. **Activity/history:** collapsed history foot on the outline — this period's review acts (quiet-log projection), prior revs, void events.

**Zero-exception variant:** the hierarchy compresses — the SummaryStop renders where the walk would begin, the section list stands ready below it, and the readiness strip's enabled CTA becomes the second thing the eye lands on. The room still shows the whole package for browsing; it just asks nothing.

**Absent by design:** charts (nothing here out-encodes text — chart doctrine); any editing affordance on package content; generic chat chrome (Ask is highlight-to-ask + the canvas panel only); "mark all reviewed" bulk acts; unread badges (C-8); confirmation dialogs (NN/g ceremony budget — the two heavy ceremonies live on Certificate and Send).

## 8. Page anatomy and regions

The ruled 3-column reconcile interior (KEPT — snapshot §5; frame-mirror spec #528) inside the shell (rail + 48px header + canvas-panel slot, which does not count against the split).

### 8.1 Left — Review outline

- **Purpose:** the walk itself — orientation, order, and progress.
- **Content:** progress header ("n of m dispositioned", CountBadge reuse — the ruled shared atom); stop groups by class with Lucide class icons (severity-only color); each `StopRow`: state dot, one-line subject, evidence-ready glyph; below the stops, the full package section list with per-section status (clean / n stops); collapsed "changed, under band" list; history foot.
- **Persistence:** pinned. **Interaction:** click a stop → canvas scrolls to its anchor + evidence auto-lights; N/P walks; sections click-scroll the canvas.
- **Minimum:** 240px expanded; 48px as labeled icon strip. **Resize/collapse:** collapses to the icon strip below 1728 or on `[` toggle; expands as an overlay over the canvas — never squeezes the canvas below its minimum.
- **Why a pane:** the queue must stay visible while the canvas scrolls — progress and orientation are load-bearing for a walk that must feel finite (R4: GitHub review progress).

### 8.2 Center — Package canvas (work window 1)

- **Purpose:** the artifact under inspection — the claim side of every stop decision.
- **Content:** the draft in lender-form section order, paper-adjacent light document surface — deliberately NOT the certified sheet's blue-violet (those six ruled hexes are reserved for the certified sheet; a draft must read as a draft); every material figure a `<Figure>` with three-actor grammar; change chips on changed-vs-prior fields; the covenant section renders TestResult rows with verdict chips, basis badges, signed headroom; narrative sections labeled "drafted — awaiting approval"; the focused stop's anchor carries the disposition bar docked beneath it.
- **Persistence:** persistent. **Interaction:** scroll, select, highlight-to-ask, click-to-trace.
- **Minimum:** 720px (frame law, work window 1). **Resize/collapse:** never compresses; the other regions collapse first (no-silent-compression law).
- **Why the big pane:** the package is the thing being judged; everything else exists to serve reading it.

### 8.3 Right — Evidence panel

- **Purpose:** proof beside the claim — the receiving side of the lit-row trace.
- **Content:** the chain breadcrumb (figure → formula → inputs → normalized → region), each link clickable to re-light at that level; the document/normalized-row viewer with the persistent lit row (`--accT` tint band + left accent bar — accent family only); artifact label (Original / "Recreated · searchable — derivative") + hash chip; confidence + confirmation state beside extracted links; "view original" artifact toggle preserving the locator.
- **Persistence:** pinned. **Interaction:** breadcrumb hops; scroll locks to the lit region on trace; row-level skeleton keeps the target marked while pages lazy-load (contract §4).
- **Minimum:** 360px. **Resize/collapse:** collapses to a labeled tab below minimums and during compare below 2048; a trace while collapsed summons it as an overlay pane positioned beside — never covering — the clicked figure (lit-row law: proof beside, never a modal).
- **Why a pane:** every stop decision is claim-vs-evidence (05 §4, "Review the composed package" row); a modal would destroy the co-visibility the decision needs.

### 8.4 Top-right — Readiness strip

- **Content:** progress meter + computed readiness reason chips (each: named check, state, deep link) + "Proceed to certify" CTA. Reason set (engine-computed; 05 §5 Package row): sections complete · every test verdicted · tie-out clean · confirmations current · no unmapped lines · checklist complete/waived · all stops dispositioned.
- **Persistence:** persistent, one row (~40px); wraps to two rows below 1440; never hides. **Interaction:** reason chips deep-link to blockers; CTA routes to the certificate route; disabled-with-reasons, never a bare disabled button (R4: GitHub required checks).

### 8.5 Compare (summoned — work window 2)

- **Content:** this package vs the prior sealed package, 50/50 synchronized, aligned by section/field anchors (peer compare per the document-modes law); per-field delta chips; both package identities + hashes in the header; each side's figures traceable.
- **Persistence:** transient — summoned from a changed-field stop (`V`), dismissed by Esc.
- **Minimum:** 560px per side (frame law). **Refusal:** below 2×560 the outline collapses to its strip and Evidence to its tab first; if still short, the prior package opens as a stacked labeled tab with a persistent delta chip — never a squeezed split.
- **Why a split:** "accept this change?" is the one decision here that requires true side-by-side (co-visibility matrix §9); max-2 work windows honored.

### 8.6 Canvas panel (floating) and modals

- **Canvas panel:** Ask, grounded, with the selection's provenance travelling (08 §6); positioned away from the lit region; movable; Esc dismisses. The ruled floating slot — separate from the split count.
- **Modals:** NONE on this surface. Typed acts with legal weight live on Certificate/Send; review dispositions are inline (NN/g ceremony budget, R4).

## 9. Co-visibility matrix

| Datum/surface A | Datum/surface B | Must be simultaneous? | User decision enabled | Default topology | Fallback |
|---|---|---:|---|---|---|
| Package draft figure | Its exact source line (lit) | YES | "Is this number right?" — every stop | center canvas + right Evidence | Evidence overlay pane beside the figure (narrow) |
| Focused stop | Its evidence chain | YES | disposition | stop focus auto-traces | same overlay |
| Stop queue | Draft section under review | YES | orientation + progress | left outline + center | outline drawer over canvas |
| This-period field | Prior-package same field | YES during changed-field disposition | "accept this change?" | 50/50 synchronized compare | inline prior-value chip on the change chip when the split refuses |
| Verdict chip + headroom | Requirement definition text (verbatim, with source region) | on demand | "is this tested per the loan's own definition?" | Evidence chain breadcrumb (definition link) | — |
| Readiness reasons | Blocking items | no | navigate to fix | links from the strip | — |
| Draft narrative | The evidence it cites | YES at approval stop | approve/edit | narrative anchor + Evidence lit on cited figures | — |
| Deadline chip | Escalation state | no | urgency awareness only | header chip; Calendar owns the ladder | — |
| Ask panel | The lit region a question concerns | YES when asking about evidence | grounded question | canvas panel positioned away from the light (08 §6) | panel is movable |
| Review Room | Composer editing | NO — never simultaneous | — | correct routes away with return path | — |

No pane exists merely because information exists: watch verdicts, under-band changes, and clean confirmed figures get no dedicated region — chips and collapsed lists only.

## 10. Layouts and viewport behavior

Topology = shell rail + [outline | canvas | evidence]. Widths in px at each ruled viewport:

| Viewport | Rail | Outline | Canvas | Evidence | Compare mode |
|---|---|---|---|---|---|
| 1440 | 240 | 48 strip (240 overlay) | 792 | 360 | evidence → tab; 2 × 672 |
| 1728 | 240 | 264 | 808 | 416 | outline → strip, evidence → tab; 2 × 720 |
| 2048 | 264 | 288 | 992 | 504 | outline → strip; evidence pinned 416; 2 × 660 |
| <1280 | 48 icon | top drawer | full width | overlay sheet | stacked labeled tabs |
| 1152×720 (minimum) | 48 | drawer | 720 | 384 overlay | stacked labeled tabs |

- **1440:** rail 240 (expanded per frame §9) + outline icon-strip 48 + canvas 792 + evidence 360. The outline expands as an overlay (`[` or hover-pin). If the user collapses the rail (48), the outline may pin expanded at 240: 48 + 240 + 792 + 360 = 1440. Compare mode: evidence collapses to a labeled tab, outline stays strip → two windows of 672 each — ≥560, allowed.
- **1728 (default design target):** rail 240 + outline 264 + canvas 808 + evidence 416. Compare mode: outline → strip 48, evidence → labeled tab → two windows of 720 each.
- **2048:** rail 264 + outline 288 + canvas 992 + evidence 504. Compare mode: outline → strip 48; evidence STAYS pinned at 416 → two windows of 660 each — the only ruled viewport where compare and the lit Evidence panel coexist (each side lights its own, per the lit-row contract §4).
- **Narrow/compact (<1280):** icon rail 48; single work window (canvas); the outline becomes a top drawer with the progress header always visible; Evidence becomes an overlay sheet summoned by any trace (frame §9); compare becomes stacked labeled tabs (This period / Prior package) with a persistent delta chip. Fully operable — dispositions and the walk work at narrow width.
- **Minimum viable:** 1152×720 (frame minimum). At 1152: rail 48 + canvas 720 + evidence overlay 384 summoned over the right edge; outline as drawer. Below 1152×720, the spine-surface "larger window required for review work" state renders (frame §9).
- **Focus behavior:** focusing a stop scrolls its canvas anchor to the upper third and traces its evidence; focus is never stolen by background recompute — a "figures updated" chip appears instead, click to re-render (no silent re-flow under the reviewer's eyes).
- **Compare behavior:** synchronized scroll locked to section/field anchors; unlocking is explicit (a toggle), never accidental.
- **Proof/source behavior:** traces light the Recreated-searchable artifact by default with the "view original" toggle (contract §4); proof always opens beside, never as a modal.
- **Tab stacking/replacement:** collapsed regions become labeled tabs (Evidence, Prior package) on the right edge; selecting one replaces none of the canvas — it overlays or re-pins per its region rules.
- **No silent compression:** every collapse above is to a *labeled* strip/tab/overlay/drawer; nothing squeezes below its minimum.

## 11. Components and exact anatomy

### 11.1 Reused (named repo components)

- **`ReviewSpread` / `Workspace`** (`src/components/covenant/`) — the 3-column chassis; adjusted to mount from the deep route with a `(loanId, period)` resolver (certificate-route pattern) and honest 404/403.
- **Evidence panel** (exists — today the app's only live paper-accent render is its lit trace row, snapshot §4) — re-pointed to the ONE resolver. Parts: chain-breadcrumb header · artifact viewer with persistent lit row · artifact label + hash chip · confidence/confirmation badges · "view original" toggle. Replacing the three ad-hoc per-renderer resolutions with `traceToEvidence` IS the U1-F1 fix (contract §3, item 1).
- **`CountBadge`** (ruled shared atom) — the outline progress header and per-section stop counts.
- **Covenant strip** (pattern shared with Loan Detail) — TestResult rows: verdict chip (pass/watch/shortfall/breach, severity-only color) + basis badge (covenanted/monitored rendered distinctly, C-9) + signed headroom in both denominations (mono).
- **`AskPanel` + `HighlightToAsk`** (exist; grounded per the agent brief) — selection travels with provenance; the panel positions away from the lit region.
- **`CommandPalette`** (exists) — place/object rows for this loan's periods; computed-answer rows carry lit-row links (08 §5).

### 11.2 NEW components

- **`ReviewOutline`** (`src/components/covenant/review/ReviewOutline.tsx`) — parts: progress header · class groups · `StopRow` (state dot, subject line, evidence-ready glyph) · section list rows (name, status, count) · under-band collapsed list · history foot. Uniform 36px rows.
- **`StopCard` + `DispositionBar`** (`review/StopCard.tsx`) — docked under the focused stop's canvas anchor. Parts: subject restated · engine facts (delta, verdict, headroom — Geist Mono, tabular slashed-zero) · agent explanation/draft (labeled as agent work) · action set by class: **Accept** · **Correct at source** (routing picker naming the owning surface + the return promise) · **Annotate** (accept-with-note) · **Approve** / **Edit in Composer** (narrative stops) · **Accept with reason** (tie-outs; reason required — GitHub dismissal-comment semantics, R4).

  Per-class card content (implementation-ready):

  | Class | Card body | Evidence auto-lit |
  |---|---|---|
  | 1 finding | Verdict chip + basis badge · signed headroom in both denominations · the requirement's definition citation (verbatim + source region link) · drafted mitigant (labeled, approve/edit) | TestResult input rows (the metric's normalized lines) |
  | 2 tie-out | This-period value vs prior-package value · the delta (mono, signed) · both chains as breadcrumb pairs · agent's benign-class proposal when applicable (labeled) | this period's source chain; the prior chain lights via the compare summon (each side its own — contract §4, honoring single-light outside compare) |
  | 3 stale confirmation | The confirmed value · what replaced its source (doc + version + date) · the diff link · original confirmer stamp | the new source region, with the old region reachable via the version chain |
  | 4 changed field | Prior value → current value · band context ("crossed the org's variance band" with the band named as policy, value visible) · agent "why this changed" (labeled) · compare summon | the field's current source line |
  | 5 narrative | The draft with interpolated engine figures rendered as live refs · edit-routes-to-Composer note | every figure the draft cites, lit in citation order on focus |
  | Void (#0) | What changed, when, by whom/what · the voided certification's identity (who/when/hash) · reopened-stop list | the changed input's source line |
  | Summary (clean) | The three claims (nothing changed beyond expected · tie-out clean · confirmations current) + expandable counts | none until a count is expanded |
- **`Figure`** (`src/components/covenant/provenance/Figure.tsx` — the app-wide contract, NEW) — wraps every material numeral; three-actor grammar; click/Enter → `traceToEvidence(ref)`; "no source" affordance on legacy fixture values (contract §5).
- **`ReadinessStrip`** (`review/ReadinessStrip.tsx`) — parts: "n of m stops" meter · reason chips (named check + state + deep link) · **Proceed to certify** CTA (accent family; disabled-with-reasons).
- **`PeerCompare`** (`review/PeerCompare.tsx`) — parts: dual headers (package identity + hash each side) · synchronized panes · per-field delta chips · sync-lock toggle.
- **`VoidStop`** — the void reason (what changed, when, by whom/what) · acknowledge act · list of reopened stops with links.
- **`SummaryStop`** (zero-exception path) — "Nothing changed beyond expected · tie-out clean · confirmations current" · counts of what was checked (n figures tied, n confirmations current, n tests passed), each count expandable to its evidence list — the fast path is auditable, not a shrug.
- **Empty/error/recovery objects** (shared with the other spine surfaces) — "Nothing to review yet" card · blocked-section gate card (names exactly what is missing) · honest 404/403 pages.

### 11.3 Engine-side modules (NEW, `src/lib/covenant/review/`)

- **`stops.ts`** — pure stop derivation + ordering law (test-first; the ordering table in §5.1 is the spec).
- **`dispositions.ts`** — typed act records, append-only; value-state binding for reopen semantics.
- **`traceToEvidence`** (`src/lib/covenant/provenance.ts`, shared) — the one resolver.

## 12. Interaction specification

- **Selection:** single stop focus (outline or canvas anchor); figure selection for trace; text selection for highlight-to-ask.
- **Hover:** figures show the provenance underline + class tooltip; confirmed values show the confirmer stamp ("confirmed {date} by {who}"); stop rows show their disposition summary; change chips show the prior value.
- **Focus:** visible focus ring (ruled tokens) on stops, figures, disposition actions, breadcrumb switchers; focus order = outline → canvas → evidence → strip.

**Keyboard map:**

| Key | Action |
|---|---|
| `N` / `P` (also `⌘↓` / `⌘↑`) | next / previous stop — lands with evidence auto-lit; `N` skips dispositioned stops (Rossum Enter-to-next-unvalidated discipline, R2) |
| `A` | accept the focused stop |
| `Shift+A` | annotate (accept with note — note field focused) |
| `C` | correct at source — opens the routing picker |
| `Enter` | on a stop: open the disposition bar · on a `<Figure>`: trace |
| `V` | summon compare (changed-field stops) |
| `E` | focus the Evidence panel (arrow keys walk the chain breadcrumb) |
| `Esc` | one layer per press: dismiss trace → exit compare → close overlays/drawers; never navigates while a layer is open |
| `[` | toggle the outline strip/expanded |
| `⌘K` / G-chords | palette / places per 08 §5 (`G V` Review in loan context) |

- **Editing and validation:** NONE on package content — the room is read-only over the draft by design (R3: Puzzle adaptation — the drill target is read-only during review; edits happen upstream in mapping/composition). The only text inputs: annotation notes and the required tie-out reason. Empty reason blocks the act with an inline message, not a dialog.
- **Bulk action:** none. Stops are dispositioned one at a time by design — bulk-accept is the rubber-stamp anti-pattern this surface exists to kill.
- **Undo/recovery:** reopen-with-reason supersedes a disposition pre-certify (append-only); Esc never discards typed annotation text without an explicit discard affordance.
- **Sorting/filtering:** stop order is engine law — not user-sortable (the one deliberate rigidity); the section list filters canvas scroll only; the under-band list expands on demand.
- **Drill-down and return path:** correct-at-source navigates with `?return=/covenant/{loanId}/{period}/review#stop-{seq}`; the owning surface pins a "Return to review" affordance; on return the engine recomputes and the stop re-evaluates (self-resolves if cleared, else reopens with the new state).
- **Source-linked selection (the lit-row behavior):** click/Enter on any `<Figure>` → its exact source line lights in Evidence and STAYS lit until dismissed or the next trace; the lit target derives only from `ref.chain` — never row index, render order, or heuristic text match (contract §2.4, the U1-F1 killer); focusing a stop auto-traces its `evidence_ref`; in compare mode each side lights its own (contract §4).
- **Save/persistence:** dispositions persist immediately as typed acts — no draft state, no save button; annotation text commits with the act.
- **Collaboration/commenting:** annotations are visible to all roles on the period and project into the quiet log; resolve semantics per GitHub conversations (R4) — a reopened stop shows its prior disposition history, never erased; the PMC preparer's dispositions are visible to the owner as "dispositioned by {preparer}" before the owner's own pass.

## 13. Visual craft direction

- **Typography roles and sizes:** section titles and stop subjects in the UI face (semibold 14–15px); all financial figures in Geist Mono with `tabular-nums slashed-zero` (ruled setting), 13–14px; canvas body ~14px; outline rows 13px; strip chips 12px; evidence chain breadcrumb 12px.
- **Financial-number treatment:** mono, tabular, slashed-zero, right-aligned in any columnar context; signed headroom always shows sign and both denominations ("+0.96 pp · −$—" per the engine's dual denomination, 05 §3).
- **The draft canvas is not the certified sheet:** paper-adjacent light document surface on the gray ladder's lightest rungs — deliberately NOT the certified sheet's blue-violet (#E7EBF8 family); those six ruled hexes are reserved for the certified sheet so "certified" stays visually unmistakable. The sealed read-only render likewise links to the certificate rather than borrowing its paper.
- **Spacing rhythm:** 8px base grid; 24px section padding on the canvas; uniform outline rows 36px (uniform-row law); disposition bar 48px.
- **Density:** the outline and evidence are dense working instruments; the canvas breathes like a document — the contrast is intentional (queue and proof feel like tools, the draft feels like the artifact under inspection).
- **Open ground vs earned boundaries:** open, not boxed — columns separated by hairline dividers on the ten-rung gray ladder (adopted 2026-08-07), zero local grays (readback-checked, per the oklch caution); the lit evidence row is the strongest painted moment in the room, by design.
- **Semantic color (ruled roles only):** accent family #7189FF (hover #8EA1FF, active #6078F4, tint #A9B5FF) for the lit-row band + left accent bar, focused-stop indication, the enabled certify CTA, and trace affordances; verdict chips severity-only (chart doctrine); change chips sit on gray-ladder ground with mono deltas — severity color only when a change crosses the band into a stop. Zero new colors.
- **Focus/selected/hover states:** ruled selected-state tokens (Ruling-J boundary: Covenant's own control layer, already ported); focus ring visible on every instrument; hover never carries meaning that focus lacks.
- **Chart style:** no charts on this surface (nothing here would out-encode adjacent text).
- **Motion and reduced motion:** scroll-to-anchor and trace-light are the only animated moments — 150–200ms ease; `prefers-reduced-motion` replaces both with instant positioning + the persistent highlight (the lit state, not the animation, carries the meaning).
- **Long-session ergonomics:** stable pane geometry between loans (the room never re-arranges itself); progress persists per period; the N-walk keeps hands on the keyboard for an entire portfolio pass; the deadline chip stays quiet until the escalation policy says otherwise.

## 14. Benchmark research and synthesis

| Product | Limited role | Official evidence | Exact pattern to take | Covenant adaptation | What to reject | Why this reference beats alternatives |
|---|---|---|---|---|---|---|
| GitHub PR review + branch protection | Review progress, resolve semantics, readiness, void-on-change | docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches · docs.github.com/en/pull-requests/reference/status-checks (read from the github/docs source repo, R4) | Required checks as named, linked requirements gating the act; approval recorded against a content state and dismissed as stale when it changes; dismissal requires a typed reason; resolve-conversation keeps history | Readiness strip = required checks; disposition-binds-to-value-state = stale reopen; tie-out accept-with-reason = the dismissal comment | Bypass lists and admin exemptions — the gate binds everyone | The only mass-proven "review a composed artifact, then a gated act" loop; stale-dismissal maps 1:1 onto void-on-change |
| DataSnipper | Figure⇄source-region provenance | knowledge.datasnipper.com/how-to-validate-data-on-a-document · knowledge.datasnipper.com/what-do-the-different-colours-of-snip-mean (R3) | Value and provenance as ONE object; selecting a figure shows the exact source location; validation-V/exception-X marks; per-document snip overview | The lit-row trace generalized to structured rows via the one resolver; V/X becomes per-line tie-out marks | Excel as host; free-form snipping (traces stay structured to normalized lines) | The audit profession's proven figure-to-evidence gesture — exactly the center-right contract |
| Puzzle | Statement-line drilldown | help.puzzle.io/en/articles/11385709-financial-statements-in-puzzle (R3) | Every statement figure is a live door to its filtered underlying records | The drill lands in the read-only Evidence panel during review; edits happen upstream | Drilling into an editable surface from the review context | Cleanest single-path figure→records contract on a fixed COA spine — Covenant's exact data shape |
| Sigma | Aggregate + underlying records | help.sigmacomputing.com/docs/drill-into-data · help.sigmacomputing.com/docs/view-underlying-data (R3) | Underlying records render at the exact granularity that fed the aggregate; drill is view state, not mutation | One fixed chain (metric → inputs → normalized → source), never ad-hoc re-slicing | Free-form exploration beside the certify gate | Proves drill-state ergonomics without data mutation — the room's safety posture |
| Numeric | Exception-first close | numeric.io/product/close · numeric.io/product/reconcile (R3) | Autosubmit-when-matched: the clean case consumes zero attention; the exception view holds only the residue; materiality thresholds decide what demands explanation | Confirmed clean figures consume zero stops; variance bands gate class-4 stops; bands are visible policy | AI-drafted explanations as default package content | The strongest statement of the exception-first law this surface is built on |
| FloQast | Tie-out gating + sign-off drift | floqast.com/blog/strict-tie-out-mode (R3) | Sign-off impossible until tied out; a post-sign-off data change notifies signers and voids signatures until re-tie-out; completeness check (nothing unmapped escapes) | Failed tie-out blocks readiness; stale reopens stops; "no unmapped lines" as a readiness reason | Workbook-in-a-cloud-folder as the evidence substrate | Certification-as-claim-about-current-data, proven in month-end close — the void-on-change ancestor |
| Modern Treasury | Expected-vs-actual state grammar | docs.moderntreasury.com/reconciliation/docs/overview (R3) | Two objects joined by an enum; humans process only the unmatched residue; manual matches recorded the same as automatic ones | Tie-out states; dispositions recorded identically whether agent-proposed or human-initiated | Payment-grade continuous streaming (Covenant matches at ingest) | The typed-state discipline that keeps the stop queue honest |
| Vanta | Graded urgency + freshness | help.vanta.com/en/articles/11345529-automated-test-evidence (R3) | Watch-before-failure grading; evidence freshness as a first-class state driving test state | Watch is informational (no stop); staleness is a stop class | Framework/control ontology indirection | Freshness-drives-state is exactly the stale-confirmation stop's mechanic |
| Hebbia | Citation click-through | hebbia.com/blog/introducing-matrix-the-interface-to-agi (R2) | Every output cell citation-linked to the exact page location; peek preview before jumping | Citation-first posture for agent explanations and narrative drafts — every quoted figure carries a lit-row ref | Grid-of-questions framing | Citation-as-default is the hallucination control the narrative stops need |
| Rossum | Keyboard-driven validation | knowledge-base.rossum.ai/docs/keyboard-shortcuts (R2) | Enter advances to the next field requiring validation; keyboard-first review flow | `N` skips dispositioned stops; the whole walk is hands-on-keyboard | Threshold auto-export past humans | The proof that a review queue can be a flow, not a form — the 60-second target's mechanics |
| NN/g (anti-pattern) | Ceremony budget | nngroup.com/articles/confirmation-dialog/ (R4) | Confirmation overuse breeds reflex clicking; typed acts reserved for rare, dangerous moments | ZERO dialogs in the review room; inline dispositions; the two heavy ceremonies stay on Certificate/Send | "Are you sure?" anywhere here | Names the failure mode that would kill a 60-second review |
| BankStride/nCino (anti-pattern) | Covenant-as-tickler | bankstride.com/tickler-tracking-management (R3) | Nothing structural | n/a | Review as document-receipt checking: due-date + upload chute, opaque numbers, no provenance | The void this room fills — reviewing computed, traceable figures instead of confirming a PDF arrived |

**Synthesis.** The Review Room composes four proven mechanics into one borrower-side original: (1) Numeric/Modern Treasury's exception-first law makes the walk short — clean confirmed figures are invisible, so the queue is only judgment; (2) DataSnipper × Puzzle/Sigma's figure-to-source gesture, generalized through the one-resolver lit-row contract, puts proof beside every claim without leaving the room; (3) GitHub's review-progress / required-checks / stale-dismissal loop gives the walk its progress meter, its computed readiness, and its void-on-change honesty; (4) Rossum's keyboard flow makes the whole thing a ~60-second N-walk. No benchmark reviews a *lender package against its own loan documents with deterministic verdicts and a structural monitored-vs-covenanted vocabulary* — that composition is what makes the result original and domain-correct for borrower-side lender reporting.

## 15. Domain references

Terminology, expected data, and workflow semantics only: **Finley** (docs.finleycms.com — covenants as computed objects with thresholds; deliverables as dated first-class objects; readiness gates reading computed compliance), **Setpoint** (setpoint.io — certify-by-recompute; the computed-vs-stated diff as the core artifact), **Numeric/FloQast** (month-end-close vocabulary: tie-out, flux, sign-off, completeness), **BankStride/nCino** (the tickler anti-model — what covenant "tracking" degrades into without computation). Domain authority does not equal visual authority: none of these products' layouts, chrome, or styling govern anything here. Covenant semantics — test definitions, thresholds, cadences, verdict vocabulary — come from each loan's own documents and Terry's rulings (domain-content firewall, 05 §1), never from any referenced product.

## 16. Accessibility, performance, and safety

- **WCAG contrast and focus:** all text on the gray ladder meets AA on its ground; verdict chips carry text labels, never color alone; the lit-row band pairs the `--accT` tint with the left accent bar and programmatic `aria-current` so the trace is never color-only; visible focus ring on every instrument.
- **Keyboard completeness:** the entire review — entry, walk, trace, disposition, compare, certify CTA — operates without a pointer (§12; acceptance T4).
- **Screen-reader semantics:** the outline as `nav` with a stop list (state announced per row); canvas sections as landmarks; a trace announces "evidence: {document}, {location}, lit" and moves focus to the lit row on Enter, returning on Esc (contract §6.4); disposition acts announce their recorded result; the readiness strip is a `status` region announcing changes politely.
- **Table virtualization/large data:** the Evidence panel virtualizes long statements (the ~150-row T-12 renders windowed); the outline virtualizes beyond ~50 stops; document pages lazy-load with the target region skeleton-marked (contract §4).
- **Loading and latency feedback:** trace-to-light under 200ms with the document already open (contract §6.6); recompute-on-view renders per-section skeletons; no figure ever renders stale without its stale badge.
- **Destructive action confirmation:** nothing destructive exists on this surface; dispositions are append-only and supersedable pre-certify via typed reopen.
- **Certify and external-send safety:** structurally elsewhere — this room can only *enable* the CTA via computed readiness; no API path from review mutates certification or send state; approved-bytes and the typed ceremonies live on Certificate/Send; void-on-change returns the period here loudly (never silent re-certification).
- **Source immutability:** the Evidence panel renders artifacts read-only; Original bytes immutable with hash visible; derivatives always labeled ("Recreated · searchable — derivative").
- **Auditability:** every disposition, reopen, annotation, approval, and void acknowledgment is an identity-stamped append-only act with evidence refs, addressable forever inside the sealed period (seal-not-wipe).

## 17. Acceptance tests and fixtures

**Fixtures.**
- `FIX-CAL-FYE18` — the Calloway Park FYE-2018 evidence spine: T-12 with Total OpEx $1,686,050, NOI $1,218,877, Net Income $460,159; the three real tie-outs: cash-flow Net Income = T-12 Net Income · interest paid $644,017 = T-12 debt-service total · period-end cash = balance-sheet cash (evidence).
- `FIX-BEX-Q2` — Bexley canon: occupancy 268/301 = 89.04% vs the monitored 90% floor (SHORTFALL), UPB $15,232,500, Fannie 6001.NR reporting-only (canon).
- `FIX-SHUFFLE` — FIX-CAL-FYE18 with normalized rows re-ordered vs extraction order (the U1-F1 killer, contract §2.4).
- `FIX-CLEAN` — a Bexley period with zero exceptions.
- `FIX-VOID` — FIX-BEX-Q2 certified, then one T-12 line corrected upstream.

1. **T1 — Stop ordering law.** Seed FIX-BEX-Q2 with one shortfall finding, one failed tie-out, one stale confirmation, two band-crossing changes, one unapproved narrative → the outline renders exactly that order (classes 1→5), 6 stops; watch verdicts and under-band changes create zero stops; within-class order follows package-section order.
2. **T2 — Zero-exception fast path.** FIX-CLEAN → exactly one SummaryStop + readiness true + enabled certify CTA; each summary count expands to its evidence list; a timed keyboard pass (enter → read summary → verify strip → CTA) completes under 60 seconds.
3. **T3 — Lit-row correctness (U1-F1 regression).** On FIX-SHUFFLE, click the draft NOI $1,218,877 → the Evidence panel lights the T-12's exact NOI row (not the row at its pre-shuffle index) and stays lit; repeat for every figure through all three former renderers; zero wrong-row lights.
4. **T4 — Keyboard-only walk.** FIX-BEX-Q2, no pointer: `N` through all stops (evidence auto-lights each), accept/annotate/approve each, Esc dismisses traces one layer at a time, the CTA is reached and activated — full pass with focus visibly tracked throughout.
5. **T5 — Tie-out blocks readiness.** Break one FIX-CAL-FYE18 tie-out (Net Income mismatch) → class-2 stop with the delta beside both source chains; the readiness strip lists "tie-out clean: failing" linking to the stop; CTA disabled; accept-with-reason requires a non-empty reason and unblocks.
6. **T6 — Correct-at-source return path.** From a changed-field stop, choose Correct → lands on Actuals with the pinned return affordance; correct the mapping; return → the engine recomputed, the stop self-resolved, progress updated; the routing and resolution both appear in the quiet log.
7. **T7 — Compare fidelity.** Summon compare from a changed-field stop → 50/50 synchronized panes aligned on the field's anchor, both package identities + hashes in the header; at 1440/1728 the Evidence panel is a labeled tab (never a squeezed pane); at 2048 it stays pinned and each side lights its own.
8. **T8 — Void return.** FIX-VOID → the period returns to in-review; stop #0 is the void reason naming what changed, when, by whom/what; only stops touching the changed value reopen; untouched dispositions persist with history intact; acknowledge is a typed act.
9. **T9 — Stale reopens.** Replace a source document feeding a dispositioned figure → its stop reopens with reason; the figure wears the stale badge with diff link everywhere it renders (03 §2).
10. **T10 — Vocabulary law.** FIX-BEX-Q2 renders SHORTFALL with the monitored basis badge; a structural renderer test asserts the string "breach" is unreachable in any render where `basis_echo=monitored` (05 §3).
11. **T11 — Readiness honesty.** Every readiness reason chip deep-links to its blocking item; the CTA is never enabled while any reason fails; no bare disabled button — each unmet check is named (R4: required checks).
12. **T12 — Viewport/no-silent-compression.** At 1440/1728/2048/1152×720 fixtures: pane widths per §10; every collapse is a labeled strip/tab/overlay/drawer; the canvas never drops below 720px; below 1152×720 the "larger window" state renders; registers elsewhere remain usable.
13. **T13 — Accessibility.** Axe-clean; the trace announces and moves focus per §16; verdict chips pass color-independence; reduced-motion swaps animation for instant positioning with the persistent highlight intact.
14. **T14 — Sealed read-only.** Open FIX-BEX-Q2 sealed via Reports → full render, sealed banner explaining inspectability, dispositions disabled with reason, every trace still resolves (seal-not-wipe).
15. **T15 — Benchmark challenger review.** A reviewer walks GitHub PR review, DataSnipper validation, and Numeric's exception view against this build and files any mechanic where the benchmark's version is stronger; ADJUST items ticketed before the gate below closes.
16. **T16 — Exception-first law.** On FIX-CAL-FYE18 with all confirmations current and all tie-outs clean, assert the stop count is exactly the exception count: every confirmed clean figure (all ~150 T-12-derived lines) generates zero stops and zero outline rows outside the section list.
17. **T17 — Trace latency.** With the T-12 evidence document open, trace-to-light completes under 200ms for any of its rows (contract §6.6); with the document cold, the target region renders skeleton-marked while pages load.
18. **T18 — Your-move consistency.** The open-stop count for FIX-BEX-Q2 rendered on Home, Inbox, and this room's progress header is one computed query returning one number (03 §4; frame test 6) — disposition here decrements all three without refresh skew.
19. **T19 — PMC role scope.** As a PMC preparer scoped to another client: honest 404/403 page. Scoped correctly: dispositions permitted, and where the engagement reserves certify to the owner, the CTA renders "ready — awaiting {owner} to certify" instead of enabling (02 §4).
20. **T20 — Disposition-binding integrity.** Accept a changed-field stop, then bump the package rev without touching that field → the disposition persists with a "carried" stamp; then change the field's value → the stop reopens citing the value-state mismatch, and the original act remains in history (data-integrity fixture on `subject_value_state`).

## 18. Build plan

- **Dependencies (named):** provenance contract adoption item 1 — the U1-F1 fix is this surface's first PR (`cross-cutting/provenance-lit-row-trace.md` §3); F2 persistence (periods, packages, act records — dispositions need real rows; tenancy scoping for the 404/403 honesty); the orchestration seam V1 ("first computed period on Review" — 05 §6, gap 6); the findings service (agent brief) for class-1 stops; the tie-out engine (built, green — needs wiring); variance-band policy config (Settings → Data).
- **Foundation work:** the deep route `/covenant/[loanId]/[period]/review` with resolver + honest 404/403 (clone the certificate-route pattern); `stops.ts` (pure, test-first — the §5.1 ordering table is the spec); `dispositions.ts` append-only act records with value-state binding.
- **Components to build first:** `<Figure>` + `traceToEvidence` (everything hangs on them) → `ReviewOutline` + `StopCard`/`DispositionBar` → `ReadinessStrip` → `SummaryStop`/`VoidStop` → `PeerCompare` last (it depends on sealed prior packages existing in persistence).
- **PR-sized work packages, in order:**
  1. `traceToEvidence` + `<Figure>` + the three-renderer re-point (the U1-F1 fix; FIX-SHUFFLE tests in the same PR — contract §3 item 1).
  2. Deep route + resolver + 404/403 + breadcrumb switchers (T12 partial, T19).
  3. `stops.ts` + `ReviewOutline` over engine-derived stops (T1, T16).
  4. `StopCard`/`DispositionBar` + `dispositions.ts` (T4, T6, T20).
  5. `ReadinessStrip` wired to engine readiness (T5, T11); `SummaryStop` (T2).
  6. Tie-out wiring + FIX-CAL-FYE18 seeded prior package (T5, T17).
  7. `PeerCompare` + viewport collapse rules (T7, T12).
  8. `VoidStop` + void-return flow (T8, T9); sealed read-only mode (T14).
- **Vertical slice (the send-vertical pattern):** one route, one loan+period — Bexley Q2 from engine data end to end: computed verdicts, a real tie-out against a seeded prior package, derived stops, persisted dispositions, computed readiness enabling the real certificate route. This slice is the product's center of gravity — the first time the ~60-second review runs on real computation instead of demo strings.
- **Migration from fixture data:** `book.ts` figures migrate to engine reads behind the `<Figure>` wrapper; unmigrated values render the "no source" affordance and count toward the CI fixture-burndown (contract §5) — the room never fakes provenance during the transition.
- **Rollout/feature flag:** `covenant-review-stops` gates the stop walk over the existing spread; the U1-F1 resolver fix ships ungated (it is a defect repair, not a feature); compare and the void-return flow follow in a second flag stage.
- **Proof artifacts required:** the FIX-SHUFFLE pass across all three former renderers (screenshots + test run); a timed screen recording of the T2 60-second clean pass and a T4 keyboard-only pass; token readback check (zero local grays, accent family only — the oklch caution); the T15 challenger-review memo.
- **Final gate:** `ADJUST` confirmed — the ruled 3-column spine stands; ship when T1–T15 pass on the vertical slice and the wrong-row defect is dead everywhere.
