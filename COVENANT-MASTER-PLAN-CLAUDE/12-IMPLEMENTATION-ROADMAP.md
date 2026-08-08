# 12 — IMPLEMENTATION ROADMAP
## Foundations → vertical slices → surface waves, every item stated against the basis-v2 repository

Written to survive the Claude Code feasibility gate: every item names its landing place among the repository's real anchors — the `(covenant)` route group in `src/app/`, `src/components/covenant/` (214 files incl. `CovenantShell.tsx`, `book.ts`), `src/lib/covenant/` (the tested engine), `/review-room`, `/covenant/[loanId]/[period]/certificate` (existing resolver + tests), `POST /api/covenant/send` (REAL), `src/styles/covenant-tokens.css`, and the five authored migrations (only `covenant_test_state` has a wired writer; application of two migrations unconfirmed — F2 begins by confirming). The wiring philosophy is the proven send-vertical pattern: **a route carrying loan + period, engine computing, persistence recording, failure honest — one seam at a time, then widening.**

## Phase F — Foundations (the debts everything else stands on)

| ID | Item | Lands in | Evidence of done |
|---|---|---|---|
| F1 | **Intake substrate**: per-org inbound email provisioning (receive → store raw message + attachments), sender recognition table, the existing pure `intake-classify.ts` wired behind a queue; two-lane routing (auto-file vs human queue) with thresholds; intake security model (unknown-sender quarantine) | `src/lib/covenant/intake/` + new `src/app/api/covenant/intake/` webhook route + migration | An emailed Calloway-Park-class T-12 lands as a stored arrival with classification + candidate match; duplicate email suppressed by hash; quarantine works (gap 1) |
| F2 | **Persistence + tenancy**: confirm/apply the five authored migrations; add org/client/sponsor/portfolio/property/pledge/loan/period tables per deliverable 02 (gap 3); migrate `book.ts` seven-loan fixture into seed data preserving demo canon; unfix the send role (roles column per roles matrix) | `supabase` migrations + `src/lib/covenant/data/` | Migrations applied and verified against the live DB; every existing surface still renders (reading seeds through a fixture-shaped adapter); send works for a non-admin holder of the send right (gap 4 part) |
| F3 | **Rent-roll canonical reader**: parse PMS detail exports; unit dedup (322 rows → 301 units); status taxonomy normalization; roll-summary tie-out; unblocks the deliberately-ghosted occupancy resolver | `src/lib/covenant/rentroll/` (new module beside the F-series spine) | The SLOT-2 evidence file parses to 301 units/six statuses; computed aggregates tie to the roll's own summary sheet; occupancy resolver un-ghosted with tests (gap 7) |
| F4 | **Package artifact rendering**: PDF/XLSX generation for package sections + the certified sheet render; content-hash over the artifact set | `src/lib/covenant/render/` + certificate route | The Calloway-park-shaped fixture package renders to PDF/XLSX; sha256 stable across re-renders of unchanged content (gap 4 part) |
| F5 | **Due-rule engine + notifications**: RequirementRecord cadences → materialized periods + deadlines; escalation ladder state machine; ActivityEvent store (the one quiet log) | `src/lib/covenant/deadlines/` + migration | Q+45d/FYE+120d rules materialize correct deadlines for fixture loans; escalation transitions fire on clock fixtures; every autonomous act writes one ActivityEvent (gaps 5/9 substrate) |
| F6 | **Provenance system**: `ProvenanceRef` types + `traceToEvidence` resolver + `Figure` component; fix the review-room wrong-row defect (U1-F1) via the one-resolver contract | `src/lib/covenant/provenance.ts` + `src/components/covenant/provenance/` | The shuffled-fixture invariant passes on all three current renderers (gap 12) |

F-items are parallelizable except F2 before F5 (stores) and F1 before nothing (independent). F6 is independent and should land first — it fixes the demo's money-shot defect.

## Phase V — Vertical slices (each proves a whole seam end to end, one loan one period)

| ID | Slice | Route (basis-v2) | Depends | Proof |
|---|---|---|---|---|
| V1 | **First computed period**: engine metrics + tests + tie-out computed live for one seeded loan+period, rendered on Review with working lit-row traces to seeded source docs | `/covenant/[loanId]/[period]/review` (deep route added to `(covenant)`, mounting the existing shell + ReviewSpread rebuilt on engine rows) | F2, F6 | Zero fixture strings on the route; every figure traces; the U3-class contradiction impossible (one store) — begins retiring gap 6 |
| V2 | **Document in**: one emailed arrival → recognition → holding checklist advance → filed in Documents with four artifacts → extraction proposal → human confirmation → normalized actuals feeding V1's computation | `/intake` + `/documents/[docId]` + `/loans/[loanId]/setup` | F1, F2, F3 | The full chain on real-shaped fixtures: scanned questionnaire gets its Recreated-searchable artifact; T-12 normalizes with COA mapping confirm; DocView no longer orphaned (gaps 1/2) |
| V3 | **Certify → send for real**: composer readiness computed; certify ceremony writes CertificationRecord (who/when/hash); void-on-change live; send UI over the existing real gate; period seals | `/covenant/[loanId]/[period]/composer` + `/certificate` + send panel | F2, F4, V1 | The bit-flip test passes (certify-send-audit spec §7); the send vertical now has its human moment (gap 4 closed) |
| V4 | **The orchestration spine joined**: V2 → V1 → V3 wired through the existing period state machine (open → in-review → ready → certified → packaged) as one running system for one loan; the three human gates structural | `src/lib/covenant/orchestration/` (existing safety core, now driven) | V1–V3 | DOCUMENT IN → RECORD runs unattended except at the three gates for the fixture loan (gap 5 — the single biggest distance closed at n=1) |
| V5 | **Proactive + Q&A seam**: findings service evaluates trigger rules over V1 outputs; one finding surfaces on Home with drafted narrative; one lender question answered from the sealed record with citations | findings service + `/loans/[loanId]/correspondence` | V4, F5 | The canon-phrasing finding ("$-denominated headroom") renders with working provenance; the drafted reply cites sealed figures (gap 8 substrate) |

## Phase W — Surface waves (widening from the proven seams)

| Wave | Surfaces (briefs) | Depends | Contents |
|---|---|---|---|
| W1 | Review Room · Composer · Certificate · Send (full briefs) | V3/V4 | Exception-walk review; compare modes; ceremony polish per briefs; certificate paints the six ruled hexes (the ruled whole-sheet decision executed) |
| W2 | Intake/Holding · Extraction & Confirmation · Documents/DocView (full briefs) + onboarding door | V2 | Chase cadences; sender rules; version/replacement flows; **P0 rider: remove the sign-in Basis-funnel link immediately — this one-line fix (C-7) ships with the first W2 PR, or sooner as a hotfix** |
| W3 | Loans/Loan Detail · Home · Inbox · Calendar · Reports (engine-backed floors) | V4, F5 | Registers on `CovenantTable`; your-move query live; trends extended; due-rule agenda; the gold-standard Reports register wired |
| W4 | Agent layer (Ask grounding, memory store, quiet-log UI, capability prompts) · Lender Q&A full | V5 | Grounded-or-refuse Ask; memory inspector; outcome-learning capture |
| W5 | Settings · Onboarding/Import · Rent Roll teaser · multi-loan aggregation polish | F2, F3 | CSV import surface over the existing library function; PMC tenancy UI; RR teaser on the F3 reader (gaps 10/11/13) |

Within each wave, each surface follows its brief's §18 build plan and ships behind a flag; fixture data remains available to demo routes until the wave's exit test (below) retires it for that surface.

## Migration from fixture data (the burndown)

- `book.ts` becomes seed data at F2; surfaces migrate read paths wave by wave; a CI metric counts (a) components importing `book.ts` directly and (b) bare numerals not rendered through `Figure` — both must reach zero by W5 exit.
- Demo canon is preserved as seeds (Bexley facts, the 89.04% shortfall canon) so demos keep working — but rendered through the engine, making the demo honest.
- No surface ships its wave exit while any of its figures render from fixture strings (the "no on-screen number is product truth" era ends surface by surface, verifiably).

## Feature flags and rollout

One flag per wave (`covenant.wave1` …), plus `covenant.intakeEmail` (F1, org-level). The demo path (`/review-room`) stays stable throughout; deep routes land dark and light up per flag.

## Challenger audits and acceptance evidence

- Each wave exit runs its briefs' §17 acceptance tests (deliverable 14 carries the fixture definitions) plus the challenger review (three fresh products per surface, PASS/ADJUST/REBUILD) filed in `docs/ia/<surface>/06-post-build-challenger-audit.md`.
- The whole-product gates: the ceremony-budget test, the hard-fail grep on all user-facing copy, the readback color verification, the keyboard-only full-period E2E, and the four-viewport CI fixtures (11 §4).

## Dependency graph (summary)

```text
F6 ──────────────┐
F2 ──┬── F5 ──┐  │
F1 ──┤        │  ▼
F3 ──┼──────► V2 ─► V4 ─► V5 ─► W4
F4 ──┼─► V3 ──┘     │
     └─► V1 ────────┘     W1 (after V3/V4) · W2 (after V2) · W3 (after V4+F5) · W5 (after F2/F3)
```

## What this roadmap deliberately does not do

- No email transport before the send gate's transport parameter is exercised download-first end to end (transport is additive on the proven gate).
- No lender-side mode, no cross-product features, no OM/underwriting anything.
- No engine formula changes: wiring only; formula correctness is the separate audit's scope.
- No rail restructure until D-1 is ruled (the deep-route work is independent of rail contents).
