# CROSS-CUTTING — ROLES, PERMISSIONS, AND TENANCY

Current state: **no real tenancy beyond the send route's org check** (gap 10); the object hierarchy has no Client FK (gap 3); the send role is **server-fixed to admin** (gap 4); auth exists (Clerk — the sign-in route and the guest bounce prove it) but the outward door is the benchmark's one BLOCK (snapshot §6). This file is the one contract for who may do what, over which slice of the record, and how every act is attributed. It consumes the actor model (02-OBJECT-RELATIONSHIP-MODEL §4), the typed-act inventory (03 §1–2, 04 §1), the Settings and Onboarding briefs' directions, and the R4/R6 research.

## 1. Why this is a system, not a feature

Permissions in a lender-reporting product are not UI polish — they are part of the audit story the product sells. A CertificationRecord that cannot say *in what capacity* its human acted is a weaker legal artifact; a PMC that cannot prove client isolation cannot take on a second client; a send gate hard-wired to "admin" (gap 4) makes the org chart lie about who actually ships packages. So this contract has one enforcement point (server-side scope resolution), one attribution rule (actor + capacity on every typed act), and one visibility rule (what you cannot do, you do not see).

## 2. The tenancy model implementation

### 2.1 The scoping chain (02 §1, made enforceable)

```text
Organization (orgId)          — the account boundary: intake address, members, roles, plan key here
└── Client (clientId)         — PMC mode only; the fee manager's owner client
    └── …every object below   — Sponsor, Portfolio, Property, Pledge, Loan, ReportingPeriod,
                                Package, CertificationRecord, SendRecord, Document, ExtractedValue,
                                NormalizedFinancialLine, Metric, TestResult, MemoryEntry,
                                Finding, ActivityEvent, CorrespondenceThread, SavedView, …
```

- **Every persisted object below Client carries `client_id`** (migration work under gap 3; the fixture types already model the graph). No object is reachable except through a scope check on `(orgId, clientId)`.
- **Owner mode = exactly one implicit Client.** Created silently at org creation; every query still passes through the same scope layer (one code path, not two). The UI renders **zero Client chrome** in owner mode: no Client column in any register, no Clients directory section in Settings, no client crumb, no client filter chip, no client scope switcher. The layer is structural, never visible, until a second Client exists (02 §1: "surfaces never show a 'Client' column to a single-owner org"). Converting an owner org to PMC mode is an org-admin act that names the implicit Client (it becomes Client #1) — no data moves.
- **PMC mode** (a fee property-management company preparing for owner clients): the Clients directory appears in Settings → Organization; registers gain a client scope switcher; the intake address gains per-client sub-addressing (Settings brief). Client is a hard read boundary: no register, palette result, Ask answer, trend series, or export may join across Clients for client-scoped members. Org-level PMC staff assigned to multiple Clients switch scope explicitly; nothing renders cross-client aggregates unless the actor's scope spans the clients *and* the surface is an explicitly org-level register (the PMC's own book view).
- **Portfolio is never a security boundary** (02 §1). Permissions bind at Organization and Client only; Portfolio is navigation and must de-duplicate by Property, never gate.

### 2.2 The one enforcement point

- NEW `src/lib/covenant/tenancy.ts` (pure, test-covered like the rest of the engine library): `resolveActorScope(session) → { orgId, clientIds[], role, grants, sodPolicy, engagementConfigs }`. Route handlers and server actions call it first; nothing trusts client-side state.
- NEW `withScope(query, scope)` helper: every data read/write in `src/lib/covenant/` composes through it. A query that does not declare its scope does not compile (lint rule, CI-enforced — the same enforcement style as the narrative-interpolation ref rule in `cross-cutting/provenance-lit-row-trace.md` §3.6).
- The pattern to copy is already REAL: `POST /api/covenant/send` checks org scope and returns honest 503/404/403 (snapshot §3). This contract extends that exact behavior to every typed-act route: **403 = scope/role/grant denial with the reason; 404 = object outside your scope** (the object's existence is not confirmed across a tenant boundary).
- The agent obeys the same layer: Ask grounding reads, findings evaluation, chase drafting, and memory reads/writes all pass through `withScope` with the *acting member's* scope when responding to a member, and the owning Client's scope when running autonomously for a period. There is no agent super-scope.

### 2.3 Scope keys by object class

| Keys at | Objects | Note |
|---|---|---|
| Organization | Organization, members/roles/grants, the designated intake address, plan/billing, escalation policy defaults | The intake address is minted per org (02 §2) — never shared with any other product |
| Client | Everything in the object graph below Client (§2.1 list) | Owner mode: the one implicit Client |
| Client engagement | EngagementConfig (NEW): reserved grants, SoD setting, reviewer visibility toggles, sub-address | Owned by Settings → Organization (PMC mode) / Team & Roles |
| Act records | ActivityEvent, CertificationRecord, SendRecord, confirmations, dispositions, waivers | Carry `(orgId, clientId)` + actor + capacity (§9); append-only |

## 3. The role matrix

Three human roles (02 §4) plus two **assignable capability grants** that ride on top of a role. Grants — not roles — carry certify and send, because who signs is an org decision, not a job title.

| Role | Meaning | Scope |
|---|---|---|
| **Owner** | Borrower principal (owner mode) or the client-side principal seat in PMC mode | Org-wide (owner mode); their Client (PMC client seat) |
| **PMC preparer** | Fee-manager staff preparing packages | Assigned Client(s) only |
| **Reviewer (read-only)** | Lender-relations counsel, partners, auditors | Assigned Client(s)/loans; read-only projection (§6) |

Grants: **CERTIFY** and **SEND** — explicitly assignable per member, per Client engagement. This **unfixes gap 4**: the send gate stops reading a server-fixed admin flag and starts reading the SEND grant from `resolveActorScope`. Defaults: owner-mode Owner holds both; PMC engagements default to *preparer prepares, owner certifies and sends* (02 §4) with the engagement able to delegate either grant to a named PMC principal. **No admin bypass exists**: an org admin without the SEND grant gets the same 403 as anyone else (R4 take from GitHub protected branches — bypass lists are the risky default Covenant rejects; even privileged users stay gated).

### 3.1 Typed acts × roles

| Typed act (owning surface) | Owner | PMC preparer | Reviewer | Agent/Engine |
|---|---|---|---|---|
| Resolve/route ambiguous arrival (Intake) | ✓ | ✓ (their Clients) | never | agent proposes only |
| Recognize a new sender (Intake, §7) | ✓ | ✓ | never | never |
| Waive a checklist item (Intake/Holding) | ✓ | ✓ | never | never |
| Confirm requirement schedule (Extraction & Confirmation) | ✓ | ✓ | never | agent proposes only |
| Confirm/correct extracted value (Extraction & Confirmation) | ✓ | ✓ | never | agent proposes only |
| Confirm COA mapping (Actuals & Computation) | ✓ | ✓ | never | agent proposes only |
| Disposition failed tie-out (Review Room) | ✓ | ✓ | never | engine computes, never dispositions |
| Disposition finding — shortfall/breach (Home/Loan Detail) | ✓ | ✓ | never | agent raises, never dispositions |
| Approve narrative draft (Composer) | ✓ | ✓ | never | agent drafts only |
| Approve outbound reply (Lender Q&A) | ✓ | ✓ | never | agent drafts only |
| **CERTIFY** (Certificate) | grant | grant (engagement-delegated) | **never** | **never — ever (locked law)** |
| **SEND** (Send & Record) | grant | grant (engagement-delegated) | **never** | **never — ever (locked law)** |
| Correct a memory entry (Settings → Agent / inline) | ✓ | ✓ | never | agent versions, never self-corrects facts |
| Comment / export permitted views | ✓ | ✓ | ✓ (where engagement permits) | — |
| Org admin: roles + grants, Clients directory, intake address rotate, escalation policy, billing (Settings) | ✓ (owner mode) / PMC org admin | never | never | never |

Two actors from 02 §4 deliberately have no column. **The lender/servicer is NEVER a user in v1** — recipients are contact data on SendRecords, not accounts; no lender role is constructible; a lender-side mode is a later product decision this contract must not presume. And the agent/engine hold no grants by construction: the certify and send gates accept only an authenticated human session performing the typed act — there is no API path around either (R4 synthesis 12).

### 3.2 Grant lifecycle

- Assignment and revocation are org-admin typed acts in Settings → Team & Roles, quiet-logged with actor + capacity like every act (§9). First assignment happens in onboarding step 3, "designate signers" (Onboarding brief).
- Revocation is immediate at the server (the next gate check fails); it never rewrites history — records certified/sent under a grant stand, stamped with the capacity that held then (§9).
- The Team & Roles surface renders the full matrix (members × role × grants × Client scope) as one table — the org chart the audit story depends on, readable at a glance.
- Escalation targeting follows grants: deadline escalation for "awaiting certification" routes to CERTIFY-grant holders for that Client, not to admins generically (Calendar/escalation machinery, gap 9).

## 4. Separation of duties (the Mercury option)

Per R4 (Mercury: "the person who initiates a payment can no longer approve it, even if they are listed as an approver," justified explicitly by audit/SOX expectations):

- **The toggle:** `preparer ≠ certifier`, org-level default, overridable per Client engagement (EngagementConfig). When ON, the member recorded as preparer-of-record for a package revision is refused at the certify gate with a named reason ("separation of duties: you prepared this package; {names} hold the certify grant").
- **Preparer-of-record is computed, not declared:** the set of members who performed typed acts feeding this package revision — confirmations, corrections, mapping confirmations, dispositions, narrative approvals — derived from the act history. Any member in that set is blocked as certifier while the toggle is ON. (The certifier may still be the sender; certify-vs-send separation is a second toggle of the same shape, OFF by default.)
- **Default OFF for owner-operators** — a 2–50-loan principal preparing and certifying their own package is the normal case, and certification is itself the accountability act (R4: Mercury's auto-fulfill default is rejected *as a default*, but the initiator-certifier collapse is legitimate exactly here — the typed attestation is the accountability).
- **Recommended ON for PMC engagements** — surfaced as the recommended setting during engagement setup (Onboarding step 3), because preparer-certifier split is the engagement's actual shape. A solo org enabling SoD gets a plain warning ("with one member holding all acts, this setting will block certification") — honest, not prevented; Mercury's structural analog: the dual-admin policy cannot even be enabled without two admins on file.
- **Timing:** the policy is evaluated at gate time (certify), not at act time — flipping the toggle mid-period affects only certifications performed after the flip; no retroactive invalidation, ever.
- Enforcement is server-side at the certify gate (never a hidden button alone); the ceremony's blocked state names the rule and the eligible certifiers.
- **The record carries both roles distinctly** whether the toggle is on or off: CertificationRecord stores preparer(s)-of-record and certifier identities (R4 take: preparer, certifier, sender distinctly recorded). The record shape supports future stacked approval rules (Modern Treasury's sequential chains — e.g. asset manager then controller) without schema change; v1 ships exactly one certifier.

## 5. Invisible, not disabled (the GitHub rule)

Per R6 (GitHub org settings: "Only organization owners see or change org settings — visibility, not just editability, is role-gated"):

- **Admin sections do not render for non-admins**: Team & Roles, Intake management, Clients directory, Notifications/escalation policy, Billing simply do not exist in a preparer's or reviewer's Settings tree — no disabled rows, no lock icons, no upsell ghosts. The Settings navigation is built server-side from the actor's visible set.
- **The same rule governs every act affordance:**

| Chrome | Non-holder rendering |
|---|---|
| Palette verbs | Verbs the actor cannot perform are absent from results (R6 synthesis 14 — dangerous verbs role-gated); places/objects still resolve within scope |
| Register row actions | Mutation affordances absent for reviewers; rows remain openable read-only |
| Review Room | Confirm/correct/disposition controls absent for reviewers; evidence + verdicts fully renderable |
| Certificate sheet | For a preparer without CERTIFY: readiness state renders, the Certify control is absent, and the sheet states who holds the grant — "awaiting certification by {name}" — because a missing button with no explanation is its own lie |
| Send panel | Same pattern for SEND ("awaiting send by {name}"), with the deadline context still visible to everyone in scope |
| Rail / Settings tree | Sections absent, never grayed |

- **Direct addresses still answer honestly.** Invisible chrome never becomes a silent dead end: a deep link into an unauthorized surface renders the permission-denied state naming the restriction (provenance contract §5 — "never a silent dead click"), and APIs return the send vertical's honest 403/404 grammar (§2.2). Hidden ≠ secret-and-confusing: the 403 names *what* is restricted and *who* administers it, without leaking the restricted content.

## 6. Read-only projections — what a Reviewer sees

The Reviewer role is a projection of the record, not a muted copy of the app:

| Surface | Default reviewer projection |
|---|---|
| Reports | Full register of **sealed** rows in scope; CSV export if `export` is on |
| Sealed period record | Review render + certificate render + SendRecord receipts, sealed banner; full lit-row provenance (traces open only artifacts the role may see — provenance §5) |
| Loan Detail | Terms (with provenance), confirmed schedule (read-only), verdict history and trends over **sealed** periods |
| Documents | Documents that feed sealed packages; originals + labeled derivatives; no unfiled arrivals |
| Home / Inbox / Intake / Composer | Not rendered (nothing actionable exists for a reviewer; Home reduces to a sealed-record entry view) |
| Open periods, drafts, findings, correspondence drafts, memory | **Absent by default** |

- **Per-engagement configuration** (EngagementConfig, set in Settings → Team & Roles): `open-period visibility` (in-progress periods read-only, drafts labeled as drafts), `correspondence visibility` (sealed threads), `export` (register CSV; never resident-level data — rent rolls render anonymized outward, deposit audits and delinquency lists are withheld, per the names law). Each toggle widens the projection; nothing ever grants mutation.
- Reviewer scope binds at Client (PMC mode) or org (owner mode) and may be narrowed to named loans (a lender-relations counsel engaged on one workout sees one loan's record).
- Reviewers may comment where the engagement permits; comments are annotations on the record, never mutations of it.
- Reviewer reads do not log (reading is not an act), but exports do (an ActivityEvent with actor + what left the system).

## 7. Intake-sender security

The designated intake address is an org-scoped front door (minted per Organization; Covenant's own address, never shared with any other product — 02 §2), and sender identity is the first permission check a document ever meets:

- **Sender recognition per org:** a recognition list (known sender addresses ↔ borrower team, PMS vendors, lender/servicer contacts) maintained in Settings → Intake, learned through use: the first arrival from an unknown address is never auto-filed; when a human routes it, the routing decision offers "recognize this sender" as a typed act, and the recognition is remembered (ask-once law — the same sender is never re-quarantined). Recognition entries carry provenance like any memory ("recognized {date} by {who} while routing {doc}").
- **Unknown-sender quarantine:** mail from an unrecognized address lands in a quarantine lane of the Intake queue — held bytes, no extraction, no checklist advancement, no content processed into the record until a human with intake rights (Owner/preparer in scope) inspects and either recognizes, files-once without recognizing, or dismisses. Quarantine is a labeled state inside the queue's "needs your decision" group; the arrival's recognition evidence (sender ↔ known contacts, content ↔ loan identity — Intake brief) renders exactly like any other arrival so the decision is informed, not blind. Quarantined items still exert deadline pressure: a quarantined T-12 near a due date is a your-move item (03 §2's awaiting-documents escalation applies).
- **Recognition is per-address, not per-domain, by default** — a spoof-resistant posture: a new sender at a known lender's domain still quarantines once. Auto-file thresholds remain org-configurable on top of recognition (Intake brief: high-confidence arrivals auto-file quietly *from recognized senders only*).
- **PMC sub-addressing:** per-Client sub-addresses (Settings brief) scope arrivals to a Client before recognition runs; a document arriving on Client A's sub-address can never auto-file into Client B, whatever its content claims.
- **Rotation:** the address is rotatable from Settings → Intake (admin-only, §5) when compromised; the old address enters a quarantine-everything grace window rather than bouncing (documents in flight are evidence, not spam); the rotation is quiet-logged with actor.

## 8. Auth reality

- **Clerk is the identity layer that exists today** — `/sign-in` is a real route and unauthenticated guests are bounced to it (snapshot §3, §6). This contract builds on that session: `resolveActorScope` consumes the Clerk session server-side; no permission decision ever rests on client state alone.
- **The outward-door BLOCK is fixed by the onboarding brief** (`surface-briefs/onboarding-signin-import.md`): the P0 sign-in repair (first-contact links go only to Covenant surfaces — the boundary violation dies there), the org-creation/invite arc, signer designation (where §3's grants are first assigned), and the proposed guarded demo mode (decision D-6). This file does not restate that work — it defines what a session *means* once the door opens.
- Invites are role-scoped and expiring (R6, Ramp's pattern: role-scoped emails with an expiry window), issued from Settings → Team & Roles; accepting an invite lands the member with exactly the role + grants + Client scope the invite named — no post-hoc widening without an admin act.
- Session expiry mid-act must not destroy work: re-authentication returns to the interrupted surface with state intact (the long-session contract, `cross-cutting/accessibility-long-session.md` §6, owns the mechanics; this file owns the rule that the restored session re-resolves scope before the interrupted act may complete).

## 9. Audit expectations — actor + capacity on every typed act

Per R4 (Modern Treasury: reviewers holding multiple Roles "must pick which Role they review as" — the capacity in which you act is recorded):

- Every typed act writes its record (ActivityEvent, plus the act's own object where one exists — CertificationRecord, SendRecord, confirmation history, disposition, waiver, grant change) with:

```text
{ actor_id, capacity,            // the role/grant under which the act was performed
  org_id, client_id,             // tenancy identity
  loan_id?, period?, package_rev?,
  when, evidence_refs[], outcome }
```

- When an actor holds exactly one applicable capacity, it is stamped silently. When more than one applies (a PMC principal who is both org admin and the engagement's delegated certifier), the ceremony asks — one select, part of the typed act, never skippable (the Modern Treasury mechanic verbatim).
- Capacity is stamped **as of act time** and never retro-edited: revoking a grant later does not alter history (append-only law; corrections are subsequent records, 02 §5).
- The two ceremonies additionally record the session's authentication context (who, when, from which authenticated session) on the certificate — R4's DocuSign Certificate-of-Completion pattern, specified in the Certificate brief.
- The quiet log renders capacity where it disambiguates ("confirmed by {name} as preparer") and stays terse where it doesn't (04 §2.3's identity-stamped grammar).

## 10. Benchmark references (per R4/R6 research; roles limited as stated)

| Product | Limited role here | Mechanic taken | Adapted / rejected | Official source |
|---|---|---|---|---|
| Mercury | Separation-of-duties option (§4) | "The person who initiates a payment can no longer approve it, even if they are listed as an approver" — an explicit toggle justified by audit expectations; dual-admin policy requires the org structure before the ceremony | Adapt: initiator→preparer-of-record, approver→certifier; reject auto-fulfill-your-own-approval as any default posture for grants | support.mercury.com/hc/en-us/articles/45985054591508-Enforcing-separation-of-duties-for-per-payment-approvals |
| GitHub | Invisible-not-disabled admin gating (§5); no-bypass gates (§3) | Org settings are visible only to owners — "visibility, not just editability, is role-gated"; protected branches bind even privileged users | Adapt: one settings surface with role-built navigation instead of GitHub's separate trees; reject bypass lists entirely | docs.github.com/en/organizations/managing-organization-settings · docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches |
| Modern Treasury | Capacity-on-record (§9); record shape for future chains (§4) | Reviewers in multiple Roles pick which Role they review as; approval rules stack into sequential chains; append-only records | Adapt: v1 ships one certifier, chain-shaped records; reject auto-send-after-approval — Covenant's send is itself a human gate | docs.moderntreasury.com/payments/docs/approval-reviews · docs.moderntreasury.com/payments/docs/approval-rules-overview |
| Ramp | Role-scoped expiring invites (§8) | Invites carry role + expiry (default 14 days, admin-adjustable); scoped external-collaborator seats | Adapt: the scoped outside-accountant seat maps to a preparer with narrowed Client scope, never a certifier by default | support.ramp.com/hc/en-us/articles/1500002006322-Getting-started-as-an-Admin |

## 11. States

| State | Behavior |
|---|---|
| Permission-denied (direct address) | Named restriction + who administers it ("Team & Roles is managed by {org admin}") — never a blank 403, never a silent redirect |
| Read-only (Reviewer projection) | Zero mutation affordances render (invisible-not-disabled); sealed banners explain the projection; provenance traces work within scope |
| Blocked by separation of duties | Certify gate names the rule + eligible certifiers (§4); a gate state, not an error |
| Grant absent | Certify/Send control absent; the surface states who holds the grant ("awaiting certification by {name}") |
| Quarantined arrival | Held, labeled, undecided; zero downstream processing; deadline pressure still renders |
| Owner mode | Zero Client chrome anywhere (the implicit Client never leaks into UI) |
| Cross-scope object address | 404 per the send-vertical grammar (existence unconfirmed across tenant boundaries) |
| Revoked mid-session | Next server check fails closed; the actor sees the permission-denied state, not a stale success |
| SoD enabled in a solo org | Honest warning at enablement; certify gate blocks with the named rule until a second granted member exists |

## 12. Build target (basis-v2)

- `src/lib/covenant/tenancy.ts` — NEW: `resolveActorScope`, `withScope`, grant + SoD evaluation, EngagementConfig reads (pure, test-covered).
- Migrations: `client_id` FKs down the hierarchy + `members/roles/grants` + `engagement_config` tables (the gap-3 migration workstream; the two unconfirmed live migrations must be verified first — snapshot §7.5).
- `POST /api/covenant/send` — CHANGE: read the SEND grant from scope resolution; delete the server-fixed admin check (gap 4).
- Certify route handler (`/covenant/[loanId]/[period]/certificate`) — grant + SoD + capacity checks server-side.
- `src/app/(covenant)/settings/team`, `/settings/organization`, `/settings/intake` — per the Settings brief; admin sections render only for admins (server-side render guard, not CSS hiding).
- Rail/palette: navigation and verb lists build from the actor's visible set (`CommandPalette` change; shared with `cross-cutting/search-command-keyboard.md`).
- Quarantine lane: intake pipeline work (gap 1) consumes the recognition list from the same store.

## 13. Acceptance tests

Fixtures: **TWO-ORG** (Org A: the Bexley canon book · Org B: Westbrook Flats); **PMC** (one PMC org — Client 1: the Calloway Park evidence spine · Client 2: a second fixture client); members covering every role × grant combination; the Calloway Park FYE-2018 period for act-level tests.

1. **Zero cross-org leakage:** every register, API route, palette query, Ask grounding read, trend series, and export in Org A returns zero Org B rows (TWO-ORG fixture; property-based test over the `withScope` layer — any unscoped query path fails CI at lint time).
2. **Owner-mode invisibility:** the owner-mode fixture renders zero Client UI — no column, no filter, no Settings section, no crumb, no switcher (DOM assertion per register).
3. **PMC isolation:** Client 1 preparer sees zero Client 2 objects; Client 1's owner seat sees only Client 1; a document arriving on Client 1's sub-address is never proposed for filing into Client 2.
4. **Gap-4 unfix:** grant SEND to a non-admin preparer → a real send succeeds through `POST /api/covenant/send`; revoke → 403 with reason; an org admin *without* the grant → the same 403 (no-bypass invariant).
5. **SoD:** toggle ON; every member of the computed preparer-of-record set is blocked at certify with the named rule + eligible certifiers; a granted non-preparer certifies → CertificationRecord carries distinct preparer(s) and certifier identities; toggle flipped mid-period affects only subsequent gate checks.
6. **Reviewer projection:** the default reviewer sees sealed records + verdicts only (draft queries return empty by policy, not by chance); `open-period visibility` ON renders read-only with drafts labeled and zero mutation affordances; all mutation APIs refuse server-side regardless of UI.
7. **Quarantine:** unknown-sender mail → quarantine lane, zero extraction/checklist movement; recognize-sender act → processed + sender remembered; the same sender's next arrival routes normally (ask-once assertion); a new address at the same domain still quarantines.
8. **Capacity stamping:** a dual-capacity member certifying is asked to pick; the record and ActivityEvent carry the chosen capacity; single-capacity members are never asked (no ceremony bloat); revoking the grant afterward changes no historical record.
9. **Invisible-not-disabled:** preparer and reviewer Settings trees contain no admin sections (absence, not disabled state); direct `/settings/team` renders the named permission-denied state; the palette omits admin and certify/send verbs for actors without them while still resolving in-scope places and objects.
10. **Grant-absent honesty:** a preparer without CERTIFY sees readiness plus "awaiting certification by {name}" on the certificate surface — asserted as rendered text, not a hidden button.
11. **Lender-not-a-user:** no lender role is constructible through any API; SendRecord recipients are contact data; the invite flow cannot target a recipient contact.
12. **Agent/engine exclusion:** no non-human session can reach the certify or send gates (API-level test — the gates require an authenticated human session; there is no service-account path).
13. **Escalation targeting:** an "awaiting certification" deadline escalation reaches exactly the CERTIFY-grant holders for that Client — never admins-in-general (gap 9 machinery test).
