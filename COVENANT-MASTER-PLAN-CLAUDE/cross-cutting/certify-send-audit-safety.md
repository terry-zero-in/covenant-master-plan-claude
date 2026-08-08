# CROSS-CUTTING — CERTIFY / SEND / AUDIT SAFETY SYSTEM
## Typed acts, approved bytes, void-on-change, append-only stores, and the audit views over them

This file is the safety spine behind the Certificate and Send briefs — the whole-product law those surfaces instantiate. The one REAL vertical (`POST /api/covenant/send`) already proves half of it: gate against persisted state, append-only record, honest failures. This spec completes the other half and makes both acts un-bypassable.

## 1. Typed acts (the closed set)

A typed act is a human decision with legal or ground-truth weight, recorded with actor, capacity, timestamp, target identity, and evidence refs. The closed set (from 08-law + 02 model):

| Act | Target | Recorded fields | Gate? |
|---|---|---|---|
| Confirm requirement record | RequirementRecord | who, when, source region, prior value on correction | Gate 1 class |
| Confirm mapping/interpretation | COA mapping, roll status mapping | who, when, scope of reuse (remembered) | — |
| Disposition exception/finding | TieOut, Finding, TestResult | who, when, disposition kind, rationale | Sometimes (blocking finds) |
| Approve outbound draft | Narrative, reply | who, when, diff from agent draft | Always (external-facing) |
| Waive checklist item | ChecklistItem | who, when, reason | — |
| **CERTIFY** | Package revision | **who, capacity, when, content hash** | **Gate 2 — ceremony** |
| **SEND** | Certified package | who, when, transport, recipient, bytes hash | **Gate 3 — ceremony** |
| Accept residual risk / close hard stop | Blocked condition | who, when, named risk | Gate |

Rules: (a) no typed act is executable by the agent, by automation, by API token, or on a schedule — the gates accept only an authenticated human session (the R4 research's one hard rejection: no auto-approve at any deadline, ever); (b) the two ceremonies (certify, send) are the **only** heavy confirmations in the product (NN/g ceremony budget) and are deliberately non-identical (typed attestation vs recipient-and-hash review) so neither trains reflex for the other; (c) admins have no bypass (rejecting the GitHub bypass-list pattern).

## 2. Approved bytes (the content-hash chain)

```text
Composer draft (mutable)
→ readiness true → CERTIFY act → CertificationRecord { who, capacity, when, sha256(package bytes) }
→ package bytes frozen (draft becomes immutable revision)
→ SEND act → send gate verifies sha256(outbound bytes) == certification.hash   ← approved-bytes law
→ SendRecord { ..., bytes_hash } appended; period seals
```

- The hash covers the rendered package artifact set (every document byte-for-byte, plus the package manifest), not a serialized approximation — what the lender receives is provably what was certified (GitHub-release-attestation pattern).
- The certification record is itself a generated, human-readable artifact (DocuSign certificate-of-completion pattern): exportable, storable alongside the package, meaningful outside the app.
- Hash display: short chip (first 8) everywhere; full hash + verify affordance on the record view and Reports register.

## 3. Void-on-change (stale-certification dismissal)

- **Trigger classes:** corrected/confirmed value feeding any package field · replaced source document with downstream use · recomputed metric/test/tie-out affecting a package figure · package composition change (section add/remove/edit) · requirement amendment touching a rendered test.
- **Mechanics:** the engine watches the certified revision's input closure (the provenance graph makes this computable — every package field's chain is known). Any change inside the closure voids the certification automatically and synchronously.
- **Rendering:** a VOID banner on the certificate and Composer ("Certification voided {when}: {what changed, by whom/what}"), the void event permanently in the period timeline (never deleted — GitHub dismissed-review pattern with mandatory reason), the period returned to in-review, and the review outline's stop #1 = the voiding change.
- **Never silent, never partial:** there is no "minor change" carve-out; narrative edits void too (lender packages are stricter than release notes — R4 adaptation). Re-certification is a fresh act over a fresh hash.

## 4. Append-only stores

| Store | Semantics |
|---|---|
| CertificationRecords | Append-only; voids are new events referencing the record, not edits |
| SendRecords | Append-only (already REAL); a mis-send is never edited — it is annotated by a subsequent correction record (Modern Treasury ledger pattern); resend = new record |
| ActivityEvents (quiet log) | Append-only; actor-stamped; the audit substrate |
| Typed-act records | Append-only; corrections version, never overwrite |
| Sealed periods | Immutable-but-inspectable forever (seal-not-wipe) |

## 5. The send gate (server-side, the existing real one, extended)

Checks in order, each with its honest failure: (1) environment configured → 503 with admin-facing fix path; (2) target exists in caller's org → 404 (no cross-org information leak); (3) caller holds the send right → 403 (role from the roles matrix — unfixing today's server-fixed admin, gap 4); (4) certification present, un-voided, hash-matching → 409-class "certification stale" with the voiding change named; (5) transport executes → append SendRecord → flip period → seal. Failures render in the send panel exactly as returned — the fixture-era habit of papering over states is the named anti-pattern.

Email transport (roadmap) rides the same gate — transport is a parameter, the gate is the invariant. No transport path exists that skips the gate; the UI never assembles its own outbound bytes.

## 6. Audit views (where the records are seen)

- **Reports** (`/reports`): the book-level register — certified/sent/hash per row; filters as audit scopes; CSV export of register metadata.
- **Period timeline** (every spine surface's period header → history): the full event sequence for the loan+period: arrivals, confirmations, dispositions, drafts, certifications, voids, sends, seal.
- **Certification record view** (from certificate + Reports): the human-readable certificate artifact + verify affordance.
- **Document audit mode** (Documents/Audit-Usage): which sealed packages reference this document version.
- **Quiet log filters**: actor=human/agent/engine; act type; loan; period — the operational audit.
- Reviewer role sees all audit views read-only; nothing about auditability depends on mutation rights.

## 7. Acceptance tests

1. Bit-flip test: alter one byte of any package artifact post-certification → certification voids; send gate refuses with the stale reason; timeline shows the void with cause.
2. No-bypass test: an admin session without the send right receives 403; no server code path writes a SendRecord without a passing gate (unit-level: the record writer is private to the gate).
3. Approved-bytes test: the Calloway Park FYE-2018 fixture package certifies, then sends; sha256 of the downloaded bundle equals the certification hash (E2E).
4. Append-only test: attempting to update or delete a CertificationRecord/SendRecord row fails at the persistence layer (constraint/policy), not just the app layer.
5. Ceremony-budget test: a full period walkthrough (arrival → sealed) encounters exactly two heavy confirmations; every other step is dialog-free.
6. Agent-immutability test: no agent-reachable code path can invoke either ceremony's mutation (typed-act writers require an interactive human session token).
7. Seal test: after sealing, every read path still works (lit-row traces included); every write path fails with the sealed state named.
