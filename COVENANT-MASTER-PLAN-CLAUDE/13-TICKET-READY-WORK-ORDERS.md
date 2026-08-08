# 13 — TICKET-READY WORK ORDERS

Work orders sized for single PRs (or small stacks) against basis-v2, sequenced per the roadmap (12), with acceptance evidence bound to the fixtures/tests of deliverable 14 and the briefs' §17. Format: ID · title · lands in · does · acceptance. Orders marked ⚡ are independent of everything and can start immediately.

## P0 — immediate (law enforcement and defect repair)

**WO-001 ⚡ — Remove the cross-product link from sign-in.**
Lands: the sign-in screen component (`src/components/covenant/` login screen; route `/sign-in`).
Does: delete/replace the Basis-funnel link (the only live link on the page today); links go only to Covenant surfaces (support/legal stubs acceptable).
Acceptance: B6 slice — zero cross-product links on sign-in; snapshot finding U12-F2 closed. (One-line fix; ships as a hotfix ahead of everything.)

**WO-002 ⚡ — Provenance system core + wrong-row repair.**
Lands: `src/lib/covenant/provenance.ts` (new), `src/components/covenant/provenance/Figure.tsx` (new), the review room's three renderers (replace ad-hoc source resolution with `traceToEvidence`).
Does: the ProvenanceRef/chain types, the single resolver, the Figure atom; rewires the existing Evidence panel lit-row to the resolver.
Acceptance: C6 on FX-SYN-SHUFFLE (all three renderers); the two currently-wrong-row cases become regression tests (U1-F1 closed).

**WO-003 ⚡ — Inbox badge semantics.**
Lands: the Inbox badge + rail `CountBadge` call sites.
Does: badge renders the your-move query stub (hardcoded to the fixture-derived actionable set until F5; never unread).
Acceptance: C8 property test at fixture level; U3-F7 closed.

## F-series (foundations)

**WO-010 — Confirm and apply migrations; tenancy schema.**
Lands: `supabase` migrations; `src/lib/covenant/data/`.
Does: verify the five authored migrations against the live DB (two unconfirmed — confirm or re-issue); add org/client/sponsor/portfolio/property/pledge/loan/period tables per 02; roles column per the roles matrix.
Acceptance: migration status report; F2 seed-parity test green.

**WO-011 — Seed the demo book from `book.ts`.**
Lands: seed scripts + a fixture-shaped read adapter.
Does: `book.ts` content → seed rows preserving canon verbatim; surfaces read through the adapter unchanged.
Acceptance: every current screen renders identically (visual regression on the six benchmark screens); FX-CANON fixtures load from DB.

**WO-012 — Intake receive path.**
Lands: `src/app/api/covenant/intake/` (webhook), `src/lib/covenant/intake/` store.
Does: per-org address provisioning, raw message + attachment storage with hashes, exact-duplicate suppression, unknown-sender quarantine.
Acceptance: F1 evidence — emailed fixture arrival stored + deduped + quarantine path; intake receipt event in the activity store (B8 slice).

**WO-013 — Wire the classifier + candidate matching.**
Lands: `src/lib/covenant/intake/` (wraps the existing pure `intake-classify.ts`).
Does: classification + loan/period candidate scoring with reasons; the two-lane threshold config.
Acceptance: FX-CAL arrivals classify with evidence-bearing candidates; threshold flips route between lanes in tests.

**WO-014 — Rent-roll canonical reader.**
Lands: `src/lib/covenant/rentroll/`.
Does: PMS detail-export parse; unit dedup; status normalization; summary tie-out; un-ghost the occupancy resolver.
Acceptance: D4 (FX-CAL-RR: 322→301, six statuses, aggregates tie; FX-CANON-BEX canon renders through the same path).

**WO-015 — Package artifact rendering + hash.**
Lands: `src/lib/covenant/render/`.
Does: PDF/XLSX section renders; bundle manifest; sha256 over the artifact set.
Acceptance: F4 evidence (stable hash across re-renders; FX-CAL-PKG-shaped bundle renders).

**WO-016 — Due-rule engine + ActivityEvent store.**
Lands: `src/lib/covenant/deadlines/`, activity migration.
Does: cadence materialization (Q+45d, FYE+120d, rider rules), escalation ladder, the one quiet-log store.
Acceptance: FX-SYN-CLOCK transitions; B8; due-rule provenance strings render ("FYE+120d per §8.02(b)(2)" pattern).

**WO-017 — CovenantTable primitive.**
Lands: `src/components/covenant/table/`.
Does: the grid law (uniform rows, open-not-boxed, column types incl. Figure cells, saved-view state, virtualization, keyboard grammar).
Acceptance: tables cross-cutting §tests; Loans register renders on it from seeds at 301-unit/50-loan scale fixtures.

## V-series (vertical slices; each order = the slice's PR stack)

**WO-020 — V1: first computed period on Review.**
Lands: `(covenant)` deep route `/covenant/[loanId]/[period]/review`; ReviewSpread rebuilt on engine rows.
Does: metrics/tests/tie-out computed live for FX-CANON-BEX's current period; every figure a Figure with working trace.
Acceptance: V1 proof (zero fixture strings on the route; D1–D3; A1 compute→review subset).

**WO-021 — V2: document-in chain.**
Lands: `/intake`, `/documents`, `/documents/[docId]`, `/loans/[loanId]/setup` routes; four-artifact pipeline.
Does: arrival → recognition → holding → filed (four artifacts) → extraction proposals → confirmation → normalization feeding V1.
Acceptance: V2 proof (FX-CAL-SCAN round-trip; DocView importer live; A2's ask-once substrate: mappings/records persist).

**WO-022 — V3: certify → send with the human moment.**
Lands: `/covenant/[loanId]/[period]/composer` + `/certificate` (existing route, ceremony added) + send panel over the real gate; typed-act writer routes in `src/app/api/covenant/`.
Does: computed readiness; the certify ceremony (typed attestation → CertificationRecord); void-on-change watcher; send UI rendering the gate's honest failures; seal.
Acceptance: A3; B2/B4/B5; the ceremony-budget check; the certificate paints the six ruled hexes verified by readback (B7 slice).

**WO-023 — V4: the orchestration spine joined.**
Lands: `src/lib/covenant/orchestration/` (drive the existing safety core).
Does: V2→V1→V3 as one state-machine-driven run for one loan; the three human gates structural.
Acceptance: full A1 on FX-CAL + FX-CANON-BEX.

**WO-024 — V5: findings + grounded reply.**
Lands: findings service; `/loans/[loanId]/correspondence`.
Does: trigger evaluation over engine outputs; one finding with drafted narrative on Home; one grounded cited reply draft.
Acceptance: V5 proof; B3 on the drafts.

## W-series (waves; one umbrella order per brief, decomposed at wave start per its §18)

WO-030…034 → W1 briefs (review-room, composer, certificate, send-record + ceremony polish). WO-040…043 → W2 (intake-holding, extraction-confirmation, documents-docview, onboarding door incl. D-6 outcome). WO-050…054 → W3 (loans/loan-detail, home, inbox, calendar, reports). WO-060…061 → W4 (agent layer, lender-qa). WO-070…073 → W5 (settings, onboarding/import full, rent-roll teaser, aggregation polish). Each umbrella order's acceptance = its brief §17 + the wave's slice of the B/C suites + the challenger audit filed under `docs/ia/<surface>/06-post-build-challenger-audit.md`.

## Consolidation orders (measurement-gated, no-visual-change discipline)

**WO-080 — CountBadge consolidation** (ruled; branch `build/covenant-count-badge-atom` exists): land the shared atom across the 37 sites/21 files. Acceptance: pixel-diff clean on the benchmark screens.
**WO-081 — Grey-ladder re-points** (ruled, adopted): execute the 55 sites measurement-gated. Acceptance: readback matches the ten-rung ladder; the one known seam (dropdown over evidence panel) visually verified.
**WO-082 — Table consolidation**: migrate per-surface fixture tables to CovenantTable configs, one surface per PR. Acceptance: visual regression + B9 progress.
**WO-083 — Figure adoption burndown**: wrap material numerals surface by surface. Acceptance: B9 counter decreasing to 0 by W5.

## Standing gates on every order

Hard-fail grep on user-facing strings (B6) · token lint (B7 slice) · axe on touched surfaces (C7 slice) · no new colors · no agent-reachable typed-act writers (B2 slice) · updated brief §17 test status in the PR body.
