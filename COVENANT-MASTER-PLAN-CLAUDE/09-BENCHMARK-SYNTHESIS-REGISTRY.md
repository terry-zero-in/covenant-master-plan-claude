# 09 — BENCHMARK SYNTHESIS REGISTRY

Method: per the Dynamic Surface Benchmarking Doctrine (2026-08-03) — the seed registry was a discovery seed, never a whitelist. Six research sweeps ran on 2026-08-08 against current official documentation, help centers, changelogs, and product pages (research notes R1–R6, preserved in the working papers; per-surface take/adapt/reject tables live in each brief's §14). Every product below carries a **limited role**; nothing here grants a product influence beyond its role. Dynamically discovered (non-seed) references are marked ◆; anti-patterns are marked ✕.

Access note: this session's egress proxy blocked direct vendor-domain fetches; evidence was gathered via web search over the exact official pages cited (URLs verified current as of 2026-08-08). The downstream feasibility/adjudication session can re-fetch any cited page directly.

## 1. Registry by surface cluster

### Intake / Holding / Inbox (R1)

| Product | Limited role | Exact pattern taken | Covenant adaptation | Rejected |
|---|---|---|---|---|
| Linear Triage (linear.app/docs/triage) | Interaction | Four-verb one-key disposition; suggestion + visible reasoning + accept/dismiss; auto-apply at high confidence (2025-09 changelog) | Verbs become File / Supersede / Not ours / Hold; auto-apply = quiet filing, always logged | Personal-notification Inbox as the intake surface; triage rotations (the agent owns routing) |
| Ramp Bill Pay (support.ramp.com) | Workflow-state | Per-customer forwarding address; hash-level duplicate suppression; explicit unprocessable-reason taxonomy; machine-generated intake receipt (created/skipped + named reason) | Receipt goes to the quiet log per arrival; duplicate logic version-aware per loan+period+doc-type | "Everything becomes a draft for a human" — the agent finishes high-confidence filings |
| Rossum (rossum.ai KB) | Ontology | Queue lifecycle (To review/Confirmed/Postponed/Rejected…); automation dial Never/Confident/Always with per-field thresholds (default 0.975); duplicate → stop automation | The dial gates filing into the checklist, not export; Reject-with-reply becomes the chase email | Export as terminal state; per-queue silos |
| Instabase (docs.instabase.com) | Evidence-provenance | Only-failed-validations reach review; field↔source-region provenance; reclassify-then-re-extract gesture | Validations = package rules (period coverage, property↔loan match) | Review-manager/SLA workforce apparatus |
| Plain (help.plain.com) | IA | 3-state queue (Todo/Snoozed/Done) with reason-coded substates; auto-reopen on external activity | "Chased — awaiting sender" auto-reopens on reply/upload | Conversation-first framing; response SLAs on arrivals |
| ◆ Floify (help.floify.com) | Domain truth | Per-loan needs list (Owed→Pending Review→Accepted); rejection re-opens with reason; pre/post-deadline chase cadence with blackout dates | Direction inverts (borrower assembles for lenders); adds `waived` state | Notification blasts at every transition |
| ◆ Dext (help.dext.com) | Shell | Product-exclusive intake addresses; per-sender rules minted from one human resolution | Sender rules = learned sender→property/loan mappings with provenance | Mode complexity pushed onto senders |
| ◆ Ocrolus (ocrolus.com) | Workflow-state | Confidence+quality routing to machine-only vs human review, invisible to the requester; cross-source validation | HITL = the borrower's own team, in-product | Outsourced review bench; lender-side framing |
| ✕ Front (help.front.com) | Anti-pattern | — | — | Shared-mailbox model: human-touch-everything, archive-as-disposition, notification churn — the opposite of quiet filing |

### Extraction & Confirmation / Documents (R2)

| Product | Limited role | Exact pattern taken | Covenant adaptation | Rejected |
|---|---|---|---|---|
| Rossum validation screen | Interaction | Blue-proposed → green-human-validated bounding boxes; grey-tick provenance; click-value-jumps-to-field bidirectional link; Enter-to-next-unvalidated | The state machine for requirement/actuals confirmation; keyboard flow for once-per-loan setup | Threshold auto-export past first-time human confirmation |
| ABBYY Vantage/FineReader (docs.abbyy.com) | Workflow-state + the derivative model | "Text under the page image" searchable PDF (the exact Recreated·searchable precedent); staged review escalation; red-dot low-confidence marks | Two stages = analyst confirms, exceptions escalate | Character-level verification loops |
| Instabase | Interaction | Type-or-draw-a-box correction (every correction re-grounds to a region); auto-revalidation on edit; show-source-for-all-fields | Cell-level lighting for T-12 tables (deliberately exceeding Instabase's whole-table limit) | Table-level-only source lighting |
| Ironclad (support.ironcladapp.com) | IA / ontology | Record = document + typed properties; related-record families; "Needs verification" saved view; Views = saved filters+columns | Families = loan ↔ riders ↔ amendments ↔ period filings | Emailed-CSV extraction review (detached from source) |
| Box (support.box.com) | Interaction | Version stack under one identity; Make Current promotion; tombstoned deletes; metadata beside preview | Explicit typed new-version-vs-replacement question | Filename-collision version inference |
| Hebbia (hebbia.com) | Evidence-provenance | Citation-linked cells; click-through to exact page; citation preview peek | Every extracted value deep-links into DocView; hover-peek from the confirmation queue | Free-prose answer cells as stored records |
| Adobe Acrobat (helpx.adobe.com) | Evidence-provenance | The "suspect" contract: uncertain OCR keeps original pixels visible, guess never presented as truth | Suspect grammar → the "unreadable" typed verb | One-word-at-a-time modal loop |
| ◆ DocuSign Navigator | Workflow-state | Accept-or-edit per AI attribute; accepted-only data enters reporting | Per-attribute confirmation acts on loan terms | Attribute panes without page-level source lighting |
| ◆ Litera Kira (litera.com) | Domain truth | Extraction-links-to-source-text as the defensibility bar; highlight-to-train | Corrections quietly improve next-loan extraction | One-off deal-room framing |
| ✕ Google Document AI HITL (deprecated 2025) | Anti-pattern | — | — | Review bolted onto a detached labeling console outside the system of record — the platform itself abandoned it |

### Actuals / Review / Tie-out (R3)

| Product | Limited role | Exact pattern taken | Covenant adaptation | Rejected |
|---|---|---|---|---|
| Vanta (help.vanta.com) | Workflow-state | Graded urgency (attention-orange vs overdue-red); per-item SLAs with business-day math; evidence freshness statuses; approved evidence flips tests | Grading maps onto watch vocabulary; staleness as a first-class value state | Control/framework indirection layers |
| ◆ Numeric (numeric.io) | Workflow-state | Autosubmit-when-matched (clean recons complete themselves); transaction-level exception view; materiality-threshold-gated flux explanations | The exception-first Actuals law; thresholds decide which variances demand narrative | AI-drafted explanations as default certified content |
| FloQast (floqast.com) | Workflow-state — **the void-on-change precedent** | Strict Tie-Out Mode: sign-off only when tied out; data drift voids signatures and notifies every signer; trial-balance completeness check | Certify-only-when-tied-out; "no unmapped lines at certify" readiness check | Workbook-in-a-cloud-folder workpapers |
| ◆ DataSnipper (knowledge.datasnipper.com) | Evidence-provenance — **the lit-row precedent** | One-gesture value+source-region objects ("snips"); bidirectional cell↔region navigation; Validation-V/Exception-X marks; Document Matching templates for recurring periods; self-contained evidence pack | The generalized lit-row trace; per-line tie-out marks; auto-relink of unchanged T-12 lines next period | Excel as host; free-form snipping (Covenant constrains targets to normalized lines) |
| Puzzle (help.puzzle.io) | Interaction | Every statement figure is a door: drill to pre-filtered underlying transactions | Drill lands in a read-only evidence pane during review | Editable drill targets inside the review room |
| Modern Treasury (docs.moderntreasury.com) | Ontology | Expected-vs-actual as separate objects with an enum'd match state incl. provisional (`tentatively_reconciled`); humans see only the unmatched residue | Provisional tie-out state for early-arriving data | Payment-grade streaming cadence |
| Sigma (help.sigmacomputing.com) | Interaction | View-underlying-data at the exact granularity that fed the aggregate; drill as temporary view state | One fixed drill path (metric → inputs → lines → source) | Ad-hoc re-slicing beside the certify gate |
| ◆ Finley (docs.finleycms.com) | Domain truth | Agreement digitized into computed covenants + dated deliverables + gated requests; deliverable due-buckets | The three-object split validates Covenant's requirement/deadline/package separation | Lender-facing system-of-record framing |
| ◆ Setpoint (setpoint.io) | Domain truth | Certify-by-recompute: verification = independent recomputation from source; computed-vs-reported diff as the core artifact | The certify gate re-derives every package figure; diffs block | Agent-in-the-middle settlement roles |
| ✕ BankStride / nCino ticklers | Anti-pattern | (only: reminder cadence discipline) | — | Covenant-as-due-date + upload chute: no computation, no headroom, no provenance — the product Covenant exists to replace |

### Certify / Send / Reports (R4)

| Product | Limited role | Exact pattern taken | Covenant adaptation | Rejected |
|---|---|---|---|---|
| GitHub PR review + branch protection (docs.github.com) | Workflow-state — **the readiness + void model** | Required checks gate the act, each linking to detail; stale-review dismissal bound to content state; mandatory dismissal reason; merge as separate confirmed act; no-bypass framing | Readiness reasons link to blockers; certification pins the content hash; voids carry reasons in the timeline | Admin bypass lists |
| Modern Treasury | Workflow-state | Approval rules/chains; `needs_approval` locks content; review-as-role capacity recording; append-only ledger with correction-records | Capacity recorded on certifications; append-only cert/send stores | Auto-send after approval (banks are MT's second gate; Covenant's send is itself human) |
| DocuSign (support.docusign.com) | Interaction — the ceremony | Identity-bound authentication before the act; adopt→finish two-beat rhythm; Certificate of Completion audit artifact; tamper-evident seal | Typed attestation replaces signature adoption; the certification record as an exportable certificate | Envelope routing/field-placement machinery |
| Stripe Dashboard (docs.stripe.com) | IA | Object detail + append-only event timeline; filter-first chartless lists; explicit CSV export; irreversibility stated plainly | The Reports register + period timelines | Charts on operational registers |
| Carta (support.carta.com) | Workflow-state | Sign-before-send as un-disableable; separate holder acceptance; escalating reminders for stuck ceremonies | Reminders for certified-but-unsent near deadline; optional delivery receipt later | Multi-officer routing in v1 |
| Mercury (support.mercury.com) | Workflow-state | Threshold approval rules; separation-of-duties toggle (initiator ≠ approver) justified by audit | Preparer/certifier/sender as distinct recorded roles; SoD toggle for PMC engagements | Auto-fulfill-own-approval as default |
| ◆ GitHub Releases | Workflow-state | Draft→publish freeze; immutable assets; release attestation (verifiable record of tag+SHA+assets); Immutable badge | The certification hash chain; the frozen package revision | Notes-still-editable-after-publish (Covenant voids on narrative edits too) |
| ◆ Avalara Managed Returns (help.avalara.com) | Domain truth | The monthly approve-to-file cadence window; review against a specific artifact before approval | Deadline pressure as escalating urgency on humans | **Auto-approval at the deadline — the named hard rejection: Covenant never certifies or sends on a human's behalf** |
| ✕ NN/g confirmation-dialog research (nngroup.com) | Anti-pattern boundary | The ceremony budget: typed confirmations reserved for rare, dangerous acts | Exactly two heavy ceremonies, mutually distinct | "Are you sure?" dialogs anywhere else |

### Records / Book / Calendar (R5)

| Product | Limited role | Exact pattern taken | Covenant adaptation | Rejected |
|---|---|---|---|---|
| Attio (attio.com/help) | IA / ontology | Object→record-page projection; ≤6 highlight widgets + titled sections + activity tab; saved views with shared state + ephemeral filtering; top view = default landing | Loan Detail skeleton; the one saved-view mechanism | User-editable schema (Covenant's schema comes from documents) |
| Linear (linear.app/docs) | IA / interaction | View scoping personal→team→workspace; star-to-sidebar; URL-shareable views; snooze-until-time-or-activity; cycles as first-class rhythm pages | Periods as first-class rhythm; snooze semantics for obligations | Single-key accept/decline on obligations (lender obligations can't be "declined") |
| Stripe | IA | Filter-first registers; search grammar (negation, metadata, loose dates); detail = facts + event timeline + related objects | Scaled to 4–6 curated filter chips for a 2–50 loan book | Open-ended query builders as primary UI |
| Mercury | Visual craft / interaction | Header stats recompute against the active filter; "Needs review" paired state+filter with return path | Portfolio header stats as filter-bound mini-dashboards | Keyword-first navigation |
| Ramp | Workflow-state | Inbox as one queue across object types with count badge; readiness gating (only actionable items enter); Remind pushes the ball to who owes input | Your-move register admits only ready items; nudge on blocked items | Manager-chain approval routing |
| ◆ Karbon (help.karbonhq.com) | Workflow-state — the recurrence precedent | Repeating work auto-instantiated on schedule; task due dates as offsets; **deadline (external) vs due date (internal) as two fields**; time-triggered status promotion; My Week auto-promoting buckets | The due-rule engine; dual dates; agenda buckets Overdue/This week/Next 30/Later | Whole-inbox email triage ambitions |
| ◆ TaxDome (help.taxdome.com) | Interaction — the grid concession evidence | "Show due date only" toggle (vendor's own admission that duration bars pollute deadline views); calendar as a lens over the list, never the workspace; saved filter templates on the calendar | If a grid ever ships: due-date-only, secondary, never landing | Day/Week/Month as the primary workflow surface |
| Vanta | Workflow-state | Deadline-in-the-row urgency (orange/red); SLA rule changes apply to new items only (versioned deadline rules) | The obligations register needs no month grid; versioned due-rules | Deadline systems hidden in per-control settings |
| ◆ Finley / ◆ LoanBoss (loanboss.com) | Domain truth | Critical-date taxonomy: covenant test dates, extension notice windows, maturity events; lender-adjusted metric definitions stored per loan | Obligation types include notice windows; thresholds carry the lender's definition | LoanBoss's rates/hedging breadth |
| ✕ Google Calendar month grid | Anti-pattern | — | — | "+N more" truncation: deadlines cluster by construction (every loan's Q+45 lands the same fortnight); the grid hides workload exactly at crunch |
| Juniper Square (junipersquare.com) | Domain research only — never a positioning comparison | One connected graph, every surface a projection | Covenant's borrower-side graph mirror | Any outward comparison |

### Agent / Onboarding / Settings / Palette (R6)

| Product | Limited role | Exact pattern taken | Covenant adaptation | Rejected |
|---|---|---|---|---|
| Inngest (inngest.com/docs) | Workflow-state | Two-pane run anatomy (step timeline + selected-step evidence); Attempt-N retry badges | The quiet-log run detail view | CEL power-filters for borrowers |
| LangSmith (docs.langchain.com/langsmith) | Workflow-state — the correction precedent | Feedback anchored to the exact wrong step; corrections capture "should have been" | The ask-once memory write path | Token/latency metrics user-facing |
| Temporal (docs.temporal.io) | Ontology | Durable workflow waiting-on-a-named-human at zero compute; Event Groups collapse noise over a full log | Periods as durable waits; quiet-log rows collapse agent minutiae | Operator query languages |
| Trigger.dev (trigger.dev/docs) | IA | Root-runs-only by default with opt-in depth; bulk-action trails; NL→structured-filter chips | The quiet log shows outcomes, not plumbing | Payload-editing replay |
| Mercury onboarding | Workflow-state | Resumable sidebar-tracked setup; heavy asks staged late | The Covenant onboarding arc | Multi-day approval gating |
| Ramp setup guide | Workflow-state | 90-day persistent activation checklist; role-scoped expiring invites; scoped advisor seats | Setup guide survives until genuinely done; advisor seat = outside accountant who never certifies | Eager artifact provisioning |
| Stripe onboarding/settings/search | Workflow-state / IA | Requirements-hash computed checklists; test-mode sample data; settings behind grouped scopes; metadata-searchable dashboard | Checklist computed from what's actually missing; one flagged sample loan | Test/live mode chrome everywhere |
| GitHub settings | IA | Admin sections invisible (not disabled) to non-admins | Role-gated settings visibility | Multi-hop settings navigation |
| Linear settings + palette | IA / interaction | Two-door settings scoping; context-first palette ranking; deliberate redundancy (every action also a visible affordance) | The palette grammar's ranking; "?" shortcut map | Engineer-density default shortcuts |
| ◆ Raycast (manual.raycast.com) | Interaction | Computed results inline in the search field; parameterized quicklinks; frequency+recency ranking | "dscr {loan}" returns the engine figure with provenance before any navigation | OS-global hotkeys; alias sprawl |
| ◆ ChatGPT memory (help.openai.com) | Interaction — the memory inspector precedent | Central manage-memory page: per-entry edit/delete/history, master toggle; in-flow save visibility | Provenance-typed entries ("learned {date} from {who}" + originating run) replace prose summaries | Opaque cross-conversation synthesis |
| ◆ Digits (digits.com) | Workflow-state — the closest live agent-posture analog | Confidence-gated routing: auto-book the certain, queue the uncertain; corrections feed forward; queue-not-chatbox agent surface | The gate decides quiet-log vs proposal — never send | "95% automated" aggregate-autonomy framing in-product |
| ◆ HubSpot import (knowledge.hubspot.com) | Interaction | Map-columns page; first-100-row inline validation; in-tool value repair; row-scoped errors | Adds the undo-import HubSpot lacks (staged batch, explicit commit, one-click reversal) | Partial-success silent imports |
| ✕ Microsoft "Copilot everywhere" | Anti-pattern | The lesson: invoked AI is welcomed, imposed AI is despised | The quiet log is the counter-design | Sparkle buttons, ambient chrome, proactive popups for finished work |

## 2. Whole-product synthesis statement

The composed Covenant is original because no reference solves its actual problem — **a borrower certifying regulated truth to a lender on a deadline, prepared almost entirely by an agent**:

1. From the intake cluster it takes decision-grammar and confidence-gated quiet filing, but inverts the direction of every ancestor (Floify chases borrowers for lenders; Covenant chases the borrower's own document sources on the borrower's behalf).
2. From the close/audit cluster it takes exception-first review and one-gesture provenance, but fuses them with a deterministic engine none of those products have — DataSnipper's snips become structural (constrained to normalized lines), FloQast's strict tie-out becomes void-on-change on a content hash.
3. From the ceremony cluster it takes readiness-as-required-checks and identity-bound typed acts, but rejects every auto-approve/auto-send fallback those products tolerate — the human gate is the product's signature, not a compliance feature.
4. From the records cluster it takes the register/record-page/saved-view system, but the schema is extracted from executed loan documents and human-confirmed, never user-modeled.
5. From the agent cluster it takes run-transparency and correctable memory, but the agent's commit boundary sits one notch safer than every analog (Digits auto-books; Covenant's agent never touches a shipping number).
6. The monitored-vs-covenanted distinction and the shortfall/breach vocabulary exist in **no** reference — they are Covenant's own domain law, and the entire status system is built around them rather than retrofitted.

## 3. Challenger protocol (per doctrine §13 and the acceptance gates)

Each brief's §14 names its challenger set — three current products not used in its planning — and the post-build challenger audit files land in `docs/ia/<surface>/06-post-build-challenger-audit.md` in basis-v2 per the doctrine's required-outputs structure. The registry above is versioned evidence, not permanent law: `benchmark-registry.yml` carries the machine-readable form with `last_verified` dates, and any surface revisit re-runs discovery rather than trusting this table.
