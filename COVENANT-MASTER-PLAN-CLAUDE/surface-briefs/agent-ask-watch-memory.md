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
- **Questions the surface must answer in scan order:**
  1. Is anything drifting toward a covenant problem, and how far is the line? (findings, headroom phrasing)
  2. Why is this number what it is? (Ask + the lit-row chain)
  3. What did we tell the lender, exactly, and when? (Ask over sealed records)
  4. What has the agent already handled without me? (quiet log root acts)
  5. What does it remember about my book, and is it right? (memory chips + inspector)
  6. Is it getting better where I corrected it? (repeat-error metric)
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

**Finding transitions:**

| Transition | Trigger | Actor | Prerequisites | Reversible? | Audit event | Failure behavior |
|---|---|---|---|---|---|---|
| — → raised | Rule fires on computation update | Engine detects; agent frames | TestResult/checklist inputs confirmed-grade | n/a | `finding.raised` + evidence refs | Rule evaluation error → logged, never a phantom finding |
| raised → acknowledged | "Acknowledge" on the card | Human | Role may disposition | No (forward-only) | `finding.acknowledged` (who/when) | — |
| raised/ack → dispositioned | Typed disposition act | Human only | Reason text required for accept-residual-risk and dismiss | No — a recurrence raises a NEW finding with `supersedes` ref | `finding.dispositioned` (kind, reason, who) | A blocking finding cannot be dismissed while it blocks readiness; the card says why |
| dispositioned → (new raised) | Same condition, changed inputs | Engine/agent | Inputs-hash differs | n/a | `finding.raised` linking prior disposition | No silent re-open, ever |

**MemoryEntry transitions:**

| Transition | Trigger | Actor | Prerequisites | Reversible? | Audit event | Failure behavior |
|---|---|---|---|---|---|---|
| — → active | Human answers an ask-once question; capture chip shown in-flow (R6: ChatGPT visible saving) | Human answers; agent captures | The question was not already remembered (structural: the store is checked before any ask renders) | Via correction | `memory.captured` (provenance) | Capture failure → the question re-renders next period; never a half-saved entry |
| active → active(v+1) | Correction in chip popover or inspector | Human | Typed edit; consequence shown if `downstream_refs` include certified fields (void-on-change cascade, 03 §2) | Prior versions retained, chain visible | `memory.corrected` (v, reason) | — |
| active → retired | Retire in inspector | Human | Typed confirm; consequence shown ("the agent will ask this again") | Re-capture on next answer | `memory.retired` | — |
| any reuse | Agent pre-fills a field | Agent | Newest version only (02 §5) | n/a | `memory.reused` (where) | A reuse that cannot resolve its entry renders the empty field + the question — never a stale guess |

**Ask exchange:** `composing → grounded-answering → answered-with-citations | refused-naming-missing`. No partially-grounded state ships: an answer that cannot cite every figure downgrades to refusal (lint gate, §4 pipeline step 6). Refusal is a success state of the pipeline, not an error state — it renders calmly, names the missing grounding class and object, and links the owning surface.

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

**The one gate this layer owns, specified per the gate law (04 §2.2 — a gate names its typed decision):**

*Blocking-finding gate* (a finding that blocks an imminent certify — e.g. a shortfall on a covenanted test in an open period):

- **Exact ask:** "Disposition this finding before certifying {loan} {period}."
- **Why blocked:** the verdict + basis ("DSCR below the covenanted 1.20x floor — shortfall" for the Westbrook-class fixture, the computed value and headroom rendered from engine refs; verdict word per vocabulary law).
- **Evidence:** the TestResult inputs, each lit-row traceable; the headroom sentence in both denominations.
- **Affected fields:** the package sections that carry the test (covenant section, narrative), listed with links.
- **Recommendation:** the drafted mitigant narrative, offered — never auto-adopted ("The analyst drafted an explanation; review and adopt it in Composer, or write your own").
- **Typed decision options:** acknowledge-and-route (open Composer/Actuals with return path) · accept residual risk (typed reason required; recorded against the finding) · correct the underlying input (routes to the owning surface; the finding re-evaluates on recompute).
- **Consequence of delay:** the period cannot reach certify; the deadline countdown renders on the gate (due-rule provenance from the Calendar's engine).
- **Audit record:** `finding.dispositioned` with kind/reason/who/when; the review stop's disposition echoes it (one record, two renders).

Memory and Ask never gate (04 §1 row 13): nothing in this layer interrupts a human except a finding that genuinely blocks, and even that renders as a review stop, not a popup.

## 7. Information hierarchy

Per host surface (this layer rides inside others):

1. **Ask panel (canvas):** the selection/question context first (what you asked about, with its provenance chip — the user must always see that the panel knows *exactly* what was highlighted) → the answer with inline mono citations → the grounding statement ("answered from: Q1 certified package · Q2 engine computation v{n}" — every answer discloses its grounding classes) → capability prompts (empty state only). The grounding statement is not a footnote nicety: it is the visible edge of the grounding contract, and it renders on every answer without exception.
2. **Finding card:** severity + verdict chip first (the triage read) → the consequence sentence (headroom phrasing — dollars before ratios, because the mitigant conversation happens in dollars, 05 §3) → evidence refs (each a lit-row link) → drafted-narrative link → disposition actions → prior related dispositions (the `supersedes` chain). Scan order is severity → consequence → proof → action; a user who reads only the first two lines has the decision-relevant content.
3. **Memory chip (inline):** the filled value first (it is the field's content), then the microcopy "learned {date} from {who} during {context}", then inspect/correct on interaction. The chip never out-shouts the field it annotates. **Inspector (Settings→Agent):** scope filter → entries table → per-entry provenance + version chain + reuse log → repeat-error metric block → master controls (pause switch last — it is the biggest hammer and the rarest need).
4. **Quiet log:** filter chips → root-acts list (newest first — recency is the only default sort a log needs) → summoned run detail (timeline left, evidence right). The log's scan promise: a borrower returning from a week away reads the root-acts list top to bottom and knows everything the analyst did, without opening a single detail pane.

**Absent by design:** a persistent chat sidebar; sparkle icons on fields; proactive popups announcing finished work; autonomy-score dashboards ("95% automated" framing — R6: Digits reject); unread badges on the log (badges are your-move only, 08 §8); a chat-history surface (exchanges live in the log as events, not as a conversation product); any agent affordance on a surface with no agent task (R6 anti-pattern: Copilot-everywhere — the named control for this entire layer).

## 8. Page anatomy and regions

**Ask (canvas panel — the ruled separate slot, does not count against the 2-window split, 08 §4):**
- Purpose: grounded Q&A beside the work, never over it.
- Content: question input; selection-context chip (when opened via highlight: the selected text/figure + its source refs travel in — 08 §6); answer stream with citation chips; grounding statement footer; refusal card when applicable.
- Input states: empty (capability prompts render) · selection-armed (context chip present, input placeholder reads "Ask about this {figure/selection}") · composing (grounding-read skeleton naming the classes being read) · answered (citations live) · refused (RefusalCard, calm tone, links out). The input never renders a spinner without naming what is being read.
- Persistence: summonable, floating, movable; Esc dismisses; never load-bearing for a decision (08 §4).
- Interaction: type or arrive-with-selection; click any citation → owning surface opens/focuses with the exact source row lit and held (frame provenance contract, 08 §7).
- Min dimensions: 360px wide × 320px tall floating; positions away from the lit region and is draggable — Ask never overlays the evidence a question is about (08 §6).
- Collapse: below 1280px viewport it docks full-height right (08 §9); its open/closed state persists per user.
- Why canvas panel: Ask is transient assistance beside any surface; a pane would claim permanent real estate the anti-chrome law forbids; a modal would destroy the context the question is about.

**Finding card (in-flow region on Home §3 / Loan Detail; not a pane):**
- Anatomy: verdict chip (pass/watch/shortfall/breach per basis) + trigger title; consequence sentence with mono interpolated figures; evidence ref row; "View drafted narrative" (opens split window 2: draft beside the finding — the agent layer's single use of the split, justified by draft-beside-evidence co-visibility); disposition button row; timestamp + "evaluated against computation v{n}".
- Min width 560px as a card; stacks single-column below 1280px.

**Memory inspector (a Settings section, standard settings page anatomy):**
- Regions: scope filter tabs (Borrower / Loans / Lenders / Property-PMS) → entries table (uniform rows: value summary · scope object · learned {date} from {who} · version count · reuse count) → row expand: full provenance (originating question/run linked — R6: ChatGPT inspector upgraded with provenance), version chain, downstream uses → footer controls: per-entry correct/retire; master pause switch ("the agent asks instead of remembering while paused").
- Why a table not cards: dozens-to-hundreds of entries; scan/filter dominates.

**Quiet log (register + summonable run detail; interior of Home §4 / Loan Detail ACTIVITY):**
- List: root acts only by default — one row per meaningful act ("Filed T-12 to Bexley Q2", "Chased missing rent schedule", "Computed Q2 tests: 3 pass · 1 shortfall") with actor icon (engine/agent/human, Lucide), object links, timestamp; a depth toggle reveals sub-steps (R6: Trigger.dev root-runs + opt-in depth).
- Run detail (summoned overlay pane within the surface, not a route): step timeline left (recognized → matched → filed, with "Attempt N" badges on retries), selected step's inputs/outputs/evidence right (R6: Inngest two-pane). Minutiae (fetch/parse/retry) collapse into their parent act row, raw event list one level down (R6: Temporal event groups).
- Filters: actor, action type, loan, period, date — plain chips, no query language (R6: reject CEL/visibility syntax).

**Capability prompts (AskPanel empty state — the locked rotating-prompt law):**
- Content: 3 prompts at a time, rotating on a slow interval, drawn ONLY from the palette's computed-answer grammar for the loans currently in scope (08 §5) — e.g. against canon fixtures: "What's Bexley's occupancy this period?" · "When is the next §8.02(b) quarterly statement due?" (evidence-class due-rule) · "What changed since the last certified package?". Because the set is computed from currently-answerable queries, a prompt that cannot complete is structurally unrenderable — the law "never advertise an ask that can't complete" is enforced by construction, not by copy review (ruling: memory `agent-ux-cycling-capability-prompts.md`, 2026-07-11).
- Anatomy: prompt text as a quiet button (accent-family text color); tap runs it as a real Ask; a shuffle control replaces rotation under `prefers-reduced-motion`.
- Placement: empty state only — the moment an exchange exists, prompts yield to the answer; prompts never interrupt or overlay (anti-Copilot).

**Memory chip states (the inline render, everywhere a remembered answer fills a field):**
- Filled: value + "learned {date} from {who}" microcopy; popover on click: full provenance (question, context, originating run link), version chain, correct/retire actions.
- Corrected: chip shows "updated {date}" until next reuse; version chain shows the diff.
- Stale (source replaced upstream): chip carries the stale badge + diff link; the field's owning surface drives re-confirmation (03 §2).
- Paused (master switch off): no chips render; every ask-once question renders as a question again; the inspector states the paused condition.

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

| Viewport | Ask panel | Finding cards (Home §3) | Memory inspector | Run detail |
|---|---|---|---|---|
| 1440px | floats 380px wide; may dock right (08 §9) | two-up at ~660px each | table full-width in Settings' content column (~960px) | 50/50 overlay within the surface |
| 1728px | floats free, 400–420px | two-up with wider consequence lines | table + inline expanded row side room | two-pane 55/45 (timeline/evidence) |
| 2048px | max-width 440px (capped) | still two-up — never three (density over sprawl) | unchanged; extra width stays whitespace | 55/45, capped content measure |
| <1280px | docks full-height right (08 §9) | single column | provenance columns collapse into row-expand (labeled) | full-width labeled overlay ("Run detail — {act}") |

- **2048px note:** no third window ever (split max 2 is law); extra width goes to the host surface. Ask stays capped — answers are prose + citations, not tables; a wider panel would invite the chat-app feel this brief forbids.
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
| `AgentEmptyState` | NEW (shared by findings/log/inspector empty states) | One quiet sentence + the reason ("No findings — last evaluated {time} against computation v{n}") + optional next-evaluation note; never an illustration, never a prompt to "try the AI" |
| `BlockedCapabilityNotice` | NEW | The honest-block render for capabilities awaiting machinery (occupancy findings pending the rent-roll reader, gap 7): names what is missing + links the dependency; replaces itself with the real feature, never with a fixture |
| `CapabilityPromptRotator` | NEW | Prompt set computed from the palette's answerable-query grammar (08 §5); 3 visible; slow rotation; shuffle button under reduced motion; tap = run as real Ask |
| Period/package selector, Evidence panel, lit-row trace | REUSE frame contracts | 08 §2, §7; the agent adds no private provenance renderer |

## 12. Interaction specification

- **Selection:** any text/figure highlight in any surface raises the HighlightToAsk affordance within 150ms, positioned to not cover the selection; the affordance is also keyboard-reachable (selection present + shortcut). Selecting a figure that carries a provenance ref packages the ref; selecting plain prose packages the surface + object context (loan/period identity from the breadcrumb) so even prose questions arrive scoped.
- **Hover:** citation chips show the chain summary (source doc + region + confirmer) on hover; memory chips show provenance summary; finding evidence refs show verdict + headroom. Hover never carries information that click cannot also reach (hover is acceleration, not the only path).
- **Focus:** panel and popovers hold focus rings per frame tokens; citation navigation returns focus to the citation on Esc-back; opening Ask while a field is mid-edit never steals focus (the panel opens unfocused with a visible "jump to Ask" affordance).
- **Keyboard:** `⌘K` palette rows "Ask the analyst: {query}" route here (08 §5 — the palette's engine rows and Ask rows are visually distinct in the palette itself, keeping the deterministic/agent boundary legible in the grammar); within AskPanel: Enter sends, `⌘Enter` sends-with-selection-context, Esc closes; finding cards: `A` acknowledge, `D` disposition menu, `N` open narrative; quiet log: J/K rows, Enter run detail, `F` filter focus, `.` toggle sub-step depth. Final chord map defers to `cross-cutting/search-command-keyboard.md` — this brief proposes, that file rules.
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
- **Iconography:** Lucide-only (actor icons in the log, chip glyphs); no emoji anywhere (locked law).
- **Empty-state tone:** factual and quiet ("No findings — last evaluated {time}"), never promotional ("Your AI assistant is ready!") — the anti-Copilot register applies to copy, not just chrome.

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

- **Finley-class covenant-tracking products** (lender-side covenant monitoring): terminology and expected-data semantics only — what "covenant status," "compliance certificate," "headroom," and "watch" mean to people who live in debt reporting; useful for naming, useless for layout.
- **Loan-servicing portals and servicer questionnaires** (the JLL evidence set, SLOT-4/5): what lenders actually ask, so findings and Ask answers speak the audience's vocabulary — the questionnaire's own field names are the target grammar for memory-filled answers.
- **Digits** (R6): the behavioral reference for a finance agent that classifies autonomously and queues the uncertain — posture, not pixels.
- **Financial-close products** (Numeric/FloQast class, R3 corpus): the exception-first review temperament the findings service inherits.

**Domain authority does not equal visual authority** — none of these products' visual treatments govern anything here. Covenant semantics — thresholds, test definitions, cadences, what counts as a shortfall versus a breach — come exclusively from each loan's own documents and from Terry's rulings (canon), never from any referenced product (domain-content firewall, kit law 8).

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
- **Prompt-injection posture:** grounding reads are structured engine/store queries, never free-text retrieval over document prose — a malicious instruction inside an arrived document cannot alter the answer pipeline because documents contribute regions-by-reference, not executable context; outbound-facing drafts (chases, replies) remain human-approved regardless (external-facing law).
- **Memory safety:** entries capture only answers a named human gave in-product; the store never infers preferences silently from behavior (every entry has a provenance row or it cannot exist — schema-level NOT NULL on provenance).
- **Latency honesty:** if a grounding read exceeds its budget, the answer says which class timed out and offers retry — never a degraded answer that silently omitted a grounding class.

## 17. Acceptance tests and fixtures

Fixtures: **Bexley canon** (UPB $15,232,500 · 301 units · monitored 90% occupancy floor · 268/301 = 89.04% shortfall) and the **Calloway Park FYE-2018 evidence spine** (T-12: Total OpEx $1,686,050 · NOI $1,218,877 · Net Income $460,159; 6001.NR §8.02(b) cadences; 322-row/301-unit roll).

**AGT-00 — Full-period workflow fixture (the agent layer's thread through arrival → sealed record).**
Run one Calloway quarterly period end to end and assert the agent layer's touchpoints in order:
1. arrival auto-filed (quiet-logged, no push);
2. one new COA code triggers a mapping question → answered → `memory.captured` with visible in-flow chip;
3. computation completes → FindingsService evaluates → zero findings on the clean path (log records the evaluation);
4. review walk: highlight a figure → grounded Ask with lit citation;
5. certify + send (human acts, no agent code path invoked — assert structurally);
6. period seals → Ask over the sealed record quotes exact sealed figures;
7. next period: the COA mapping pre-fills with its MemoryChip, zero re-ask.
Every step's ActivityEvent exists, identity-stamped, in one store.

**AGT-01 — Grounded answer with lit-row citation.**
Given the Calloway FYE-2018 spine computed by the engine, when the user asks "why is NOI $1,218,877?", then the answer quotes the figure as an `AskCitation` carrying the Metric ref; clicking it lights the T-12 NOI grouping row in Evidence and the row STAYS lit (chain per 06 §2). FAIL if any numeral in the answer lacks a value ref, or if the lit row is not the exact source row (the U1-F1 defect class).

**AGT-02 — Numeral lint, build and runtime.**
Given an agent template containing a bare hardcoded "$1.2M", the CI lint fails the build naming the template and line. Given a composed answer at runtime with one unresolvable figure ref, the whole answer renders as `RefusalCard` — never partial prose with the figure inlined (04 §2.1 fail-closed).

**AGT-03 — Refusal names the missing.**
Given Q2 in-review (no certification), when asked "what did we send the lender for Q2?", then the refusal reads "I don't have a certified Q2 package to answer from", links the Q2 period, and offers the grounded alternates (certified Q1; Q2's current engine computation, labeled provisional). Zero generated figures anywhere in the refusal.

**AGT-04 — Selection travels with provenance.**
Given the Review Room rendering Bexley occupancy, when the user highlights "89.04%" and taps "Ask about this", then AskPanel opens carrying the selection chip with the occupancy TestResult ref, and the answer's citations resolve through the same chain (unit dedup 322 rows → 301 units visible in the opened chain, per 06 §4).

**AGT-05 — Vocabulary law is type-enforced.**
The Bexley occupancy finding renders verdict SHORTFALL with `basis_echo=monitored`. A type-level test proves the string "breach" is unreachable in any agent template when `basis_echo=monitored` (05 §3). Any render path that could print "breach" for a monitored miss = FAIL.

**AGT-06 — The canon finding.**
Given a DSCR-class test entering its watch band, the finding card renders the consequence sentence in the canon shape — "you are $240K of NOI from a DSCR shortfall" — with both figures as engine refs (signed, dual-denominated), a drafted mitigant narrative attached behind "View drafted narrative", and disposition as a typed act that writes `finding.dispositioned` with who/when/reason.

**AGT-07 — Blocking finding gates review.**
Given the Westbrook Flats fixture (covenanted 1.20x DSCR) with a shortfall verdict in an open period, the finding appears as a review stop; readiness remains false, the Certify CTA lists it as a linked reason, and dismissal is unavailable while it blocks (the card says why).

**AGT-08 — Zero re-asks (the ask-once acceptance test).**
Given two consecutive Calloway periods with unchanged facts (same PMS export shape, same COA codes, same lender forms, same signers), when period 2 runs end to end, then the agent asks ZERO questions that period 1 answered; every pre-filled field renders its MemoryChip with "learned {date} from {who}". One repeat question = FAIL of the whole layer.

**AGT-09 — Memory correction versions and cascades.**
Given a remembered management-fee-line answer (evidence class: the JLL questionnaire's fee-% question), when corrected in the inspector, then v2 is created with v1 retained in the visible chain and next period pre-fills v2. Given the entry's `downstream_refs` include a certified package field, the correction flow states the void-on-change consequence BEFORE commit, and committing voids the certification with the reason recorded (03 §2).

**AGT-10 — The repeat-error alarm.**
Given three same-class tone corrections on the same narrative pattern across two cycles, `RepeatErrorMetric` shows the class non-decreasing and enters alarm state — the ruled test: "If he misses the same shit every time, we're dead in the water" (memory `covenant-agent-behavior-self-sufficient-proactive.md`). Given a class whose recurrence falls cycle-over-cycle, the metric shows the downtrend. The metric renders in `/settings/agent`, per class, with example links.

**AGT-11 — Quiet log unity (kills U3-F2).**
Given one auto-filed arrival, the identical event renders on Home §4 and Loan Detail ACTIVITY from the one store (filtered views; a divergence is impossible by construction). Expanding the row opens the two-pane run detail; a retried extraction step shows an "Attempt 2" badge with both attempts comparable (R6: Inngest).

**AGT-12 — No-push.**
Given a full autonomous prep cycle (arrival → recognized → filed → normalized → computed) completing overnight, zero push notifications are emitted; every act is discoverable in the log. Given a deadline crossing into escalation per org policy, exactly one push fires, deep-linking the blocking item (03 §5 — the one push-eligible class).

**AGT-13 — Scope wall.**
Given PMC mode with two clients, a preparer's Ask about the other client's loan refuses on scope with zero data leakage in the refusal text; a reviewer sees no disposition or correction affordances anywhere (hidden, not disabled — R6: GitHub).

**AGT-14 — Capability prompts honest by construction.**
Every rendered prompt, when tapped, completes against current fixtures; unit test: remove a loan's computed TestResults → its dependent prompts drop out of the rotation set the same render (the set derives from the palette's computed-answer grammar, 08 §5).

**AGT-15 — Viewports.**
1440 / 1728 / 2048 / 1024-narrow fixture pass: panel float/dock per 08 §9; finding-narrative split honors both ≥560px or stacks as labeled tabs; run detail becomes full-width overlay below 1440px; memory inspector collapses provenance columns into row-expand, labeled — no silent compression anywhere.

**AGT-16 — Accessibility.**
Keyboard-only pass: summon Ask, ask, walk citations, disposition a finding, correct a memory entry — zero pointer use. Screen reader announces the lit-row event and citation boundaries. Contrast audit on memory microcopy and grounding footer against AA.

**AGT-17 — Benchmark challenger review.**
A reviewer holding R6 confirms: log defaults to root acts with opt-in depth (Trigger.dev); run detail is two-pane (Inngest); minutiae collapse with raw events one level down (Temporal); corrections are step-anchored with captured should-have-been (LangSmith); the inspector is provenance-typed with per-entry control and a master switch (ChatGPT upgraded); and NO ambient agent chrome exists on any surface (anti-Copilot control passes).

## 18. Build plan

- **Dependencies (named):**
  - Persistence: migrations for `memory_entries`, `findings`, `activity_events`, `correction_events` (today the persistence layer is mostly contract-only; only `covenant_test_state` has a wired writer — snapshot §3). Tables key to org tenancy; `activity_events` append-only at the database level (no UPDATE/DELETE grants).
  - Engine wiring: real TestResults from the F-series spine before FindingsService can evaluate anything but fixtures.
  - The lit-row contract fix (U1-F1, `cross-cutting/provenance-lit-row-trace.md`) — **Ask does not ship citations until the wrong-row defect is fixed**; a citation that lights the wrong row is worse than no citation.
  - The rent-roll canonical reader (F3) for the flagship occupancy finding — until it lands, that finding class renders its BLOCKED state honestly (gap 7).
  - The orchestration spine for real run traces (until it exists, run detail renders only the acts that actually execute — never simulated steps).
  - Tenancy/roles for the scope walls (PMC client scoping; reviewer read-only).
- **Foundation work, in order:**
  1. **ActivityEvent store + write path** from every existing engine/agent code path (send gate, trend assembler, intake classifier when wired) — first because it retires the U3-F2 contradiction class structurally and gives every later feature its audit substrate.
  2. **Grounding read-layer** (`src/lib/covenant/agent/ask.ts`) over engine functions + sealed records + Documents + confirmed values — the unwired analyst-grounding substrate is the starting point (snapshot §3).
  3. **Numeral lint** in CI over agent templates + the runtime fail-closed gate (pipeline step 6).
  4. **Memory store + capture/reuse API**, then the inspector; **FindingsService + trigger evaluation**, then the cards.
- **Components to build first:** `AskCitation` + `RefusalCard` (the trust primitives — everything else quotes through them) → `QuietLog` re-point of existing feed components onto the one store (delete the fixture arrays) → `MemoryChip` → `FindingCard` → `RunDetail` → `MemoryInspector` → `RepeatErrorMetric`.
- **Vertical slice (the send-vertical pattern, one loan+period end to end):** Bexley Q2 from engine data: FindingsService evaluates the real occupancy TestResult → the 268/301 = 89.04% shortfall finding renders on Home §3 and Loan Detail with a drafted mitigant narrative → the user highlights the figure in Review → Ask answers grounded, with a working lit-row citation into the roll aggregation chain → disposition writes `finding.dispositioned` to the one log. One seam, real data, the entire agent story demonstrable in a single session.
- **Migration from fixture data:** AskPanel's scripted golden answer is deleted the day the grounded service answers the same question from engine data — the golden answer's content becomes acceptance test AGT-01's expected shape. Fixture activity feeds re-point to the store and their arrays are deleted in the same PR (no dual-source window). The hand-authored `deal_config` continues standing in for extraction until the Extraction brief's flow lands; Ask grounds on whatever confirmed-grade data exists and refuses beyond it — the refusal behavior IS the honest migration state.
- **Rollout/feature flags:** `agent.ask.grounded` · `agent.findings` · `agent.memory` · `agent.quietlog` · `agent.learning` — independent flags because the capabilities have independent dependencies; quiet log ships first (lowest risk, highest structural payoff), Ask citations ship only after U1-F1.
- **Proof artifacts required:** screen capture of the full lit-row citation walk on Bexley (highlight → Ask → citation → lit source row held); the two-period zero-re-asks run log; the overnight no-push cycle log with the one deadline-escalation push; repeat-error metric screenshot with a seeded downtrend and a seeded alarm; the type-level vocabulary test output proving "breach" unreachable for monitored rows.
- **Final gate:** ADJUST confirmed for AskPanel/HighlightToAsk only when the grounded service passes AGT-01 through AGT-04; each NEW service passes its acceptance set before the layer claims PASS. The three prohibitions — never certifies, never sends, never computes a shipping number — are verified structurally (no call path from any module in `src/lib/covenant/agent/` or `src/components/covenant/agent/` to the certify or send gates; a numeral without a value ref cannot compile), not by review promise.
