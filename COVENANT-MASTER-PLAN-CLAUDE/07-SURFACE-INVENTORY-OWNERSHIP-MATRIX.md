# 07 — MASTER SURFACE INVENTORY AND OWNERSHIP MATRIX

The canonical inventory reconciling the snapshot (§3 surface table, §6 gap map), the current build's routes/components, the engine library, the knowledge corpus, and the real evidence documents. Current-state classification uses the ruled vocabulary (FIXTURE / REAL / PARTIAL / ABSENT); the graded verdicts live in deliverable 15.

## 1. Inventory

### A — Global frame

| # | Surface | Current state | Repository address (current → planned) | Blueprint |
|---|---|---|---|---|
| A1 | Shell chassis (rail, 48px header, view switcher, canvas panel) | FIXTURE (architecture real, data demo) | `src/components/covenant/CovenantShell.tsx` via `/review-room` → unchanged home; deep routes mount it | 08 + `surface-briefs/*` |
| A2 | Breadcrumb altitude gate | FIXTURE | shell header → unchanged | 08 |
| A3 | Rail contents/grouping | FIXTURE; IA rework FLAGGED-OPEN | shell rail → proposal in 08; Terry decision D-1 | 08 |
| A4 | Command palette / search | FIXTURE (navigates only) | `CommandPalette`/`SearchPalette` components → compute-and-return per `cross-cutting/search-command-keyboard.md` | cross-cutting |
| A5 | Ask panel + highlight-to-ask | FIXTURE (one scripted answer) | `AskPanel`, `HighlightToAsk` → grounded per agent brief | `agent-ask-watch-memory.md` |
| A6 | Provenance lit-row trace | PARTIAL (wrong row in 2 of 3 renderers) | review-room Evidence panel → app-wide contract | `cross-cutting/provenance-lit-row-trace.md` |
| A7 | Notifications / your-move surfacing | ABSENT (machinery) | new: notification service + frame affordance | cross-cutting |
| A8 | Keyboard model (G-chords, palette, focus) | FIXTURE (chords exist) | shell → completed per cross-cutting | cross-cutting |

### B — Portfolio floors

| # | Surface | Current state | Repository address | Blueprint |
|---|---|---|---|---|
| B1 | Home / Overview | FIXTURE (U3-F2 feed contradiction) | review-room Home view → `/home` | `home-overview.md` |
| B2 | Inbox | FIXTURE (U3-F7 badge; U3-C1 3-of-7) | Inbox view → `/inbox` | `inbox.md` |
| B3 | Portfolio / Loans / Loan Detail | FIXTURE + the one runtime wire (trend assembly) | Loans views → `/loans`, `/loans/[loanId]` | `portfolio-loans-loan-detail.md` |
| B4 | Calendar / Obligations | FIXTURE (agenda liked; month-grid OPEN D-3) | Calendar view → `/calendar` | `calendar-obligations.md` |
| B5 | Reports (the gold-standard list page) | FIXTURE | Reports view → `/reports` | `reports.md` |
| B6 | Documents + DocView | FIXTURE; DocView orphaned (no importer) | Documents views → `/documents`, `/documents/[docId]` | `documents-docview.md` |
| B7 | Settings | FIXTURE | Settings view → `/settings/*` | `settings.md` |
| B8 | Onboarding / Sign-in / Import | ABSENT (door BLOCKED; C-7 boundary violation) | `/sign-in` exists → onboarding flow + CSV import surface | `onboarding-signin-import.md` |
| B9 | Rent Roll teaser (free v1, RULED inside the shell) | NOT STARTED | new `/rent-roll` | `rent-roll-teaser-placement.md` |

### C — Reporting spine (per loan, per period)

| # | Surface | Current state | Repository address | Blueprint |
|---|---|---|---|---|
| C1 | Intake / Holding | FIXTURE (no email/classifier/routing) | Intake views → `/intake` (+ per-period holding in period header) | `intake-holding.md` |
| C2 | Extraction & Confirmation | ABSENT as surface (substrate: loan-doc extraction lib; hand-authored config stands in) | new `/loans/[loanId]/setup` + period exceptions | `extraction-confirmation.md` |
| C3 | Actuals & Computation | ABSENT as surface (engine F-series built) | new `/covenant/[loanId]/[period]/actuals` | `actuals-computation.md` |
| C4 | Review Room | FIXTURE (3-column reconcile layout RULED KEPT) | ReviewSpread/Workspace → `/covenant/[loanId]/[period]/review` | `review-room.md` |
| C5 | Composer | FIXTURE (readiness decorative) | Composer view → `/covenant/[loanId]/[period]/composer` | `composer.md` |
| C6 | Certificate & certify gate | Route REAL (resolver + tests); sheet paints none of the eight ruled paper tokens; PDF/XLSX ABSENT | `/covenant/[loanId]/[period]/certificate` | `certificate-certify-gate.md` |
| C7 | Send & Record | **REAL** (`POST /api/covenant/send`) with no UI wired as the user's moment | API real → send surface inside Certificate/Composer flow | `send-record.md` |
| C8 | Lender Q&A / Correspondence | ABSENT | new `/loans/[loanId]/correspondence` | `lender-qa-correspondence.md` |

### D — Agent layer

| # | Surface | Current state | Repository address | Blueprint |
|---|---|---|---|---|
| D1 | Ask (grounded Q&A) | FIXTURE (scripted) | AskPanel → grounded service | `agent-ask-watch-memory.md` |
| D2 | Proactive watch | ABSENT (engine emits headroom; nothing frames it) | findings service + Home/Loan Detail surfacing | same |
| D3 | Ask-once memory | ABSENT | memory store + inline render + Settings inspector | same |
| D4 | Outcome learning | ABSENT | edit-event capture → memory | same |
| D5 | Quiet log | PARTIAL (activity feeds exist as fixtures) | ActivityEvent store; per-loan/period render | same + cross-cutting |
| D6 | Capability prompts | FIXTURE | rotating prompts, domain-real, never advertising an incompletable ask | same |

### E — Cross-cutting systems (each with its own file in `cross-cutting/`)

Identity graph (Loan⇄Property⇄Pledge) · period lifecycle/seal · provenance lit-row · document artifacts · certify/send/audit safety · status vocabulary · tables/grids · charts under doctrine · notifications/deadlines/escalation · search/command/keyboard · roles/permissions/tenancy · accessibility/long-session.

## 2. Ownership matrix (object → one owner; everything else summarizes)

The complete matrix is deliverable 02 §3 (no-double-homing map). Per-surface briefs restate their owned set in template §3. Duplication audit (Gate 4) findings on the current build:

| Duplication found in current build | Resolution in this plan |
|---|---|
| Activity feeds render on Home AND Loan Detail AND review room with independent fixture content (U3-F2 contradiction was possible only because feeds are not one store) | One ActivityEvent store; every feed is a filtered view of it; contradiction becomes structurally impossible |
| Document lists appear in Documents, Intake, and Composer attachments with separate fixture arrays | Documents owns the canonical register; Intake/Composer render checklist-filtered *links* |
| Obligation/due-date info in Calendar, Home, and period headers independently | Calendar/Obligations owns deadlines (due-rule engine); Home and period headers render computed summaries of the same rows |
| The your-move notion exists as an Inbox badge, Home section, and rail count with different fixture numbers | One computed your-move query (definition in 03 §4); every appearance renders the same query |
| Covenant strip verdicts on Loan Detail vs Review carry independent fixture strings | TestResult is the single source; both render engine rows |

## 3. Entry/exit spine (how surfaces hand off)

```text
Home ──your-move item──▶ the owning surface (Intake exception / Review / Certificate / Q&A draft)
Inbox ──arrival──▶ Intake decision ──filed──▶ Documents (link) · checklist advances (Holding)
Loans ──loan──▶ Loan Detail ──period──▶ Review ◀──▶ Composer ──ready──▶ Certificate ──certified──▶ Send
Calendar ──deadline──▶ the period it belongs to (period header opens Review)
Reports ──sent package──▶ sealed period record (read-only Review/Certificate view)
Any figure anywhere ──click──▶ its evidence (lit-row) ──deeper──▶ DocView (source artifact)
Ask ──answer citation──▶ the same lit-row chain
```

Return paths are structural: every deep surface carries the loan+period breadcrumb (altitude gate) back to its floor; Esc closes summoned panes before it ever navigates.
