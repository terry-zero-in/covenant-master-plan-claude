# 03 — END-TO-END WORKFLOW, PERIOD LIFECYCLE, AND STATE MACHINES

## 1. The borrower's arc (the product's one sentence, expanded to states)

> Borrower dumps documents (upload or Covenant's designated intake email) → the agent prepares the entire package end to end → the borrower gives a ~60-second review and sign-off → it goes to the lender.

Expanded into the full chain with actors and gates (E=engine, A=agent, H=human, G=gate, Q=quiet log):

```text
 1. ARRIVAL            document lands via intake email or upload                 [A recognizes; Q logs arrival]
 2. RECOGNITION        sender + content matched to known loan(s) and period      [A proposes; H resolves only ambiguity → G if unroutable]
 3. HOLDING            period checklist advances; missing items chased           [A chases on approved cadence; Q logs each chase]
 4. EXTRACTION         terms/covenants/actuals proposed from documents           [A proposes with source regions]
 5. CONFIRMATION       proposals confirmed/corrected                             [H typed act — exception-only after first period]
 6. NORMALIZATION      actuals land on the chart-of-accounts spine               [E; exceptions surface as review items]
 7. COMPUTATION        metrics, covenant tests, watch bands, signed headroom     [E only — deterministic]
 8. TIE-OUT            this package reconciled against the prior package         [E; failed tie-out blocks readiness]
 9. COMPOSITION        sections assembled; lender form transcribed; narratives   [A drafts; E computes readiness]
10. REVIEW             the ~60-second pass: exceptions first, evidence beside    [H; G = the review gate]
11. CERTIFY            typed act; who/when/content-hash; void-on-change          [H only — G, the signature moment]
12. SEND               second typed act against the send gate                    [H only — G; E enforces approved bytes]
13. RECORD             append-only send record; period seals (seal-not-wipe)     [E; Q logs the seal]
14. FOLLOW-UP          lender questions → drafted replies → approval → thread    [A drafts; H approves; Q logs]
```

The product's center of gravity: **steps 1–9 happen without the borrower.** The borrower's time concentrates in 10–12. Steps that complete autonomously write to the quiet log, never push.

## 2. Period lifecycle (the ruled state machine, with the send extension)

The orchestration safety core defines: `open → in-review → ready → certified → packaged`, three human gates, agent immutability (snapshot; `[UNVERIFIED-BY-SESSION]` on exact enum spelling — the send vertical additionally flips a period to `sent`, proven REAL).

```text
        ┌─────────┐   checklist complete    ┌───────────┐   all exceptions     ┌────────┐
  ──────▶  OPEN   │──── + extraction ──────▶│ IN-REVIEW │─── dispositioned ───▶│ READY  │
        │(holding)│      done [E/A]         │           │    tie-out clean [E] │        │
        └─────────┘                         └───────────┘                      └───┬────┘
             ▲                                    ▲                                │ H: CERTIFY (gate 2)
             │ new cadence occurrence             │ any change voids cert          ▼
             │ [E schedules]                      │ (void-on-change) [E]      ┌──────────┐
        ┌────┴────┐                               └───────────────────────────│CERTIFIED │
        │ (next   │                                                           └───┬──────┘
        │ period) │                                                               │ H: SEND (gate 3)
        └─────────┘                                                               ▼
                                                                              ┌──────────┐    seal-not-wipe
                                                                              │PACKAGED/ │──▶ read-only record,
                                                                              │  SENT    │    full history kept
                                                                              └──────────┘
```

- **Gate 1 (into in-review):** the human confirms extracted requirements/actuals the first time they appear; period-over-period, confirmation is exception-only (ask-once).
- **Gate 2 (certify):** typed act, identity-bound, content-hash recorded. Disabled-with-reasons until engine readiness is true; each reason links to its blocking item.
- **Gate 3 (send):** separate typed act; the send gate re-checks certification validity and org/role scope; append-only record.
- **Void-on-change:** any mutation that touches certified content (a corrected value, a replaced document that feeds a package field, a recomputed metric) automatically voids the certification, states why, and returns the period to in-review/ready. Nothing re-certifies silently.
- **Seal-not-wipe:** a packaged/sent period is immutable but fully inspectable forever: documents, values, verdicts, narratives, the certification, and every send record remain addressable.

### Exception states that ride alongside the main line

| State | Meaning | Who resolves | Where it surfaces |
|---|---|---|---|
| awaiting-documents | Checklist has missing expected items | Agent chases; human can waive | Holding checklist; Home your-move only if a deadline approaches and chasing failed |
| unrecognized-arrival | Intake could not match sender/content to a loan | Human (routing decision) | Intake queue (a real gate) |
| extraction-exception | Low confidence, conflict, or unreadable region | Human (confirm/correct/mark-unreadable) | Extraction & Confirmation |
| normalization-exception | Unmapped COA line, control-total mismatch | Human confirms mapping (remembered thereafter) | Actuals & Computation |
| failed tie-out | Prior-package reconciliation broke | Human dispositions with reason | Review Room, blocking readiness |
| watch / shortfall / breach | Test verdicts (per requirement basis) | Human dispositions shortfall/breach findings; watch is informational | Review Room + Loan Detail + Home summary |
| stale | A source document was replaced after downstream values were confirmed | Engine flags; human re-confirms affected values | Everywhere the affected figures render (stale badge with diff link) |
| blocked | Fail-closed engine stop (unknown form, missing definition) | Human supplies the missing definition via authoring | The blocked step renders the gate; never papered over |

## 3. Ask-once-remember-forever — the memory model

**What the agent may ask, at most once, per scope:**

| Scope | Example questions (from the evidence and canon) | Where the answer lives | How it is reused |
|---|---|---|---|
| Borrower (org) | "Who signs certifications?" · "Preferred send-day margin before deadlines?" | MemoryEntry scope=borrower | Pre-fills every certification ceremony and chase schedule |
| Loan | "This agreement's rent-schedule format — is the PMS detail export acceptable to this servicer?" · "Confirm the due-rule: Q+45d?" | MemoryEntry scope=loan | Drives the period checklist and Composer section list |
| Lender/servicer | "JLL's questionnaire asks management-fee %; include incentive fee in the same line?" | MemoryEntry scope=lender | Pre-fills the recurring form answer with provenance |
| Property/PMS | "Map PMS account 4135 'RAF' → Other Income?" | COA mapping (a specialized MemoryEntry) | Every future normalization of that property's statements |

Mechanics (law-derived):
- A remembered answer renders **beside the field it fills**, labeled "learned {date} from {person} during {context}", with one-click inspect/correct.
- Corrections version the entry; the agent uses the newest and never re-asks.
- Memory is inspectable in one place (Settings → Agent memory) and at every point of use; the store is one store (no per-surface memory silos).
- The agent's outcome learning (edits to drafts) writes the same way: an edited narrative teaches a style/content preference entry whose provenance is the edit event. "If he misses the same shit every time, we're dead in the water" — the acceptance test for learning is repeat-error rate on the same correction class.

## 4. Portfolio-level workflow (the book across the month/quarter)

The book-level rhythm the portfolio floors serve:

```text
Month start:  E schedules cadence occurrences → new OPEN periods appear with checklists
Weeks 1–2:    documents arrive (email/upload) → recognition → holding advances quietly
              A chases missing items on approved cadence; Calendar shows due-date pressure
Weeks 2–3:    extraction/normalization/computation complete per loan → periods flip IN-REVIEW
              Proactive findings raise early (headroom trends, shortfall risk) with drafted narratives
Deadline−N:   Home concentrates your-move: periods READY awaiting certify; drafts awaiting approval
Deadline:     certify → send per loan; periods seal; Reports shows the sent register
Anytime:      lender follow-ups arrive → drafted replies await approval
```

Your-move (the Home/Inbox semantic, fixing C-8): the count of items **blocked on a human decision** — periods awaiting certify/send, unrecognized arrivals, extraction exceptions, failed tie-outs, findings awaiting disposition, drafts awaiting approval, deadline escalations. Unread is not your-move; completed agent work is quiet-log, not your-move.

## 5. Cadence and deadline engine (gap 9 machinery, modeled)

- Each RequirementRecord carries a due-rule (`Q+45d`, `FYE+120d`, `monthly on request`, rider-specific). Evidence: 6001.NR §8.02(b) — quarterly statements within 45 days; annual within 120; rent schedule at both cadences; on-request items capped.
- The engine materializes cadence occurrences into ReportingPeriods + Deadlines; the Calendar renders them; escalation policy (remind → chase → your-move → overdue) is org-configurable, agent-executed, quiet-logged.
- Deadline consequence is real (a late package is a compliance failure): overdue items are the one class allowed to escalate from quiet log to push notification, per the notification policy in Settings.

## 6. Where each workflow step lives (surface addresses)

| Step | Owning surface (route in basis-v2) |
|---|---|
| Arrival, recognition, holding | Intake/Holding — `/review-room` intake views today; target `(covenant)` route `/intake` (see brief) |
| Extraction & confirmation | Extraction & Confirmation — target `/loans/[loanId]/setup` (first-time) and per-period exceptions in `/review` |
| Normalization & computation | Actuals & Computation — target `/covenant/[loanId]/[period]/actuals` |
| Review | Review Room — `/review-room` (exists) → deep-linkable `/covenant/[loanId]/[period]/review` |
| Composition | Composer — `/covenant/[loanId]/[period]/composer` |
| Certify | Certificate — `/covenant/[loanId]/[period]/certificate` (route exists with resolver + tests) |
| Send + record | Send surface over `POST /api/covenant/send` (REAL) |
| Follow-up | Lender Q&A — `/loans/[loanId]/correspondence` |
| Book rhythm | Home `/home`, Inbox `/inbox`, Calendar `/calendar`, Reports `/reports`, Loans `/loans` |

Route-group note: today every screen mounts through `/review-room`'s view-switching shell. This plan keeps the shell chassis (it survived adversarial benchmarking) and adds **addressable deep routes** under the `(covenant)` group that mount the same shell — the certificate route already proves the pattern (own route, own resolver, same design system). Detail in deliverable 08 and the roadmap.
