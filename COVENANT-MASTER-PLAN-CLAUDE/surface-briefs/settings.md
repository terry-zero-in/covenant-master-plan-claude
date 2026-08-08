# SETTINGS

## 1. Identity and verdict

- Product layer: portfolio floor (07-SURFACE-INVENTORY §1 B7) with cross-cutting reach — Settings is where org-level law (tenancy, roles, intake identity, escalation policy, agent memory administration) becomes legible and editable.
- Route/address in the basis-v2 repository: existing Settings view inside the `/review-room` shell → addressable `/settings/*` in the `(covenant)` route group (08-GLOBAL-FRAME-SHELL-PLAN §1). Section routes: `/settings/organization`, `/settings/clients` (PMC mode only), `/settings/intake`, `/settings/team`, `/settings/agent`, `/settings/notifications`, `/settings/data`, `/settings/billing` (workspace door) and `/settings/profile`, `/settings/appearance` (personal door). Components in `src/components/covenant/settings/`.
- Current build state: `FIXTURE` (snapshot §3: account/settings shell, demo strings).
- Existing-surface verdict: `ADJUST` (shell exists; sections, scoping, and every wire are new).
- Research tier: B.
- Primary users/roles: owner/admin (workspace door, all sections); PMC preparer (Agent section within client scope + personal door); reviewer (personal door only). Role-gated sections are **invisible, not disabled**, to those without the role (R6 research: GitHub).
- Frequency and session duration: heavy in week one (org setup), then rare, short, purposeful visits — change a grant, correct a memory entry, adjust escalation offsets. Never on the monthly critical path.
- Error cost: high in specific cells — a mis-granted send right lets the wrong person execute the send gate to a lender; a leaked or stale intake address misroutes lender-facing documents; a wrong memory correction propagates into every future package field it pre-fills. General error cost (appearance, density) is trivial; the surface's design weight goes exactly where the error cost is.
- Success criterion: every org-level policy that governs a gate is findable in one palette jump, legible in one screen, and changes only through a typed, audited act; the send role is genuinely assignable (gap 4 unfixed → fixed); no non-admin ever sees an admin section.

## 2. User job and decisions

- Primary job-to-be-done: "Make the org run the way our engagement letter says it runs — who prepares, who certifies, who sends, where documents arrive, when we get chased — and be able to prove the current configuration."
- Decisions made here, by section:
  - Organization: legal/display name, outward branding for exports, PMC-mode identity.
  - Clients (PMC): add/rename/archive owner clients; per-client scoping of everything below.
  - Intake: rotate the designated address; approve/reject sender recognition; issue per-client sub-addresses.
  - Team & Roles: role assignment; **certify and send grants, explicitly and separately assignable per scope** (the gap-4 unfix); invites; the external-preparer seat.
  - Agent: correct/retire memory entries; pause reuse; chase cadence defaults; capability-prompt visibility.
  - Notifications: escalation ladder offsets (remind → chase → your-move → push); quiet-log display window.
  - Data: retire COA mappings on PMS change; request exports; read the retention statement.
  - Billing: plan/seats (placeholder decisions only in v1).
  - Personal door: profile identity; density; reduced motion.
- Questions the surface must answer in scan order: (1) what scope am I editing — workspace or my account? (2) which section am I in and who else can see it? (3) what is the current value/policy? (4) what will change downstream if I edit it? (5) who set it last, when?
- What the user should not have to decide here: anything per-period (dispositions, confirmations, review — those live on the spine); any shipping number; the vocabulary or thresholds of covenant verdicts (loan documents + engine own those); design tokens.
- Entry paths: org switcher (top-left of the rail) → workspace settings; profile cluster (rail bottom, 08 §3) → personal settings — Linear's two doors, one mental model (R6 research: Linear). Also: `⌘K` palette ("intake address", "roles", "memory") deep-links to the exact section; inline "learned {date} from {who}" chips anywhere in the app link to the matching memory entry in `/settings/agent`; Intake/Holding's read-only address card links here.
- Exit paths: back to the previous surface (Esc/breadcrumb — Settings is book-altitude, no loan/period crumbs, 08 §2); inline memory chips return to their point of use; invite flow ends with a link back to Team.
- Completion/advancement conditions: none — Settings never advances a period. Its acts feed gates elsewhere (a certify grant makes a person eligible at gate 2; the send gate re-checks grants at gate 3, 03 §2).

## 3. Object and ownership model

| Object/action/status | Owned here? | Summary/link elsewhere | Address/identity | Notes |
|---|---:|---|---|---|
| Organization identity (name, plan ref, branding-for-exports) | YES | Rail org switcher shows name | `orgId` | (02-OBJECT-RELATIONSHIP-MODEL §2) |
| Client directory (PMC mode) | YES | Client scope chips render app-wide | `clientId` | Invisible in owner mode — one implicit Client (02 §1) |
| Designated intake address (issue/display/rotate) + sender recognition list + sub-addressing | YES | Intake/Holding displays the address read-only ("send documents to …") — the ruled summary allowance (02 §3) | per-org address record | Covenant's OWN address, minted per organization, NEVER shared with any other product (product-boundaries law) |
| Roles + certify/send grants + invites | YES | Certificate/Send surfaces render "eligible: {names}"; the gates enforce | member `userId` × scope | Unfixes gap 4: sender is currently server-fixed to admin (snapshot §6.4) |
| Escalation policy (remind→chase→your-move→push ladder) + chase cadence defaults | YES (the policy document) | Calendar/Obligations owns deadlines and *executes* the ladder (02 §3); period headers summarize | org policy record | The ONE push-eligible class is deadline escalation (04 §2.3) |
| Quiet-log retention window (display window, never deletion) | YES | Quiet log renders per loan/period | org policy record | Seal-not-wipe forbids deletion inside sealed periods (02 §5) |
| MemoryEntry store | NO — the agent layer owns the store (02 §2) | **This surface hosts the central inspector** (Settings → Agent memory, per 02 §2); entries also render inline at point of use | `memId` | Corrections version; the agent never re-asks (03 §3) |
| COA mapping rows (propose/confirm/correct) | NO — Actuals & Computation owns the lifecycle (02 §3) | `/settings/data` hosts the **administrative register**: browse, search, retire-on-PMS-change, export | `(propertyId, coaCode)` | Retire is an administrative act owned here; confirmation acts never happen here |
| Appearance (density, reduced motion) | YES (personal scope) | — | per-user prefs | |
| Billing (plan, seats, invoices) | YES (placeholder) | — | org billing record | Roadmap; no pricing invention (the RR v1-free/v2-paid boundary lives in `rent-roll-teaser-placement.md`) |

No-double-homing boundary: Settings owns *configuration and administration*; it never owns execution. Deadlines execute in Calendar/Obligations, gates enforce on Certificate/Send, memory fills fields at point of use, mappings confirm in Actuals — each of those surfaces may render the governing setting only as a read-only summary with a link here, and Settings renders their live state (e.g., "3 escalations fired this month") only as a count with a link out.

The two doors are also an ownership boundary: workspace records key on `orgId` (and `clientId` below it); personal records key on `userId` and never affect any other member or any gate. Nothing is dual-keyed — a preference that would affect the org (e.g., escalation timing) is by definition a workspace record, which is why the personal door carries only delivery channel, identity, and rendering preferences.

## 4. Data and semantic model

- Source facts: none arrive here (no documents; Settings is configuration, not evidence).
- Extracted values awaiting confirmation: none by design.
- Confirmed values: every setting is a confirmed value with actor + timestamp; grants and policies are versioned records, not mutable cells.
- Deterministic outputs: eligibility sets computed from grants ("who can certify {loan}"); the materialized escalation schedule (engine combines policy offsets with due-rules, 03 §5); sender-recognition match results; sub-address token minting.
- Agent proposals/drafts: none — the agent never edits settings. The Agent section *displays* the agent's memory and cadence behavior; proposals stay on work surfaces.
- Human decisions: grant changes, address rotation, sender approval/removal, ladder edits, memory correct/retire, mapping retire, invite issue/revoke, client add/archive, export request.
- Certified values: none; Settings never touches package content. (It touches who may certify — metadata about the gate, never the gate's content.)
- Versions/periods/packages: settings records version by change history (who/when/old→new); no period grain anywhere on this surface.
- Evidence/provenance: every memory entry carries "learned {date} from {who} during {context}" + the originating question/run link (03 §3); every grant carries grantor + timestamp; every sender-recognition row carries a first-seen message ref and its authentication result.
- Permissions/read-only projections, exactly:
  - Owner/admin: both doors, all sections, all acts.
  - PMC preparer: personal door + the Agent section filtered to their client scope (they answer the agent's questions, so they inspect and correct the memory those answers created); no other workspace section renders.
  - Reviewer (read-only): personal door only; zero workspace render, zero workspace DOM.
  - External-preparer seat: personal door only; their uploads/preparation permissions live on work surfaces, not here.

Field groups and grain:

- Organization: `orgId`, name, PMC flag, intake address record ref, policy refs, branding-for-exports.
- Client: `clientId`, name, owner contacts, sub-address token, archive state (02 §2).
- Member: `userId` × org — role (owner/preparer/reviewer per 02 §4), certify grant scope[], send grant scope[], client scope[] (PMC), external-preparer flag, invite state + expiry.
- PolicyRecord: escalation stage offsets, chase cadence defaults, quiet-log display window, version chain.
- MemoryEntry: `memId`, scope (borrower/loan/lender/property-PMS, 03 §3), question, answer, provenance, versions, reuse log.
- COAMappingRow: `(propertyId, coaCode)`, PMS account label, confirmer, status (active/retired), source refs.
- BillingRecord: plan, seat count, invoice refs (placeholder grain).
- Loan/period/package/test grain: absent by design — the surface's one structural guarantee against becoming a second spine.

## 5. State machine and exceptions

| State (template-required) | On this surface | Trigger → resolution | Actor / reversibility / audit |
|---|---|---|---|
| empty | First visit pre-setup: sections render explicit "not configured" cards (no fake defaults) | Onboarding acts fill them (see `onboarding-signin-import.md`) | Human; n/a; each fill audited |
| awaiting-documents | N/A by design — no documents arrive here; the Intake section shows a link-out count of held arrivals only | — | — |
| loading/processing | Section skeleton on the gray ladder; never fake values | Data loads → render | Engine |
| partial/incomplete | A section with required fields unset (e.g., no send grantee) renders a labeled incomplete banner with the downstream consequence ("the send gate has no eligible actor") | Admin completes | Human; reversible |
| extracted/unconfirmed | N/A — nothing is extracted here | — | — |
| stale/superseded | A retired intake address inside its grace window; a superseded memory version; a retired mapping | Rotation/correction | Prior versions kept, labeled |
| low confidence | Sender-recognition proposals below the auto-match threshold render as "needs review" rows | Admin approves/rejects | Human; reversible; audited |
| conflict | Two admins edit the same policy: last-write warning with both versions shown; explicit re-apply required | Second editor resolves | Human; reversible via history |
| failed tie-out | N/A — no package math here | — | — |
| watch/shortfall/breach | Never rendered here — verdict vocabulary appears only on covenant surfaces (vocabulary law) | — | — |
| permission denied | Structurally unreachable for hidden sections (invisible, not disabled — R6 research: GitHub); direct URL hit → honest 403 page naming the required role, zero org data in the response | — | Access attempt audited |
| read-only | Reviewer's personal door; a preparer viewing org-scoped memory outside their client scope sees nothing (scope-filtered at the query, not grayed) | — | — |
| blocked/gated | Address rotation blocked while an unresolved arrival sits on the old address (fail-closed: rotating would orphan it) | Resolve the arrival → rotation proceeds | Engine gate; honest reason |
| certified (void-on-change) | Not applicable to settings records. Stated to prevent over-engineering: a grant revocation never voids an existing CertificationRecord — the act was valid when performed; revocation only removes future eligibility | — | — |
| sent/sealed | N/A | — | — |
| recovery/undo | Every setting change is history-versioned and revertible EXCEPT: address rotation (an old address cannot be un-retired — new mint only; irreversibility stated inside the confirm), invite revocation (re-invite instead), and memory hard-delete (does not exist — retire is the strongest act, and retire is reversible) | — | Append-only history |

Sub-machines, exactly:

- Intake address: `active` → (typed rotate act, gate-checked) → `rotating` (old + new both deliver for the grace window; old-address arrivals badge "received on retired address") → `retired` (old address bounces with a neutral rejection that names no product).
- Invite: `sent` (expiry 14 days default, admin-adjustable — R6 research: Ramp) → `accepted` | `expired` | `revoked`; expired invites re-issue as new records.
- Grant: `granted(scope)` → `revoked` (history kept); a scope edit is revoke+grant in one typed act, one audit event.
- MemoryEntry (as seen from the inspector): `active(v1)` → `corrected(v2…)` → optionally `retired`; the agent always reads the newest active version (02 §5).

Audit-event taxonomy (every transition above emits exactly one, on the one ActivityEvent store — ticket-ready names):

- `settings.org.updated` · `settings.client.created|renamed|archived`
- `settings.intake.address_rotated` (old mint, new mint, grace window) · `settings.intake.sender_recognized|rejected|unlinked` · `settings.intake.subaddress_minted`
- `settings.team.invite_sent|accepted|expired|revoked` · `settings.team.role_changed` · `settings.team.grant_changed` (right: certify|send; scope; old→new — the row the send gate's audit joins against)
- `settings.agent.memory_corrected` (memId, v(n)→v(n+1), corrector) · `settings.agent.memory_retired|restored` · `settings.agent.reuse_paused|resumed` · `settings.agent.cadence_changed`
- `settings.notifications.ladder_changed` (per-stage old→new offsets) · `settings.notifications.quietlog_window_changed` · `settings.personal.channel_changed`
- `settings.data.mapping_retired` (propertyId, coaCode) · `settings.data.export_requested|completed`

## 6. Engine / Agent / Human / Gate / Quiet Log

Five-lane table per chapter of the surface (per 04-ENGINE-AGENT-HUMAN-GATE-MAP §1; rows reconcile with 04's "Memory & learning" and "Calendar & deadlines" chapters):

| Chapter | ENGINE (deterministic) | AGENT (prepares/proposes) | HUMAN (typed decisions) | GATE (interrupts) | QUIET LOG |
|---|---|---|---|---|---|
| Organization / Clients | Tenancy integrity (client FK, archive constraints) | — | Rename, add/archive clients | Archive blocked while a client has open periods | `settings.client.*` events |
| Intake identity | Address + sub-address minting; sender authentication (SPF/DKIM); grace-window enforcement | Proposes sender↔loan matches with confidence (the recognition rows it can't auto-match) | Approve/reject senders; rotate address | Rotation blocked by an unresolved old-address arrival | Rotations, recognitions, sub-address mints |
| Team & Roles | Eligibility sets from persisted grants — read by the real send gate at execution (retires gap 4: sender no longer server-fixed to admin); invite expiry | — | Role assignment; certify/send grant acts; invites; external-preparer seat | Zero-eligible confirm ("the send gate will have no eligible actor") | `settings.team.*` events |
| Agent (memory, cadence, prompts) | Storage/provenance integrity of memory versions (04 §1) | The store's *content* is agent-written elsewhere; here it is only displayed | Correct (versioned), retire, pause reuse, cadence defaults | — (memory never gates, 04 §1) | Corrections, reuse counts ("pre-filled from memory {ref}") |
| Notifications | Escalation-schedule materialization from offsets × due-rules (03 §5) | — | Ladder offsets; quiet-log window; personal channel | — (execution and its gates live in Calendar/Obligations) | Ladder changes |
| Data / Billing | Export job execution; retention invariants (sealed periods immutable) | — | Mapping retire; export request | — | Exports queued/completed — completion is quiet-logged, never pushed |

Lane invariants checked (04 §2): no shipping number is produced anywhere on this surface; the agent lane is display-only here; every gate row above names its typed decision; all events append-only and identity-stamped.

## 7. Information hierarchy

1. Page/frame header: shell header, book-altitude breadcrumb `Covenant / Settings` (no loan/period crumbs — 08 §2 altitude gate).
2. Decision/status summary: the section nav with per-section incomplete badges (shared CountBadge) — the scan answers "what needs an admin".
3. Primary work region: the active section's content pane — current values first, edit affordances second.
4. Secondary context: per-record downstream-consequence lines ("used by 4 loans' normalization", "eligible senders: 2").
5. Evidence/proof: provenance chips on every record (who/when/context); memory entries open their originating question/run.
6. Actions: inline per record; destructive/legal-weight acts through typed confirms.
7. Activity/history: per-section change history (a filtered quiet-log view), collapsed at the bottom of each section.

Scan order differs by door:

- Workspace door (admin): nav badges first (what's incomplete) → Team & Roles and Intake carry the standing weight (they govern gates) → policy sections → Billing last.
- Personal door (everyone): profile identity → appearance → the personal delivery channel; no counts, no badges — nothing in the personal door is ever "incomplete" in a way that blocks the org.

Absent by design: any covenant verdict, any figure from a package, any per-period state, and any chart — nothing on this surface out-encodes text, so under the chart doctrine zero charts ship.

## 8. Page anatomy and regions

| Region | Purpose / content | Persistence | Interaction | Min size | Collapse behavior | Why this container |
|---|---|---|---|---|---|---|
| Section nav (left, inside the work area) | Two labeled scope groups: **Workspace** (Organization, Clients [PMC], Intake, Team & Roles, Agent, Notifications, Data, Billing) and **Your account** (Profile, Appearance); settings-search field at top (R6 research: Stripe — settings reachable from search) | pinned | Click/↑↓ to switch section; type-to-filter leaves matching leaves only | 240px | Below 1280px total: collapses to a labeled section dropdown above content | One surface, two clearly labeled scopes — Linear's two doors collapsed into one addressable surface (R6 research: Linear + the GitHub adapt); a second *work window* is unjustified: no settings decision needs side-by-side panes |
| Section content (the one big pane) | The active section; single-column record cards and registers | persistent | Scroll; inline edit; registers on the shared grid | 720px | Never silently compresses; registers scroll within the pane | Pane-model law: one big pane + view switching |
| Canvas panel | Ask ("what does the escalation ladder do?") and memory-entry quick inspect summoned from inline chips elsewhere | summonable | Esc dismisses | per frame law | floats/docks per 08 §4 | Transient tooling, never load-bearing |
| Typed-confirm modal | Rotation, grant changes affecting certify/send, memory retire, client archive | transient | Type-to-confirm + consequence statement | 480px | — | Modal law: typed acts with legal weight and destructive confirms only |

Non-admins never render the Workspace group at all — the nav shows only "Your account" (invisible, not disabled; R6 research: GitHub).

### 8.1 Per-section content specification

- **Organization** (`/settings/organization`): identity card (legal name, display name, PMC-mode indicator — the mode is set at onboarding and shown here, changed only through a support path in v1); branding-for-exports card (logo/letterhead used on outbound package cover sheets, per Client override in PMC mode); org-id + created-date footer.
- **Clients** (`/settings/clients`, PMC only): client register on the shared grid — name, owner contacts, loans count (link-out to Loans filtered), sub-address (Geist Mono, copy), archive state; "Add client" creates `clientId` + mints the sub-address token; archive is a typed confirm that blocks while the client has open periods (fail-closed, honest reason).
- **Intake** (`/settings/intake`): IntakeAddressCard (below, §11); SenderRecognitionTable; security-posture card — plain-language statements of the address's guarantees: per-org unguessable local part, sender authentication checked on every arrival, unrecognized senders quarantined to the Intake queue (never auto-filed), and the product-isolation sentence ("this address serves Covenant only and is never shared with any other product" — product-boundaries law).
- **Team & Roles** (`/settings/team`): RoleMatrixGrid; pending-invites strip; InviteComposer; a plain-language gate legend under the grid: "Certify and send are separate rights. Covenant's gates check them at execution time." (03 §2 gates 2–3).
- **Agent** (`/settings/agent`): MemoryInspectorTable; ChaseCadenceEditor; capability-prompts card (toggle: show rotating capability prompts in Ask; prompts remain domain-real and engine-completable only — locked law, snapshot §5).
- **Notifications** (`/settings/notifications`): EscalationLadderEditor with worked-example strip; quiet-log window card (display window length; sentence: "history is never deleted — sealed periods keep everything," 02 §5); personal-delivery note ("how the one push class reaches each member is set in their personal door").
- **Data** (`/settings/data`): COAMappingRegister grouped by property/PMS (evidence: SLOT-1 — the T-12's numbered chart of accounts, 4010 Gross Potential Rent … 8010 Asset Management Fee, is exactly the source side of these rows); exports card (full-book export: loans, confirmed schedules, sealed packages index — queued job with quiet-log completion, no push); retention card (statement-only in v1: originals immutable, sealed periods immutable, nothing user-deletable inside them).
- **Billing** (`/settings/billing`): plan card, seat count, invoice register placeholder — explicitly labeled roadmap; no pricing UI in v1.
- **Profile** (`/settings/profile`): name, email, avatar; security hand-off to the auth provider (password/2FA managed there; the card links out honestly rather than re-implementing).
- **Appearance** (`/settings/appearance`): density (comfortable/compact — affects register row heights app-wide, uniform within any one table); reduced motion (disables all transitions app-wide); per-user delivery channel for the one push-eligible class (deadline escalation: push and/or email — the org policy decides *when*, the person decides *how it reaches them*, 04 §2.3).

## 9. Co-visibility matrix

| Datum/surface A | Datum/surface B | Must be simultaneous? | User decision enabled | Default topology | Fallback |
|---|---|---:|---|---|---|
| Member row (Team & Roles) | Consequence line ("can execute send for {scope}") | YES | Grant/revoke with eyes open | Same row, inline | — |
| Grant-change confirm | Current eligible-actor count | YES | Avoid zero-eligible states | Inside the modal | — |
| Intake address card | Sender recognition list | YES | A rotation decision needs to see who mails the address | Same section, stacked | Anchor links |
| Memory entry | Its provenance + version history | YES | Correct vs retire | Row expands in place (no modal — proof beside the claim, lit-row law) | — |
| Memory entry | The field it fills (its point of use) | NO | Link-out suffices | "Used at →" link | — |
| Escalation ladder | A worked example rendered from a real due-rule (Q+45d; evidence: SLOT-3 §8.02(b)) | YES | Offsets are meaningless without a concrete date line | Example strip under the editor | — |
| COA mapping row | Its confirming actor + property/PMS identity | YES | Retire decision | Row columns | — |
| Sender row | Its authentication result | YES | Approve only authenticated senders knowingly | Same row column | — |
| Pending invite | Its expiry countdown | YES | Re-issue vs wait | Same row | — |
| Seat count (Billing) | Member count (Team) | NO | Link between sections suffices | Cross-link chips | — |
| Settings section | Any spine surface | NO | Never — Settings is never part of a review decision | Navigate away | — |

No pane exists merely because information exists: the two-pane topology (nav + content) is the entire surface; everything else is inline expansion or link-out.

## 10. Layouts and viewport behavior

| Viewport | Rail (frame) | Section nav | Content pane | Notes |
|---|---|---|---|---|
| 1440px | expanded or user-collapsed (08 §9) | 240px | ~900–1100px, single column | registers full-width within content |
| 1728px | expanded 240–280px | 260px | max 1180px | register history may sit as an interior right column |
| 2048px | expanded | 260px | capped 1280px | extra ground stays open; nothing stretches |
| <1280px | icon rail 48px | labeled dropdown above content | full width | typed confirms remain full modals |

- Narrow/compact (<1280px): nav becomes a labeled dropdown; registers keep uniform rows with horizontal scroll inside the pane; typed confirms remain full modals.
- Default pane topology: one work window always; canvas panel summonable; split-of-two never offered here (no co-visibility row above requires it).
- Focus behavior: section switch preserves scroll position per section within a session.
- Compare behavior: none (no split justified).
- Proof/source behavior: provenance expands in place; originating runs open in the canvas panel, not a modal.
- Minimum viable dimensions: 1152×720 (frame law, 08 §9); registers remain usable below spine minimums.
- Tab stacking/replacement: not applicable — one work window.
- No silent compression: the nav collapses to a labeled dropdown, never squeezes; register columns drop to a priority order with an explicit "N columns hidden" chip.

## 11. Components and exact anatomy

- **CovenantShell** — REUSE (`src/components/covenant/CovenantShell.tsx`). Mounts `/settings/*` like every surface.
- **SettingsNav** — NEW. Parts: scope group headers ("Workspace", "Your account"), section rows (Lucide icon + label + CountBadge for incomplete), settings-search field, role-filtered render (workspace group absent for non-admins).
- **SettingsSectionScaffold** — NEW. Parts: section title, one-line description, record cards, collapsed history footer ("changes" — filtered quiet-log query).
- **RoleMatrixGrid** — NEW on the shared grid (uniform row heights, open-not-boxed). Columns: member (name/email), role (owner·preparer·reviewer per 02 §4), certify-grant scope chips, send-grant scope chips, client scope (PMC only), last-active, state. Row action: "Edit access" → GrantEditor. Footer strip: pending invites with expiry countdowns.
- **GrantEditor** — NEW. Parts: role select; certify toggle + scope picker (org-wide / per-client / named loans); send toggle + scope picker; consequence preview line ("this person will be able to execute the send gate for {scope}"); hand-off to TypedConfirmModal when certify/send changes.
- **IntakeAddressCard** — NEW. Parts: the address in Geist Mono with copy button; identity line ("minted for {org} · Covenant-only — never shared with any other product"); status chip (active/rotating/retired-history link); rotate act (gate-checked); PMC sub-address list (`{org-token}+{client-token}@…` per client, each with copy + client link).
- **SenderRecognitionTable** — NEW on the shared grid. Columns: sender address, matched loan/lender, authentication result, first-seen link (lit-row into Intake), status (recognized / needs review / rejected), row actions (approve/reject/unlink). Bulk approve on the needs-review filter only.
- **MemoryInspectorTable** — NEW on the shared grid. Modeled on the ChatGPT manage-memory pattern, provenance-typed per R6 (R6 research: ChatGPT memory — take: the central inspector with per-entry edit/delete and a master switch; adapt: entries are value + "learned {date} from {who} during {context}" + originating run link + version history, never a synthesized prose summary). Columns: scope (borrower/loan/lender/property-PMS, 03 §3), question, current answer, provenance chip, reuse count, versions. Filters: scope, loan, client (PMC). Row expand: full chain + correct (versioned typed act) + retire. Header: "Pause reuse" master toggle — the agent asks instead of pre-filling; entries retained (the kill switch adapted to a compliance product).
- **ChaseCadenceEditor** — NEW. Org default chase offsets (org-configurable placeholders — no invented cadence numbers; per the loan's own confirmed due-rules); note linking per-loan overrides to the loan's requirement records.
- **EscalationLadderEditor** — NEW. Four fixed stages remind → chase → your-move → push (03 §5, 04 §2.3); editable offset per stage; the push stage carries a fixed label "deadline escalation only — the one push class"; worked-example strip rendering the ladder against a real Q+45d deadline (evidence: SLOT-3 §8.02(b)).
- **COAMappingRegister** — NEW on the shared grid. Grouped by property + PMS identity; columns: PMS account (code + label), COA code, confirmer + date, status, retire act; provenance note "confirmed in Actuals & Computation" (ownership per 02 §3).
- **InviteComposer** — NEW. Parts: email, role, scope, expiry (14-day default, adjustable — R6 research: Ramp), external-preparer seat option (uploads/prepares, never certifies/sends — the Ramp Advisor-Console adapt), send + resulting pending row.
- **TypedConfirmModal** — REUSE pattern (the certify ceremony's typed-act anatomy). Parts: consequence statement, type-to-confirm field, audit note, cancel/commit.
- **CountBadge** — REUSE (the consolidated shared atom, snapshot §4). Incomplete-section counts.
- **CommandPalette** — REUSE (`CommandPalette`/`SearchPalette`). Every settings leaf registers as a palette place. Registered entries (exact strings, role-filtered like the nav):
  - "Settings: Organization" · "Settings: Clients" · "Settings: Intake address" · "Settings: Sender recognition"
  - "Settings: Team & roles" · "Settings: Who can certify" · "Settings: Who can send" (the last two land on RoleMatrixGrid filtered to the grant column)
  - "Settings: Agent memory" · "Settings: Chase cadence" · "Settings: Notifications" · "Settings: Escalation ladder"
  - "Settings: Data & exports" · "Settings: COA mappings" · "Settings: Billing" · "Settings: Profile" · "Settings: Appearance"
- Empty/error/recovery object — REUSE the app-wide pattern: explicit not-configured cards, honest 403 page, history-revert affordance.

## 12. Interaction specification

- Selection: single-row focus in registers; multi-select only on the recognition list's needs-review filter.
- Hover: row hover reveals actions; provenance chips show full context on hover, open on click.
- Focus: visible accent-family focus ring on every control; the section nav is a listbox.
- Keyboard: `⌘K` → any settings leaf; `/` filters the active register; ↑↓/Enter in nav; Tab order follows scan order (§7); Esc closes confirms, then expanded rows, then returns to the prior surface. A dedicated G-chord for Settings is deferred to `cross-cutting/search-command-keyboard.md` (08 §5 reserves the chord map there).
- Editing and validation: inline edit per record card; grant edits validate against zero-eligible states before offering the confirm; rotation validates the no-orphan gate before offering the confirm (gates precede modals — the modal is never where you discover you're blocked).
- Bulk action: recognition bulk-approve only; grants are never bulk (each is a typed act).
- Undo/recovery: per-section change history with one-click revert (revert writes a new version — history is append-only); irreversible acts (rotation, revoked invites) say so inside the confirm.
- Sorting/filtering: registers sort by column; the memory inspector filters by scope/loan/client; saved views via the shared SavedView mechanism (02 §2).
- Drill-down and return path: a memory chip elsewhere → `/settings/agent?entry={memId}` with the row expanded → "back to {surface}" returns exactly; recognition first-seen → Intake arrival → browser back returns with the row still focused.
- Source-linked selection (lit-row): memory provenance opens the originating question/run in the canvas panel; recognition rows light the first-seen arrival in Intake — the lit-row contract applied to configuration provenance (06-DATA-LINEAGE §1 chain shape).
- Save/persistence: explicit save per record card (no autosave on grant/policy records — deliberate acts); personal appearance prefs save instantly with visible confirmation.
- Collaboration/commenting: none by design; the change history is the collaboration record.

Typed-act inventory (which saves are ceremonies vs plain saves — the exact split, so builders don't over- or under-modal):

| Act | Treatment |
|---|---|
| Certify-grant change, send-grant change | TypedConfirmModal (legal-weight: changes who can act at gates 2–3) |
| Intake address rotation | TypedConfirmModal (irreversible retirement of the old mint) |
| Client archive | TypedConfirmModal (destructive to scope visibility; blocked while periods open) |
| Memory retire | TypedConfirmModal (removes a reuse source; reversible, still ceremonial) |
| Memory correct | Inline typed act with reason field — versioned, no modal (correction is routine work, not ceremony) |
| Sender approve/reject, ladder offsets, cadence defaults, quiet-log window, mapping retire | Explicit save button + inline confirmation; audited, not modal |
| Profile, appearance, personal channel | Instant save with visible confirmation |

## 13. Visual craft direction

- Typography roles: section titles 16px/600; record labels 13px/500; values 13px/400; provenance and consequence lines 12px on a mid gray rung; group headers 11px/600 uppercase tracking on a quiet rung.
- Financial-number treatment: no shipping figures render here, but addresses, tokens, seat counts, and offsets set in Geist Mono with `tabular-nums slashed-zero` (ruled setting) for scanability.
- Spacing rhythm: 8px base; record cards on a 24px vertical rhythm; registers uniform 40px rows (36px in compact density).
- Density: comfortable default; the personal density preference applies to registers, never to typed-confirm modals.
- Open ground vs earned boundaries: no boxed cards — sections separate by whitespace + 1px hairline dividers on the ruled gray ladder; the only filled panels are status banners (accent tint #A9B5FF at low emphasis for informational; gray rungs otherwise).
- Dividers/elevation: flat surface; the confirm modal is the only elevated element (standard shell elevation).
- Semantic color: accent family only for interactive/focus (#7189FF, hover #8EA1FF, active #6078F4, on-accent #0B1020); incomplete counts use the standard CountBadge treatment; no verdict colors anywhere on this surface (vocabulary law — verdicts don't render here).
- Certified-sheet treatment: not touched — Settings never renders the certified sheet; the six ruled hexes do not appear here.
- Focus/selected/hover states: nav selected state uses Covenant's own ruled selected-state tokens (Ruling-J boundary, snapshot §4); hover = one gray-rung shift; selection never uses color alone (left accent bar + weight).
- Chart style: no charts (doctrine-honest).
- Motion: section switch is an instant swap; row expand 120ms ease-out; the reduced-motion preference (set on this very surface) disables all transitions app-wide.
- Long-session ergonomics: minimal need — sessions are short by design; line lengths capped ~72ch; no dense scanning fields.

Token application map (consuming ruled tokens only — zero new values):

- Interactive/focus/selected: `--acc #7189FF` family (`--accH` hover, `--accA` active, `--accT` tint for informational banners, `--accOn` on accent fills).
- Text hierarchy: three gray-ladder rungs — primary text on the darkest text rung, labels one rung lighter, provenance/consequence lines on the mid rung.
- Hairlines/dividers, skeletons, hover shifts: adjacent gray-ladder rungs per the shipped `covenant-tokens.css` ladder (verify by canvas readback, never eyeball — snapshot §4 oklch caution).
- Certified-sheet hexes (#E7EBF8 / #DDE4FB / #B3BCDA / #33419D / #5265C5 / #121459): not consumed here, listed to make the prohibition checkable.

## 14. Benchmark research and synthesis

| Product | Limited role | Official evidence | Exact pattern to take | Covenant adaptation | What to reject | Why this reference beats alternatives |
|---|---|---|---|---|---|---|
| Linear | Settings IA model | https://linear.app/docs/workspaces · https://linear.app/docs/account-preferences | Two scoped doors (workspace name → workspace settings; avatar → personal), one mental model; members see only their own scope, admins additionally see administration | Org switcher → Workspace group; profile cluster → Your account group; one `/settings/*` surface, two labeled groups | Engineer-centric shortcut density | The cleanest live two-scope IA; matches the rail anatomy already ruled (08 §3) |
| GitHub | Admin-gating control case | https://docs.github.com/en/organizations/managing-organization-settings · https://docs.github.com/en/account-and-profile/setting-up-and-managing-your-personal-account-on-github/managing-user-account-settings | Role gates that hide, not disable: only org owners *see* org settings — visibility itself is role-gated | Workspace group absent from non-admin nav and DOM; direct URL → honest 403 | Multi-hop navigation (profile → orgs → org → settings) | The strictest visibility precedent; "disabled but visible" leaks org structure to read-only reviewers |
| Stripe | Settings reachability + team roles | https://docs.stripe.com/dashboard/basics · https://docs.stripe.com/get-started/account/teams | Gear → grouped business settings; Team tab with a clarified role matrix; settings findable from global search | Settings-search field + every leaf a palette place; RoleMatrixGrid with explicit, separate grant columns | Payments-grade multi-mode chrome (test/live toggles) | The reference for *findable* settings at scale; its role matrix is the closest live analog to certify/send grant semantics |
| ChatGPT | Agent-memory inspector | https://help.openai.com/en/articles/8590148-memory-faq · https://openai.com/index/memory-and-new-controls-for-chatgpt/ | Central Manage Memory: every entry listed, per-entry delete/edit, master toggle; saving visible at capture time | MemoryInspectorTable: provenance-typed entries (value + learned-from + run link + versions); correct-versioned instead of delete; "pause reuse" instead of wipe; retire keeps the audit trail | Opaque cross-conversation synthesis; the free-text memory summary | The only mainstream user-inspectable AI memory UI; R6 names both the reference and the required adaptation (R6 research: ChatGPT memory) |
| Ramp | Invites + the external seat | https://support.ramp.com/hc/en-us/articles/1500002006322-Getting-started-as-an-Admin · https://support.ramp.com/hc/en-us/articles/11280280648339-Advisor-Console-account-access-and-setup | Role-scoped expiring invites (14d default, 1–365 adjustable); the Advisor Console's scoped external-collaborator seat | InviteComposer expiry defaults + the external-preparer seat (uploads/prepares, never certifies/sends) | Card-issuance-style eager provisioning | Directly solves the outside-accountant actor in 02 §4 without inventing a new role class |

Synthesis: Linear supplies the two-door scoping, GitHub the invisibility rule, Stripe the findability and role-matrix explicitness, ChatGPT the memory-inspector skeleton, Ramp the invite mechanics — but the result is domain-original because the load-bearing cells exist in none of them: certify and send as *separately assignable, per-scope grants that a real server gate reads at execution time* (the gap-4 unfix), an intake address that is a per-org compliance artifact with rotation gates, sender authentication, and a hard product-isolation guarantee, and a memory inspector where every entry is a provenance-typed compliance fact ("learned {date} from {who} during {context}") rather than a lifestyle preference. Settings here is the legible constitution of a lender-reporting organization — no benchmark has that job, so no benchmark could have produced this surface.

## 15. Domain references

Loan-servicing and financial-close products are consulted for terminology and expected workflow semantics only: servicer submission workflows of the kind behind the JLL forms (evidence: the quarterly Property Questionnaire's signed certification block — the real-world preparer-vs-certifying-officer split that makes separate certify/send grants domain-correct rather than an invention), and close-management tools' preparer/reviewer/approver vocabulary for the role ladder. Domain authority does not equal visual authority: none of these products governs a pixel, a pane, or a token here. Covenant semantics — what a certification is, who may sign, what a due-rule means, what a monitored threshold is — come from the loan documents and Terry (evidence: SLOT-3 §8.02(b); the knowledge corpus per snapshot §5), never from any referenced product.

## 16. Accessibility, performance, and safety

- WCAG contrast and focus: AA on all rungs used for text; visible focus ring everywhere; accent-on-accent pairs use `--accOn #0B1020`.
- Keyboard completeness: every act (including grant changes and rotation) completable keyboard-only; typed confirms are keyboard-native by definition.
- Screen-reader semantics: nav = `nav` + listbox with group labels; registers = table semantics with row headers; provenance chips are labeled buttons ("provenance: learned 14 July 2026 from J. Torres"); consequence statements are read before the confirm field.
- Table virtualization: memory inspector and mapping register virtualize past ~200 rows; filters execute server-side.
- Loading and latency feedback: gray-ladder skeletons; save acts show inline pending state; no optimistic rendering of grant changes (the gate must be truthful at all times).
- Destructive action confirmation: rotation, retire, revoke, archive — all typed confirms with consequence statements.
- Certify and external-send safety: grant changes are typed acts, audited, and enforced server-side at the real gates (`POST /api/covenant/send` re-checks grants at execution; a revocation between certify and send denies with an honest 403); a grant change never mutates an existing CertificationRecord; approved-bytes and void-on-change live on their own surfaces, untouched from here.
- Source immutability: settings history append-only; memory versions immutable; retired addresses unrecoverable by design.
- Auditability: every change an ActivityEvent (actor, scope, old→new); section history views are filtered quiet-log queries, not a second store (07 §2 duplication rule).

## 17. Acceptance tests and fixtures

Fixtures: the 7-loan demo book with Bexley (canon); a PMC org fixture with two Clients; member fixtures (1 owner, 2 preparers, 1 reviewer, 1 external-preparer seat); the Calloway Park confirmed schedule for the due-rule example (evidence: SLOT-3 §8.02(b), Q+45d / FYE+120d).

1. **Gap-4 unfix (the vertical):** grant send to preparer P for Client A only → P executes `POST /api/covenant/send` for a Client-A loan → 200 + append-only SendRecord; the same call for a Client-B loan → 403 naming the scope; revoke the grant → immediate 403. The server reads persisted grants; the hardcoded-admin path is deleted.
2. **Invisible-not-disabled:** log in as reviewer → the Workspace group is absent from nav and DOM; direct `GET /settings/team` → honest 403 naming the required role, zero org data in the response body.
3. **Zero-eligible guard:** attempt to revoke the only send grant → the confirm states "the send gate will have no eligible actor"; completing it renders the Team incomplete banner and a your-move item for the owner (03 §4 your-move definition).
4. **Rotation gate:** stage an unresolved arrival on the current address → the rotate act is blocked with the honest reason; resolve the arrival → rotation proceeds; the old address delivers during grace with the "retired address" badge; post-grace delivery bounces; address history shows both mints.
5. **PMC sub-addressing:** Client A and Client B each render a distinct sub-address; a message to A's sub-address can never surface in B's scope (assert the scope filter at the query layer, not the render layer).
6. **Product isolation:** assert the intake address record is minted per `orgId` in Covenant's own store and that no other product's configuration references or can mint it (product-boundaries law: one designated address per product, never crossing).
7. **Memory inspector:** correct the remembered management-fee answer (the 03 §3 lender-scope example, evidence-shaped from the JLL questionnaire's fee question) → new version with corrector provenance; the old version remains visible in history; the inline chip at the point of use renders the new value; the agent does not re-ask next period (the zero-repeat-question assertion across two consecutive periods, shared with the agent brief).
8. **Pause reuse:** toggle on → the agent asks instead of pre-filling and entries are retained; toggle off → reuse resumes from the newest versions; both toggles audited.
9. **Escalation editor:** set offsets → the worked example recomputes from the Q+45d fixture; only the push stage is push-eligible; assert no settings path can enable push for any other event class (04 §2.3).
10. **COA mapping retire:** retire a mapping for the fixture property → Actuals & Computation surfaces the unmapped line as a normalization exception next period (03 §2 exception table) — the administrative act here provably feeds the owning surface.
11. **Invite lifecycle:** issue a 14-day invite → accept before expiry → member row appears with the granted scope; issue → let expire → state flips, acceptance link dead; revoke → same.
12. **Conflict edit:** two admin sessions edit the ladder → the second save shows both versions and requires explicit re-apply; no silent last-write.
13. **Provenance fixture (lit-row):** click a recognition row's first-seen link → the exact arrival opens in Intake with the source row lit and staying lit.
14. **Viewport fixtures:** 1440/1728/2048 render per §10; at 1279px the nav becomes the labeled dropdown (no silent compression); 1152×720 remains usable.
15. **Personal channel:** set a member's escalation delivery to email-only → a fired deadline escalation reaches them by email and produces no push; the org ladder itself is unchanged (org decides when, person decides how).
16. **Export job:** request a full-book export → job queued, completion appears in the quiet log only (no push); the export excludes resident-level data from any outward-formatted artifact (names law: anonymized rolls only).
17. **Incomplete-state fixture:** an org with no send grantee and no issued intake address renders exactly two incomplete badges in the nav (Team, Intake), each banner naming its downstream consequence; completing each clears its badge without reload.
18. **History revert:** revert a ladder change from the section history → a new version is written (the reverted-away version remains in history); the worked example re-renders from the restored offsets.
19. **Accessibility:** keyboard-only pass through grant → confirm → audit; screen reader announces consequence statements before the confirm field; contrast audit on every rung used.
20. **Benchmark challenger review:** a reviewer fluent in Linear/GitHub/Stripe settings must find the intake address, the send rights, and a named memory entry in under 15 seconds each via the palette — all three succeed or the nav/palette registration fails the gate.

## 18. Build plan

- Dependencies: tenancy hierarchy (gap 3: org→client FKs — the hard prerequisite for scoped grants and sub-addressing); a persisted role/grant table read by the send gate (the gap-4 fix); the memory store (agent brief, D3); notification/escalation machinery (gap 9); the intake pipeline for address minting (gap 1 — the Intake section ships with an honest "not yet issued" card until it lands).
- Foundation work: `/settings/*` routes mounting CovenantShell; role-filtered SettingsNav; settings change-history events written to the one ActivityEvent store.
- Components to build first: SettingsNav → RoleMatrixGrid + GrantEditor + TypedConfirmModal (the gap-4 seam) → IntakeAddressCard → MemoryInspectorTable → EscalationLadderEditor → the registers.
- Vertical slice (the send-vertical pattern): Team & Roles wired end to end — persisted grants → `/settings/team` UI → `POST /api/covenant/send` reading grants → the 200/403 flip provable in one demo against the Bexley period the send vertical already serves.
- Migration from fixture data: the fixture Settings view's demo strings are deleted, not migrated; grants seed from the current server-fixed admin (that admin becomes the first owner grant, preserving day-one behavior).
- Rollout/feature flag: `settings-v1` per section; Team & Roles ships first behind the flag with the legacy admin check as fallback until the 403 fixtures pass.
- Build order (waves):
  1. Routes + SettingsNav + role filtering (invisible-not-disabled proven in DOM).
  2. Team & Roles vertical (the gap-4 seam — highest-consequence wire in the whole surface).
  3. Intake section (blocked on gap-1 minting; ships the honest "not yet issued" card first).
  4. Agent memory inspector (blocked on the D3 memory store; the inspector UI can land against the store's contract fixtures).
  5. Notifications + Data + Clients + Billing placeholder + personal door.
- Proof artifacts required: the 200/403 flip recording; the invisible-nav DOM assertion; the rotation-gate recording; a memory-version chain screenshot with provenance visible.
- Final gate: `ADJUST` confirmed — the chassis and the section shell are kept; every section's content is rebuilt from FIXTURE to wired; no rebuild of the frame.
