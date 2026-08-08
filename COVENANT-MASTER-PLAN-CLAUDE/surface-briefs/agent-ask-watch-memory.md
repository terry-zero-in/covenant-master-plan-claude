# AGENT LAYER — ASK · PROACTIVE WATCH · ASK-ONCE MEMORY · OUTCOME LEARNING · QUIET LOG · CAPABILITY PROMPTS

## 1. Identity and verdict

- **Product layer:** agent (inventory rows D1–D6, 07 §1D). This is the one brief for the whole agent presence: the Ask surface, the findings service (proactive watch), the ask-once memory store, the outcome-learning loop, the quiet log, and the rotating capability prompts. Agent presence is contextual, not chrome (04 §2.4): the layer has almost no routes of its own — it appears inside other surfaces' anatomy.
- **Route/address in basis-v2:**
  - Ask: no route — the canvas-panel slot of `CovenantShell` (08 §4, §6), summonable everywhere; components `src/components/covenant/AskPanel.tsx` + `HighlightToAsk.tsx` (exist, scripted — snapshot §3).
  - Findings: surfaced on `/home` region 3, `/loans/[loanId]` (Overview covenant strip + Findings), and as review stops in `/covenant/[loanId]/[period]/review` when blocking; service NEW at `src/lib/covenant/agent/findings.ts`.
  - Memory: inline chips at every point of use; central inspector at `/settings/agent`; store NEW (`memory_entries` migration + `src/lib/covenant/agent/memory.ts`).
  - Learning: capture hooks inside Composer/Correspondence editors; NEW `src/lib/covenant/agent/corrections.ts`.
  - Quiet log: one `ActivityEvent` store (NEW `activity_events`, append-only) rendered as filtered views on Home §4, Loan Detail ACTIVITY tab, period headers; run detail is a summonable panel, not a route.
- **Current build state:** D1 FIXTURE (AskPanel carries one scripted golden answer — snapshot §3); D2 ABSENT (engine emits status + signed headroom, nothing frames it — snapshot gap 8); D3 ABSENT; D4 ABSENT; D5 PARTIAL (activity feeds exist as contradictory fixtures — U3-F2); D6 FIXTURE. Substrate that exists unwired: analyst-grounding library, intake classifier, trigger library as a doc (`docs/covenant/PROACTIVE_AGENT_TRIGGER_LIBRARY.md`).
- **Existing-surface verdict:** ADJUST the scaffold (AskPanel/HighlightToAsk keep their anatomy and gain a real grounded service); the four services behind it are NEW builds.
- **Research tier:** C (R6 in full: Inngest, LangSmith, Temporal, Trigger.dev, ChatGPT memory, Digits, anti-pattern Copilot).
- **Primary users/roles:** owner (all capabilities), PMC preparer (client-scoped), reviewer (read-only asks; no dispositions, no memory corrections).
- **Frequency/session:** Ask is intermittent and bursty (a question mid-review); findings concentrate at weeks 2–3 of the cycle (03 §4); the quiet log is read weekly ("what did the analyst do while I was away"); memory is invisible until inspected.
- **Error cost:** terminal — a hallucinated figure quoted by the agent, repeated by the borrower into a lender conversation or pasted into a narrative, is the same class of harm as a wrong certified number. Hence the grounding contract and the numeral lint (§4 below) are the load-bearing walls of this brief.
- **Success criterion:** every agent-rendered numeral traces to an engine value; zero re-asked remembered questions across consecutive periods; repeat-error rate per correction class trends down; zero push notifications for completed routine work; the borrower describes the agent as "the analyst who prepared this," never as "the chatbot."

## 2. User job and decisions

- **Primary job-to-be-done:** trust the preparation. The agent layer exists so the ~60-second review is believable: the borrower can interrogate any figure (Ask), be warned before a covenant problem matures (Watch), never answer the same question twice (Memory), watch the agent get measurably better (Learning), and audit everything it did without being interrupted by any of it (Quiet log).
- **Decisions made here:** disposition a finding (acknowledge / accept residual risk with typed reason / route to the owning surface / dismiss with reason); correct a remembered answer (versioned); approve nothing — approvals of drafts happen on the surfaces that own the drafts (Composer, Correspondence).
- **Questions the surface must answer in scan order:** Is anything drifting toward a covenant problem? (findings) → Why is this number what it is? (Ask + lit-row) → What has the agent already handled? (quiet log) → What does it remember about my book, and is it right? (memory inspector).
- **What the user should not have to decide here:** whether a figure is trustworthy (provenance is structural); what the agent may do autonomously (the five-lane law decides); when to be notified (policy in Settings, one push class).
- **The watch's rule classes (the trigger-library concept, framed without inventing domain content):** the findings service evaluates rules of exactly three shapes over engine outputs, each rule referencing the loan's own confirmed RequirementRecords (never a global default):
  1. **Headroom trend rules** — signed headroom (dual-denominated, 05 §3) shrinking across N periods toward a threshold ("watch-band approach"); the canon instance: DSCR headroom framed as "$240K of NOI from a DSCR shortfall".
  2. **Watch-band entry rules** — a TestResult entering its authored watch band (authoring default floor × 1.05, [VERIFY against library; never hard-code] — 05 §1.2); verdict flips pass → watch are findings-grade, not push-grade.
  3. **Checklist-risk rules** — required deliverables missing with deadline proximity crossing the escalation ladder's your-move rung (03 §5); these findings link the holding checklist, they do not duplicate it (Intake owns the checklist).
  A rule fires at most once per (trigger, loan, period, inputs-hash) — recomputation with identical inputs re-raises nothing; changed inputs raise a new finding linked to the prior one.
- **Entry paths:** highlight any text/figure → "Ask about this" (08 §6); palette rows routed as "Ask the analyst: …" (08 §5); Home §3 finding cards; Loan Detail Findings; review stops for blocking findings; Settings → Agent for memory; ACTIVITY tabs for the log.
- **Exit paths:** every Ask citation exits to the owning surface with the source row lit; finding dispositions exit to Composer (adopt narrative) or Actuals/Extraction (fix the input); memory chips exit to the inspector; quiet-log rows exit to the object they touched (arrival, document, period).
- **Completion/advancement conditions:** the agent layer never advances a period by itself. A blocking finding dispositioned unblocks readiness (via the Review Room stop it owns); everything else here is read, explain, remember, log.

## 3. Object and ownership model

| Object/action/status | Owned here? | Summary/link elsewhere | Address/identity | Notes |
|---|---:|---|---|---|
| AskExchange (question, grounding set, answer, citations) | yes (transient, session-scoped) | Each exchange writes one ActivityEvent | canvas panel; no route | No persistent chat history surface — deliberate (anti-chatbox, R6 anti-pattern) |
| Finding | yes (02 §2) | Home §3 cards; Loan Detail; review stop when blocking | `findingId` | trigger ref + computed evidence + drafted narrative ref + state |
| Finding disposition (typed act) | yes | Quiet-logged; blocking dispositions echo in Review Room | on the Finding | acknowledge / accept-residual-risk / route / dismiss-with-reason |
| Drafted mitigant narrative | yes, while a draft attached to its Finding | Adopting it into a package makes it a PackageSection — Composer's object thereafter | narrative ref on Finding | The agent drafts; only Composer's approval flow ships it |
| MemoryEntry (incl. COA-mapping specialization) | yes — ONE store (02 §3) | Inline chip beside every field it fills; Loan/Lender detail summaries | `memId`, scope: borrower / loan / lender / property-PMS | Inspected centrally at `/settings/agent` and at every point of use |
| CorrectionEvent (edit-event capture) | yes | Repeat-error metric renders in `/settings/agent` | `correctionId` → anchored step ref | LangSmith-shaped: anchored to the exact step + should-have-been (R6) |
| ActivityEvent (quiet log) | yes — one append-only store | Home §4, Loan Detail ACTIVITY, period headers are filtered views | `eventId` | Kills U3-F2 structurally: feeds cannot disagree because there is one store (07 §2) |
| Capability prompts | yes (rotation logic) | Rendered in AskPanel empty state | static + computed set | Engine-completable only (locked law) |
| Metrics, TestResults, headroom | NO — engine, owned by Actuals (02 §3) | Agent quotes them with provenance | — | The agent never computes a shipping number |
| Certify / send acts | NO — never | — | — | Restated in §6; structural |

**No-double-homing boundary:** the agent layer owns Findings, MemoryEntries, CorrectionEvents, ActivityEvents, and the Ask interaction. Every number it utters is owned elsewhere (engine/Documents) and only quoted with a ref. Home/Inbox count findings but do not own them; Settings hosts the memory inspector but the store is the agent layer's single store; Intake owns the undo-window on auto-filed arrivals even though the auto-file event renders in this quiet log.

## 4. Data and semantic model

- **Source facts:** filed documents (Documents canon, four artifacts per §09 law) — quotable by Ask with docId + version + region.
- **Extracted values awaiting confirmation:** NOT grounding. Ask may name their existence and state ("a proposed T-12 extraction is awaiting confirmation") but never quotes a proposed value as an answer figure.
- **Confirmed values:** ExtractedValues (state=confirmed), confirmed RequirementRecords, MemoryEntries — grounding class 4.
- **Deterministic outputs:** Metric, TestResult (verdict + signed dual-denomination headroom, `basis_echo` — 05 §3), tie-outs, readiness, deadlines — grounding class 1, the primary one.
- **Agent proposals/drafts:** finding narratives, chase drafts, reply drafts — always labeled drafts, figures interpolated by reference (04 §2.1).
- **Human decisions:** finding dispositions, memory corrections — typed, versioned, quiet-logged.
- **Certified values:** sealed packages, CertificationRecords, SendRecords — grounding classes 2/3; the canonical answer source for "what did we tell the lender" (06 §6: Lender Q&A drafts ground here).
- **Versions/periods/packages:** every Ask answer pins the period/package revision it read; every Finding pins the TestResult inputs; every MemoryEntry versions by correction (02 §5).
- **Evidence/provenance:** THE GROUNDING CONTRACT — an Ask answer is composed ONLY from: (1) deterministic engine reads, (2) sealed records, (3) filed documents, (4) confirmed values (incl. memory). General model knowledge may supply prose connective tissue, never a figure, threshold, date, or interpretation of a covenant definition (the agent never blesses a threshold interpretation — 02 §4). **Numeral lint (per 04 §2.1):** every numeral in agent-rendered output — currency, ratio, percentage, unit count, date-derived count — must be an interpolated reference carrying a `value_id`/metric ref and a lit-row citation. A bare agent numeral without a value ref is a lint error: enforced at build time over agent templates and at runtime by the answer composer (a violating answer fails closed and renders as a refusal, never as unattributed prose).
- **Refusal semantics:** when grounding is insufficient, the agent refuses BY NAMING WHAT IS MISSING — "I don't have a certified Q2 package to answer from; Q2 is in-review. I can answer from the certified Q1 package or from Q2's current engine computation, labeled as such." — with links to the missing thing's owning surface. Never generation from thin air; never a plausible guess (04 §2.5).
- **Permissions/read-only projections:** Ask's scope is the org's book only, filtered by role and Client (PMC mode: a preparer's Ask cannot read another client's loans; structurally scoped queries, not prompt-level filtering). Reviewers ask read-only questions; disposition and correction affordances do not render for them.
- **Grain:** organization (memory scope=borrower; log tenancy), client (PMC scoping), loan (memory scope=loan; findings; log filters), lender/servicer (memory scope=lender), property+PMS (COA-mapping memory), reporting period (findings pin; log filters; Ask period-pinning), document (grounding class 3), requirement/test (finding trigger refs), package/certification/send records (grounding class 2).

**The answer-composition pipeline (implementation contract for `src/lib/covenant/agent/ask.ts`):**

```text
1. RESOLVE SCOPE      org + role + client filters applied to the query context     [engine; structural]
2. BIND CONTEXT       selection provenance refs (if highlight-entry) + current
                      loan/period from the breadcrumb altitude                     [frame]
3. PLAN GROUNDING     which of the four classes the question needs
                      (engine reads / sealed records / filed documents /
                       confirmed values incl. memory)                              [agent]
4. READ               deterministic reads only; each read returns value + ref      [engine]
5. COMPOSE            prose with figures as interpolated refs, never literals      [agent]
6. LINT GATE          every numeral resolves to a value_id/metric ref with a
                      lit-row citation, or composition FAILS CLOSED                [engine]
7. RENDER             answer + citation chips + grounding statement footer
                      — or RefusalCard naming the missing grounding               [surface]
8. LOG                one ActivityEvent: question, grounding set, citations        [quiet log]
```

**Core data shapes (consumed by the components in §11; identities per 02 §2):**

```text
Finding {
  findingId, loanId, period
  trigger_ref                      ← trigger-library rule id + inputs hash
  severity_rank                    ← consequence ordering key (04 §1 row 12)
  verdict_context: { testresult_ref, basis_echo }     ← vocabulary law rides in
  consequence: { template_ref, figure_refs[] }        ← "you are {ref:$240K} of NOI from a DSCR shortfall"
  drafted_narrative_ref?           ← the mitigant draft (agent-owned until adopted)
  state: raised → acknowledged → dispositioned
  disposition?: { kind: acknowledge|accept-residual-risk|route|dismiss, reason?, who, when }
  supersedes?: findingId           ← re-raise chain, never silent re-open
}

MemoryEntry {
  memId, scope: borrower|loan|lender|property-PMS, scope_object_ref
  question, answer_value
  provenance: { who_answered, when, context_ref }     ← the originating run/question, linked
  versions[]: { value, corrected_by, when, reason }   ← corrections version, never overwrite
  reuse_log[]: { where, when }                        ← "pre-filled from memory {ref}"
  downstream_refs[]                                   ← package fields this entry feeds (void-on-change cascade)
}

CorrectionEvent {
  correctionId, draft_ref, step_anchor               ← the exact step that was wrong (R6: LangSmith)
  diff_class: tone | structure | content-fact
  was, should_have_been                              ← captured improved output, not just a score
  fed_memory_ref?                                    ← when the correction taught a reusable preference
}

ActivityEvent {
  eventId (append-only), actor: engine|agent|human (+identity)
  act, object_refs[], loanId?, period?
  evidence_refs[], outcome, next_scheduled_action?
  sub_steps[]: { step, attempt_n, input_ref, output_ref }   ← collapsed by default (R6: Temporal groups)
}
```

## 5. State machine and exceptions

**Finding lifecycle** (02 §2): `raised → acknowledged → dispositioned`, engine-triggered raise, human-only disposition, reversible only forward (a dispositioned finding whose underlying condition recurs raises a NEW finding linked to the prior disposition — no silent re-open). Every transition writes an ActivityEvent.

**MemoryEntry lifecycle:** `proposed-from-answer → active → corrected(v+1) → retired`. The capture moment is visible in-flow ("Remembered: …" inline chip at the moment of capture — R6: ChatGPT's visible saving, upgraded with provenance). Corrections version; the agent always uses the newest (02 §5); retirement (entry deleted in the inspector) is a typed act with a shown consequence ("the agent will ask this again next period").

**Ask exchange:** `composing → grounded-answering → answered-with-citations | refused-naming-missing`. No partially-grounded state ships: an answer that cannot cite every figure downgrades to refusal.

| Template state | How it renders in this layer |
|---|---|
| empty | Ask: capability-prompt rotation (below). Findings: "No findings. Next evaluation runs when {period}'s computation updates." Memory: inspector empty state explains capture ("answers you give the analyst are remembered here"). Log: "No activity yet for {scope}." |
| awaiting documents | Ask answers about the period name the holding gaps from the checklist ("the T-12 hasn't arrived; last chase {date}") — grounded in ChecklistItem state, linked to Intake. |
| loading/processing | Answer composition shows grounding-in-progress skeleton (which classes are being read); findings evaluation timestamp visible ("evaluated {time} against computation v{n}"). |
| partial/incomplete | An answer grounded in a subset says so ("Q2 normalization has 3 open exceptions; NOI below is provisional engine output, not review-complete"). |
| extracted/unconfirmed | Named, never quoted as figures (§4). Finding triggers do not fire on unconfirmed inputs. |
| stale/superseded | A source replaced after confirmation → dependent MemoryEntries and Findings flag stale; Ask answers that would read stale values carry the stale badge + diff link (03 §2 exception table). |
| low confidence | Not an Ask state (Ask is grounded-or-refuses). Low-confidence extraction lives on Extraction surfaces; Ask references it only by state. |
| conflict | Two grounding sources disagreeing (e.g. roll summary vs computed aggregate) → Ask surfaces the conflict as the answer, both refs cited, linked to the owning exception row — never silently picks one. |
| failed tie-out | Findable and explainable: Ask cites the TieOut diff; the finding class "tie-out failed" raises as a blocking review stop. |
| watch/shortfall/breach | The findings service's home ground. Vocabulary law structural: `breach` only where `basis=covenanted` (05 §3); Bexley's monitored 90% floor renders SHORTFALL at 268/301 = 89.04% (canon), never breach. |
| permission denied | Ask refuses with scope honesty ("that loan is outside your client scope") — same refusal grammar, no data leakage in the refusal text. |
| read-only | Reviewer role: all render, no mutate; disposition/correct affordances absent (hidden, not disabled — R6: GitHub role-gated visibility). |
| blocked/gated | The occupancy resolver pending the rent-roll reader (gap 7): the flagship occupancy finding renders BLOCKED honestly — "occupancy findings unavailable: rent-roll reader not yet processing this roll" — never a fixture number (04 §2.5). |
| certified (void-on-change) | A memory correction whose entry feeds a certified package field triggers void-on-change like any input mutation (03 §2): the correction modal states this consequence before commit. |
| sent/sealed | Sealed periods are Ask's strongest grounding; findings never mutate sealed records; the log of a sealed period remains addressable forever (seal-not-wipe). |
| recovery/undo | Memory corrections are versioned, never destructive (full chain visible). ActivityEvents are append-only — undo impossible by design; the undo that exists (auto-file re-route window) is Intake's, linked from the log row. Finding dispositions are not undoable; a compensating re-raise references the disposition. |

## 6. Engine / Agent / Human / Gate / Quiet Log

| Concern | ENGINE (deterministic) | AGENT (prepares/proposes) | HUMAN (typed decisions) | GATE (interrupts) | QUIET LOG |
|---|---|---|---|---|---|
| Ask | Serves every figure: engine reads, sealed records, document regions; enforces numeral lint at compose time; fails closed | Composes grounded prose around referenced values; refuses naming what's missing; never generates a figure, threshold, or covenant interpretation | Asks; follows citations; nothing to approve | — (Ask never gates) | Every exchange logged (question, grounding set, citations) |
| Proactive watch | Computes trend series, headroom time-series, watch-band crossings, checklist-vs-deadline risk (04 §1 row 12) | Evaluates trigger-library rules over engine outputs; frames findings in consequence language (canon: "you are $240K of NOI from a DSCR shortfall"); drafts mitigant narratives; ranks by consequence | Dispositions findings (typed: acknowledge / accept residual risk / route / dismiss-with-reason) | A finding that blocks an imminent certify (e.g. shortfall on a covenanted test in an open period) becomes a review stop | Findings raised + their evidence; non-blocking findings never push |
| Memory | Storage integrity; provenance stamps; version chains; scope enforcement | Asks once; reuses with provenance ("pre-filled from memory {ref}"); never re-asks a remembered answer | Corrects/retires entries (versioned); answers new questions | — (memory never gates — 04 §1 row 13) | Every capture and every reuse logged |
| Outcome learning | Diff computation on draft edits; repeat-error metric per correction class | Classifies corrections (tone / structure / content-fact); anchors each to the exact step; applies newest preference forward | Edits drafts (the correction IS the teaching act — no extra ceremony) | — | Every correction captured; metric movements logged |
| Quiet log | Append-only writes; identity stamps (actor, evidence, loan/period, outcome — 04 §2.3) | Writes one human-readable row per meaningful act; collapses minutiae | Reads; filters; opens run detail | — | Is the lane |
| Capability prompts | Supplies the completable-query set (palette computed-answer grammar, 08 §5) | Rotates prompts drawn only from that set | Taps a prompt to run it | — | — |
| **Hard prohibitions (structural, restated)** | All shipping numbers computed here and only here | **NEVER certifies. NEVER sends. Never computes or alters a shipping number. Never mutates ground truth past a human gate. Never re-asks a remembered answer. Never pushes for completed routine work** (02 §4; 04 §2) | Certify and send are human-only typed acts on their own surfaces | Certify/send gates belong to Certificate/Send briefs | Push: deadline escalation ONLY, per org policy (03 §5) |

## 7. Information hierarchy

Per host surface (this layer rides inside others):

1. **Ask panel (canvas):** the selection/question context (what you asked about, with its provenance chip) → the answer with inline mono citations → the grounding statement ("answered from: Q1 certified package, Q2 engine computation v{n}") → capability prompts (empty state only).
2. **Finding card:** severity + verdict chip → the consequence sentence (headroom phrasing) → evidence refs (each a lit-row link) → drafted-narrative link → disposition actions → prior related dispositions.
3. **Memory chip (inline):** the filled value → "learned {date} from {who} during {context}" → inspect/correct. **Inspector (Settings→Agent):** scope filter → entries table → per-entry provenance + version chain + reuse log → repeat-error metric block → master controls.
4. **Quiet log:** filter chips → root-acts list (newest first) → summoned run detail (timeline left, evidence right).

**Absent by design:** a persistent chat sidebar; sparkle icons on fields; proactive popups announcing finished work; autonomy-score dashboards ("95% automated" framing — R6: Digits reject); any agent affordance on a surface with no agent task (R6 anti-pattern: Copilot-everywhere).

## 8. Page anatomy and regions

**Ask (canvas panel — the ruled separate slot, does not count against the 2-window split, 08 §4):**
- Purpose: grounded Q&A beside the work, never over it.
- Content: question input; selection-context chip (when opened via highlight: the selected text/figure + its source refs travel in — 08 §6); answer stream with citation chips; grounding statement footer; refusal card when applicable.
- Persistence: summonable, floating, movable; Esc dismisses; never load-bearing for a decision (08 §4).
- Interaction: type or arrive-with-selection; click any citation → owning surface opens/focuses with the exact source row lit and held (frame provenance contract, 08 §7).
- Min dimensions: 360px wide × 320px tall floating; positions away from the lit region and is draggable — Ask never overlays the evidence a question is about (08 §6).
- Collapse: below 1280px viewport it docks full-height right (08 §9); its open/closed state persists per user.
- Why canvas panel: Ask is transient assistance beside any surface; a pane would claim permanent real estate the anti-chrome law forbids; a modal would destroy the context the question is about.

**Finding card (in-flow region on Home §3 / Loan Detail; not a pane):**
- Anatomy: verdict chip (pass/watch/shortfall/breach per basis) + trigger title; consequence sentence with mono interpolated figures; evidence ref row; "View drafted narrative" (opens split window 2: draft beside the finding — the one agent use of the split, justified by draft-beside-evidence co-visibility); disposition button row; timestamp + "evaluated against computation v{n}".
- Min width 560px as a card; stacks single-column below 1280px.

**Memory inspector (a Settings section, standard settings page anatomy):**
- Regions: scope filter tabs (Borrower / Loans / Lenders / Property-PMS) → entries table (uniform rows: value summary · scope object · learned {date} from {who} · version count · reuse count) → row expand: full provenance (originating question/run linked — R6: ChatGPT inspector upgraded with provenance), version chain, downstream uses → footer controls: per-entry correct/retire; master pause switch ("the agent asks instead of remembering while paused").
- Why a table not cards: dozens-to-hundreds of entries; scan/filter dominates.

**Quiet log (register + summonable run detail; interior of Home §4 / Loan Detail ACTIVITY):**
- List: root acts only by default — one row per meaningful act ("Filed T-12 to Bexley Q2", "Chased missing rent schedule", "Computed Q2 tests: 3 pass · 1 shortfall") with actor icon (engine/agent/human, Lucide), object links, timestamp; a depth toggle reveals sub-steps (R6: Trigger.dev root-runs + opt-in depth).
- Run detail (summoned overlay pane within the surface, not a route): step timeline left (recognized → matched → filed, with "Attempt N" badges on retries), selected step's inputs/outputs/evidence right (R6: Inngest two-pane). Minutiae (fetch/parse/retry) collapse into their parent act row, raw event list one level down (R6: Temporal event groups).
- Filters: actor, action type, loan, period, date — plain chips, no query language (R6: reject CEL/visibility syntax).

## 9. Co-visibility matrix

| Datum/surface A | Datum/surface B | Must be simultaneous? | User decision enabled | Default topology | Fallback |
|---|---|---:|---|---|---|
| Highlighted selection | Ask panel with its provenance context | yes | ask about exactly this | selection stays visible; canvas floats adjacent | panel docks; selection anchor chip in panel |
| Ask answer citation | The cited source row, lit | yes | verify the figure | citation click focuses Evidence beside the claim (06 §1) | owning surface opens with row lit + return path |
| Finding | Its drafted mitigant narrative | on demand | adopt/edit the draft | split window 2 (draft) beside the finding | narrative opens as stacked tab below 1280px |
| Finding | Its evidence (TestResult inputs) | yes | disposition honestly | evidence refs inline; lit-row on click | — |
| Memory chip | The field it fills | yes (chip is beside the field, always) | trust or correct the pre-fill | inline adjacency, never a hover-only reveal | chip wraps under the field at narrow widths |
| Quiet-log row | Run detail | on demand | audit the act | summoned two-pane detail | full-width overlay below 1440px |
| Repeat-error metric | The correction class's recent examples | on demand | judge whether learning works | metric block expands to example list | links to correction events |

No pane exists merely because information exists: findings and memory render in-flow; only Ask and run detail summon panels, both transient.

## 10. Layouts and viewport behavior

- **1440px:** Ask floats 380px wide over any surface, may dock right (08 §9); finding cards two-up on Home §3 at ~660px each; memory inspector table full-width within Settings' content column (~960px); quiet-log run detail opens as a 50/50 overlay within the surface.
- **1728px:** Ask floats free 400–420px; finding card + drafted narrative split both ≥560px honors the split law; run detail two-pane 55/45 (timeline/evidence).
- **2048px:** no third window ever (split max 2 is law); extra width goes to the host surface; Ask max-width 440px — answers are prose+citations, not tables; wider would invite the chat-app feel this brief forbids.
- **Narrow/compact (<1280px):** Ask docks full-height; finding cards single-column; run detail becomes a full-width labeled overlay ("Run detail — {act}"); the memory inspector table's provenance columns collapse into the row-expand (labeled, not truncated — no silent compression).
- **Focus behavior:** opening Ask never steals focus from a mid-edit field; citation navigation preserves a return path (Esc walks back: lit-row → panel → host surface — 07 §3).
- **Compare behavior:** none owned here; finding-vs-prior-period comparisons deep-link to the owning surfaces' compare modes.
- **Minimum viable:** Ask 360×320 floating / full-height dock; finding card 560px; below frame minimum (1152×720) the agent layer follows its hosts.

## 11. Components and exact anatomy

| Component | Reuse/NEW | Anatomy |
|---|---|---|
| `AskPanel` | REUSE (exists, FIXTURE — snapshot §3), rewire | Header (drag handle, context chip, close) · answer scroll region · `AskCitation` chips inline · grounding footer · input row · `CapabilityPromptRotator` in empty state |
| `HighlightToAsk` | REUSE (exists), extend | Selection listener → quiet affordance ("Ask about this", accent-family text button) → packages selection text + provenance refs (valId/docId/region) into AskPanel context — the selection travels WITH its provenance (08 §6) |
| `AskCitation` | NEW | Mono figure (Geist Mono, `tabular-nums slashed-zero`) + provenance underline per 06 §7 grammar + click → lit-row; carries `value_id`/metric ref — the lint's unit of enforcement |
| `RefusalCard` | NEW | "Can't answer from the record" title · named missing grounding · links to owning surfaces · the offer of what CAN be answered (grounded alternates) |
| `FindingsService` | NEW (`src/lib/covenant/agent/findings.ts`) | Evaluates trigger-library rules (headroom trends, watch-band entries, checklist-risk-vs-deadline) over engine outputs on each computation update; emits Finding rows (02 §2); idempotent per (trigger, loan, period, inputs-hash) |
| `FindingCard` | NEW | Per §8 anatomy; disposition row renders per role; severity color only (chart doctrine) |
| `FindingSparkline` | NEW | Headroom trend, ≤12 discrete marks, each mark clickable to its period's TestResult (every mark an instrument); ships only where it out-encodes the sentence — else the sentence alone |
| `MemoryChip` | NEW | Filled value + "learned {date} from {who}" microcopy + inspect/correct popover (popover shows provenance + version chain inline — point-of-use inspection, 03 §3) |
| `MemoryInspector` | NEW (Settings section) | Per §8; per-entry actions: correct (versioned), retire (typed, consequence shown), view reuse log |
| `CorrectionCapture` | NEW (hook in draft editors) | Diffs saved edits; classifies tone/structure/content-fact; anchors to the exact draft step and stores the should-have-been text (R6: LangSmith corrections) |
| `RepeatErrorMetric` | NEW | Per correction class: occurrences over trailing periods, trend arrow, alarm state when non-decreasing across two cycles |
| `ActivityEventStore` + `QuietLog` | NEW store; REUSE feed styling from existing activity components, re-pointed | Uniform rows, open-not-boxed; actor Lucide icon; filter chips; `CountBadge` (shared component) nowhere — the log never badges (badges are your-move only, 08 §8) |
| `RunDetail` | NEW | Two-pane per §8; attempt badges; raw-events disclosure |
| Period/package selector, Evidence panel, lit-row trace | REUSE frame contracts | 08 §2, §7; the agent adds no private provenance renderer |

## 12. Interaction specification

- **Selection:** any text/figure highlight in any surface raises the HighlightToAsk affordance within 150ms, positioned to not cover the selection; the affordance is also keyboard-reachable (selection present + shortcut).
- **Hover:** citation chips show the chain summary (source doc + region + confirmer) on hover; memory chips show provenance summary; finding evidence refs show verdict + headroom.
- **Focus:** panel and popovers hold focus rings per frame tokens; citation navigation returns focus to the citation on Esc-back.
- **Keyboard:** `⌘K` palette rows "Ask the analyst: {query}" route here (08 §5); within AskPanel: Enter sends, `⌘Enter` sends-with-selection-context, Esc closes; finding cards: `A` acknowledge, `D` disposition menu, `N` open narrative; quiet log: J/K rows, Enter run detail, `F` filter focus. Final chord map defers to `cross-cutting/search-command-keyboard.md` — this brief proposes, that file rules.
- **Editing and validation:** memory corrections use a typed edit field with the version consequence shown ("creates v{n+1}; the agent uses the newest"); disposition reasons are required text for accept-residual-risk and dismiss.
- **Bulk action:** none on findings (each disposition is an individual typed act — deliberately no "dismiss all"); memory inspector allows multi-select retire with per-entry confirm list.
- **Undo/recovery:** per §5 — versions, not deletions; append-only log.
- **Sorting/filtering:** findings default-rank by consequence (04 §1 row 12), secondary by deadline proximity; log default newest-first; all filters are visible chips (editable, shareable state).
- **Drill-down and return:** every citation, evidence ref, and log-row link carries loan+period identity and opens the owning surface with return path (07 §3 spine).
- **Source-linked selection:** the lit-row behavior is THE agent trust mechanic — every agent-quoted figure participates; the U1-F1 wrong-row defect must be fixed before Ask ships citations (dependency, §18).
- **Save/persistence:** Ask exchanges persist only as ActivityEvents; panel position/size per user; filters per user via the shared SavedView mechanism where a register hosts the log.
- **Collaboration:** finding dispositions and memory corrections are attributed acts visible to the org; no commenting layer here (annotation lives on review stops).

## 13. Visual craft direction

- **Typography roles:** agent prose in the UI sans at body 13–14px/1.5; all quoted figures in Geist Mono `tabular-nums slashed-zero` (ruled) — the type break IS the trust boundary: prose = agent, mono = engine. Microcopy ("learned {date} from {who}") at 11px in a muted gray rung; never lighter than the accessibility floor.
- **Financial numbers:** mono, tabular, slashed-zero, right-aligned in tables; signed headroom always signed (+/−) in both denominations ("−0.96pp · −$240K NOI" shape, canon phrasing).
- **Spacing rhythm:** 8px base grid; finding cards 16px internal padding; log rows uniform height (28–32px dense register rhythm).
- **Density:** the log and inspector are dense registers; Ask is airy (prose measure ~64ch max).
- **Open ground vs boxes:** log rows and inspector tables open-not-boxed (hairline separators from the gray ladder's faint rung); finding cards earn a hairline border as decision objects; no shadowed chat bubbles — answers are typeset text, not bubbles.
- **Dividers/elevation:** canvas panel carries the frame's standard floating elevation; run detail overlay one elevation step; nothing else floats.
- **Semantic color:** verdict chips use the ruled status colors only, severity-only (chart doctrine); interactive affordances use the accent family (#7189FF, hover #8EA1FF, active #6078F4); memory chips and provenance underlines use gray rungs + the ruled dotted/solid underline grammar (06 §7) — no new colors anywhere.
- **Certified-sheet adjacency:** when Ask cites a certified figure, the citation chip carries the certified badge grammar; the sheet's blue-violet hexes render only on the sheet itself (Certificate brief) — this layer links to it, never repaints it.
- **Focus/selected/hover:** frame-standard tokens; lit-row highlight held (persistent) until dismissed per the provenance contract.
- **Charts:** only `FindingSparkline`, under full doctrine (≤12 marks, severity-only color, every mark clickable, must out-encode the adjacent sentence or not ship).
- **Motion:** panel summon 150ms ease-out; lit-row light-up 200ms; capability prompts rotate on a slow interval with a full-stop under `prefers-reduced-motion` (rotation becomes a static prompt + shuffle button).
- **Long-session ergonomics:** no pulsing, no unread-style badges, no attention-seeking chrome — the layer is calm by law.

## 14. Benchmark research and synthesis

| Product | Limited role | Official evidence | Exact pattern to take | Covenant adaptation | What to reject | Why this beats alternatives |
|---|---|---|---|---|---|---|
| Inngest | Quiet-log run-detail anatomy | https://www.inngest.com/docs/platform/monitor/inspecting-function-runs (R6, checked 2026-08-08) | Two-pane run detail: step timeline left, selected step's inputs/outputs right; "Attempt N" retry badges | "Classification → extraction → filing" traces per act; attempts visible on retried steps | Bulk cancel/replay as first-class; time-brush zoom (runs here are short) | Purpose-built run inspection; cleaner than log-stream tools |
| LangSmith | Corrections write-path | https://docs.langchain.com/langsmith/observability-concepts · https://docs.langchain.com/langsmith/annotate-traces-inline (R6) | Feedback anchored to ANY intermediate step + corrections captured as structured "should have been" values | `CorrectionCapture`: draft edits diffed, classed, anchored to the exact step, should-have-been stored — the write path of ask-once memory and the learning loop | Raw token/latency/cost metrics in borrower UI; labeling-queue framing | The only mature pattern for step-anchored human corrections |
| Temporal | Event collapsing; durable waiting | https://docs.temporal.io/web-ui · https://docs.temporal.io/ai-cookbook/human-in-the-loop-python (R6) | Event Groups: noisy event triples collapse to one activity row, raw history one level down; work that waits indefinitely on a named human | Log rows = meaningful acts; minutiae in disclosure; "waiting on {name} to certify" rendered without decay | Operator query languages and search-attribute syntax | Proves log-legibility and human-blocking state at production scale |
| Trigger.dev | Quiet-log list grammar | https://trigger.dev/docs/runs · https://trigger.dev/changelog/new-run-filters (R6) | Root-runs-only by default, subtask depth opt-in; plain filter set; bulk actions leave a findable trail | Root acts only in the log; depth toggle; filters = actor/type/loan/period/date chips | CEL expressions; payload-editing replay | The cleanest outcomes-not-plumbing default on record |
| ChatGPT memory | Memory inspector reference UI | https://help.openai.com/en/articles/8590148-memory-faq · https://openai.com/index/memory-and-new-controls-for-chatgpt/ (R6) | Central Settings inspector: every entry listed, per-entry edit/delete, master switch; visible in-flow capture moment | Upgraded with provenance: value + "learned {date} from {who}" + originating question/run linked + version chain — never a synthesized prose summary | Opaque cross-conversation synthesis (compliance product: every memory traces to a named human's answer) | The pattern users already know for "what does the AI remember" |
| Digits | Finance-agent posture (confidence-gated) | https://digits.com (R6; + cited coverage) | Confidence routes: auto-handle the certain, queue the uncertain residue; named blocking statuses ("Needs the T-12") | The gate decides quiet-log vs proposal — NEVER send; corrections feed future behavior (ask-once in production) | "95% automated" aggregate autonomy framing in-product | Closest live analog: autonomous classification + human review, in finance |
| Microsoft Copilot (ANTI-PATTERN) | The named control — what this layer must not become | R6 rollout/retreat coverage (digitaltrends.com, futurism.com, windowsnews.ai; checked 2026-08-08) | The lesson: invoked AI is welcomed, imposed AI is despised; Microsoft is publicly removing ambient Copilot chrome | Direct counter-design: quiet log (discoverable, zero push for routine completions), Ask summoned never ambient, agent presence = results in context | Persistent chat sidebars; sparkle buttons per field; proactive popups for finished work | The one documented at-scale failure of ambient agent chrome |

**Synthesis:** the agent layer is original because no benchmark combines a *grounded-or-refuses* answer surface with a *provenance-typed* memory and a *consequence-ranked* findings service under a hard non-actor rule (never certify, never send, never compute). The run-observability donors (Inngest/Temporal/Trigger.dev) contribute legibility mechanics; LangSmith/ChatGPT contribute the correction-and-memory write/read paths; Digits proves the confidence-gated posture in finance; the Copilot retreat supplies the negative space. Domain-correctness comes from the lit-row law and the vocabulary law: every quoted figure is engine truth with an open chain, every verdict word is basis-aware — properties none of the donors have or need.

## 15. Domain references

Finley-class covenant-tracking products and loan-servicing portals: terminology and workflow expectations only (what borrowers expect "covenant status," "compliance certificate," and servicer questionnaires to mean); Digits for finance-agent behavioral posture. **Domain authority does not equal visual authority** — none of these products' visual treatments govern anything here. Covenant semantics — thresholds, test definitions, cadences, what counts as a shortfall — come exclusively from each loan's own documents and from Terry's rulings (canon), never from any referenced product (domain-content firewall).

## 16. Accessibility, performance, and safety

- **Contrast/focus:** all agent text on gray rungs meets WCAG AA; verdict chips carry text labels, never color alone; focus rings on every interactive citation, chip, and disposition control.
- **Keyboard completeness:** Ask fully operable without pointer (summon, ask, walk citations, close); highlight-to-ask reachable from keyboard selection; log and inspector are standard table-nav surfaces.
- **Screen readers:** answers announce citation boundaries ("figure, cited, source available"); refusals announce as status not error; log rows read actor + act + object + time; the lit-row light-up has an ARIA live-region announcement.
- **Large data:** the log virtualizes past ~200 rows; the memory inspector paginates per scope; findings are naturally few (consequence-ranked, deduped per trigger+inputs-hash).
- **Loading/latency:** answer composition streams prose but withholds each figure until its ref resolves (a figure never renders before its provenance); grounding-read skeletons name the classes being read.
- **Destructive confirmation:** memory retire and dismiss-with-reason require typed confirmation; nothing in this layer deletes data (versions and append-only stores).
- **Certify/send safety:** structurally out of reach — no code path in this layer calls the certify or send gates; the palette's dangerous verbs are human-only and role-gated (08 §5); a memory correction touching certified inputs routes through void-on-change with the consequence stated first.
- **Source immutability:** the agent reads originals, never writes documents; grounding reads are read-only projections.
- **Auditability:** every exchange, finding, disposition, capture, correction, and reuse is an identity-stamped ActivityEvent; the sealed period's agent history is inspectable forever.

## 17. Acceptance tests and fixtures

Fixtures: **Bexley canon** (UPB $15,232,500 · 301 units · monitored 90% occupancy floor · 268/301 = 89.04% shortfall) and the **Calloway Park FYE-2018 evidence spine** (T-12: Total OpEx $1,686,050 · NOI $1,218,877 · Net Income $460,159; 6001.NR §8.02(b) cadences; 322-row/301-unit roll).

1. **Grounded answer:** Ask "why is NOI $1,218,877?" against Calloway FYE-2018 → answer quotes the figure as an `AskCitation` carrying the Metric ref; click lights the T-12 NOI grouping row in Evidence and it stays lit (chain per 06 §2). FAIL if any numeral in the answer lacks a value ref.
2. **Numeral lint:** a test template containing a bare hardcoded "$1.2M" fails the build lint; at runtime, a composed answer with an unresolvable figure ref renders `RefusalCard`, never partial prose with the figure inlined (04 §2.1).
3. **Refusal names the missing:** Ask "what did we send the lender for Q2?" with Q2 in-review → refusal reads "I don't have a certified Q2 package to answer from" + link to the Q2 period + the grounded alternate (certified Q1). Zero generated figures in the refusal.
4. **Selection travels with provenance:** highlight "89.04%" in the Review Room → Ask opens with the selection chip carrying the occupancy TestResult ref; the answer's citations resolve to the same chain (unit dedup 322→301 visible in the chain per 06 §4).
5. **Vocabulary law:** the Bexley occupancy finding renders verdict SHORTFALL with `basis_echo=monitored`; the string "breach" is unreachable in any agent template for monitored rows (type-level test, 05 §3).
6. **Canon finding:** with a DSCR-class test in watch band, the finding card renders the consequence sentence in the canon shape "you are $240K of NOI from a DSCR shortfall" with both figures as engine refs, a drafted mitigant narrative attached, and disposition as a typed act writing an ActivityEvent.
7. **Blocking finding gates review:** a shortfall on a covenanted test (Westbrook Flats 1.20x DSCR fixture) in an open period appears as a review stop; readiness stays false until dispositioned.
8. **Zero re-asks:** run two consecutive Calloway periods with unchanged facts (same PMS, same COA codes, same lender forms) → the agent asks zero questions in period 2 that period 1 answered; every pre-fill renders its MemoryChip with "learned {date} from {who}". Any repeat question = FAIL.
9. **Memory correction versions:** correct the management-fee memory entry → v2 created, v1 retained in chain, next period pre-fills v2; if the entry fed a certified package field, the void-on-change consequence modal appeared before commit.
10. **Learning alarm:** seed three tone-class corrections on the same narrative pattern across two cycles → `RepeatErrorMetric` shows the class non-decreasing and enters alarm state (the "misses the same shit every time" test — Terry ruling, memory `covenant-agent-behavior-self-sufficient-proactive.md`); a decreasing class shows the downtrend.
11. **Quiet log unity:** the same auto-file event renders identically on Home §4 and Loan Detail ACTIVITY (one store, filtered views) — the U3-F2 contradiction class is structurally impossible; row expand shows the Inngest-style two-pane with an Attempt 2 badge on a retried extraction step.
12. **No-push:** complete an entire autonomous prep cycle (arrival → filed → computed) → zero push notifications emitted; flip a deadline into escalation per policy → exactly one push, linked to the blocking item.
13. **Scope wall:** in PMC mode, a preparer's Ask about another client's loan refuses on scope with no data in the refusal; a reviewer sees no disposition or correction affordances (hidden, not disabled).
14. **Capability prompts honest:** every rotating prompt, when tapped, completes against current fixtures (prompts are drawn from the palette's computed-answer set); a prompt whose query cannot complete for the loans in view never renders (locked law).
15. **Viewports:** 1440/1728/2048/1024-narrow fixture pass — panel float/dock per 08 §9, split minimums honored, no silent compression (narrative-beside-finding stacks to tabs below 1280px).
16. **Accessibility:** keyboard-only citation walk; screen-reader announcement of lit-row; AA contrast on memory microcopy.
17. **Benchmark challenger:** reviewer holding R6 confirms the log defaults to root acts (Trigger.dev), the run detail is two-pane (Inngest), corrections are step-anchored with should-have-been (LangSmith), the inspector is provenance-typed (ChatGPT upgraded), and no ambient chrome exists anywhere (anti-Copilot).

## 18. Build plan

- **Dependencies:** persistence for `memory_entries`, `findings`, `activity_events`, `correction_events` (Supabase migrations; today mostly contract-only — snapshot §3); the F-series engine wiring (TestResults must be real before findings can be); the lit-row contract fix U1-F1 (`cross-cutting/provenance-lit-row-trace.md`) — **Ask does not ship citations until the wrong-row defect is fixed**; the rent-roll canonical reader (F3) for the occupancy finding (blocked honestly until then); the orchestration spine for run traces; tenancy/roles for scope walls.
- **Foundation work:** (1) the ActivityEvent store + write-path from existing engine/agent code paths — first, because it retires U3-F2 and gives every later feature its audit substrate; (2) the grounding read-layer over engine functions + sealed records (the unwired analyst-grounding substrate is the starting point — snapshot §3); (3) the numeral-lint rule in CI over agent templates.
- **Components first:** `AskCitation` + `RefusalCard` (the trust primitives) → `QuietLog` re-point of existing feed components onto the one store → `MemoryChip` → `FindingCard`.
- **Vertical slice (send-vertical pattern):** one loan+period — Bexley Q2 from engine data end to end: FindingsService evaluates the real occupancy TestResult → the 268/301 = 89.04% shortfall finding renders on Home and Loan Detail with a drafted mitigant narrative → highlight the figure in Review → Ask answers grounded with a working lit-row citation → disposition writes the ActivityEvent. One route set, real data, the full agent story in one seam.
- **Migration from fixture:** AskPanel's scripted golden answer is deleted the day the grounded service answers the same question from engine data (the golden answer becomes acceptance test 1); fixture activity feeds re-point to the store and their arrays are deleted.
- **Rollout/flag:** `agent.ask.grounded`, `agent.findings`, `agent.memory`, `agent.quietlog` flags per capability; memory ships with the master pause switch ON-able from day one.
- **Proof artifacts:** screen-capture of the lit-row citation walk on Bexley; the zero-re-asks two-period run log; the no-push cycle log; repeat-error metric screenshot with a seeded downtrend.
- **Final gate:** ADJUST confirmed for AskPanel/HighlightToAsk only if the grounded service passes tests 1–4; every NEW service passes its acceptance set before the layer claims PASS. Nothing here certifies, sends, or computes a shipping number — verified structurally (no call path), not by review.
