# CROSS-CUTTING — THE FOUR-ARTIFACT DOCUMENT MODEL & THE RECREATED-SEARCHABLE DERIVATIVE
## Original / Recreated / Structured / Extracted: storage, pipeline, labeling law, modes, compare, and audit — as one implementation spec

Ruled law (09-DOCUMENT-INTAKE-PROVENANCE-LAW, restated in 06-DATA-LINEAGE-AND-PROVENANCE §8): every processed document maintains four separately versioned artifacts, the derivatives are always labeled derivatives, and certification attaches to reviewed values — never silently to a derivative. Current state: the Documents surface is FIXTURE, DocView is measured **orphaned** (no importer), permanent document storage is ABSENT, and the dump→recreate-searchable gasp moment "is a tag on a fixture card" (snapshot §3 + gap 2). This file is the contract that makes it real, once, for every surface that renders a document: Documents/DocView, Intake previews, the Review Room Evidence pane, Extraction & Confirmation, and the Composer's attachment views.

## 1. The four artifacts (the data model)

Owner: **Documents** owns the filed canon (artifact sets, versions, metadata); Intake owns pre-routing arrivals and may only summarize + link after filing (no-double-homing, 02-OBJECT-RELATIONSHIP-MODEL §3). Every artifact hangs off the Document object (`docId` + version, 02 §2).

| Artifact | What it is | Mutability | Who makes it |
|---|---|---|---|
| **1 · Original** | The uploaded/emailed bytes + sha256, exactly as received | IMMUTABLE forever (§8) | Intake, at arrival |
| **2 · Recreated · searchable** | Page-true render of the original pixels with the OCR text layer *underneath* — coordinates + confidence per word | Regenerable (new processing version); each version immutable once written | Pipeline (deterministic processing; the agent never edits it) |
| **3 · Structured reading** | Paragraphs / tables / cells / fields with normalized values, confidence, and locators back to 1 or 2 | Regenerable; versioned | Pipeline proposes |
| **4 · Extracted records** | Requirement records, terms, balances, actuals — typed objects on the loan, each carrying its source region | Proposed → confirmed / corrected via typed human acts (04-ENGINE-AGENT-HUMAN-GATE-MAP, Gate 1) | Agent proposes; human confirms |

### 1.1 Type shape

```ts
// src/lib/covenant/documents/artifacts.ts (NEW; pure types + invariant checks)
type ArtifactSet = {
  doc_id: string; doc_version: number          // filing-version axis (§4)
  original: OriginalArtifact                    // exactly one per doc version; immutable
  recreated?: RecreatedArtifact[]               // 0..n processing versions; scans always ≥1
  structured?: StructuredArtifact[]             // 0..n processing versions
  extracted_record_ids: string[]                // ExtractedValue / RequirementRecord refs (02 §2)
}
type OriginalArtifact = {
  sha256: string                                // content address; computed at arrival, before routing
  bytes_ref: string                             // object-store key = the sha256 (content-addressed)
  mime: string; byte_size: number
  received: { channel: 'intake-email' | 'upload'; when: string; arrival_id: string }
  text_layer: 'none' | 'native' | 'partial'     // probed at intake; drives the pipeline fork (§3)
}
type RecreatedArtifact = {
  parent_sha256: string                         // MUST equal original.sha256 (hash chain, §5)
  own_sha256: string
  processing_version: string                    // OCR engine + settings fingerprint
  pages: Array<{
    page: number
    image_ref: string                           // page-true render of the ORIGINAL pixels
    words: Array<{ text: string; bbox: [number, number, number, number];
                   confidence: number; suspect: boolean }>
  }>
  label: 'Recreated · searchable — derivative'  // literal, non-optional, asserted in CI (§6)
}
type StructuredArtifact = {
  parent: { original_sha256: string; recreated_sha256?: string }  // scans ground through Recreated
  processing_version: string
  blocks: Array<ParagraphBlock | TableBlock | FieldBlock>         // each block carries a locator:
  // { page + bbox }        — scan path
  // { sheet + cellRange }  — native xlsx path
  // { msgPart }            — email-body path
  confidences: Record<string, number>
}
```

Artifact 4 is deliberately NOT stored in this module: extracted records are the ExtractedValue / RequirementRecord / NormalizedFinancialLine objects of 02 §2, living on the loan and period where they belong, each carrying `source_region` → a Structured locator. The document model points at them; it never duplicates them (no-double-homing applies to data as well as screens).

### 1.2 Storage shape (the gap-2 fill)

Gap 2 names "no permanent document storage behind the Documents surface." The landing shape:

- **Object store** (`src/lib/covenant/documents/store.ts`, NEW): content-addressed blobs, key = sha256. Originals and Recreated page images live here. Write-once semantics enforced at the API boundary — the store exposes `put(bytes) → sha256` and `get(sha256)`, no update, no delete (tombstoning is metadata, §8).
- **Tables** (extending the persistence contract named in snapshot gap 3):
  - `documents` — doc_id, loan/period/client links, type, filing metadata, current doc_version;
  - `document_versions` — (doc_id, doc_version), original sha256, filed-by, filed-when, replaces-note, tombstone flag;
  - `artifacts` — artifact_id, doc_id + doc_version, kind (recreated | structured), parent hashes, processing_version, own sha256, status;
  - `pipeline_runs` — run_id, doc ref, stages with timings + outcomes, processing versions, failure detail (append-only; feeds the quiet log).
- Route surface (08-GLOBAL-FRAME-SHELL-PLAN §1): `/documents` (register) and `/documents/[docId]` (DocView with the mode set of §9); both mount CovenantShell.

## 2. The ABBYY precedent and the labeling law

The Recreated artifact has an exact industry precedent: ABBYY's searchable-PDF mode **"text under the page image"** — the whole page is saved as the scan image with the recognized text layer beneath it, so the document "looks virtually the same as the original" yet is searchable and copyable; ABBYY's own docs note that text-layer edits are not visible because the image sits on top (R2 research: ABBYY, https://support.abbyy.com/hc/en-us/articles/19098939370515). That is Covenant's Recreated artifact verbatim: **original pixels on top, machine text beneath, never mistaken for original text.** The R2 synthesis makes the same identification: Covenant's "derivative, never the original" label is exactly ABBYY's own framing.

**The labeling law (absolute):**

1. The derivative is labeled **"Recreated · searchable — derivative"** in EVERY render context: DocView header chip, Evidence pane header, both compare panes, thumbnails, search-result rows, palette results, exports, and print footers. The label is part of the artifact type (§1.1) and a CI-asserted render invariant (§14 test 2) — no context may drop it, including embedded and partial renders.
2. The Recreated artifact is NEVER represented as the original. Anywhere bytes leave the system — download, outbound package attachment, lender Q&A citation — only the Original's bytes travel. The Recreated exists for reading, search, and coordinates; it has no outbound life.
3. **Certification attaches to reviewed values, never silently to a derivative.** The CertificationRecord's content hash covers the package built from *confirmed* extracted records (cross-cutting/certify-send-audit-safety.md §2); no OCR output reaches a certified figure except through a typed confirm/correct act (Gate 1). A pipeline rerun can never move a certified number — it can only raise a new proposal, and an accepted change past certification fires void-on-change (06 §6).

## 3. One pipeline, two entry forks (the real-evidence hook)

Both fork conditions exist in the real Calloway Park kit, so the pipeline is specified against evidence, not hypotheticals:

- **Scan path — evidence:** the executed JLL questionnaires and certifications are **flattened scans with zero text layer** (verified at intake probe; evidence: SLOT-4/5). Without artifact 2 they are unsearchable, unquotable, and provenance-dead. This is the Recreated artifact's reason for existing. The executed loan agreement (SLOT-3, Fannie Mae 6001.NR + riders) takes the same path.
- **Native path — evidence:** the T-12 is a PMS xlsx export (SLOT-1) — full native text, **no OCR needed** — but it still gets a Structured reading (the 14-column × ~150-row grid extraction onto COA lines 4010…8010, 06 §2 Chain A) because searchable ≠ structured. Same for the 24-column, 322-row rent roll (SLOT-2), whose Structured reading is what feeds the 322-rows→301-units dedup (06 §4 Chain C).

```text
ARRIVAL (Intake; workflow 03 §1 steps 1–3)
  → HASH      sha256(bytes) → Original written to the content-addressed store      [E; quiet-logged]
  → PROBE     text layer: none | native | partial                                  [E]
  ├─ SCAN PATH (text_layer = none/partial — JLL questionnaires, executed SLOT-3)
  │    → RENDER   page-true images of the original pixels                          [pipeline]
  │    → OCR      words + coordinates + confidence; suspect flags (§7)             [pipeline]
  │    → RECREATED artifact written (parent_sha256 = original)                     [versioned]
  │    → STRUCTURE fields/tables/paragraphs with page+bbox locators                [A proposes]
  └─ NATIVE PATH (text_layer = native — T-12 xlsx, rent-roll xlsx, digital PDFs)
       → no OCR stage; Recreated omitted, OR generated for PDF page-render parity
       → STRUCTURE grid/cells with sheet+cellRange locators                        [A proposes]
  → EXTRACT   typed records proposed, each carrying source_region                  [A proposes]
  → CONFIRM   typed human acts; exception-only after the first period              [H — Gate 1]
  → downstream: normalization → computation → composition (03 §1 steps 6–9)
```

### 3.1 Per-stage contract

| Stage | Input | Output | Actor | On failure |
|---|---|---|---|---|
| HASH | raw bytes | Original + sha256 | Engine | arrival rejected with visible reason (never a half-written Original) |
| PROBE | Original | text_layer verdict | Engine | defaults to scan path (safe: over-processing, never under) |
| RENDER | Original | page images | Pipeline | `processing-failed` state on the register + retry; Original stays readable |
| OCR | page images | words + confidence + suspects | Pipeline | same; partial-page results are discarded, never half-written |
| STRUCTURE | Recreated or native content | blocks with locators | Agent proposes | exception item; document readable throughout |
| EXTRACT | Structured | proposed records with source regions | Agent proposes | exception item in Extraction & Confirmation |
| CONFIRM | proposals | confirmed/corrected records | Human (typed act) | not a failure mode — a gate; unconfirmed blocks readiness (§2.3) |

Stage rules: every stage records `processing_version` and parent hashes; stages are idempotent (same inputs + same version ⇒ byte-identical artifact, which is what makes §14 test 1 assertable); no stage mutates a prior stage's output; failures are visible states with retry actions, never silent drops. Pipeline lives in `src/lib/covenant/documents/pipeline.ts` (NEW). Completed autonomous runs write to the quiet log — no push (agent-presence law).

## 4. Versioning (two axes, never conflated)

1. **Filing axis (`doc_version`)** — human-meaning versions: the borrower files a corrected rent roll; the lender re-issues a form. Mechanics per Box's version stack, hardened (R2 research: Box, https://support.box.com/hc/en-us/articles/360043697054-Accessing-Version-History): one canonical file identity per filed slot; every version listed with per-version Preview / Download / Make-Current; deletes tombstone (visible in the stack, restorable), never erase. Rejected from Box: filename-collision-implies-new-version. Covenant asks the explicit typed question at filing time — **"new version of the filed document, or a replacement/correction?"** — and records the answer, which drives downstream recompute scope: a *new version* opens fresh proposals beside the old confirmations; a *replacement/correction* additionally flags every downstream value whose source region resolved into the replaced bytes (the correction-history "downstream impact" panel, §9.2, names the moved package fields).
2. **Processing axis (`processing_version`)** — machine versions: a better OCR engine or extractor reruns against the same Original. New Recreated/Structured versions append; old versions remain addressable forever, because a provenance chain cited by a sealed period must resolve against the exact processing version it used (seal-not-wipe, 02 §2).

An Original is never versioned in place: a "new version" is a new Original (new sha256) linked into the stack. Nothing on either axis ever overwrites. Make-Current is a promotion pointer move, recorded as an ActivityEvent — the demoted version stays in the stack.

### 4.1 Recompute scope by filing answer

| Filing answer | Prior confirmations | Proposals | Downstream values | Certified/sealed periods |
|---|---|---|---|---|
| **New version** (e.g., next month's roll) | Untouched — they belong to the prior version's records | Fresh proposals open against the new version; remembered mappings (ask-once) pre-fill and are exception-only | New period values compute from the new version; prior periods keep citing the prior version | Untouched |
| **Replacement / correction** (same period, corrected bytes) | Flagged for re-confirmation wherever their source region resolved into the replaced bytes; confirmations grounded elsewhere stand | Re-proposed only for affected regions | Every downstream value fed by an affected region flags with the downstream-impact list (§9.2) before anything moves | A certified-but-unsent period voids on accepted change (visible, reasoned); a SEALED period never mutates — the correction files forward as an addendum document with its own chain (seal-not-wipe) |

The filing answer is a typed act (who/when/answer recorded); the engine derives the affected-region set deterministically from stored locators — never from filename or heuristic text diff.

## 5. The hash chain

```text
Original.sha256  (computed at arrival; the root of every chain)
  ↳ Recreated  { parent_sha256 = Original.sha256, own_sha256, processing_version }
  ↳ Structured { parent = { original_sha256, recreated_sha256? }, processing_version }
      ↳ ExtractedValue.source_region → Structured locator → (page+bbox | sheet+cell)
          ↳ NormalizedFinancialLine / Metric / TestResult input refs (06 §1 chain shape)
              ↳ CertificationRecord.content_hash   (confirmed values only — §2.3)
                  ↳ SendRecord.bytes_hash == certification hash (approved-bytes law)
```

Invariants (engine-enforced in `artifacts.ts`):

- a derivative whose parent hash matches no stored Original is unwritable (the store rejects it);
- every chain link is verifiable offline — hash the bytes, compare the recorded value;
- hashes render in Geist Mono, abbreviated with click-to-copy, per the token law (no new styling);
- the provenance resolver (`traceToEvidence`, cross-cutting/provenance-lit-row-trace.md §2.3) walks this exact chain — the lit-row mechanic and the artifact model share one spine, one resolver, zero renderer-local source resolution;
- a trace lights the **Recreated** artifact by default (it carries coordinates), with a "view original" toggle preserving the locator (lit-row §4; document-modes law §9).

## 6. DerivativeBadge (the one labeling component)

`<DerivativeBadge artifact>` (NEW, `src/components/covenant/docview/DerivativeBadge.tsx`) renders the artifact-class chip:

- `Original` — hash abbreviated in Geist Mono, click to copy the full sha256;
- `Recreated · searchable — derivative`;
- `Structured reading — derivative`.

Anatomy: chip field on a gray-ladder rung, label text at register weight, the accent family used only for the interactive copy affordance (no new colors; token law). Hover card states parentage: "derived from {original hash} by {processing_version} on {date}", with a link to the pipeline run. Every document-rendering surface composes this component; a raw page render without a badge is a lint error (the enforcement mechanism behind labeling-law clause 1). Icons, where any, are Lucide-only.

## 7. Suspect words and low confidence (the honesty mechanics)

The Adobe suspect contract governs (R2 research: Adobe Acrobat, https://helpx.adobe.com/acrobat/desktop/create-documents/scan-documents-to-pdfs/fix-scanned-text.html): when an OCR match is uncertain, Acrobat marks the word a "suspect" — it **keeps displaying the original bitmap** and places the searchable text behind it; suspects are a designed-in state, not an error condition. The guess is never presented as truth.

Covenant's application:

1. In the Recreated artifact, `suspect: true` words render as the original pixels always (structurally guaranteed — the image is on top); the under-layer text for suspects is excluded from agent quotes and citations, and a search hit on suspect text renders with a low-confidence marker, never as a clean match.
2. In the Extracted/Structured inspector, a low-confidence field shows the **source-region pixels zoomed beside the proposed value** (source-region zoom, 09-law compare mechanics) — the reviewer judges pixels, not the guess. Proposed values carry confidence + the dotted provenance underline, never bold-final type (three-actor grammar, 06 §7).
3. Three typed verbs on any proposed value:
   - **accept** — the field turns human-validated (Rossum's blue→green state machine; R2 research: Rossum, https://knowledge-base.rossum.ai/docs/interactive-bounding-boxes-in-rossum);
   - **correct** — type a new value or redraw the region; a correction ALWAYS carries a new source region and re-runs applicable validations (Instabase's re-grounding + auto-revalidation; R2 research: Instabase, https://docs.instabase.com/automate/review);
   - **unreadable** — Covenant's adaptation of Acrobat's per-word "Recognized as → Accept" loop: pin the region, escalate to a human who can source the real value, never force a guess. Unreadable is a first-class exception state, visible until resolved.
4. Rejected outright (per R2 ABBYY-reject): character-by-character verification looping. Covenant review is field/term-level, ordered by rule failures first, then confidence — never OCR proofreading.

## 8. Immutability guarantees and audit

1. Original bytes are content-addressed (key = sha256) in an append-only store: no update path exists at the API boundary; deletion is tombstoning — metadata marked, bytes retained per retention policy, tombstone visible in the version stack (§4).
2. The hash is computed at arrival, before any human or agent touches routing — Intake writes it into the arrival record; the Documents filing act links to it and never re-hashes from mutable state.
3. Derivative artifacts are immutable per processing version (§4 axis 2); regeneration appends, never rewrites.
4. Every pipeline run, filing act, Make-Current promotion, tombstone, and unreadable-escalation is an ActivityEvent (append-only quiet log, 02 §2) with actor = engine/agent/human, artifact refs, and outcome.
5. Correction history on extracted values records who / when / prior value / new source region **and downstream impact** — which normalized lines, metrics, and package fields move. Past certification, an accepted correction fires void-on-change with a visible, reasoned event (06 §6).
6. Sealed periods pin their chains: the exact doc_version + processing_version cited at certify time resolve forever, read-only (seal-not-wipe). Lender follow-ups years later re-open the same lit regions (06 §6, Lender Q&A grounding).
7. DocView's audit inspector combines Box's Details panel (uploader, arrival channel, dates, size, hash; R2 research: Box) with Rossum's per-field validation-source provenance (who confirmed, hover reveals the act; R2 research: Rossum). It is a read surface — no audit row is editable anywhere.

## 9. Default document modes and compare mechanics (the modes law)

Ruled mode set (09-law): **Readable | Source | Extracted | Compare** — mounted on `/documents/[docId]` (08 §1) and reused wherever a document renders in place. Reuse: DocView's porcelain paper sheet is the KEPT base viewer (snapshot §3). NEW components in `src/components/covenant/docview/`: `ModeBar` (the four modes as labeled segments, keyboard `1–4`), `ArtifactToggle`, `CompareBar`, `RegionHighlight` (shared with the lit-row resolver).

### 9.1 The modes

| Mode | Layout | Contract |
|---|---|---|
| **Readable** (default) | ONE dominant viewer | Scans: the Recreated render (original pixels, selectable text beneath) with the DerivativeBadge. Native files: the native render. Routine reading never splits the screen (09-law: one dominant viewer). |
| **Source** | ONE viewer | The Original bytes; hash chip visible; download lives here and only here (labeling-law clause 2). |
| **Extracted** | ~65/35 asymmetric split — document dominant left, inspector right (inspector ≥420px; at 1440 ≈ 940/420; at 1728 ≈ 1120/480; at 2048 the document pane caps at readable page width and the margin absorbs the rest) | Fields/records beside their page. Click a field → its region lights (`RegionHighlight`); click a region → jumps to its field (bidirectional linking per Rossum). Accept / correct / unreadable inline (§7). Suspect-first ordering after rule failures. |
| **Compare** | TWO synchronized peers, 50/50 | Version ⇄ Version and Package ⇄ Prior-package only — genuine peer comparison earns the split (pane law: at most two work windows; co-visibility justified by difference-reading, which requires both sides at equal weight). |

**Original ⇄ Recreated is NOT a Compare case.** Visible pixels are materially identical by construction, so a permanent side-by-side would show two identical pages — dead co-visibility, forbidden by the pane law's justification requirement. It is a **toggle/overlay** on the Readable viewer (`ArtifactToggle`): swap artifacts in place preserving page, zoom, scroll, and any lit region; an overlay option dims the page and outlines the OCR word boxes with a confidence tint (accent-family tokens only) for QA reading. (06 §8; 09-law.)

### 9.2 Compare mechanics (both peers)

- **Synchronized page, scroll, and zoom** — one scrollbar drives both panes; an explicit unlink control breaks sync (re-link restores alignment); zoom is mirrored.
- **Linked text/region selection** — select in one pane → the counterpart region lights in the other, via the same locator resolution the lit-row trace uses.
- **Difference markers** in a shared center gutter, with next/previous difference navigation (`n` / `p` keys plus visible chevrons); the count renders in the CompareBar.
- **Low-confidence markers** in the same gutter, visually distinct from difference markers (gray-ladder rung vs accent treatment — no new colors).
- **Source-region zoom** on any marker: the region's pixels enlarge in place for judgment before acting.
- **Accept / correct / unreadable** directly on a difference — resolving a difference IS one of §7's typed acts, recorded identically.
- **Correction history with downstream impact**: each act lists what moved — e.g., "moves: Composer income-statement line 4105; occupancy aggregate; TestResult inputs" — so a reviewer sees package consequences before confirming (09-law: "which package fields move").
- Below minimum content width (<1100px), Compare collapses to labeled tabs with the difference gutter retained — no silent compression (pane law).

### 9.3 Search over the word layer

- Register search and the palette's document scope hit the Recreated word layer (scans) and native text (born-digital) through one index; results carry the document's loan/period identity chips (08 §5) and — for recreated sources — the derivative label on the result row (labeling-law clause 1).
- A hit lands in Readable mode with the matched words lit via `RegionHighlight` (same persistent-band treatment as a lit-row trace; accent-family tokens).
- Hits on `suspect` words rank below clean hits and render with the low-confidence marker — a suspect match is a lead, never presented as an exact match (§7.1).
- Search never indexes tombstoned versions by default; a "search all versions" scope exists and labels every non-current hit with its version.

### 9.4 Keyboard

`1–4` switch modes (ModeBar) · `o` toggles Original ⇄ Recreated in Readable (holds page/zoom/lit region) · `n` / `p` next/previous difference in Compare (next/previous suspect in Extracted) · `Enter` advances to the next unconfirmed field in Extracted (Rossum's flow-not-form keyboarding; R2 research: Rossum, https://knowledge-base.rossum.ai/docs/keyboard-shortcuts) · `Esc` dismisses the active highlight, then exits Compare. All actions focusable and reachable without a pointer; the region-redraw correction has a keyboard alternative (type-a-value with explicit "no region change" recorded — flagged for later re-grounding).

## 10. Agent presence in the document surfaces (contextual, not chrome)

Per the agent-presence law, no chat box demonstrates AI here. The agent appears only as:

- **confidence beside an arrival** (Intake preview: "matched to {loan}, {period} — confidence" with the routing evidence);
- **proposed extraction beside its source** (Extracted mode's inspector rows, dotted-underline grammar, region lit on focus);
- **a remembered answer beside the field it fills** — "learned {date} from {who}" on pre-filled mappings; the agent never re-asks a remembered COA or roll-status mapping (ask-once law), and a stale-format flag (§13 states) is the only thing that re-opens one;
- **the quiet log** for completed pipeline runs — no push, no toast for autonomous work; deadline escalation is the only push class, and it belongs to Calendar/Obligations, not Documents.

The agent NEVER: edits an artifact, confirms a value, files a version, answers the filing question, or resolves unreadable — those are human typed acts, every one recorded.

## 11. The gasp-moment honesty rule

"Dump a scan in, get a searchable recreation back" is a genuine gasp **when real**. Today it is a tag on a fixture card (snapshot gap 2: "The dump→recreate-searchable gasp moment is a tag on a fixture card"). The rule: **the tag may not render unless clicking it opens a real Recreated artifact — actual OCR text at actual coordinates under the actual scan's pixels.** No demo, screenshot, or walkthrough presents the label over a fixture. The honest demo IS §3's scan path: drop the executed JLL questionnaire (a flattened scan, zero text layer — verified), watch it come back searchable, select text the original never had, and see the badge admit it is a derivative. The gasp survives honesty; it does not survive being caught faking. This file makes the tag load-bearing — pipeline (§3) + storage (§1.2, §8) + viewer modes (§9) are the build order.

## 12. Where the model surfaces (adoption map, PR-sized order)

1. **Store + pipeline core** — `store.ts`, `artifacts.ts`, `pipeline.ts` with the §3.1 stage contracts and §14 tests 1/3/4 as the definition of done. This un-orphans DocView (the importer gap 2 names).
2. **DocView modes** — ModeBar + Readable/Source on `/documents/[docId]`; DerivativeBadge everywhere a page renders.
3. **Extracted mode** — the 65/35 inspector with accept/correct/unreadable, wired to ExtractedValue states (Gate 1).
4. **Evidence pane adoption** — the Review Room Evidence pane renders through the same viewer, receiving lit-row traces (lit-row §3 item 1 lands first; this item makes the artifact toggle available inside Evidence).
5. **Compare mode** — Version ⇄ Version first (rent-roll fixture), then Package ⇄ Prior (needs Composer package revisions).
6. **Intake preview** — arrivals preview through the same viewer pre-filing, badge included (Intake renders, never files; no-double-homing).
7. **Search over recreated text** — register + palette search hitting the word layer, suspect hits flagged (§7.1); palette results carry the derivative label (labeling-law clause 1).

Each item is one PR-sized unit with tests; none may ship its UI ahead of the artifact reality it renders (§11).

## 13. States (register + DocView)

| State | Behavior |
|---|---|
| Arrived, unrouted | Lives in Intake only; Documents shows nothing yet (no-double-homing) |
| Filed, processing | Original readable immediately (hash chip live); Recreated/Structured slots show named pipeline progress — stage labels, not bare spinners |
| Processing-failed | Visible failure state + retry action; Original remains fully readable; quiet-logged |
| Scan, recreated ready | Readable mode = Recreated + badge; search enabled over the word layer |
| Native, no recreated | Readable = native render; ArtifactToggle absent (nothing to toggle) — never a fake toggle |
| Suspects present | Suspect-count chip on the DocView header; Extracted mode orders rule failures first, then suspects |
| Extracted-unconfirmed | Proposed grammar (dotted underline + confidence); Gate 1 pending; readiness blocked upstream |
| Conflict (cross-document) | A conflict finding links both documents' regions side by side; resolved only by a typed act |
| Stale mapping | A COA/roll mapping older than the document's PMS export format flags for re-confirmation, never silently reapplies |
| Superseded version | Tombstone/stack chrome; "current is v{n}" link; still fully readable |
| Certified then changed | Void-on-change event visible on the document's audit trail and the period header (06 §6) |
| Sealed period citation | All cited artifact versions resolve read-only forever; seal badge on the chain |
| Permission-denied | Named restriction (e.g., resident-level rent-roll columns withheld from outward roles) — never a silent dead click |
| Read-only viewer role | All modes render; accept/correct/unreadable and filing acts absent, with the role named |

## 14. Acceptance tests (ticket-ready; fixtures = Calloway Park kit + Bexley canon)

1. **Hash stability.** Upload the SLOT-4 executed questionnaire scan; record sha256. Re-download: bytes identical, hash identical. Re-upload the same bytes: dedupe to the same content address — no second Original. Rerun the pipeline with a bumped OCR `processing_version`: a second Recreated appears; the Original's sha256 and bytes are unchanged; both Recreated versions list with parentage and their pipeline runs.
2. **Derivative labeling in every render context.** Automated walk of all render contexts — DocView header, Evidence pane, both Compare panes, thumbnail, search-result row, palette result, export, print footer — with a Recreated artifact mounted: each asserts the literal string "Recreated · searchable — derivative" via `<DerivativeBadge>`; a page render lacking the badge fails lint/CI.
3. **Scanned-questionnaire round trip.** Precondition asserted: the SLOT-4 scan's text-layer probe = `none` (evidence). The pipeline produces: (a) Recreated — text selectable at correct coordinates over unchanged pixels; (b) Structured — the questionnaire's 11 question fields with page+bbox locators (evidence: occupancy month-end fields, capital-improvement categories, management-fee band); (c) Extracted records — each carrying a source region. Click each extracted record → its exact region lights on the Recreated page; ArtifactToggle to Original preserves page/zoom and the lit locator; toggling back loses nothing.
4. **Native path.** SLOT-1 T-12 xlsx: the pipeline skips OCR (no Recreated required; no fake toggle renders); the Structured reading carries sheet+cellRange locators; the Chain-A spine holds — normalized lines tie to the sheet's own "Total Operating Expenses" $1,686,050 and NOI $1,218,877 (evidence), and each certified statement row lights its T-12 cell (06 §2).
5. **Suspect contract.** A fixture scan with forced low-confidence words: suspect words render original pixels; agent quotes and search snippets exclude/flag suspect text; the Extracted inspector shows the zoomed source region beside the proposal; **unreadable** pins the region, blocks silent acceptance, and raises an exception item that persists until humanly resolved.
6. **Compare.** Rent-roll v1 vs v2 (SLOT-2 fixture + an altered copy): synchronized scroll/zoom holds across all pages; `n`/`p` walks every difference exactly once; correcting one difference records the typed act and lists downstream impact (the 279/301 occupancy aggregate recompute flagged); below 1100px the split collapses to labeled tabs with the gutter retained.
7. **Filing question.** Uploading a file into a filed slot always asks "new version or replacement/correction?" — no filename-based inference path exists; each answer produces the correct recompute scope (§4.1) and a stack entry.
8. **Certification firewall.** Attempt to build a certifiable package from unconfirmed proposals: readiness stays false with the reason named ("extracted values unconfirmed"). After confirmation + certify, rerun the pipeline with a new processing version: certified figures do not move; the new proposal surfaces as an exception; accepting it fires void-on-change with a visible, reasoned event (canon fixture: Bexley — occupancy 268/301 = 89.04%, a shortfall against the monitored 90% floor; the verdict vocabulary is untouched by any reprocessing).
