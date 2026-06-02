---
type: prospect_architecture
prospect: matta
demo_name: the_refinery
architecture_pattern: stateful_pre_deployment_intelligence_sidecar
shipped_components:
  - deterministic_adc_router
  - stage1_prioritization_n3_ensemble
  - stage1_5_deterministic_stub
  - stage2_five_section_dossier_chain
  - transactional_outbox
  - byte_density_validator
  - container_boot_citation_validator
  - three_pane_theater_ui
designed_only:
  - real_oauth_surfaces_slack_hubspot_drive
  - wired_alembic_migrations
  - multi_replica_safety
  - section_granular_dscp_for_all_five_sections
  - gemini_3_model_family
status: live_demo_built_not_sent
last_updated: 2026-06-02
canonical: true
key_tags:
  - the_refinery
  - stateful_sidecar
  - two_stage_pipeline
  - deterministic_adc
  - n3_ensemble
  - ensemble_agreement_gating
  - transactional_outbox
  - gemini_2_5
  - vertex_europe_west4
  - prd_vs_shipped_audit
---

# Matta — Architecture: The Refinery

> **Lineage:** The Refinery is the third architecture cycle on Matta. For the two
> predecessor architectures killed at the 1F-red gate (CMMS Bridge, The Brief) and how
> this one was selected and survived a hostile audit, see `kill_log.md` (internal only).

Code is ground truth. Where `docs/MATTA_MASTER_PRD_v2.md` and the shipped repo disagree,
the **shipped code wins** and the divergence is documented in the audit section below.

## Sprint scope

48–72 hour single-phase sprint (Phase 1.7 in the repo's terms), Hafeedh (architecture) +
Isaac (demo production). Deliverable: a working, locally-runnable demo of the full
ingest → ranked-queue → on-demand pre-visit briefing flow, recorded for a Vidyard
walkthrough that accompanies a cold email. Not production-hardened (the repo says so
explicitly): test coverage, wired migrations, multi-replica safety, and real OAuth are
named as a later "Stage 2 engagement" / Stage G, not as shipped.

## Anti-Replication boundary (what the code never touches)

The deliverable contains **no** path to SENTRY / TALLY / GAUGE / TRACE, the Manufacturing
Foundation Models, the Manufacturing OS, edge firmware, or any camera / inference stream.
Confirmed structurally: `packages/adc/rules.py` has zero `genai`/`gemini` imports; the
only inference path is a Vertex AI client in `europe-west4`; `grep` of `pyproject.toml`
returns zero hits for `anthropic`, `openai`, `boto3`. All persistent state is Kaide's own
Postgres inside the container. Output surfaces are reversible projections into Matta's own
Slack / CRM / Drive. The repo's own audit confirms **zero `cmms_*` identifiers** remain
(the DLQ table is `outbox_dlq`, not `cmms_outbox_dlq`) — residue from the killed Cycle-1
architecture was purged during the build.

## The bottleneck addressed

Pre-deployment scoping + slot allocation: 100+ raw trade-show leads → ~12 deployment
candidates → per-candidate pre-visit briefing, compressed from a week of evenings into
one run. See `overview.md` for the full articulation.

## System architecture — the actual two-stage stateful pipeline

**Shape.** A FastAPI ingress + a Celery worker fleet over Postgres + Redis, with a Next.js
"Theater" UI. Two LLM stages ride a shared orchestration spine, fronted by a deterministic
router (Stage 0) and joined by a deterministic-only stub stage (Stage 1.5).

It is **stateful at the system level** and *stateless at the process level* — and both
halves matter. `LeadProspect` entities persist in Postgres for months as the multi-year
waitlist evolves (the README's own "stateless sidecar" line refers only to the API
process holding no in-process state beyond connection pools; the *pipeline* is durably
stateful, which is the whole point — a stateless-per-interaction sidecar could not model a
multi-year waitlist, and that mismatch is exactly what killed the predecessor architecture
"The Brief"; see `kill_log.md`). Persistent tables: `ingest_batches`, `lead_prospects`,
`dossier_artifacts`, `dossier_stubs`, `outbox` (+ `outbox_dlq`), `enrichment_artifacts`.
Redis holds idempotency keys and the Slack distributed lock.

**Stage 0 — Deterministic Action Domain Classifier** (`packages/adc/rules.py`). A pure
Python `route_request(envelope)` that maps payload **type** to a route with zero LLM
calls. The repo ships **four** routes — `PRIORITIZATION`, `DOSSIER_STUB`, `DOSSIER_FULL`,
`HUMAN_REVIEW` — not the PRD's "two-route" framing. `LeadIntakeBatch`, `SlackEventPayload`,
and `CRMLeadSignal` → `PRIORITIZATION`; `DossierRequest` → `DOSSIER_FULL`; unmatched →
`HUMAN_REVIEW` (no LLM ever reached). Functionally it reads as two-route to a buyer (batch
upload → Stage 1; single briefing request → Stage 2), but the code is four-route and
accepts Slack/CRM webhook envelopes as ingestion triggers, which the PRD did not specify.

**Stage 1 — Prioritization** (`refinery_worker/tasks/score_batch.py` + siblings),
per-prospect across a batch:
1. **Deterministic enrichment** (`packages/enrichment/`): Companies House, a bounded
   Playwright web scraper, and **Tavily news** (`tavily_news.py`). Wrapped with
   circuit-breaker / status semantics; failures degrade, don't block. Results persist to
   `enrichment_artifacts` with a five-value status (`fetched | not_applicable |
   fallback_empty | failed | not_attempted`).
2. **Vertical classification** (`classify_vertical.py`): **N=3 Gemini 2.5 Flash** ensemble
   at temperatures **0.1 / 0.5 / 0.9**, aggregated by **confidence-informed
   self-consistency (CISC, arXiv 2502.06233)** — a confidence-weighted majority vote, not
   naive logprob. No 2-of-3 consensus → `vertical_uncertain`, the three outputs persist to
   `lead_prospects.vertical_ensemble_outputs`, and the dossier later renders an explicit
   deferral block instead of substituting a default vertical.
3. **Deterministic fitness scoring** (`packages/scoring/fitness.py`, `weights.py`):
   pure-math weighted sum — `vertical_match(0.40) + size_band(0.25) +
   trade_show_provenance(0.20) + capacity_decay(0.15·(1−decay))`, clipped to [0,1]. No LLM
   in the score.
4. **Queue assembly** → ranked `lead_prospects`; Slack/queue surfaces update.

**Stage 1.5 — Deterministic stub** (`generate_dossier_stub.py`): for the top-12, writes a
`dossier_stubs` row (verified vertical + deterministically-selected headline KG anchor +
`slot_readiness ∈ {ready_for_dossier, requires_human_review, low_signal}`) with **zero LLM
calls**, plus three `outbox` envelopes (slack/crm/drive) so all three surfaces light in one
beat. This stub layer is a real shipped component the PRD only implied.

**Stage 2 — Dossier composition** — a **linear Celery task chain** (NOT a chord/group;
each section feeds the next as context), kicked off by `generate_dossier.py` which inserts
a `state='generating'` `dossier_artifacts` row (`ON CONFLICT DO UPDATE` for retry-safety):
1. **§1 Process Taxonomy** — `dossier_section_taxonomy.py`, **Gemini 2.5 Pro**, N=1 →
   `ProcessTaxonomy`.
2. **§2 Defect Hypothesis** — `dossier_section_defect.py`, **Gemini 2.5 Flash N=3**
   (temps 0.1/0.5/0.9) → `LikelyDefectClassHypothesis` via
   `compute_agreement_set(samples, calib)`. This is the **only** section running the full
   uncertainty pattern (it carries `agreement_set`, `coverage`, `inter_model_agreement_score`,
   `deferral_reason`, `requires_human_review`). On no-KG-anchor for the vertical it defers
   the section (`requires_human_review=True`) and **still dispatches the next task** rather
   than silently killing the chain (an explicit DS-CP "Tightening 4" fix).
3. **§3 Comparable Matta Deployment** — `dossier_section_comparable.py`, **Gemini 2.5
   Pro**, N=1. **Anchor selection is deterministic** (`packages/knowledge_graph/select.py`)
   — the LLM only writes the ≤250-char `dimension_of_comparability` prose. Schema
   `selection_method` is `Literal["deterministic_rules","no_comparable_available"]`; there
   is no `llm_picked` option, so an LLM-invented customer reference is structurally
   impossible. `evidence_strength` grades anchors strong→weak (named-customer specific
   deployment … down to `vertical_precedent`) so honest contrast is visible.
4. **§4 Integration Risk Register** — `dossier_section_risk.py`, **Gemini 2.5 Pro**, N=1 →
   `RiskRegister`, findings keyed to a fixed 8-category enum (severity ∈
   identified/unknown/not_applicable).
5. **§5 Suggested Approach** — `dossier_section_approach.py`, **Gemini 2.5 Pro**, N=1 →
   `SuggestedApproach`, `template` ∈ a 4-value enum (two/four-camera pilot, full-line,
   Caracol-AM OEM partnership).
6. **Compose** — `compose_dossier.py`: loads all five sections, renders deterministic
   blocks (company facts, verified KG anchors w/ peer-anchor cross-table, fitness decision
   tree, risk checklist, approach phase breakdown) + LLM blocks, constructs
   `PreVisitDossier` (triggers the byte-density validator), then in **one Postgres
   transaction** updates `dossier_artifacts` to `complete` and inserts three `outbox`
   envelopes (`slack_canvas`, `crm_note`, `drive_doc`). On byte-density `ValidationError`,
   the dossier is marked `rejected_byte_ratio` and **never persisted to outbox** — a hollow
   briefing never ships.

**State / idempotency / degradation.** Batch idempotency on `(file_hash, user, day)` via
Redis; prospect upsert on `external_lead_id`; dossier idempotency on `(prospect_id,
signal_hash, kg_version)`. Celery configured `acks_late=True`, `reject_on_worker_lost=True`,
`prefetch_multiplier=1`, Redis broker `visibility_timeout=3600`. Slack retries deduped by a
Redis `SET NX EX 60` lock keyed on `event_id`. Enrichment circuit-breakers degrade to
partial dossiers with gaps marked.

**Theatre surfaces** (`apps/theater_ui/`, Next.js 14 + Tailwind, served at `:3000`;
FastAPI at `:8080`): a three-pane "Theater Console" with the Matta wordmark — **left** a
Slack pane (`#fde-lead-refinery`, the CSV + ranked prospect cards), **center** the Theater
Console (Run Demo button → ADC route pill → Stage 1 progress → five Stage-2 section rows
firing grey→peach→green, byte-density gradient bar, KG citation popover), **right** a
**Google Drive** document pane that fills section-by-section, plus a fixed CRM (HubSpot)
inset. Note this is **Google Drive, not the PRD's "Notion-style" panel** — a real
divergence. Demo entry point: `/sandbox?mode=quickdemo` (re-exports `index`), the
prospect-facing URL referenced from the cold email.

**Integrations — live vs mocked:**
- **Live:** Vertex AI (Gemini 2.5) inference; Postgres; Redis; the full Celery pipeline;
  container-boot KG citation validation; the byte-density validator; the Slack lock; the
  transactional outbox + dispatcher + DLQ.
- **Mocked:** Slack, CRM/HubSpot, and Google Drive output surfaces are **mock servers**
  (`apps/mocks/`, ports `:8090/:8091/:8092`) that accept POST writes and return success —
  the outbox dispatcher cannot tell them from real APIs. Real OAuth (Slack app review,
  HubSpot private app, Google Drive consent) is explicitly deferred. LinkedIn signal
  enrichment is mocked.

## PRD design-intent vs shipped-state audit (Ylookup-grade)

| # | PRD `MATTA_MASTER_PRD_v2.md` specified | Shipped code does | Disposition |
|---|---|---|---|
| 1 | Model strings `gemini-3-flash-preview` / `gemini-3.1-pro-preview` | `gemini-2.5-flash` / `gemini-2.5-pro` | **Divergence.** Gemini 3 family is behind a Google project allowlist Kaide is not enrolled in. Architecture is generation-agnostic; swap is one `sed` across `packages/prompts/`. **Historical-stack note only — still 100% Google/Vertex; clean-mandate compliance intact** (no Anthropic/OpenAI/Bedrock anywhere). |
| 2 | "Conformal prediction set" with coverage guarantee | **Ensemble-agreement vote-share gating** with per-class hand-tuned thresholds (`packages/uncertainty/agreement.py`) + section-granular DS-CP | **Honesty divergence — the README explicitly disclaims** it is "not split-conformal prediction (no nonconformity score, no marginal coverage guarantee)." The mechanism is real and calibrated against a 30-event holdout, but the formal name was downgraded to match what the code actually guarantees. Pattinson-grade integrity move. |
| 3 | "Knowledge graph," ~360 anchor records | A **flat citation/anchor table** (`graph.json`) with container-boot quote-pinning against `Matta_Intel_cleaned.md`; no entities/relations/traversal | **Naming divergence, disclosed.** "Knowledge Graph" kept in UI labels with an explicit popover footer noting it is a verified citation table. Provenance validation is real and load-bearing (container refuses to boot if a quote drifts). |
| 4 | Two-route ADC | **Four** routes (PRIORITIZATION / DOSSIER_STUB / DOSSIER_FULL / HUMAN_REVIEW); also accepts Slack + CRM webhook envelopes | **Expansion.** Still fully deterministic, still zero LLM in routing. |
| 5 | Right pane = Notion-style document | **Google Drive** document pane (`DriveDossierRightPane.tsx`, `mock_drive`) | **Surface divergence.** Drive better fits "shareable link the FDE forwards." |
| 6 | Stage 2 fan-out implied; N=3 deep ensemble | **Linear** Celery chain (each section is upstream context); N=3 is **temperature-varied sampling of one Flash model**, not independently-initialized networks | **Disclosed.** README states it is "not a deep ensemble in Brion et al.'s sense"; chord/group deferred (no observable gain at 5 sections). |
| 7 | Magic Moment under 90s (§3.4/§6.8) | Quickdemo rank-1 path ~85s (Stage 1 pre-baked at boot); **full ingest→briefing ~7–9 min** over the 124-prospect batch | **Timing divergence — see Magic Moment below.** PRD's T+85/88 was specced against a 10-prospect simulator + gemini-3; as-built is honest and documented in `MATTA_RECONCILIATION.md §4(a)/§6.8`. |
| 8 | `extra="forbid"` at every boundary | Present at every schema boundary (README says 35, the walkthrough says 28 — **[VERIFY]** exact count) | Compliant; count is the only ambiguity. |
| 9 | Defect demo path reads vertical dynamically | Defect section still effectively `vertical="metal_casting"` for the demo prospect (other four sections fixed in Phase 1.5) | **Known scaffold.** Harmless because William Cook *is* `metal_casting`; documented in `MATTA_RECONCILIATION.md §6.7`. |
| 10 | `RootCauseHypothesis` appears in PRD §6.3 code sample (line 506) | **Not present in shipped code** (residue from the killed CMMS-Bridge v1 schema that leaked into the PRD prose) | PRD-only residue; shipped schema is `LikelyDefectClassHypothesis`. Flag for PRD hygiene, not a code issue. |

**Model-mandate verdict:** fully Gemini / Vertex AI `europe-west4` via `google-genai`. No
non-Google model anywhere. The 2.5-vs-3 gap is a stack-currency note, not a mandate breach.

## Magic Moment (verified against code, not PRD claims)

The PRD's "under 90 seconds" was **aspirational** — measured against a 10-prospect
simulator and the (unavailable) gemini-3 family. The **as-built, measured** reality:

- **Quickdemo path** (`/sandbox?mode=quickdemo`): Stage 1 is **pre-baked at container
  boot** (`startup_prebake.py` runs the default 65-row Industrial AI Summit CSV during the
  ~6-minute warm-up), so the *demo-time* rank-1 → full briefing experience lands near the
  README's **~85 seconds** — but only because the queue is already warm.
- **Full cold run** (drag a 124-row CSV): Stage 1 ~**5–6 min** + Stage 2 ~**3 min** =
  **~7–9 min** end-to-end, verified across **three §H smoke runs** with byte-density ratios
  **0.685 / 0.652 / 0.675** (all PASS above the 0.60 floor) and cost ~**$0.037–0.042 per
  dossier** vs a $0.10 ceiling. This is **measured, not aspirational** — there are
  `dossier_id`s from each run.

The load-bearing visual is **structural**, not the timer: top-12 ranking + three-surface
lockstep (Slack/CRM/Drive in one Postgres commit), then a five-section briefing with the
byte-density gate and the "Deterministic KG selection — LLM did not pick this anchor"
callout. Headline demo prospect: **William Cook Sheffield** (`metal_casting`, fit 0.84,
anchor `metal_casting_unnamed`, citation substrate lines 271/421).

## 5-Pillar verdict (honest partials)

1. **Bottleneck Assassin — PASS.** Four independent verbatim sources for the pre-sales
   triage bottleneck (waitlist + capacity cap, 124-lead trade-show volume, FDE JD,
   Special-Projects JD). The build absorbs exactly that slice.
2. **Anti-Replication — PASS (clean).** Zero core-surface contact; reversible; ego-check
   clear. CMMS residue from the killed cycle fully purged.
3. **Native Environment — PARTIAL.** Right surfaces (Slack coordination / CRM identity /
   Drive shareable artifact, desktop-anchored for an internal team), but all three are
   **mock servers** — no real OAuth. Architecturally faithful, integration-incomplete.
4. **Magic Moment — PARTIAL.** Sub-90s holds **only** on the pre-baked quickdemo path;
   the honest full-run number is ~7–9 min. Structurally complete and measured; the
   headline timer is demo-staged, and the team is transparent about it.
5. **System Resilience & Immunity — STRONG-PARTIAL.** Transactional outbox + DLQ,
   Redis Slack lock, three-layer idempotency, circuit-breaker degradation,
   container-boot citation validator, Goodhart-resistant byte-density gate, deterministic
   routing — all real and unit-tested. Gaps: alembic present but **not wired**
   (`create_all` at lifespan), single-replica assumptions, and **DS-CP fully wired only on
   the defect section** (the other four LLM sections persist output but carry no
   uncertainty fields yet).

## What was Matta-specific vs reusable

- **Matta-specific:** the manufacturing-vertical enum and KG anchors (B&W, Caracol AM,
  global drinks brand, metal-casting/polymer unnamed); the citation substrate
  (`Matta_Intel_cleaned.md`) and its boot-time pinning; the fitness weights tuned to
  deployment-capacity logic; the Brion-echo ensemble framing; the William Cook demo seed;
  stack-mirroring to Denic's exact toolset for absorbability.
- **Reusable (→ the pattern):** the Stateful Pre-Deployment Intelligence Sidecar shape
  itself — deterministic ADC front door, N=3 CISC ensemble for the one categorical
  judgment, deterministic scoring + deterministic comparable-selection with LLM-prose-only,
  the byte-density deterministic-floor gate, the transactional-outbox three-surface
  lockstep, and container-boot citation provenance. See forward-ref in `CLAUDE.md`.

## Forward-reference notes

- `frameworks/architectural_patterns/stateful_pre_deployment_intelligence_sidecar.md` —
  **expected-missing.** Per `frameworks/architectural_patterns/CLAUDE.md`, the Matta
  pattern (placeholder "Bridge++") is deliberately **held out of the library until the
  engagement closes** to avoid contaminating the pattern with an in-flight, not-yet-sent
  build. File it once outcome is known.
- `kill_log.md` — internal lineage for the two killed predecessor cycles.
