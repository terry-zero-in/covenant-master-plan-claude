# ONBOARDING / SIGN-IN / IMPORT

## 1. Identity and verdict

- Product layer: frame (the door) + portfolio-floor entry. Three connected jobs on one brief: the sign-in page (first contact), the onboarding arc (zero → working book), and the CSV book-import surface (a UI over the existing import library that outlives onboarding).
- Route/address in the basis-v2 repository: `/sign-in` (EXISTS — fix in place); NEW under the `(covenant)` route group: `/onboarding` (setup-guide home), `/onboarding/org`, `/onboarding/book`, `/onboarding/book/upload`, `/onboarding/book/import`, `/onboarding/signers`, `/onboarding/intake`, `/onboarding/team`; the import surface additionally addressable post-onboarding at `/loans/import`; PROPOSED (decision D-6): `/demo` guarded read-only demo book. Components in `src/components/covenant/onboarding/`.
- Current build state: `ABSENT` (onboarding + import surface; snapshot §6.10) with `/sign-in` existing as a screen. The door is the benchmark's one BLOCK: a guest is bounced to Clerk or the Basis login, and the sign-in page's only live link exits to the Basis marketing funnel (snapshot §3; benchmark U12-F2; conflicts ledger C-7).
- Existing-surface verdict: `REBUILD` of the door (the DIRECTION's word), `UNBUILT` for the arc and import surface.
- Research tier: B.
- Primary users/roles: the founding owner or PMC principal (creates the org, becomes first admin); invited members (accept-invite path); the outside world (guests hitting the door — today they hit a wall).
- Frequency and session duration: once per org for the arc (one sitting — R6 research: Mercury reject of multi-day gating); the import surface recurs whenever the book grows; sign-in recurs every session.
- Error cost: P0 is a *law* violation, not a usability bug — the first-contact surface routes prospects into a different product's funnel (product-boundaries law; C-7). Inside the arc: a mis-imported book poisons every downstream surface (wrong loans → wrong checklists → wrong packages); an undone-able import is the single most dangerous absent affordance (R6 research: HubSpot — the undo it lacks).
- Success criterion, measurable: (1) zero non-Covenant links on `/sign-in`, enforced in CI forever; (2) a new org reaches a real your-move on Home — a first period open, with its checklist — in one sitting (≤15 minutes rehearsed, §17.12); (3) an imported book reverses in one click within its eligibility window; (4) the guide reflects true state at all times (computed, reopening on invalidation); (5) if D-6 approves, a guest reaches the Bexley review room read-only without an account.

## 2. User job and decisions

- Primary job-to-be-done: "Get my loan book into Covenant and reach the moment where the product is doing the work — without a form marathon, and without being able to wreck anything permanently while I learn."
- Decisions made here: org identity + mode (owner vs PMC); which book path (upload loan documents — the magic path — vs CSV bulk import); column mappings and row repairs (import); who certifies and who sends (signers); whether to invite the team now or later; when to forward the first lender email (activation).
- Questions the surface must answer in scan order: (1) where am I in setup and what's left? (2) what does Covenant need from me *right now*? (3) what happens when I finish this step? (4) can I stop and come back? (yes — resumable, always visible); (5) what did that import just create, and can I take it back?
- What the user should not have to decide here: covenant thresholds or test definitions (extraction proposes from the loan's own documents; confirmation happens on the Extraction & Confirmation surface); lender-form semantics; anything about periods (the engine opens them from confirmed due-rules, 03 §5); design or layout choices.
- Entry paths: `/sign-in` → create-org or accept-invite; a Ramp-style persistent Setup Guide card on Home re-enters the arc at the first incomplete step (R6 research: Ramp — the guide persists until genuinely done, not until first dismissal); `/loans` empty state → `/loans/import` or `/onboarding/book/upload`; palette ("import loans", "setup guide").
- Exit paths: every step exits to the Setup Guide; arc completion lands on Home with a real your-move (the DIRECTION's step 6); the import surface exits to `/loans` filtered to the created batch; sign-in exits only into Covenant surfaces (P0).
- Completion/advancement conditions: the requirements hash empties (R6 research: Stripe — the checklist is computed from what is actually missing, not from a hardcoded step list): org exists · ≥1 loan with a confirmed requirement schedule · certify and send grantees designated · intake address issued · first period open. Team invites are listed but never block completion.

## 3. Object and ownership model

| Object/action/status | Owned here? | Summary/link elsewhere | Address/identity | Notes |
|---|---:|---|---|---|
| The sign-in page and its link policy | YES | — | `/sign-in` | P0: links only Covenant surfaces (C-7 fix) |
| SetupGuide (the computed requirements checklist) | YES | Home renders the guide card until done (the ruled summary allowance) | `orgId` setup record | Computed, never hardcoded (R6: Stripe requirements hash) |
| ImportBatch (staged CSV batch: rows, mappings, validation, commit/undo) | YES | Loans shows "imported {date}" provenance chips on created loans | `batchId` | The undo HubSpot lacks (R6 research: HubSpot) |
| Org creation act | YES (the act) | Settings owns the Organization record thereafter (settings brief §3) | `orgId` | Mode (owner/PMC) set here |
| Loan records created by import/upload | NO — Loan Detail owns loans (02 §2) | This surface creates them in "setup needed" state and links out | `loanId` | Creation ≠ ownership; no loan editing here |
| Extraction proposals + confirmation (magic path) | NO — Extraction & Confirmation owns (02 §3) | The book step embeds/links that surface for the first loan | `/loans/[loanId]/setup` | First extraction experience framed here, executed there |
| Signer designation act | NO — Team & Roles owns grants (settings brief §3) | Step 3 embeds the grant act; guide shows done/not-done | member × scope | One owner: the settings grant record |
| Intake address issuance | NO — Settings owns the address (02 §3) | Step 4 embeds the mint act; displays + copy; "forward your next lender email here" | per-org address record | Covenant's own address, never shared with any other product |
| Team invites | NO — Team & Roles owns invites | Step 5 embeds InviteComposer | invite records | Optional step, never blocks |
| First period + checklist | NO — the reporting spine owns periods (02 §2) | Guide shows "first period opens" as an *outcome*, links to Home | `(loanId, period)` | Engine materializes from confirmed due-rules (03 §5) |
| Demo book mode (PROPOSED, D-6) | YES (the mode and its guards) | — | `/demo` | Read-only projection of the canon book; below |

No-double-homing boundary: onboarding is an *orchestrating* surface — it owns the guide, the batch, and the door, and it performs embedded acts whose records live with their owners (grants in Settings, loans in Loan Detail, address in Settings, periods on the spine). Nothing created here is editable here afterward; every step card links to the owning surface.

The door's link policy is itself an owned artifact: a declared allowlist (Covenant routes + Covenant legal pages) that the CI test (§17.1) enforces. Any future link added to `/sign-in` must extend the allowlist in the same commit — making the C-7 class of violation impossible to reintroduce without tripping a named check.

## 4. Data and semantic model

- Source facts: uploaded loan documents (magic path — Original bytes + hash on arrival, artifact law); the uploaded CSV file (kept verbatim as the batch's source artifact).
- Extracted values awaiting confirmation: the agent's proposed loan record + covenant/reporting schedule from uploaded documents, each field carrying its source region (owned and confirmed on Extraction & Confirmation; rendered here only as the step's progress).
- Confirmed values: committed import rows become Loan records in "setup needed" state (terms present, schedule unconfirmed); the org record; grants; the issued address.
- Deterministic outputs: the requirements hash (which setup items are `currently_due` — computed per org and per loan); CSV validation results (first-100-rows during mapping, full set at commit — R6 research: HubSpot); period materialization once a schedule confirms.
- Agent proposals/drafts: column-mapping proposals (file column → loan field, with confidence); the proposed loan record on the magic path. Proposals only — the human commits.
- Human decisions: path choice; mapping confirmations; row repairs; batch commit; undo; signer designation; invite issuance; the forward-your-next-email act (implicitly: sending the mail).
- Certified values: none — nothing on this surface can certify or send, structurally; the arc *configures who may later do both*.
- Versions/periods/packages: ImportBatch versions by re-validation; committed batches are immutable records (undo creates a reversal record, never deletes the batch history); no package grain here.
- Evidence/provenance: every created loan carries `created_by: import batch {batchId}` or `created_by: extraction from {docId}`; every batch row keeps its source line number; the guide's done-marks each link to the record that satisfied them.
- Permissions/read-only projections: only admins run the arc's workspace acts (org, signers, address); any member can view the guide; the import surface requires the admin role; the proposed `/demo` mode is a read-only projection for unauthenticated guests (D-6).
- Demo-mode data (D-6): the canon fixture book only (`book.ts` spine — snapshot §3: one fixture file so surfaces can never disagree), served through the same read paths as real data but against fixture identity — no real org's rows are ever reachable from an anonymous session, structurally (separate data source, not a filtered query).

Field groups and grain: SetupGuideItem (`orgId` × item: state missing/currently-due/done, satisfying record ref) · ImportBatch (`batchId`: file ref, mapping set, row states, committed-at, undone-at) · ImportRow (line no., parsed fields, errors[], repairs[], created `loanId?`) · MappingProposal (file column, target field, confidence, confirmed?) · the embedded objects (org, grant, address, invite, loan, period) at their owners' grain.

The requirements hash, item by item (the guide's exact computation — each item is a predicate over persisted state, with its satisfying record):

| Item | Predicate (`done` when…) | Satisfying record | Prerequisite (`locked` until…) |
|---|---|---|---|
| 1 Create org | Organization row exists with mode set | `orgId` | — |
| 2 Bring the book | ≥1 Loan row exists (any state) | first `loanId` / `batchId` | item 1 |
| 2′ Confirm a schedule (surfaced inside item 2's done-card) | ≥1 loan has a confirmed RequirementRecord set (02 §2) | `reqId` set | a loan exists |
| 3 Designate signers | ≥1 active certify grant AND ≥1 active send grant (may be one person) | grant records | item 1 |
| 4 Your intake address | Address record minted and active | address record | item 1 (independent of the book) |
| 5 Invite team | Never required — renders `optional`; `done` when ≥1 invite accepted | invite record | item 1 |
| 6 First period opens | ≥1 ReportingPeriod materialized `open` with a checklist | `(loanId, period)` | item 2′ (the engine opens it; no human act) |

Reopen rule: any predicate that later evaluates false (send grant revoked; the only loan undone) flips its item back to `currently-due` and the Home guide card returns — the hash is computed, never remembered (R6 research: Stripe).

## 5. State machine and exceptions

### 5.1 The door (sign-in)

| Transition | Trigger | Actor | Reversible | Audit event | Failure behavior |
|---|---|---|---|---|---|
| `anonymous` → `authenticating` | Credential/SSO submit | Human | n/a | — | Honest auth-provider error inline; no lockout theater |
| `authenticating` → `authenticated-no-org` | Auth success, no membership | Engine | sign-out | session event | — |
| `authenticated-no-org` → `authenticated-with-org` | Create-org act or invite acceptance | Human | org leave (support path) | `onboarding.org_created` / `settings.team.invite_accepted` | Expired invite: shows state + who can re-issue; never a dead end |
| any deep link, unauthenticated | URL hit | — | — | — | → `/sign-in` with destination preserved, restored post-auth |
| `authenticated-with-org` → Home or guide | Guide incomplete? | Engine | — | — | — |

P0 acceptance rides this machine: every link on `/sign-in` resolves inside Covenant (C-7; U12-F2); the state machine contains no edge that leaves the product.

### 5.2 The arc (per setup item, Stripe-style)

Each SetupGuideItem: `missing` → `currently-due` (its prerequisites met) → `done` (satisfying record exists) — recomputed from state, never checked off manually; an item whose satisfying record is later removed (e.g., the only send grant revoked) *reopens*, and the guide card returns to Home (R6 research: Ramp — persists until genuinely done).

### 5.3 The import batch

| Transition | Trigger | Actor | Prerequisites | Reversible | Audit event |
|---|---|---|---|---|---|
| `empty` → `file-staged` | Upload; parse succeeds (columns detected, encoding/delimiter resolved) | Human + engine | admin role | discard batch | `import.batch_staged` |
| `file-staged` → `mapping` | Mapping proposals computed | Agent proposes; engine validates first 100 rows live (R6 research: HubSpot) | — | re-upload supersedes (old file kept in history) | — |
| `mapping` → `validated` | All mapped columns confirmed; full-set validation run | Human confirms; engine validates | zero unconfirmed mappings | back to mapping freely | `import.batch_validated` (error counts) |
| `validated` → `committed` | Typed commit act — all-or-repair: every included row `clean | repaired`, or explicitly excluded; no partial-success silent imports (R6 reject) | Human | zero error rows included | via undo only | `import.batch_committed` (created loanIds) |
| `committed` → `undone` | One-click undo within eligibility | Human | eligibility below | terminal for the batch; mappings/repairs survive for re-commit | `import.batch_undone` (reversed loanIds) |
| `committed` → `permanent` | Eligibility lapses | Engine | — | per-loan archive on Loan Detail thereafter | — |

Undo eligibility: until any created loan has a confirmed requirement schedule, a filed document, or an open period with arrivals — after that, per-loan archive on Loan Detail is the path. Stated in the UI at commit time: "you can undo this import until you start working a loan."

### 5.4 Template-required states, covered

| State | Where it appears here |
|---|---|
| empty | `/loans` empty state routes here; the guide with zero items done; the import surface pre-file |
| awaiting-documents | The magic path after loan creation: the first period's checklist opens in Holding — linked, not owned (02 §3) |
| loading/processing | Document processing on upload (recreate-searchable derivative labeled per artifact law); CSV parse; validation runs |
| partial/incomplete | The guide mid-arc; a batch with error rows (row-scoped, never import-scoped — R6: HubSpot) |
| extracted/unconfirmed | The proposed loan record awaiting confirmation (rendered on Extraction & Confirmation) |
| stale/superseded | A re-uploaded CSV supersedes the staged file (old file kept as batch history); a reopened guide item |
| low confidence | Mapping proposals below threshold render unconfirmed with the confidence shown; commit is blocked until every mapped column is confirmed |
| conflict | An import row matching an existing loan (same lender + loan identifier) → explicit resolve: skip / update-nothing-create-nothing / create-anyway (flagged duplicate) — never silent merge |
| failed tie-out | N/A here — no package math; control-total checks on the CSV (row counts, UPB sums per column mapping) render as validation errors instead |
| watch/shortfall/breach | Never rendered here (vocabulary law: verdicts live on covenant surfaces; a fresh book has no verdicts yet) |
| permission denied | Non-admin hitting the arc's workspace steps or `/loans/import` → honest 403 naming the role; guests → `/sign-in` |
| read-only | The proposed `/demo` mode: the entire shell read-only (D-6, §below); a completed guide is a read-only record |
| blocked/gated | Commit blocked by unconfirmed mappings or error rows (fail-closed with named reasons); intake step blocked until the pipeline exists (honest "not yet available" rather than a fake address) |
| certified (void-on-change) | Not reachable from this surface — stated so nobody wires a shortcut |
| sent/sealed | Not reachable from this surface |
| recovery/undo | The batch undo (§5.3 — the one-click reversal HubSpot lacks); org creation is not undoable from here (support path); grants/address revert via Settings history |

Audit-event taxonomy for this surface (one ActivityEvent store, ticket-ready names): `onboarding.org_created` · `onboarding.guide_item_done|reopened` (item, satisfying/invalidating record) · `import.batch_staged|validated|committed|undone` · `import.row_repaired` (line, old→new) · `import.row_excluded` · `import.duplicate_resolved` (choice) · `onboarding.magic_upload_received` (docId, hash) · `onboarding.activation_first_arrival` (arrival ref) — plus the embedded acts' own events at their owners (`settings.team.grant_changed`, `settings.intake.address_minted`, invite events).

## 6. Engine / Agent / Human / Gate / Quiet Log

Five-lane table (extends 04 §1's "Onboarding & import" row to the full surface):

| Chapter | ENGINE (deterministic) | AGENT (prepares/proposes) | HUMAN (typed decisions) | GATE (interrupts) | QUIET LOG |
|---|---|---|---|---|---|
| Door | Auth session; org scoping; deep-link preservation | — | Sign in; create org; accept invite | Auth itself | Org created; member joined |
| Setup guide | Requirements-hash computation (what is `currently_due` per org/loan — R6: Stripe); reopen-on-invalidation | Suggests the next step in dependency order ("designate a sender so the send gate has an actor") | Do the steps | An arc step whose embedded act is blocked (e.g., zero-eligible guard from the settings brief) | Each item done/reopened |
| Magic path (upload) | Hashing + artifact registration; processing status; fail-closed on unknown form (04 §1 requirement-extraction row) | Proposes the loan record + covenant/reporting schedule with source regions (executed on Extraction & Confirmation) | Confirm the proposal — the first extraction experience | Unreadable/unknown documents block with named reasons | Upload, processing, proposal events |
| CSV import | Parse; per-row validation; control totals; duplicate detection; commit transaction; undo reversal | Proposes column mappings with confidence; proposes row repairs (e.g., date-format normalization) as accept-able suggestions | Confirm mappings; repair/exclude rows; commit; undo | Commit blocked on unconfirmed mappings/error rows | Batch staged/validated/committed/undone with row counts |
| Signers / intake / team | Grant persistence + eligibility (settings vertical); address minting; invite expiry | — | Designate certifier + sender; issue address; invite | Zero-eligible guard | Grant/mint/invite events |
| Activation | First-arrival detection on the new address → flips the waiting card | Recognizes the forwarded email's sender + content (03 §1 steps 1–2) | Forward the email (outside the product); resolve routing only if unrecognized | Unroutable first arrival (a real gate — the user is watching this one) | Arrival + recognition logged; the activation moment renders in place, not as a push |

Lane invariants (04 §2): the agent proposes mappings and loan records but commits nothing; no number computed here ships anywhere (imports create *records*, not package figures); the two dangerous verbs (certify/send) do not exist on this surface in any form. One further invariant specific to this brief: the arc never fabricates progress — a step blocked by missing machinery (the intake pipeline pre-gap-1) renders blocked-honest, exactly as a fail-closed computation would (04 §2.5); fixture-era "pretend it works" is the anti-pattern the whole arc exists to retire.

## 7. Information hierarchy

1. Page/frame header: pre-org, a minimal Covenant-only header (wordmark, sign-out) — no rail, nothing to navigate yet; post-org-creation, the full CovenantShell with the guide as the work area.
2. Decision/status summary: the setup-guide sidebar — steps in dependency order with done/current/locked states (R6 research: Mercury — the persistent progress sidebar you can leave and resume).
3. Primary work region: the current step's pane (form, dropzone, mapping table, address card).
4. Secondary context: per-step consequence lines ("confirming this schedule opens your first period, due {computed date}").
5. Evidence/proof: mapping rows show source column samples; proposed loan fields show source regions (via the embedded extraction surface); batch rows link to their CSV line.
6. Actions: one primary action per step (Mercury's momentum pattern); commit/undo on the batch; copy on the address.
7. Activity/history: batch history (staged/committed/undone); the guide's done-marks with links to satisfying records.

Scan order differs by sub-surface:

- The door: wordmark → auth form → the two secondary links → legal footer. Nothing else exists.
- The arc: sidebar progress → current step's ask → consequence → primary action.
- The import surface (standalone at `/loans/import`): file identity → mapping state → validation state → commit bar; the guide sidebar is absent post-onboarding (the surface stands alone).

Absent by design: any covenant verdict, any chart, any package content, any marketing copy — the door and the arc are working surfaces, not a funnel; no cross-product link of any kind, anywhere (product-boundaries law).

## 8. Page anatomy and regions

### 8.1 Sign-in (`/sign-in`) — P0 fix, exact anatomy

- One centered column (max 400px) on the app background: Covenant wordmark → auth form (email + SSO options per the auth provider) → two secondary links: "Create your organization" (→ post-auth `/onboarding/org`) and — only if Terry approves D-6 — "Explore the demo book" (→ `/demo`). Footer: terms/privacy (Covenant's own).
- REMOVED: the link into the Basis marketing funnel — the page's only live link today (snapshot §3; U12-F2; C-7). The fix is a deletion plus a link-policy test (§17.1); sign-in links only Covenant surfaces, with zero cross-product references in copy, links, or metadata.
- Why this container: a single-purpose page, no shell — the shell requires an org context.

### 8.2 Onboarding arc (`/onboarding/*`)

| Region | Purpose / content | Persistence | Interaction | Min size | Collapse behavior | Why |
|---|---|---|---|---|---|---|
| Setup-guide sidebar (left) | Steps in dependency order: 1 create org · 2 bring the book · 3 designate signers · 4 your intake address · 5 invite team (optional) · 6 first period opens (outcome); each with state dot + done-link | pinned | Click any unlocked step; resume lands on the first `currently-due` | 260px | <1280px: horizontal stepper above content | Mercury's resumable sidebar (R6) — progress must be visible from every step |
| Step pane (the one big pane) | The current step's work | persistent | Per step | 720px | scrolls | Pane-model law |
| Consequence footer | What completing this step causes | pinned within pane | — | — | — | The arc teaches the product's causality as it runs |

Consequence-footer copy per step (the exact teaching lines — engine-computed values interpolated, never invented):

- Step 1: "Your organization is the boundary — every document, permission, and send record keys to it."
- Step 2: "Each loan gets its own reporting schedule from its own documents."
- Step 3: "Covenant's gates will check these names at certify and send time."
- Step 4: "Anything sent to this address lands in your Intake queue — recognized, classified, and held to the right loan's checklist."
- Step 5: "Invites expire in {N} days; roles limit what each person can touch."
- Step 6: "{Loan}'s {period} is open — {M} items expected, due {computed date}."

Step panes, exactly:

- **Step 1 — Create org** (`/onboarding/org`): name; mode choice (owner vs PMC — sets the tenancy shape, 02 §1); PMC adds first-client name. Pre-filled wherever the invite/auth already knows a value (R6: Mercury pre-fill).
- **Step 2 — Bring the book** (`/onboarding/book`): the two-path chooser, presented as equals with honest guidance: "Have the loan documents? Upload them and Covenant sets the loan up from the source" (→ `/onboarding/book/upload`) vs "Have a loan list? Import it and attach documents later" (→ `/onboarding/book/import`). Paths compose: import the book, then upload documents per loan later.
- **Step 2a — Upload path (the magic path)** (`/onboarding/book/upload`): full-pane dropzone → processing list per file (Original registered → Recreated-searchable label appears — the first gasp moment, snapshot §5) → "Covenant proposes: {loan record} + {N} covenant/reporting requirements" → embedded Extraction & Confirmation view for the confirm act (owned there; 02 §3) → created loan card with "setup needed → confirmed" state and the computed first due date. Multi-file behavior: files group by proposed loan (the agent clusters a dropped agreement + riders + statements to one proposal); a second loan's documents start a second proposal card — the path handles one-loan-first gracefully but never caps the drop. Fail-closed: an unknown form blocks that proposal with the missing definition named (04 §1 requirement-extraction row); the other proposals proceed.
- **Step 2b — CSV import path** (`/onboarding/book/import`, same surface as `/loans/import`): §8.3.
- **Step 3 — Designate signers** (`/onboarding/signers`): two picks — who certifies, who sends (may be the same person; separate acts per 03 §2 gates 2–3) — executing the Team & Roles grant act (owned by Settings; the gap-4 grant model from day one). Consequence line: "Covenant's gates will check these names at certify and send time."
- **Step 4 — Your intake address** (`/onboarding/intake`): the mint act → IntakeAddressCard (settings brief §11) with copy button → the activation ask, verbatim framing: **"Forward your next lender email here — Covenant takes it from there."** → a live waiting card ("watching for your first arrival…") that flips in place to the recognized arrival with its classification when the first email lands (the activation moment — R6 research: Mercury's "fund the account" analog, adapted per R6 to "first document arrives at your intake address").
- **Step 5 — Invite team** (`/onboarding/team`): InviteComposer (settings brief §11); optional, skippable, never blocks.
- **Step 6 — First period opens** (outcome card, not a form): renders the engine-materialized first ReportingPeriod + its checklist and lands the user on Home with a real your-move (03 §4). The guide collapses to the Home card, which persists until every item is genuinely done (R6: Ramp).

### 8.3 CSV import surface (`/loans/import`) — anatomy

- File stage: dropzone + parsed preview (first rows, detected delimiter/encoding); the file is kept as the batch's source artifact; a downloadable CSV template with the target field set.
- The template's target fields (mirroring the loan master record, 02 §2 — no invented fields): lender name · servicer name · loan identifier · program/form label (free text; e.g. "Fannie 6001.NR" — the *form* semantics come later from the documents, never from the CSV) · UPB · rate structure (fixed/floating + rate) · IO/amortization note · maturity date · property name (outward) · property address · unit count · client (PMC mode only; must match an existing Client or the row errors — no client auto-creation from a CSV). Required minimum per row: lender, loan identifier, UPB, property name; everything else may arrive later from documents (the row lands "setup needed" either way — the CSV boards the book, the documents make it real).
- Map-columns table (the HubSpot pattern, R6): one row per file column — file column name, sample values (3), proposed target field with confidence, confirm control, "create custom field" deliberately ABSENT (loan fields are schema-fixed; a column with no target maps to "ignored," visible and reversible — reject of HubSpot's create-property-inline: covenant loan records are not a CRM).
- Validation panel: runs on the first 100 rows live during mapping; full set at validate; errors are row-scoped with hover detail and in-tool repair (edit the value in place; the repair is logged to the row, the file untouched) — R6: HubSpot take.
- Row-results register (shared grid): line no., parsed loan (lender, identifier, UPB, rate, maturity, property), state chip (clean/repaired/error/excluded/duplicate), error detail.
- Commit bar: "{N} loans will be created in setup-needed state" → typed commit → per-row results → "Undo this import" affordance with its eligibility window stated (§5.3) — the undo HubSpot lacks (R6).

### 8.4 Demo book (`/demo`, PROPOSED — decision D-6 for Terry)

- Proposal: a guarded read-only mode mounting CovenantShell over the canon demo book (Bexley + Westbrook Flats + the five fictional peers) for unauthenticated guests — fixing the outward door's BLOCK (a guest today can reach nothing; snapshot §3 benchmark verdict).
- Guards, exactly: read-only projection (every mutation affordance renders disabled-with-reason "demo book — sign in to work your own"); certify/send ceremonies render their gates honestly but cannot execute; no intake address exists in demo; no exports; a persistent "Demo book — sample data" banner chip in the header; no resident-level data anywhere in the fixtures (names law); session is anonymous and stateless (no demo accounts to leak).
- Demo surface scope, exactly (what a guest can reach):
  - INCLUDED: Home, Loans + Bexley Loan Detail, the Review Room over Bexley's period (the provenance lit-row and the 268/301 = 89.04% shortfall against the monitored 90% floor rendering with correct vocabulary — canon), the Composer and Certificate views read-only (the gates visibly disabled with their honest reasons — the trust hierarchy *demonstrated*, not just claimed), Reports and Documents registers.
  - EXCLUDED: Settings (both doors), Intake (no address exists), the import surface, Ask (no grounded engine for anonymous scope in v1), and every write path — excluded at the route level, not hidden-but-reachable.
- Why propose it: the demo book demonstrates the review-room provenance mechanics — the product's money shot — without a sign-up wall, and Stripe's test-mode precedent shows fictitious-data exploration converts (R6 research: Stripe — sandboxes with fictitious companies; adapted to one clearly flagged sample book, rejecting test/live mode chrome).
- Existing-law alternative (presented per the kit): keep the door closed — sign-in only, no guest surface; the BLOCK verdict then stands as accepted posture. **Terry decides: D-6.**

## 9. Co-visibility matrix

| Datum/surface A | Datum/surface B | Must be simultaneous? | User decision enabled | Default topology | Fallback |
|---|---|---:|---|---|---|
| Setup-guide progress | Current step pane | YES | "Where am I / what's left" while working | Sidebar + pane | Horizontal stepper <1280px |
| File column + samples | Proposed target field + confidence | YES | The mapping confirm | Same row of the map table | — |
| Row error | The offending value, editable | YES | In-tool repair without re-upload | Row expands in place | — |
| Commit count | Undo eligibility statement | YES | Informed commit | Commit bar | — |
| Proposed loan field (magic path) | Its source region in the document | YES | The confirm act (lit-row law — proof beside the claim) | The embedded extraction surface's split | Per that brief |
| Intake address | The activation waiting card | YES | "Forward your next lender email here" needs the address in view | Same step pane, stacked | — |
| Invite step | Signers already designated | NO | Link suffices ("{name} certifies · {name} sends") | Summary chip | — |
| Validation error class | Its member rows | YES | Repair-by-class efficiency | Class expands to rows in the panel | — |
| Duplicate row | The existing loan it collides with | YES | The resolve choice needs both in view | Side-by-side within the row expansion | — |
| Guide item done-mark | Its satisfying record | NO | Link suffices | Done-link | — |
| Demo banner (D-6) | Every demo screen | YES | The guest always knows it's sample data | Persistent header chip | — |

## 10. Layouts and viewport behavior

| Viewport | Sidebar | Step pane / import surface | Notes |
|---|---|---|---|
| 1440px | 260px | ~1000px pane; map table full-width | validation panel stacks under the map table |
| 1728px | 280px | pane max 1180px; map table + validation side-by-side (interior columns, not work windows) | |
| 2048px | 280px | capped 1280px; extra ground open | dropzones never stretch beyond 900px |
| <1280px | horizontal stepper | single column; map table scrolls horizontally inside the pane with an explicit "N columns" chip | no silent compression |

- Default pane topology: one work window throughout; the arc never splits (no decision here needs side-by-side beyond what single panes with interior columns provide); the embedded extraction confirm follows its own brief's topology.
- Focus behavior: each step autofocuses its primary control; the guide preserves position across sessions (resumable — R6: Mercury, "log out and resume exactly where you left off").
- Compare behavior: none.
- Proof/source behavior: mapping samples inline; extraction proof per the embedded surface; batch rows link to CSV lines.
- Minimum viable dimensions: 1152×720 (frame law, 08 §9); below it, the import surface remains usable (registers law) while the magic path shows the "larger window required" state only on the embedded extraction split.
- Tab stacking/replacement: not applicable (single window).
- No silent compression: sidebar→stepper and table-column chips are the two explicit degradations.

## 11. Components and exact anatomy

- **CovenantShell** — REUSE (`src/components/covenant/CovenantShell.tsx`) once an org exists; the pre-org door and arc use a minimal header variant (NEW: `OnboardingFrame` — wordmark, step context, sign-out).
- **SetupGuide** — NEW. Parts: step list with state dots (done/current/locked), per-step done-link to the satisfying record, computed from the requirements hash (never a manual checklist); renders as the sidebar during the arc and as the persistent Home card after (R6: Ramp's 90-day Setup Guide — persists until genuinely done; reopens on invalidation).
- **StepPane scaffold** — NEW. Title, one-line promise, the work, consequence footer, single primary action.
- **DropZone** — NEW (shared with Documents' importer when that lands). Drag/browse, per-file progress, artifact-state chips ("Original registered · hash {…}" → "Recreated · searchable" — labels per the artifact law).
- **ProcessingList** — NEW. One row per uploaded file: name, type classification with confidence, state, "view proposal" link when extraction proposes.
- **MapColumnsTable** — NEW on the shared grid (uniform rows, open-not-boxed). Columns: file column, 3 sample values (Geist Mono), proposed target field + confidence chip, confirm control, ignore toggle. Inline first-100-row validation badge per row (R6: HubSpot take).
- **ValidationPanel** — NEW. Error classes with counts (unparseable value, missing required field, duplicate loan, control-total mismatch), each expanding to its rows.
- **RowResultsRegister** — NEW on the shared grid. Line no., parsed fields, state chip, error detail, in-tool repair (inline edit, logged), exclude toggle.
- **CommitBar** — NEW. Creation count, typed commit act, post-commit result summary, **UndoImportControl** (one-click reversal + eligibility statement — the affordance HubSpot lacks, R6).
- **IntakeAddressCard** — REUSE from the settings brief (§11); here with the activation ask and the live waiting card (**ActivationCard**, NEW: watching state → flips to the recognized first arrival with classification + link into Intake).
- **InviteComposer** — REUSE (settings brief §11; R6: Ramp expiring role-scoped invites).
- **SignerDesignation** — NEW thin wrapper over the Team & Roles grant act: two person-pickers (certifies / sends), eligibility preview, typed confirm (the act itself is the settings-owned grant).
- **DemoBanner** — NEW (D-6 only). Persistent header chip "Demo book — sample data".
- **CountBadge** — REUSE for guide remaining-count on the Home card.
- **CommandPalette** — REUSE; registers "Setup guide", "Import loans", "Upload loan documents", "Copy intake address" (once minted — a computed-result row per the palette grammar, 08 §5).
- Empty/error/recovery object — REUSE app-wide pattern; the import surface's error rows are its recovery objects. The empty states this brief owns, exactly:
  - `/loans` empty (no loans): "Bring your book" card with both paths — the arc's step 2 rendered in place.
  - `/documents` empty: "Documents arrive by upload or your intake address" with the address card (once minted) — a summary of the Settings-owned record, linked (02 §3).
  - `/loans/import` empty: the dropzone + template download.
  - The guide with everything done: the Home card retires itself (Ramp's guide ends when genuinely done — R6).

## 12. Interaction specification

- Selection: row focus in the map table and results register; multi-select for bulk exclude of error rows.
- Hover: sample values expand; error chips show detail; guide steps show their satisfying record.
- Focus: each step autofocuses; visible accent-family ring everywhere.
- Keyboard: full arc completable keyboard-only (dropzones accept browse; pickers are comboboxes); `⌘K` from any step ("skip to invite team", "import loans"); Esc backs out of a step to the guide without losing state; G-chords inactive pre-org, active once the shell mounts.
- Editing and validation: mapping confirms are per-column explicit acts; row repair is inline edit with the original value preserved and shown struck-through; validation re-runs on repair, live. Field validation rules (deterministic, engine-owned): UPB/figures parse as currency with thousands tolerance; dates parse against common formats with the resolved format shown per column (never guessed silently per cell — one format per column, confirmable); rate parses as percent or decimal with the interpretation displayed ("4.17" → 4.17%); the PMC client column must match an existing Client exactly (no fuzzy client matching — a wrong client scope is a tenancy breach, not a typo).
- Bulk action: bulk-exclude error rows; bulk-accept high-confidence mappings ("confirm all ≥ high confidence" — one act, logged as one event with the column list).
- Undo/recovery: the batch undo (§5.3) — reverses every created loan and their guide effects in one transaction, writes `import.batch_undone`, and returns the surface to `validated`; repairs and mappings survive for a corrected re-commit. Where undo is impossible by design: after a created loan is worked (confirmed schedule/filed doc/arrivals) — the commit bar said so up front.
- Sorting/filtering: results register filters by state (error/repaired/clean/excluded/duplicate); sort by line number default.
- Drill-down and return path: a created loan card → `/loans/[loanId]` → back returns to the batch results; guide done-links → owning surfaces → the Home card remains the return anchor.
- Source-linked selection (lit-row): magic-path proposal fields light their source regions in the embedded extraction view (the app-wide contract, 06 §1); batch rows highlight their CSV source line in a file preview strip.
- Save/persistence: everything persists continuously (resumable arc — R6: Mercury); the CSV file and all repairs survive sign-out; commit and undo are the only transactional acts.
- Collaboration/commenting: none — the arc is single-admin work; invites bring the team in at the end.

## 13. Visual craft direction

- Typography roles: step titles 20px/600 (the arc is the one place Covenant is allowed a touch of welcome); pane body 13px/400; consequence lines 12px on the mid gray rung; the activation ask set at 15px/500 — it is the sentence the whole arc walks toward.
- Financial-number treatment: every parsed figure (UPB, rate) in Geist Mono `tabular-nums slashed-zero` from the first sample cell — the book looks like Covenant from the first minute (canon example rendering: `$15,232,500 · 4.17%`).
- Spacing rhythm: 8px base; step panes on 32px vertical rhythm (roomier than working surfaces — one decision per screenful); registers at the standard 40px uniform rows.
- Density: comfortable, deliberately; the import register honors the user's density preference once set.
- Open ground vs earned boundaries: no boxed steps — the sidebar separates by whitespace + hairline; dropzones are dashed-hairline regions on a gray rung, not filled cards.
- Dividers/elevation: flat; the typed commit confirm is the only elevated element.
- Semantic color: accent family for progress dots, confirmed mappings, and the primary action (#7189FF; hover #8EA1FF; active #6078F4; tint #A9B5FF for the waiting/activation card ground; on-accent #0B1020); error rows use the app's standard error treatment on the gray ladder — no verdict colors (no covenant verdicts exist here, vocabulary law).
- Certified-sheet treatment: not touched — the arc never renders the certified sheet; the six ruled hexes do not appear (the certificate surface owns that paper).
- Focus/selected/hover: Covenant's own ruled selected-state tokens (Ruling-J boundary); guide current-step marked by accent dot + weight, never color alone.
- Chart style: no charts (nothing here out-encodes text — doctrine-honest).
- Motion: step transitions 150ms slide within reduced-motion respect; the activation card's flip (waiting → first arrival) is the arc's one earned animation — a single crossfade, no confetti, no celebration chrome.
- Long-session ergonomics: n/a — the arc is one sitting by design; the import register supports long files via virtualization, not visual density.

Token application map (consuming ruled tokens only — zero new values):

- Progress/confirmed/primary action: `--acc #7189FF` family (`--accH` hover, `--accA` active, `--accOn` on fills).
- The activation/waiting card ground: `--accT #A9B5FF` at low emphasis — the one tinted region in the arc.
- Dropzones, hairlines, skeletons, sample text: adjacent gray-ladder rungs per the shipped `covenant-tokens.css` (canvas-readback verification per snapshot §4's oklch caution).
- Error rows: the app's standard error treatment on the ladder — explicitly not the verdict palette (no verdicts exist here).
- Certified-sheet hexes (#E7EBF8 / #DDE4FB / #B3BCDA / #33419D / #5265C5 / #121459): not consumed anywhere on this brief — listed to make the prohibition checkable.

## 14. Benchmark research and synthesis

| Product | Limited role | Official evidence | Exact pattern to take | Covenant adaptation | What to reject | Why this reference beats alternatives |
|---|---|---|---|---|---|---|
| Stripe | Requirements-driven checklist + activation framing | https://docs.stripe.com/connect/api-onboarding · https://docs.stripe.com/stripe-apps/patterns/activation-flow | Inspect the `requirements` hash and collect only what is `currently_due` — the checklist is computed from missing requirements, not hardcoded steps | SetupGuide computed per org/loan (no docs? no signer? no address?), self-updating and reopening on invalidation | Payments-grade test/live mode chrome everywhere | The only benchmark whose checklist is *state*, not a script — exactly the anti-drift property a compliance setup needs (R6 research: Stripe) |
| Mercury | Resumable flow structure | https://support.mercury.com/hc/en-us/sections/43281880835860-Onboarding-Guides · https://support.mercury.com/hc/en-us/articles/35621290252052-Application-tips | Persistent progress sidebar; log out and resume exactly where you left off; pre-filled forms; heavy asks staged late | The arc's sidebar + continuous persistence; loan book now, lender specifics later; activation = first document at the intake address (Mercury's "fund the account" analog, adapted per R6) | Multi-day approval gating | The strongest live example of a finance product reaching a working state in one sitting (R6 research: Mercury) |
| Ramp | Persistent setup guide + invites | https://support.ramp.com/hc/en-us/articles/1500002006322-Getting-started-as-an-Admin | The in-product Setup Guide persisting ~90 days, task-shaped in dependency order; role-scoped expiring invites | The Home guide card persisting until genuinely done; InviteComposer expiry defaults | Eager artifact provisioning before the book exists | Proves the guide must outlive first-run — Covenant's arc ends at "first period opens," not at signup (R6 research: Ramp) |
| HubSpot | The CSV import wizard | https://knowledge.hubspot.com/import-and-export/understand-the-import-tool · https://knowledge.hubspot.com/import-and-export/troubleshoot-import-errors | Map-columns page; validation on the first 100 rows during mapping; row-scoped errors; in-tool value repair without re-upload | All of it, plus the **undo import** HubSpot lacks: staged batch, explicit all-or-repair commit, one-click reversal (R6 research: HubSpot) | Create-property-inline (loan schema is fixed); partial-success silent imports | The most battle-tested import UI in SaaS, with its one documented gap turned into Covenant's differentiator |
| Stripe (test mode) | Pre-data explorability precedent for D-6 | https://docs.stripe.com/connect/testing-verification | Fictitious-company sandboxes let every surface render before real data exists | The proposed `/demo` read-only canon book — one clearly flagged sample book, no mode toggle | Test/live dual-mode chrome | The nearest legitimate precedent for a guest-viewable working product without fake "trial" accounts (R6 research: Stripe) |

Synthesis: Stripe supplies the computed checklist, Mercury the resumable spine, Ramp the persistence horizon, HubSpot the import mechanics — and the result is original because the arc's destination exists in none of them: the checklist items are *compliance* facts (a confirmed covenant schedule, a designated certifier, a designated sender, a minted intake address), the import commits a *loan book* whose corruption would poison lender-facing packages (hence the all-or-repair commit and the undo none of the benchmarks needed this badly), and the activation moment is not funding an account but **forwarding a lender email and watching Covenant recognize it** — the product's entire thesis, delivered as the last step of setup. The door itself carries the plan's one law-level fix: first contact links only Covenant.

## 15. Domain references

Loan-servicing and financial-close products are consulted for terminology and expected data only: servicer onboarding packets and new-loan boarding checklists (the "loan boarding" notion — lender, loan identifier, UPB, rate structure, maturity, property — is the domain-correct field set for the CSV template, matching the loan master record in 02 §2), and the document-request lists behind agency reporting (evidence: SLOT-3 §8.02(b)'s enumerated deliverables are exactly what the first period's checklist will demand). Domain authority does not equal visual authority: no boarding portal governs a pixel here. Covenant semantics — what a requirement is, when a period opens, who may certify — come from the loan documents and Terry, never from any referenced product.

## 16. Accessibility, performance, and safety

- WCAG contrast and focus: AA on all used rungs; visible focus ring; the demo banner (D-6) meets contrast on its tint ground.
- Door security posture: no user enumeration (identical response for unknown vs known emails); rate limiting on auth attempts; invite tokens single-use and expiring; deep-link preservation never carries credentials in the URL.
- Keyboard completeness: the entire arc and the entire import (including repair and undo) keyboard-only completable; dropzones have browse equivalents; the map table navigates as a grid (arrows between cells, Enter confirms the focused mapping, `e` opens repair on a focused error row).
- Screen-reader semantics: the guide is a labeled list with state announcements ("step 3 of 6, current"); the map table announces proposal confidence; validation errors are associated with their cells; the activation card announces its flip.
- Table virtualization/large data: the results register virtualizes (a 2–50 loan book is trivial, but PMC books and future CSV re-imports may run long); validation is streamed with a progress readout.
- Loading and latency feedback: per-file processing states; validation progress; commit shows a transactional pending state — never optimistic (a half-created book must be impossible; the commit is one transaction).
- Destructive action confirmation: commit (creates) and undo (reverses) are both typed acts; undo's eligibility and consequences are stated before commit, not discovered after.
- Certify and external-send safety: structurally out of reach — no certify or send control exists on any onboarding/import/demo screen; signer designation only writes grants that the real gates check later (03 §2).
- Source immutability: uploaded originals immutable with hashes (artifact law); the CSV source file immutable per batch; repairs are logged deltas, never file edits.
- Auditability: every batch transition, guide completion, grant, mint, and invite is an ActivityEvent; the undo writes a reversal event naming every reversed loan.

## 17. Acceptance tests and fixtures

Fixtures: the Calloway Park document kit for the magic path (evidence: SLOT-3 loan agreement Form 6001.NR + riders 6220/6241; SLOT-1 T-12; the servicer forms — the executed forms' flattened zero-text-layer scans exercise the Recreated-searchable label); a 7-row CSV of the canon book (Bexley `$15,232,500 · 4.17% · 301 units`, Westbrook Flats, five peers) with three seeded defects (one unparseable rate, one duplicate loan identifier, one missing lender); a fixture lender email (recognized sender, statement attachment) for the activation test; member fixtures per the settings brief; an unknown-form PDF for the fail-closed test.

1. **P0 — the door (C-7 / U12-F2):** crawl every anchor, button-href, and redirect on `/sign-in` (authenticated and not): all resolve to Covenant routes or Covenant's own legal pages; zero references to any other product in links, copy, or metadata. This test is CI-permanent — the boundary violation can never regress silently. Ships before everything else on this brief.
2. **Requirements-hash guide:** create an org → guide shows exactly the missing items; import the book → book item flips done and the signers item becomes `currently-due`; revoke the only send grant later → the guide item *reopens* and the Home card returns (Stripe-pattern recompute, R6).
3. **Magic path:** upload the Calloway Park kit → Original registered with hash; Recreated-searchable label appears on the flattened executed forms (their zero-text-layer state is the label's reason — evidence); the proposal renders the loan record + the §8.02(b) reporting requirements with source regions; confirm → loan created; the first period materializes from the confirmed Q+45d due-rule with its checklist (03 §5).
4. **Fail-closed extraction:** upload an unknown-form document → the step blocks naming the missing definition; nothing plausible is invented (04 §2.5); manual authoring is offered via the extraction surface.
5. **CSV mapping:** the map table proposes targets for all seven columns; the seeded rate defect surfaces during mapping (first-100 validation); repair it in-tool → the row flips repaired; the original value renders struck-through in the row log.
6. **All-or-repair commit:** attempt commit with the missing-lender row unresolved → blocked, row named; exclude the row → commit succeeds; results register shows 6 created (setup-needed) + 1 excluded; `/loans` shows the batch's provenance chips.
7. **Duplicate handling:** the seeded duplicate identifier triggers the explicit resolve choice; "create-anyway" flags the loan as a duplicate on both records; no silent merge occurs.
8. **Undo import:** one click within eligibility → all created loans reversed in one transaction; guide items recompute backward; `import.batch_undone` names every reversed loan; the mappings and repairs survive → re-commit succeeds. Work a loan (confirm its schedule) → the undo control disables with the stated reason.
9. **Signers:** designate certifier + sender → grants persist in the settings store; `POST /api/covenant/send` honors them (shared vertical with the settings brief's test 1).
10. **Activation:** issue the address → forward a fixture lender email → the waiting card flips to the recognized arrival (sender + classification + period match) without reload and without any push notification; the event is quiet-logged (03 §1 steps 1–2).
11. **Resume:** abandon the arc mid-mapping, sign out, sign back in → the guide lands on the same step with the staged file, mappings, and repairs intact (R6: Mercury).
12. **One-sitting fixture:** a rehearsed operator completes org → import → signers → address in under 15 minutes with no step requiring outside waiting (the Mercury-derived pacing bar; activation email may land later by nature).
13. **Demo guards (D-6, if approved):** `/demo` renders the canon book read-only; every mutation affordance is disabled-with-reason; certify/send cannot execute by construction (no gate endpoints callable from demo context — server-enforced); the banner renders on every screen; no resident-level data appears; a demo guest hitting "sign in to work your own" lands on the fixed `/sign-in`.
14. **Permission fixtures:** non-admin member hits `/loans/import` → honest 403; guest hits `/onboarding` → `/sign-in` with destination preserved and restored post-auth.
15. **Viewport fixtures:** 1440/1728/2048 per §10; <1280px stepper + horizontal-scroll map table with the columns chip; 1152×720 usable.
16. **Keyboard fixture:** the full arc keyboard-only, including a mapping confirm, a row repair, commit, and undo.
17. **Provenance fixtures (lit-row):** a proposal field lights its exact source region in the embedded extraction view and stays lit; a batch row highlights its CSV line.
18. **Template round-trip:** download the CSV template, fill it with the canon book, import → zero mapping corrections needed (the template's headers auto-match at full confidence).
19. **Per-column date format:** a column mixing two date formats errors at the column level with the resolved-format choice explicit — no per-cell silent guessing.
20. **PMC client guard:** a row naming a non-existent client errors (no client auto-creation); after adding the client in Settings, re-validation clears the row.
21. **Guide-undo interaction:** undo the only committed batch → guide item 2 reopens to `currently-due`, item 6's period is reversed with it, and the Home card returns — the hash computes backward as well as forward.
22. **Accessibility:** screen-reader pass over the guide states, map table confidence announcements, and the activation flip.
23. **Benchmark challenger review:** an operator fluent in HubSpot imports must attempt to break the batch (partial commits, double-undo, re-commit after undo, concurrent commits) — every path ends in a consistent, audited state.

## 18. Build plan

- Dependencies: auth (exists via the current provider); tenancy (gap 3 — org creation is its first real write); the CSV import library (**exists** in `src/lib/covenant/`, unwired — snapshot §3 engine layer); the loan-doc extraction library (exists, unwired — gap 2) for the magic path; the intake pipeline (gap 1) for steps 4/10 — the address step ships blocked-honest until it lands; the Extraction & Confirmation surface (its brief) for the embedded confirm; the settings grant vertical (gap 4) for step 3.
- Foundation work: `OnboardingFrame`; the SetupGuide requirements-hash service; ImportBatch persistence (batch, rows, mappings, repairs — append-only history).
- Components to build first: the P0 sign-in fix (a deletion + the CI link-policy test — ships immediately, independent of everything); then MapColumnsTable + ValidationPanel + CommitBar/Undo (the import vertical); then SetupGuide; then the magic-path panes; then ActivationCard when gap 1 lands.
- Vertical slice (the send-vertical pattern): **the import vertical** — one route (`/loans/import`), the real CSV library end to end: stage → map → validate → commit → loans persisted with provenance → undo → reversal proven. It is the cheapest full-stack seam on this brief and unblocks every downstream surface's need for real loans.
- Migration from fixture data: none to migrate — this surface has no fixture predecessor; the demo book stays canon for `/demo` (D-6) and is never written by onboarding.
- Rollout/feature flag: `onboarding-v1` for the arc; the import vertical can ship to existing internal orgs first (it stands alone at `/loans/import`); `/demo` ships only on a D-6 GO.
- Build order (waves):
  1. P0 sign-in fix + CI link-policy test (independent of everything; ships day one).
  2. The import vertical (`/loans/import` over the existing CSV library): stage → map → validate → commit → undo, persisted.
  3. SetupGuide service + `/onboarding/*` shell with steps 1/3/5 (org, signers, invites — all on existing/settings verticals).
  4. Magic path (blocked on gap-2 extraction wiring + the Extraction & Confirmation surface; the step ships as "coming — import your list meanwhile" honesty until then).
  5. Intake step + ActivationCard (blocked on gap-1 pipeline).
  6. `/demo` (on D-6 GO; pure read-only projection over canon fixtures — cheapest wave, gated only by the ruling).
- Proof artifacts required: the CI link-policy test green on `/sign-in`; a recorded import commit + undo round-trip; the magic-path proposal with lit source regions over the Calloway Park kit; the activation flip recording once gap 1 lands.
- Final gate: `REBUILD` of the door confirmed done when test 17.1 is CI-permanent and a guest has a lawful path (sign-in, plus `/demo` if D-6 approves); the arc and import graduate `UNBUILT → REAL` on their verticals, never through fixtures.
