# 10 — DESIGN SYSTEM AND COMPONENT REGISTRY
## Consuming the ruled token system; mapping every component to an existing repository component or naming the new one

Token authority (consumed verbatim, amended nowhere): Review-9 package — Covenant accent **#7189FF periwinkle family** (`--acc #7189FF · --accH #8EA1FF · --accA #6078F4 · --accT #A9B5FF · --accOn #0B1020`); the **ten-rung gray ladder** (adopted 2026-08-07; 55 re-point sites execute measurement-gated); the **certified sheet whole-sheet blue-violet** six hexes (panel `#E7EBF8` · tint `#DDE4FB` · hairline `#B3BCDA` · accent sentences + Certify `#33419D` · hover `#5265C5` · pressed `#121459`, light document surface only); **Geist Mono** as the mono face; `tabular-nums slashed-zero`; colors pinned as hex in `src/styles/covenant-tokens.css` (the oklch-compiles-darker trap — verify by readback). Basis stays #1399FD and appears nowhere in Covenant. **This plan proposes zero new colors.** Where a component below names a semantic role, it binds to an existing ruled token, and the binding is verified by canvas readback in CI, not by eye.

## 1. Typography and number roles (existing rulings applied, not amended)

| Role | Face/setting | Used by |
|---|---|---|
| UI text | The shipped app face per current tokens | Everything non-figure |
| Financial figure | Geist Mono, `tabular-nums slashed-zero`, right-aligned in grids | Figure component, grid financial columns, certificate values, palette computed answers |
| Paper/certificate text | The certificate's ruled sheet treatment (light paper; accent sentences #33419D) | Certified sheet, DocView porcelain contexts |
| Provenance affordance | Dotted underline on figures carrying refs; grayed variant for no-source legacy values | Figure component |

## 2. Component registry

Legend: **EXISTS** = named in the snapshot/current build (reuse; adjust as noted) · **EXTEND** = exists, gains a contract · **NEW** = to build. Every NEW component lands in `src/components/covenant/` unless noted; pure logic lands in `src/lib/covenant/`.

### Frame

| Component | Status | Contract / notes |
|---|---|---|
| `CovenantShell` | EXISTS — keep | Rail + 48px header + view switcher + canvas panel; gains route-addressability (08 §1); no visual change |
| Rail | EXISTS — adjust | Contents per 08 §3 (pending D-1); collapsed icon mode; one Starred group |
| Breadcrumb altitude gate | EXISTS — extend | Crumbs become loan/period switch popovers (08 §2) |
| `CommandPalette` / `SearchPalette` | EXISTS — extend | Three result classes incl. engine-computed answers with provenance refs (08 §5); dangerous verbs navigate-only |
| Canvas panel | EXISTS — keep | Hosts Ask, quick previews, palette detail; Esc dismisses |
| `CountBadge` | EXISTS (ruled consolidation: 37 atoms → 1, on `build/covenant-count-badge-atom`) | The only count badge; your-move counts everywhere |
| Your-move service | NEW (lib) | The one computed query (03 §4); consumed by rail badge, Home, Inbox |
| Notification/escalation service | NEW (lib) | Due-rule engine + escalation ladder (cross-cutting spec) |

### Data display

| Component | Status | Contract / notes |
|---|---|---|
| `CovenantTable` | NEW (consolidating the current per-surface fixture tables) | Uniform row heights; open-not-boxed; column type system; saved-view state; virtualization; keyboard grammar (cross-cutting tables spec). Per-surface columns via config — no forked grids |
| `Figure` | NEW | The provenance-carrying numeral (lit-row contract §2.2); three-actor grammar; the atom every material number renders through |
| Verdict chip + basis badge | NEW (pair) | Word-carrying chips per the status-vocabulary law; basis badge mandatory beside every verdict |
| Covenant strip | EXISTS as fixture — rebuild on engine rows | TestResult rows: requirement title, basis badge, verdict, signed headroom (dual denomination), watch-band note |
| Headroom readout | NEW | The "$ and ratio distance" object; used in strips, findings, review stops |
| Trend chart | EXISTS (the one runtime wire: `assembleTrendSeries`) — extend | Chart-doctrine compliant; every mark a `Figure`-grade instrument clicking to its sealed period |
| Period header | NEW | Loan + period identity, status, deadline, seal/void banners; renders on every spine surface (period-lifecycle spec) |
| Saved views | NEW (one mechanism) | Linear/Attio scoping (personal→shared, star-to-rail, URL-carried); consumed by every register |
| Status/state banners | NEW (family) | VOID banner, sealed banner, blocked/fail-closed banner, stale badge — one family, per-state content |

### Documents and evidence

| Component | Status | Contract / notes |
|---|---|---|
| DocView (porcelain sheet) | EXISTS — extend | Mode strip Readable/Source/Extracted/Compare; derivative labeling; suspect-word grammar (documents-artifacts spec) |
| Evidence panel | EXISTS (review room) — extend | Receives `traceToEvidence`; persistent lit row (`--accT` band + accent bar); chain breadcrumb header |
| Document compare | NEW | Synchronized 50/50 peer compare (version↔version, package↔prior); Original↔Recreated toggle overlay |
| Arrival preview | NEW | Email envelope + attachments + DocView-render (Intake center pane) |
| Decision inspector | NEW | Candidate matches with confidence + why; the File/Supersede/Not-ours/Hold verb set (Intake right pane) |
| Holding checklist | NEW | Arrived/missing/chased/waived rows; chase history; completeness meter |
| Extraction proposal card | NEW | Requirement/term/actual proposal: kind, basis badge, verbatim definition quote, confidence, source-region link, Confirm/Correct/Unreadable/Author actions |
| Downstream-uses panel | NEW | "This value feeds …" (documents Audit-Usage mode + correction impact strips) |

### Workflow and ceremony

| Component | Status | Contract / notes |
|---|---|---|
| Review outline / stop walk | NEW | Ordered exception stops; N/P keyboard; disposition inline (review-room brief) |
| Readiness rail | NEW | Computed reasons each linking to its blocker (GitHub required-checks pattern); consumed by Composer, Review, Certificate |
| Certified sheet | EXISTS (route + resolver) — paint per ruling | The six ruled hexes painted exactly as its brief specifies (currently zero of eight paper tokens render — the phase-3 negative); every figure a `Figure` |
| Certify ceremony modal | NEW | Typed attestation; identity-bound; writes CertificationRecord; one of exactly two heavy ceremonies |
| Send panel + receipt | NEW over the REAL gate | Recipient (lender-scope memory), approved-bytes hash display, typed SEND, SendRecord receipt; honest 503/404/403 renders |
| Timeline / activity feed | EXISTS as fixtures — rebuild on ActivityEvent store | One store, filtered projections (kills the U3-F2 contradiction class) |
| Finding card | NEW | Trigger evidence + headroom phrasing + drafted-narrative link + typed disposition |
| Memory chip | NEW | "Learned {date} from {who}" inline render beside filled fields; inspect/correct popover |
| Quiet-log run detail | NEW | Two-pane run anatomy (step list + selected-step evidence), event-group collapsed |
| Chase composer | NEW | Agent-drafted chase; approved-cadence controls; blackout dates |
| Draft editor (narratives/replies) | NEW | Agent draft + tracked human edits; engine figures interpolate as locked `Figure` refs (numerals never hand-typed) |
| Import mapper | NEW | CSV map-columns + first-N validation + in-tool repair + staged commit + undo-import |
| Onboarding checklist | NEW | Requirements-computed steps; resumable; persists until genuinely done |

## 3. Reuse boundaries (what must NOT be shared)

- The certified sheet's paper treatment is **certificate-surface-only** (plus DocView porcelain where already ruled). No other surface borrows the paper hexes for emphasis.
- Verdict severity color never leaks into non-verdict chips (severity-only color law).
- The Ask panel is the only conversational component; no other surface embeds chat.
- Basis components, tokens, and styles: never imported. Ruling-J confirmed Covenant reconciles under its own tokens; the boundary is a lint rule (no cross-product imports in `src/components/covenant/`).
- The intake email address renders via one read-only component (Settings owns management).

## 4. Consolidation orders (existing debt, measurement-gated like the CountBadge precedent)

1. Count badges → the one `CountBadge` (ruled; branch exists) — 37 atoms across 21 files.
2. Per-surface fixture tables → `CovenantTable` config instances (count at implementation time; same measurement-gated, no-visual-change discipline).
3. Ad-hoc activity feeds → ActivityEvent projections.
4. Hand-rolled figure spans → `Figure` (CI counts bare numerals in JSX as the fixture-migration burndown metric).
5. Grey re-points: the 55 ruled sites execute under the adopted ten-rung ladder (already Terry-ruled; listed here as sequencing, not decision).

## 5. Registry-to-repository map summary

| Repo location | Contents after this plan |
|---|---|
| `src/components/covenant/` | All components above; one directory per component family (frame/, table/, evidence/, ceremony/, agent/) |
| `src/lib/covenant/` | Engine (existing, wired); provenance resolver; your-move query; due-rule engine; findings service; memory store contracts |
| `src/styles/covenant-tokens.css` | Unchanged token authority (hex-pinned); zero additions from this plan |
| `src/app/(covenant)/` | Deep routes per 08 §1 |
| `src/app/api/covenant/` | `send` (existing REAL) + the typed-act writers (certify, confirmations) as gate-guarded routes |
