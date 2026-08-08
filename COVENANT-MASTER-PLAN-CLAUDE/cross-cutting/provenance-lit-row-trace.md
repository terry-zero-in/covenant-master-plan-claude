# CROSS-CUTTING — PROVENANCE: THE LIT-ROW TRACE, APP-WIDE

Ruling: click any number → its exact source line lights in Evidence and STAYS lit; Anthropic-citation quality; extend to every figure in the app (memory `covenant-provenance-trace-anchor.md`). Current state: PARTIAL — the mechanic renders in the review room but lights the WRONG source row in 2 of 3 renderers (U1-F1, the demo's money-shot interaction). This file is the component contract that fixes it once and extends it everywhere.

## 1. Why this is a system, not a feature

The wrong-row defect happened because each renderer resolves "my source" independently. The fix is one contract: **figures carry provenance refs; one resolver turns a ref into a lit region; renderers never compute their own targets.**

## 2. The contract

### 2.1 ProvenanceRef (attached to every material figure at render time)

```ts
// src/lib/covenant/provenance.ts (NEW; pure)
type ProvenanceRef = {
  value_id: string           // the addressable value (metric, test, normalized line, package field, trend point)
  chain: ProvenanceLink[]    // ordered, figure → source
}
type ProvenanceLink =
  | { kind: 'formula';    fn: string; version: string; inputs: string[] }        // value_ids
  | { kind: 'normalized'; coa?: string; row_id: string }
  | { kind: 'extracted';  doc_id: string; artifact: 'structured'; field_id: string; confidence: number }
  | { kind: 'region';     doc_id: string; artifact: 'original'|'recreated';
      locator: { page?: number; bbox?: [number,number,number,number]; sheet?: string; cellRange?: string; msgPart?: string } }
  | { kind: 'confirmation'; act_id: string; who: string; when: string }
  | { kind: 'memory';     mem_id: string }                                        // remembered-answer fills
```

### 2.2 The one renderer-side component

`<Figure>` (NEW, `src/components/covenant/provenance/Figure.tsx`): wraps any rendered numeral/claim; props `{ ref: ProvenanceRef, format }`. Behavior: renders with the three-actor grammar (source/inferred/certified — deliverable 06 §7); click → dispatches `trace(ref)`; keyboard: focusable, Enter traces, Esc dismisses the trace.

### 2.3 The one resolver

`traceToEvidence(ref)` (NEW, one implementation): walks the chain to the deepest `region` link, opens/focuses the Evidence context for that document, scrolls to and **lights the exact row/region** (persistent highlight until dismissed or another trace starts), and renders the chain as a breadcrumb stack in the Evidence header (each link clickable to walk up/down the chain). Where the surface has no evidence pane, the trace summons the Evidence overlay pane (frame plan 08 §4) — beside, never a modal, and positioned to not cover the clicked figure.

### 2.4 Correctness invariant (the U1-F1 killer)

The lit target is derived **only** from `ref.chain` — never from row index, render order, or heuristic text match. Acceptance: a property-shuffled fixture (rows reordered vs extraction order) still lights the exact source line for all renderers.

## 3. Renderer adoption order (each item = one PR-sized unit, tests included)

1. Review room Evidence panel — replace the three renderers' ad-hoc resolution with `traceToEvidence`; U1-F1 regression fixture (the current wrong-row cases become tests).
2. Certificate sheet — every ruled-value figure wraps in `<Figure>`; trace summons Evidence overlay.
3. Covenant strips (Loan Detail + Review) — verdict chips and headroom values trace to TestResult inputs.
4. Actuals surface — normalized grid cells trace to source cells (sheet/cellRange locator).
5. Trend points (the existing Loans runtime wire) — each mark traces to its sealed-period value.
6. Agent-quoted numbers (Ask answers, drafted narratives) — interpolated engine values carry refs; an agent sentence without a ref for a numeral is a lint error (CI-enforced: the narrative interpolation API only accepts `value_id`s).
7. Palette computed answers — engine-answer rows carry refs (frame plan 08 §5).
8. Rent Roll teaser aggregates — occupancy figures trace to contributing rows.

## 4. Evidence panel behavior (the receiving side)

- Persistent highlight: the lit row keeps a `--accT`-tinted band + left accent bar (#7189FF family tokens; no new colors) until dismissed; subsequent traces move the light; multiple simultaneous lights only in compare mode (each side lights its own).
- The chain breadcrumb renders top-down (figure → formula → inputs → normalized → region); clicking an intermediate link re-lights at that level (e.g., show all input rows for a formula).
- Confidence and confirmation state render beside extracted links (three-actor grammar).
- Original vs Recreated: the trace lights the Recreated-searchable artifact by default (it carries coordinates); a "view original" toggle swaps artifacts preserving the locator (per document-artifact law, the derivative is labeled).
- Latency: chain resolution is synchronous from the store; document render may lazy-load — the row-level skeleton keeps the target region marked while pages load.

## 5. States

| State | Behavior |
|---|---|
| Ref missing (legacy fixture value) | `<Figure>` renders with a "no source" affordance — visibly weaker (grayed provenance underline), never pretending; CI counts these toward the fixture-migration burndown |
| Source document not yet imported (DocView orphan era) | Trace opens the chain to the deepest available link and states "source document not in the record" with the arrival path (Intake) |
| Region OCR-uncertain | Light the region + confidence note; suspect-word styling per DocView |
| Sealed period | Trace works forever (seal-not-wipe); chain shows the seal badge |
| Permission-limited viewer | Trace opens only artifacts the role may see; otherwise states the restriction (never a silent dead click) |

## 6. Acceptance tests

1. Shuffled-fixture invariant (§2.4) across all three current renderers — the U1-F1 cases pass.
2. Calloway Park FYE-2018 chain: certified NOI figure → formula → normalized lines → T-12 sheet cells → original xlsx bytes; every hop lands exactly (fixture from deliverable 06 chain A).
3. Occupancy chain: teaser aggregate → contributing rows → the 322-rows/301-units dedup step is visible in the chain (deliverable 06 chain C).
4. Keyboard-only: focus a figure, Enter, Evidence receives focus on the lit row, Esc returns focus to the figure.
5. Narrative interpolation: an agent draft cannot render a numeral without a `value_id` (unit test on the interpolation API).
6. Performance: trace-to-light under 200ms on a 150-row statement with the document already open; skeleton-marked target when lazy-loading.
