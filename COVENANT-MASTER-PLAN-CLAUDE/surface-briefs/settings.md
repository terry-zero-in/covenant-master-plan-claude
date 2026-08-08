# SETTINGS

## 1. Identity and verdict

- Product layer: portfolio floor (B7) with cross-cutting reach — Settings is where org-level law (tenancy, roles, intake identity, escalation policy, agent memory administration) becomes legible and editable.
- Route/address in the basis-v2 repository: existing Settings view inside the `/review-room` shell → addressable `/settings/*` in the `(covenant)` route group (08-GLOBAL-FRAME-SHELL-PLAN §1); section routes: `/settings/organization`, `/settings/clients` (PMC mode only), `/settings/intake`, `/settings/team`, `/settings/agent`, `/settings/notifications`, `/settings/data`, `/settings/billing` (workspace door) and `/settings/profile`, `/settings/appearance` (personal door). Components in `src/components/covenant/settings/`.
- Current build state: `FIXTURE` (snapshot §3: account/settings shell, demo strings).
- Existing-surface verdict: `ADJUST` (shell exists; sections, scoping, and every wire are new).
- Research tier: B.
- Primary users/roles: owner/admin (workspace door, all sections); PMC preparer (Agent section within client scope + personal door); reviewer (personal door only). Role-gated sections are **invisible, not disabled**, to those without the role (R6 research: GitHub).
- Frequency and session duration: heavy in week one (org setup), then rare, short, purposeful visits — change a grant, correct a memory entry, adjust escalation offsets. Never on the monthly critical path.
- Error cost: high in specific cells — a mis-granted send right lets the wrong person execute the send gate to a lender; a leaked or stale intake address misroutes lender-facing documents; a wrong memory correction propagates into every future package field it pre-fills. General error cost (appearance, density) is trivial; the surface's design weight goes exactly where the error cost is.
- Success criterion: every org-level policy that governs a gate is findable in one palette jump, legible in one screen, and changes only through a typed, audited act; the send role is genuinely assignable (gap 4 unfixed → fixed); no non-admin ever sees an admin section.

## 2. User job and decisions

- Primary job-to-be-done: "Make the org run the way our engagement letter says it runs — who prepares, who certifies, who sends, where documents arrive, when we get chased — and be able to prove the current configuration."
- Decisions made here: role and certify/send grant assignment (per client scope in PMC mode); intake address rotation and sender recognition; escalation ladder offsets; chase cadence defaults; memory corrections/retirements; COA mapping administration (retire/rename); client directory maintenance; invite issuance; export/retention choices; personal density and motion preferences.
- Questions the surface must answer in scan order: (1) what scope am I editing — workspace or my account? (2) which section am I in and who else can see it? (3) what is the current value/policy? (4) what will change downstream if I edit it? (5) who set it last, when?
- What the user should not have to decide here: anything per-period (dispositions, confirmations, review — those live on the spine); any shipping number; the vocabulary or thresholds of covenant verdicts (loan documents + engine own those); design tokens.
- Entry paths: org switcher (top-left of the rail) → workspace settings; profile cluster (rail bottom, 08 §3) → personal settings — Linear's two doors, one mental model (R6 research: Linear). Also: `⌘K` palette ("intake address", "roles", "memory") deep-links to the exact section; inline "learned {date} from {who}" chips anywhere in the app link to the matching memory entry in `/settings/agent`; Intake/Holding's read-only address card links here.
- Exit paths: back to the previous surface (Esc/breadcrumb — Settings is book-altitude, no loan/period crumbs, 08 §2); inline memory chips return to their point of use; invite flow ends with a link to Team.
- Completion/advancement conditions: none — Settings never advances a period. Its acts feed gates elsewhere (a certify grant makes a person eligible at gate 2; the send gate re-checks grants at gate 3).

## 3. Object and ownership model

| Object/action/status | Owned here? | Summary/link elsewhere | Address/identity | Notes |
|---|---:|---|---|---|
| Organization identity (name, plan ref) | YES | Rail org switcher shows name | `orgId` | (02-OBJECT-RELATIONSHIP-MODEL §2) |
| Client directory (PMC mode) | YES | Client scope chips render app-wide | `clientId` | Invisible in owner mode (02 §1) |
| Designated intake address (issue/display/rotate) + sender recognition list + sub-addressing | YES | Intake/Holding displays the address read-only ("send documents to …") — the ruled summary allowance (02 §3) | per-org address record | Covenant's OWN address, minted per organization, NEVER shared with any other product (product-boundaries law) |
| Roles + certify/send grants + invites | YES | Certificate/Send surfaces render "eligible: {names}"; gates enforce | member `userId` × scope | Unfixes gap 4: sender is currently server-fixed to admin (snapshot §6.4) |
| Escalation policy (remind→chase→your-move→push ladder) + chase cadence defaults | YES (the policy document) | Calendar/Obligations owns deadlines and *executes* the ladder (02 §3); period headers summarize | org policy record | The ONE push-eligible class is deadline escalation (04 §2.3) |
| Quiet-log retention window (display window, never deletion) | YES | Quiet log renders per loan/period | org policy record | Seal-not-wipe forbids deletion inside sealed periods (02 §5) |
| MemoryEntry store | NO — agent layer owns the store (02 §2) | **This surface hosts the central inspector** (Settings → Agent memory, per 02 §2); entries also render inline at point of use | `memId` | Corrections version; the agent never re-asks (03 §3) |
| COA mapping rows (propose/confirm/correct) | NO — Actuals & Computation owns lifecycle (02 §3) | `/settings/data` hosts the **administrative register**: browse, search, retire-on-PMS-change, export | `(propertyId, coaCode)` | Retire is an administrative act owned here; confirmation acts never happen here |
| Appearance (density, reduced motion) | YES (personal scope) | — | per-user prefs | |
| Billing (plan, seats, invoices) | YES (placeholder) | — | org billing record | Roadmap; no pricing invention (RR v1/v2 boundary lives in `rent-roll-teaser-placement.md`) |

No-double-homing boundary: Settings owns *configuration and administration*; it never owns execution. Deadlines execute in Calendar/Obligations, gates enforce on Certificate/Send, memory fills fields at point of use, mappings confirm in Actuals — each of those surfaces may render the governing setting only as a read-only summary with a link here, and Settings renders their live state (e.g., "3 escalations fired this month") only as a count with a link out.

## 4. Data and semantic model

- Source facts: none arrive here (no documents; Settings is configuration, not evidence).
- Extracted values awaiting confirmation: none by design.
- Confirmed values: every setting is a confirmed value with actor + timestamp; grants and policies are versioned records, not mutable cells.
- Deterministic outputs: eligibility sets computed from grants ("who can certify {loan}"); the materialized escalation schedule (engine combines policy offsets with due-rules, 03 §5); sender-recognition match results.
- Agent proposals/drafts: none — the agent never edits settings. The Agent section *displays* the agent's memory and cadence behavior; proposals stay on work surfaces.
- Human decisions: grant changes, address rotation, sender approval/removal, ladder edits, memory correct/retire, mapping retire, invite issue/revoke, client add/archive, export request.
- Certified values: none; Settings never touches package content. (It touches who may certify — metadata about the gate, never the gate's content.)
- Versions/periods/packages: settings records version by change history (who/when/old→new); no period grain anywhere on this surface.
- Evidence/provenance: every memory entry carries "learned {date} from {who} during {context}" + the originating question/run link (03 §3); every grant carries grantor + timestamp; every sender-recognition row carries first-seen message ref and authentication result.
- Permissions/read-only projections: workspace sections render only for admins (invisible otherwise); Agent section renders for owner + preparer, client-scoped in PMC mode; reviewers get the personal door only.

Field groups and grain: Organization (`orgId`: name, intake address record, policy refs) · Client (`clientId`: name, owner contacts, sub-address token, branding-for-exports) · Member (`userId` × org: role, certify grant scope[], send grant scope[], invite state) · PolicyRecord (escalation offsets, chase cadence defaults, quiet-log window) · MemoryEntry (`memId`: scope borrower/loan/lender/property-PMS, question, answer, provenance, versions, reuse log) · COAMappingRow (`propertyId`+PMS account → COA code, confirmer, status) · BillingRecord. Loan/period/package/test grain: absent by design.

## 5. State machine and exceptions

| State (template-required) | On this surface | Trigger → resolution | Actor / reversibility / audit |
|---|---|---|---|
| empty | First visit pre-setup: sections render with explicit "not configured" cards (no fake defaults) | Onboarding acts fill them (see `onboarding-signin-import.md`) | Human; n/a; each fill audited |
| awaiting-documents | N/A by design — no documents arrive here; the Intake section shows a link-out count of held arrivals only | — | — |
| loading/processing | Section skeleton on the gray ladder; never fake values | Data loads → render | Engine |
| partial/incomplete | A section with required fields unset (e.g., no send grantee) renders a labeled incomplete banner with the downstream consequence ("send gate has no eligible actor") | Admin completes | Human; reversible |
| extracted/unconfirmed | N/A — nothing is extracted here | — | — |
| stale/superseded | A retired intake address inside its grace window; a superseded memory version; a retired mapping | Rotation/correction | Prior versions kept, labeled |
| low confidence | Sender-recognition proposals below auto-match threshold render as "needs review" rows | Admin approves/rejects | Human; reversible; audited |
| conflict | Two admins edit the same policy: last-write warning with both versions shown; explicit re-apply | Second editor resolves | Human; reversible via history |
| failed tie-out | N/A — no package math here | — | — |
| watch/shortfall/breach | Never rendered here — verdict vocabulary appears only on covenant surfaces (vocabulary law) | — | — |
| permission denied | Structurally unreachable for hidden sections (invisible, not disabled — R6 research: GitHub); direct URL hit → honest 403 page naming the required role | — | Audited access attempt |
| read-only | Reviewer's personal door; preparer viewing org-scoped memory entries outside their client scope sees nothing (scope-filtered, not grayed) | — | — |
| blocked/gated | Address rotation blocked while an unresolved arrival sits on the old address (fail-closed: rotate would orphan it) | Resolve arrival → rotate proceeds | Engine gate; honest reason |
| certified (void-on-change) | Not applicable to settings records; NOTE: a grant revocation never voids an existing CertificationRecord (the act was valid when performed) — stated to prevent over-engineering | — | — |
| sent/sealed | N/A | — | — |
| recovery/undo | Every setting change is history-versioned and revertible EXCEPT: address rotation (old address cannot be un-retired — new mint only; irreversibility stated in the confirm), invite revocation (re-invite instead), and memory retirement (retire is reversible; hard delete does not exist — audit trail law) | — | Append-only history |

Intake-address sub-machine: `active` → (typed rotate act) → `rotating` (old + new both deliver for the grace window; old-address arrivals badge "received on retired address") → `retired` (old bounces with a neutral non-product-revealing rejection). Invite sub-machine: `sent(expiry 14d default, admin-adjustable — R6 research: Ramp)` → `accepted | expired | revoked`.

## 6. Engine / Agent / Human / Gate / Quiet Log

Five-lane table (per 04-ENGINE-AGENT-HUMAN-GATE-MAP §1; Settings rows reconcile with "Memory & learning" and "Calendar & deadlines" chapters):

| Lane | On Settings |
|---|---|
| ENGINE | Eligibility computation from grants (feeds the real send gate — the gate reads persisted grants, not a hardcoded admin, retiring gap 4); escalation-schedule materialization from policy offsets × due-rules; sender authentication (SPF/DKIM result display); sub-address token minting; invite expiry; storage/provenance integrity of memory versions (04 §1 "Memory & learning": engine = storage integrity, provenance stamps) |
| AGENT | None as editor. Displayed here: the memory store it maintains, its chase cadence behavior, its rotating capability prompts (visibility toggle). The agent never proposes a settings change; it may *surface* a finding elsewhere ("no send grantee configured") that links here |
| HUMAN | All acts: grants, rotation, recognition decisions, ladder edits, memory correct/retire (typed, versioned — 04 §1: "Correct a remembered answer (versioned)"), mapping retire, invites, exports |
| GATE | Settings itself gates nothing on the period spine. Local gates: rotation blocked by unresolved old-address arrival; grant-removal confirm when it would leave zero eligible senders/certifiers ("the send gate will have no eligible actor") |
| QUIET LOG | Every settings change writes an ActivityEvent (actor, old→new, scope); memory reuse events ("pre-filled from memory {ref}") aggregate as per-entry reuse counts here; zero push from this surface |

## 7. Information hierarchy

1. Page/frame header: shell header, book-altitude breadcrumb `Covenant / Settings` (no loan/period crumbs — 08 §2 altitude gate).
2. Decision/status summary: the section nav with per-section incomplete badges (shared CountBadge) — the scan answers "what needs an admin".
3. Primary work region: the active section's content pane — current values first, edit affordances second.
4. Secondary context: per-record downstream-consequence lines ("used by 4 loans' normalization", "eligible senders: 2").
5. Evidence/proof: provenance chips on every record (who/when/context); memory entries open their originating question/run.
6. Actions: inline per-record; destructive/legal-weight acts through typed confirms.
7. Activity/history: per-section change history (filtered quiet-log view), collapsed at the bottom of each section.
- Absent by design: any covenant verdict, any figure from a package, any per-period state, any chart (nothing here out-encodes text — chart doctrine applied honestly: zero charts).

## 8. Page anatomy and regions

| Region | Purpose / content | Persistence | Interaction | Min size | Collapse behavior | Why this container |
|---|---|---|---|---|---|---|
| Section nav (left, inside the work area) | Two labeled scope groups: **Workspace** (Organization, Clients [PMC], Intake, Team & Roles, Agent, Notifications, Data, Billing) and **Your account** (Profile, Appearance); settings-search field at top (R6 research: Stripe — settings reachable from search) | pinned | Click/↑↓ to switch section; type-to-filter leaves matching leaves only | 240px | Below 1280px total: collapses to a section dropdown above content | One surface, two clearly labeled scopes — Linear's two doors collapsed into one addressable surface (R6 research: Linear + GitHub adapt); a second *work window* is unjustified: no settings decision needs side-by-side panes |
| Section content (the one big pane) | The active section; single-column record cards and registers | persistent | Scroll; inline edit; registers use the shared grid | 720px | Never silently compresses; registers scroll within the pane | Pane model law: one big pane + view switching |
| Canvas panel | Ask ("what does the escalation ladder do?") and memory-entry quick inspect summoned from inline chips elsewhere | summonable | Esc dismisses | per frame law | floats/docks per 08 §4 | Transient tooling, never load-bearing |
| Typed-confirm modal | Rotation, grant changes affecting certify/send, memory retire, client archive | transient | Type-to-confirm + consequence statement | 480px | — | Modal law: typed acts with legal weight and destructive confirms only |

Non-admins never render the Workspace group at all — the nav shows only "Your account" (invisible, not disabled; R6 research: GitHub).

## 9. Co-visibility matrix

| Datum/surface A | Datum/surface B | Must be simultaneous? | User decision enabled | Default topology | Fallback |
|---|---|---:|---|---|---|
| Member row (Team & Roles) | Consequence line ("can execute send for {scope}") | YES | Grant/revoke with eyes open | Same row, inline | — |
| Grant-change confirm | Current eligible-actor count | YES | Avoid zero-eligible states | Inside the modal | — |
| Intake address card | Sender recognition list | YES | Rotation decision needs to see who mails the address | Same section, stacked | Anchor links |
| Memory entry | Its provenance + version history | YES | Correct vs retire | Row expands in place (no modal — proof beside the claim, lit-row law) | — |
| Memory entry | The field it fills (its point of use) | NO | Link-out suffices | "Used at →" link | — |
| Escalation ladder | A worked example rendered from a real due-rule (e.g., Q+45d from the confirmed schedule; evidence: SLOT-3 §8.02(b)) | YES | Offsets are meaningless without a concrete date line | Example strip under the editor | — |
| COA mapping row | Its confirming actor + property/PMS identity | YES | Retire decision | Row columns | — |
| Settings section | Any spine surface | NO | Never — Settings is never part of a review decision | Navigate away | — |

## 10. Layouts and viewport behavior

- 1440px: nav 240px + content 900–1100px single column; registers (recognition list, memory inspector, mappings) full-width within content; no split offered.
- 1728px: nav 260px + content max-width 1180px, centered-left; history column may sit right of a register as a second *interior* column (not a work window).
- 2048px: same, content capped at 1280px — settings never stretches line lengths; extra ground stays open.
- Narrow/compact (<1280px): nav becomes a dropdown; registers keep uniform rows with horizontal scroll inside the pane; typed confirms remain full modals.
- Default pane topology: one work window always; canvas panel summonable. Focus behavior: section switch preserves scroll per section within a session. Compare behavior: none (no split justified). Proof/source behavior: provenance expands in place. Minimum viable: 1152×720 (frame law, 08 §9); registers remain usable below spine minimums. No silent compression: the nav collapses to a labeled dropdown, never squeezes.

## 11. Components and exact anatomy

| Component | Reuse/NEW | Anatomy |
|---|---|---|
| CovenantShell | REUSE (`src/components/covenant/CovenantShell.tsx`) | Mounts `/settings/*` like every surface |
| SettingsNav | NEW | Scope group headers, section rows (icon Lucide + label + CountBadge for incomplete), search field, role-filtered render |
| SettingsSectionScaffold | NEW | Section title, description line, record cards, collapsed history footer |
| RoleMatrixGrid | NEW on the shared grid (uniform row heights, open-not-boxed) | Rows = members; columns = name/email, role (owner·preparer·reviewer per 02 §4), certify-grant scope chips, send-grant scope chips, client scope (PMC), last-active, invite state; footer row = pending invites |
| GrantEditor | NEW | Scope picker (org-wide / per-client / per-loan-set), consequence preview line, typed confirm hand-off |
| IntakeAddressCard | NEW | The address in Geist Mono, copy button, per-org identity line ("minted for {org}; Covenant-only — this address is never shared with any other product"), rotate act, grace-window status, sub-address list (PMC: `{org-token}+{client-token}@…` rows per client) |
| SenderRecognitionTable | NEW on shared grid | Sender address, matched loan/lender, authentication result, first-seen link, status (recognized / needs review / rejected), row actions |
| MemoryInspectorTable | NEW on shared grid | Modeled on the ChatGPT manage-memory pattern, provenance-typed per R6 (R6 research: ChatGPT memory — take the central inspector with per-entry edit/delete; adapt: entries are value + "learned {date} from {who} during {context}" + originating run link + version history, never a synthesized prose summary). Columns: scope (borrower/loan/lender/property-PMS, 03 §3), question, current answer, provenance chip, reuse count, versions. Filters: scope, loan, client (PMC). Row expand = full chain + correct (versioned) + retire. Header: "Pause reuse" master toggle (agent asks instead of reusing; entries retained — the kill switch adapted to a compliance product, R6 research: ChatGPT memory) |
| ChaseCadenceEditor | NEW | Org default offsets (placeholders, org-configurable — no invented cadence numbers), per-loan override note linking to the loan's own confirmed due-rules |
| EscalationLadderEditor | NEW | Four fixed stages remind → chase → your-move → push (03 §5, 04 §2.3); editable offsets per stage; push stage labeled "deadline escalation only — the one push class"; worked-example strip |
| COAMappingRegister | NEW on shared grid | Property/PMS grouping, PMS account → COA code, confirmer + date, status (active/retired), retire act; "confirmed in Actuals & Computation" provenance (ownership per 02 §3) |
| InviteComposer | NEW | Email, role, scope, expiry (14d default, adjustable — R6 research: Ramp), external-preparer seat option (uploads/prepares, never certifies/sends — Ramp Advisor Console adapt) |
| TypedConfirmModal | REUSE pattern (certify ceremony's typed-act anatomy) | Consequence statement, type-to-confirm, audit note |
| CountBadge | REUSE (shared atom, snapshot §4) | Incomplete-section counts |
| CommandPalette | REUSE (`CommandPalette`/`SearchPalette`) | Every settings leaf is a palette place ("intake address", "who can send") |

## 12. Interaction specification

- Selection: single-row focus in registers; no multi-select except recognition-list bulk approve.
- Hover: row hover reveals actions; provenance chips show full context on hover, open on click.
- Focus: visible focus ring (accent family) on every control; section nav is a listbox.
- Keyboard: `⌘K` → any settings leaf; `/` filters the active register; ↑↓/Enter in nav; Esc closes confirms then returns to prior surface. G-chord for Settings deferred to `cross-cutting/search-command-keyboard.md` (08 §5 reserves the map there).
- Editing and validation: inline edit per record; grants validate against zero-eligible states before offering the confirm; address rotation validates the no-orphan gate.
- Bulk action: recognition list only (approve N proposed senders); grants are never bulk (each is a typed act).
- Undo/recovery: change history per section with one-click revert (writes a new version — history is append-only); irreversible acts (rotation) say so inside the confirm.
- Sorting/filtering: registers sort by column; memory inspector filters by scope/loan/client; saved views via the shared SavedView mechanism (02 §2).
- Drill-down and return: memory chip elsewhere → `/settings/agent?entry={memId}` with the row expanded → "back to {surface}" returns exactly.
- Source-linked selection: memory provenance opens the originating question/run (quiet-log detail); recognition rows open the first-seen arrival in Intake — the lit-row contract applied to configuration provenance.
- Save/persistence: explicit save per record card (no autosave on grant/policy records — deliberate acts); personal appearance prefs save instantly.
- Collaboration/commenting: none by design; the change history is the collaboration record.

## 13. Visual craft direction

- Typography: section titles 16px/600; record labels 13px/500; values 13px/400; provenance lines 12px on a mid gray rung. Addresses, tokens, and any figure in Geist Mono with `tabular-nums slashed-zero` (ruled setting).
- Spacing rhythm: 8px base; record cards on 24px vertical rhythm; registers uniform 40px rows.
- Density: comfortable by default (settings is read-rarely); the personal density preference does not apply to typed-confirm modals.
- Open ground vs earned boundaries: no boxed cards — sections separate by whitespace + 1px hairline dividers on the ruled gray ladder; the only filled panels are status banners (accent tint #A9B5FF at low emphasis for informational, gray rungs otherwise).
- Semantic color: accent family only for interactive/focus (#7189FF, hover #8EA1FF, active #6078F4); incomplete badges use the standard CountBadge treatment; no verdict colors anywhere on this surface (vocabulary law — verdicts don't render here).
- Certified-sheet treatment: not touched — Settings never renders the certified sheet; the six ruled hexes do not appear here.
- Focus/selected/hover: nav selected state uses the ruled selected-state tokens (Ruling-J boundary: Covenant's own tokens, snapshot §4); hover = one gray rung shift.
- Charts: none (doctrine-honest — nothing here out-encodes text).
- Motion: section switch is an instant swap; row expand 120ms ease-out; reduced-motion preference (set on this very surface) disables all transitions app-wide.
- Long-session ergonomics: minimal — sessions are short by design; line lengths capped at ~72ch.

## 14. Benchmark research and synthesis

| Product | Limited role | Official evidence | Exact pattern to take | Covenant adaptation | What to reject | Why this beats alternatives |
|---|---|---|---|---|---|---|
| Linear | Settings IA model | https://linear.app/docs/workspaces · https://linear.app/docs/account-preferences | Two scoped doors (workspace name → workspace settings; avatar → personal), one mental model; members see only their own scope, admins additionally see administration | Org switcher → Workspace group; profile cluster → Your account group; one `/settings/*` surface, two labeled groups | Engineer-centric shortcut density | The cleanest live two-scope IA; matches the rail anatomy already ruled (08 §3) |
| GitHub | Admin-gating control case | https://docs.github.com/en/organizations/managing-organization-settings | Role gates that hide, not disable: only org owners *see* org settings | Workspace group absent from non-admin nav; direct URL → honest 403 | Multi-hop navigation (profile → orgs → org → settings) | The strictest visibility precedent; "disabled but visible" leaks org structure to reviewers |
| Stripe | Settings reachability + team roles | https://docs.stripe.com/dashboard/basics · https://docs.stripe.com/get-started/account/teams | Gear → grouped business settings; Team tab with a clarified role matrix; settings findable from global search | Settings-search field + every leaf a palette place; RoleMatrixGrid with explicit grant columns | Payments-grade multi-mode chrome (test/live toggles) | The reference for *findable* settings at scale; its role matrix is the closest to certify/send grant semantics |
| ChatGPT | Agent-memory inspector | https://help.openai.com/en/articles/8590148-memory-faq · https://openai.com/index/memory-and-new-controls-for-chatgpt/ | Central Manage Memory: every entry listed, per-entry delete/edit, master toggle; saving visible at capture time | MemoryInspectorTable: provenance-typed entries (value + learned-from + run link + versions); correct-versioned instead of delete; "pause reuse" instead of wipe; retire keeps audit | Opaque cross-conversation synthesis; free-text memory summary | The only mainstream user-inspectable AI memory UI; R6 names it the reference and the adaptation (R6 research: ChatGPT memory) |
| Ramp | Invites + external seat | https://support.ramp.com/hc/en-us/articles/1500002006322-Getting-started-as-an-Admin | Role-scoped expiring invites (14d default, 1–365 adjustable); Advisor Console scoped external seat | InviteComposer expiry + external-preparer seat (uploads/prepares, never certifies/sends) | Card-issuance eager provisioning | Directly solves the outside-accountant actor in 02 §4 without inventing a role |

Synthesis: Linear supplies the two-door scoping, GitHub the invisibility rule, Stripe the findability and role-matrix explicitness, ChatGPT the memory-inspector skeleton, Ramp the invite mechanics — but the result is domain-original because the load-bearing cells exist in none of them: certify and send as *separately assignable, per-scope grants that a real server gate reads* (gap 4), an intake address that is a per-org compliance artifact with rotation gates and sender authentication, and a memory inspector where every entry is a provenance-typed compliance fact ("learned 2026-07-14 from J. Torres during Q2 review") rather than a lifestyle preference. Settings here is the legible constitution of a lender-reporting org — no benchmark has that job.

## 15. Domain references

Loan-servicing and financial-close products consulted for terminology and expected semantics only: servicer submission portals of the kind behind the JLL questionnaire workflow (evidence: quarterly Property Questionnaire with signed certification block — the preparer-vs-certifying-officer distinction that makes certify/send grants a real-world role split, not an invention), and close-management tools' preparer/reviewer/approver vocabulary. Domain authority does not equal visual authority: none of these governs a pixel here. Covenant semantics — what a certification is, who may sign, what a due-rule means — come from the loan documents and Terry (evidence: SLOT-3 §8.02(b); knowledge corpus per snapshot §5), never from any referenced product.

## 16. Accessibility, performance, and safety

- WCAG: AA contrast on all rungs used for text; focus ring visible on every interactive element; the accent-on-accent pairing uses `--accOn #0B1020`.
- Keyboard completeness: every act (including grant changes and rotation) completable keyboard-only; typed confirms are keyboard-native by definition.
- Screen-reader semantics: nav = `nav` + listbox; registers = proper table semantics with row headers; provenance chips are labeled buttons ("provenance: learned 14 July 2026 from J. Torres").
- Table virtualization: memory inspector and mappings virtualize past ~200 rows; filters run server-side.
- Loading/latency: skeletons on the gray ladder; save acts show inline pending state; no optimistic rendering of grant changes (the gate must be truthful).
- Destructive confirmation: rotation, retire, revoke, archive — all typed confirms with consequence statements.
- Certify/send safety: grant changes are typed acts, audited, and enforced server-side at the real gates (`POST /api/covenant/send` re-checks at execution; a revocation between certify and send denies with an honest 403); a grant change never mutates an existing CertificationRecord.
- Source immutability: settings history append-only; memory versions immutable; retired addresses unrecoverable by design.
- Auditability: every change an ActivityEvent (actor, scope, old→new); the section history views are filtered quiet-log queries, not a second store (07 §2 duplication rule).

## 17. Acceptance tests and fixtures

Fixtures: the 7-loan demo book with Bexley (canon), a PMC org fixture with two Clients, member fixtures (1 owner, 2 preparers, 1 reviewer, 1 external-preparer seat), the Calloway Park confirmed schedule for the due-rule example (evidence: SLOT-3 §8.02(b)).

1. **Gap-4 unfix (the vertical):** grant send to preparer P for Client A only → P executes `POST /api/covenant/send` for a Client-A loan → 200 + append-only SendRecord; same call for a Client-B loan → 403 naming the scope; revoke → immediate 403. Server reads persisted grants; the hardcoded admin path is deleted.
2. **Invisible-not-disabled:** log in as reviewer → Workspace group absent from nav and DOM; direct `GET /settings/team` → 403 page naming the required role; no org data in the response.
3. **Zero-eligible guard:** attempt to revoke the only send grant → confirm modal states "the send gate will have no eligible actor"; completing it renders the Team section's incomplete banner and a your-move item for the owner.
4. **Rotation gate:** stage an unresolved arrival on the current address → rotate act blocked with the honest reason; resolve the arrival → rotation proceeds; old address delivers during grace with the "retired address" badge; post-grace delivery bounces; the address history shows both mints.
5. **PMC sub-addressing:** Client A and B each render a distinct sub-address; a message to A's sub-address can never surface in B's scope (assert scope filter at the query layer).
6. **Product isolation:** assert the intake address record is minted per `orgId` in Covenant's own store and no other product's configuration references it (product-boundaries law; one designated address per product, never crossing).
7. **Memory inspector:** correct the remembered management-fee answer (03 §3 lender-scope example) → new version with corrector provenance; the old version visible in history; the inline chip at point of use renders the new value; the agent does not re-ask next period (zero-repeat-question assertion across two consecutive periods, agent brief's shared test).
8. **Pause reuse:** toggle on → the agent asks instead of pre-filling, entries retained; toggle off → reuse resumes from the newest versions.
9. **Escalation editor:** set offsets → the worked example recomputes from the Q+45d due-rule fixture; only the push stage is push-eligible; assert no other settings path can enable push for any other event class.
10. **Provenance fixture:** click a recognition row's first-seen link → the exact arrival opens in Intake with the source lit (lit-row contract).
11. **Viewport fixtures:** 1440/1728/2048 renders per §10; at 1279px the nav becomes the labeled dropdown (no silent compression); minimum 1152×720 usable.
12. **Accessibility:** keyboard-only pass through grant→confirm→audit; screen-reader announces consequence statements; contrast audit on all rungs used.
13. **Benchmark challenger review:** a reviewer familiar with Linear/GitHub/Stripe settings attempts to find intake address, send rights, and a memory entry in under 15 seconds each via palette — all three must succeed.

## 18. Build plan

- Dependencies: tenancy hierarchy (gap 3: org→client FKs — the hard prerequisite for scoped grants and sub-addressing); persisted role/grant table read by the send gate (the gap-4 fix); memory store (agent brief D3); notification/escalation machinery (gap 9); intake pipeline for address minting (gap 1 — the Intake section ships read-only "not yet issued" until it lands).
- Foundation work: `/settings/*` routes mounting CovenantShell; role-filtered SettingsNav; settings change-history events on the ActivityEvent store.
- Components first: SettingsNav → RoleMatrixGrid + GrantEditor + TypedConfirmModal (the gap-4 seam) → IntakeAddressCard → MemoryInspectorTable → EscalationLadderEditor → registers.
- Vertical slice (send-vertical pattern): Team & Roles wired end to end — persisted grants → `/settings/team` UI → `POST /api/covenant/send` reading grants → 200/403 flip provable in one demo, against the Bexley period the send vertical already serves.
- Migration from fixture data: the fixture Settings view's demo strings are deleted, not migrated; grants seed from the current server-fixed admin (the admin becomes the first owner grant, preserving behavior on day one).
- Rollout/feature flag: `settings-v1` flag per section; Team & Roles ships first behind the flag with the legacy admin check as fallback until the 403 fixtures pass.
- Proof artifacts: the 200/403 flip recording; the invisible-nav DOM assertion; the rotation-gate recording; memory-version chain screenshot.
- Final gate: `ADJUST` confirmed — shell kept, every section rebuilt from FIXTURE to wired, no rebuild of the chassis.
