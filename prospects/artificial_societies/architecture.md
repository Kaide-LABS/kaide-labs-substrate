---
type: prospect_architecture
prospect: artificial_societies
vertical: synthetic_societies_ai
demo_name: radiant_calibration_sidecar
sprint_duration: 72_hours
last_updated: 2026-05-21
canonical: true
architecture_pattern: calibration_sidecar
key_tags:
  - stateless_sidecar
  - upstream_ingestion
  - multi_agent_extraction
  - parallel_extractors
  - deterministic_validation_gate
  - field_state_engine
  - evidence_merger
  - long_context_synthesis
  - family_scoped_semantic_validation
  - nia_validator
  - chromadb_retrieval
  - sse_theatre
  - flagged_for_human_review_surface
  - gemini_flash_extractors
  - gemini_flash_lite_triage
  - demo_cadence_override
---

# Artificial Societies — Architecture: Radiant Calibration Sidecar (RCS)

## Sprint scope

72-hour build of a stateless containerized sidecar that converts heterogeneous F100 research artifacts (PDF decks, SPSS .sav, .qsf survey instruments, CSV/XLSX exports, DOCX transcripts) into a schema-validated `RadiantPersonaCalibration` JSON payload consumable by Radiant's existing audience-build path. Backend: FastAPI on Cloud Run, `google-genai` SDK against Vertex AI. Frontend: Next.js + Tailwind drag-drop + live SSE theatre. One downstream contract with the core engine: one validated JSON payload.

The repo is `rcs-sidecar/`. What follows is the verified architecture as it stands in the repo, with at-time-of-pitch and demo-cadence deltas called out explicitly.

## Anti-Replication boundary

The sidecar operates strictly upstream of Tom Whittle's core engine. At design time, specifically NOT touched:

- The 2.5M-persona database
- The multi-agent network simulation engine (1000+ agent runtime)
- The 95%-accuracy methodology and underlying published research
- The Pulsar social-listening data pipe (RCS is the static enterprise-artifact feed; Pulsar is the dynamic feed — complementary, not competing)
- The existing network-visualization UI
- Mirror World (LinkedIn URL → persona chat)
- The Reach consumer product (sunset)

The sidecar terminates at JSON handoff. If AS unplugs it, the simulation engine still runs — the team just reverts to manual artifact ingestion. Tom Whittle's "Unplug Guarantee" is structural.

## The bottleneck addressed

Radiant promises F100 customers a 24-hour bespoke turnaround. The simulation itself runs in 30 seconds to 2 minutes. The latency lives in pre-field manual data ingestion — translating each customer's research warehouse into Radiant's structured calibration schema. Industry-standard custom-segment stand-up is 6–12 weeks. The RCS target was to compress that pre-field phase from days/weeks of bespoke consulting work to ~90 seconds of automated extraction + synthesis + validation, while preserving the deterministic safety guarantees required for enterprise legal review.

## System architecture

### Data flow

```
multipart upload (1-25 artifacts, ≤50MB each)
  → [1] Ingestion: format detection + per-format parsers
  → [2] Triage agent: classify each artifact into 10 ArtifactType values
  → [2b] Field-State Engine: build a per-agent extraction plan from the triage manifest
  → [2c] (optional, flag-gated) Index engagement corpus into ChromaDB
  → [3] 6 parallel extractors via asyncio.gather, each with continuous Pydantic validation
  → [4] Evidence Merger: deterministic dedup, source-authority ranking, contradiction detection
  → [5] Synthesis agent: long-context consolidator → draft RadiantPersonaCalibration
  → [6] Deterministic validation: 8 rules; targeted retry (budget=0 in demo)
  → [7] Output: validated JSON + Field-State summary + coverage_gaps
SSE stream emits TheaterEvent on every stage boundary
```

The pipeline is implemented in `pipeline.py:run_calibration`. Cloud Run config: `cpu=2, memory=2Gi, min_instances=1, max_instances=50, concurrency=40, timeout=300`.

### The 6 extraction agents (verified against repo)

All extractors use `response_schema=PydanticClass` for native structured output and have continuous Pydantic validation on the response. Models verified by reading `agents/*.py`:

| Agent | Model | Thinking | Role |
|---|---|---|---|
| `triage_agent` | `gemini-3.1-flash-lite-preview` | MINIMAL | Classify each artifact into one of 10 ArtifactTypes (brand_tracker, segmentation_study, focus_group_transcript, crm_export, survey_instrument, ethnography, competitive_intel, verbatim_corpus, **regulatory_filing**, other). Returns a TriageManifest. |
| `segment_extractor` | `gemini-3-flash-preview` | LOW | Extract named audience segments with descriptions, weights (sum→1.0), demographic + psychographic attributes. ChromaDB-retrieval-aware. |
| `verbatim_distiller` | `gemini-3-flash-preview` | LOW | Extract representative direct quotes from transcripts/ethnography, tag with sentiment, associate with segments. Top_k=30 for quote scatter. ChromaDB-retrieval-aware. |
| `demographic_normalizer` | `gemini-3.1-flash-lite-preview` | MINIMAL | Map raw demographic data to canonical fields (age_range, income_bracket, education_level, etc.). ChromaDB-retrieval-aware. |
| `behavioral_extractor` | `gemini-3-flash-preview` | LOW | Extract behavioral attributes keyed against the canonical `behavioral` vocabulary set (media_consumption, purchase_frequency, trust_in_institutions, ...). |
| `brand_tone_extractor` | `gemini-3-flash-preview` | LOW | Extract brand voice/tone constraints, forbidden language, messaging guardrails. |
| `campaign_benchmark_extractor` | `gemini-3-flash-preview` | LOW | Extract campaign KPI baselines (brand_awareness_pct, NPS, email_open_rate, etc.). |

Extractors that don't get an assignment from the Field-State Engine plan (e.g., behavioral_extractor when no triage-flagged artifacts call for it) short-circuit at `if not assigned: return` and never make an LLM call. This is intentional — extraction is targeted, not exhaustive.

### Synthesis agent (demo-cadence override flagged)

**PRD design intent** (ULTIMATE_PRD.md + PRD.md): `gemini-3.1-pro-preview` with `thinking_level=HIGH`, `location=global`, long-context consolidation over the merged evidence graph (~150K–500K tokens after Evidence Merger reduction).

**Repo shipped state** (`pipeline.py:synthesis_agent`): `MODEL_FLASH` (`gemini-3-flash-preview`) with `thinking_level=LOW`. This is a deliberate demo override to hold the <90s SLA. The PRD's Pro/HIGH path is documented and reachable — the demo just doesn't use it. The targeted_retry function still uses MODEL_PRO with MEDIUM thinking, but `retry_budget = 0` in the pipeline (see `pipeline.py`: `retry_budget = 0  # demo: skip targeted-retry loop to keep latency under control`), so retries don't fire in the demo path either.

The honest framing of the demo is: Flash/LOW synthesis + no retries + deterministic rules catch what slips through. Violations get appended to `coverage_gaps` and offending segments are marked `requires_human_review=True` rather than blocking the response.

### Two semantic substrates

Two separate Nia/embedding-backed semantic surfaces serve two different purposes. This was a single Nia surface initially; the retrieval-side was migrated to ChromaDB after a production-grade failure on F100 corpus scale.

**Substrate 1 — Validator (Nia, current)**
- Module: `validators/semantic_validator.py`
- Corpus: `validators/canonical_vocabulary.json` — 5 families, ~67 canonical keys total, ~350 aliases
- Indexed once via `validators/seed_nia.py` into 5 separate Nia `local_folder` sources (one per family)
- Called at validation time by `rules_engine.canonical_attribute_vocabulary` to classify each extracted attribute key against its family's canonical vocabulary
- HTTP: `POST /v2/search` with `mode=query, skip_llm=true, local_folders=[family_source_id]`
- Threshold: `NIA_MATCH_THRESHOLD=0.50` (recalibrated for Nia's reranker score scale, not the JSON's 0.85 cosine assumption)
- Verdict: `valid` / `non_canonical` / `ambiguous` / `fallback_valid` / `fallback_non_canonical`
- Fallback: difflib `SequenceMatcher` at 0.7 ratio when Nia is unavailable/disabled/times out (3s hard timeout)

**Substrate 2 — Retrieval (ChromaDB, post-migration)**
- Module: `retrieval/chroma_corpus.py` (replaces the original `validators/nia_corpus.py`)
- Embeddings: `gemini-embedding-001` via the same Vertex AI client; `PersistentClient` at `data/chromadb/`
- Indexes the engagement-specific artifact corpus per request, queries it from segment_extractor / verbatim_distiller / demographic_normalizer with family-tuned queries (top_k=20–30), tears down the collection at end of run
- Flag-gated: `RCS_NIA_EXTRACTION_ENABLED=true` (name preserved from the original Nia integration for env-var continuity)
- Default OFF in the demo path; extractors fall back to the truncation path (`raw_text[:50000]`) when corpus retrieval is disabled or fails

**Why the migration**: The Nia `/v2/sources` endpoint hit a server-side ingestion ceiling on a 13.9MB Unilever 20-F during F100 stress testing. Chunked upload (~3.5MB parts) succeeded on POST but Nia's downstream indexer didn't flip the source to `indexed` within 15 minutes. ChromaDB sidesteps the remote-indexing throughput ceiling entirely — local `PersistentClient` plus Gemini embeddings batch at ~25s for a 1MB corpus. Validator-side Nia is unchanged (the canonical vocabulary is small, indexed once, and Nia's reranker quality is the value there).

[VERIFY] At the time of the AS pitch (March–May 2026 engagement window), the retrieval substrate may still have been Nia rather than ChromaDB. The productization_v2.md migration notes are dated 2026-05-04+. I haven't reconciled git history against the AS demo-send timestamp.

### The 8 deterministic validation rules

`rules_engine.RULES` (verified by reading `rules_engine.py`):

1. `weights_sum_to_one` — segment weights must sum to 1.0 ± 0.01
2. `every_attribute_has_citation` — `len(attr.citations) >= 1` for every behavioral/demographic/psychographic attribute
3. `verbatims_per_segment_minimum` — ≥5 verbatims per segment
4. `canonical_attribute_vocabulary` — async; calls `validators/semantic_validator.validate_canonical` per attribute, family-scoped. Returns violations for `non_canonical` and `ambiguous` verdicts; `fallback_valid` lands in `coverage_gaps` as a soft warning.
5. `confidence_monotonic_with_sources` — `attr.confidence <= sqrt(len(attr.citations) / 10) + 0.1`. Single-source attributes cap at sqrt(0.1) + 0.1 = **0.416**, which is the "0.42 single-source cap" referenced elsewhere.
6. `no_pii_in_verbatims` — regex scan for emails, US phone numbers, SSNs in `Verbatim.text`
7. `provenance_completeness` — every segment's attribute citations must intersect with `global_provenance` artifact IDs
8. `brand_constraints_have_examples` — async; hard fail on zero examples, soft warning (coverage_gap) when description fails the brand_constraint canonical vocabulary check

When a rule fires, the orchestrator (`pipeline.run_calibration`) appends the violation description to `calibration.coverage_gaps` and marks any affected segment `requires_human_review=True`. The response still ships — the calibration is delivered with explicit "these fields are flagged" annotations rather than blocked. This is the load-bearing "we never invent" guarantee for enterprise legal review.

### Field-State Engine

`field_state.py:FieldStateEngine` is pure Python, no LLM. It tracks 11 registered fields through a 4-state lifecycle:

- `UNKNOWN` — no evidence yet
- `CANDIDATE` — evidence merger surfaced ≥1 evidence node for this field
- `VALIDATED` — the rules engine ran and no violation touches this field
- `BLOCKED` — a violation touches this field and the retry budget is exhausted

The Field-State Engine also produces the `FieldExtractionPlan` deterministically from the triage manifest — mapping ArtifactType → which agents should process which artifacts. This is the targeted-extraction discipline: extractors only process artifacts assigned to them, never the full set.

The 11 registered fields (per `field_state._initialize_from_schema`): `segments`, `segments[].label`, `segments[].description`, `segments[].weight`, `segments[].demographic_attributes`, `segments[].psychographic_attributes`, `segments[].behavioral_attributes`, `segments[].information_sources`, `segments[].verbatims`, `brand_constraints`, `campaign_benchmarks`.

### Evidence Merger

`evidence_merger.EvidenceMerger.merge` is pure Python, no LLM. It:

1. Collects all extraction results, keying each evidence node by its target schema field path
2. Deduplicates by `(artifact_id, locator)` tuple, keeping the higher-confidence node
3. Detects contradictions — multiple nodes with different categorical values for the same field
4. Ranks per-field nodes by source authority (`primary_research` > `secondary` > `inferred`) then by confidence

The Merger is what makes synthesis input size bounded — instead of feeding the synthesis agent all 6 extractors' raw outputs, it hands over one deduplicated, ranked evidence graph plus an explicit contradictions list. This is what allows the demo's Flash/LOW synthesis to stay within token budget and latency targets.

## Theatre layer

The Next.js frontend (`rcs-sidecar/frontend/`) is the "Pipeline Activity" theatre — the buyer watches the schema fill in real time. Critical components:

- `DropZone.tsx` — drag-drop ingestion surface
- `TheaterPanel.tsx` — SSE-tail stream of `TheaterEvent` objects emitted by the backend on every stage boundary (ingest, triage, field_state, extract, merge, synthesize, validate, done, error). Per-event expandable meta (model, input_tokens, output_tokens, latency_ms). Includes a UUID→artifact-#N renaming layer so the buyer doesn't see raw artifact_ids.
- `FieldStateBar.tsx` — horizontal stacked bar showing validated / candidate / unknown / blocked counts. Reads from `calibration.field_state_summary` after completion; falls back to the latest theater message during streaming. **This is the load-bearing "magic moment" component** — the buyer literally watches the schema fill.
- `CalibrationResult.tsx` — segment cards with weight bars, attribute blocks (demographics / psychographics / behavioral), verbatims preview, brand constraints, and the **Flagged for Human Review** surface
- `LoadButton.tsx` — "Load into Simulation →" CTA; opens a toast confirming N segments handed off

### "Flagged for Human Review" surface

The flagged-review surface is the visible enforcement of the deterministic gates. Implementation in `CalibrationResult.tsx:FlaggedForReview` groups gaps by category:

- **VOCABULARY** — key is not a canonical key for its family
- **AMBIGUITY** — key is semantically ambiguous between multiple canonical candidates within a family
- **EVIDENCE** — confidence exceeds the bound for the attribute's citation count (rule 5)
- **MISSING DATA** — field is `unknown` in field_state
- **OTHER** — uncategorized

The surface deliberately groups EVIDENCE violations by signature (collapsing numeric variants of "confidence X exceeds bound Y") so the buyer sees a count rather than 22 individual rows. The grouping signature for VOCABULARY/AMBIGUITY preserves the offending canonical key.

The strategic message: **the calibration shipped, the engine can consume it, AND the system told you exactly what it was unsure about**. Enterprise legal teams care about the second clause more than the first.

## Latency profile

- Demo target SLA: **<90 seconds** end-to-end
- Reference run (qa_demo_e2e.py, flag-OFF, 5 sample artifacts ~30KB total): **~76s elapsed, 9/11 fields validated, 4 segments, 20 verbatims, 22 flagged-for-review items**
- Retrieval-enabled path (flag-ON, ChromaDB indexing + per-extractor queries): 2–4× slower than truncation path on small corpora; bounded by Gemini embeddings throughput on larger corpora
- F100 stress test (Pfizer corpus, ~1.6M chars post-HTML-strip, 4 SEC filings): 85s with retrieval ON, 10/11 fields validated, 3 segments. F100-scale latency is workable but not demo-cadence.

The latency budget breaks down approximately: ingestion + triage ~5s, parallel extraction ~30–45s (gated by slowest extractor), evidence merge <1s, synthesis ~15–25s (Flash/LOW), validation <2s.

## Output schema

`RadiantPersonaCalibration` (Pydantic, `schemas.py`):

- `schema_version`: `"1.1.0"` (literal)
- `project_id`, `target_audience_brief`
- `segments`: 1–8 `PersonaSegment` objects (Pydantic `min_length=1, max_length=8`). **Demo typically produces 4 segments** on the sustainable-packaging fixture set (Conviction Buyers / Pragmatic Parents / Skeptical Shoppers / Convenience Prioritizers). 4 is not a schema constraint — it's the typical shape of the demo corpus.
- `brand_constraints`: `BrandConstraint[]`
- `campaign_benchmarks`: `CampaignBenchmark[]`
- `global_provenance`: `SourceCitation[]`
- `coverage_gaps`: `string[]` — rule-violation descriptions
- `field_state_summary`: `Dict[str, FieldState]` — per-field lifecycle map for the frontend

Pydantic config: `extra="forbid"` at every API boundary — extra/hallucinated fields hard-fail validation. This is the "we never invent" guarantee in code.

The `PartialCalibrationResponse` envelope wraps the response when synthesis fails — the pipeline never crashes the request; it ships a `status="partial"` payload with the warning and the field_states so the buyer still sees the work done.

## 5-Pillar verdict

1. **Bottleneck Assassin** ✅ — directly compresses the pre-field manual data ingestion phase that gates Radiant's 24-hour bespoke promise
2. **Anti-Replication** ✅ at design time — strict upstream sidecar, no touch to the 2.5M-persona DB, simulation engine, Pulsar pipe, Reach, Mirror World, or the existing UI. (At-time-of-engagement Ego-Check verdict — see `outcome.md` for the load-bearing-assumption failure that surfaced post-demo)
3. **Native Environment** ✅ — designed as Step 2 inside `app.societies.io/radiant/new`, reusing the AS Tailwind tokens
4. **Magic Moment** ✅ — sub-90s drag-to-validated-JSON, with the Field-State Bar filling visibly during the run
5. **System Resilience** ✅ — deterministic Python rules engine as the only certification gate; LLM extractions go through Pydantic validation continuously; `coverage_gaps` + `requires_human_review` surface preserves the "we never invent" guarantee for enterprise legal review

## What was specific to AS vs reusable

**AS-specific:**
- The investor-shaped canonical vocabulary keys (`investor_type`, `holding_period`, `engagement_with_ir_communications`, `proxy_vote_participation_pct`, `earnings_call_attendance`) — tuned to AS's named F100 logo (Teneo) and the IR audience use case
- The `RadiantPersonaCalibration` schema shape — calibrated to what AS's audience-build path actually consumes
- The "Load into Simulation →" CTA copy — borrows AS's existing app vocabulary

**Reusable (pattern-level):**
- The Calibration Sidecar pattern itself (stateless upstream ingestion → parallel extractors → evidence merger → long-context synthesis → deterministic rules gate → schema-validated JSON handoff)
- The Field-State Engine pattern (per-field 4-state lifecycle, deterministic plan generation from a triage manifest)
- The Evidence Merger pattern (pure-Python aggregation between LLM stages to bound synthesis input)
- The family-scoped semantic validation pattern (Nia with one source per family; difflib fallback)
- The evidence-bounded confidence cap formula (`sqrt(n/10) + 0.1`)
- The SSE Pipeline Activity theatre + Field-State Bar + Flagged-for-Review surface as the dual-buyer demo trio (engine team sees the architecture proof; commercial team sees the JSON outcome and the audit trail)
- The Nia → ChromaDB retrieval-substrate migration learning — F100-scale corpora need local indexing, not vendor remote indexing

## Reusability for future prospects

This architecture is a reference pattern for any future prospect matching:

- A downstream simulation/decisioning/scoring engine that consumes structured input but whose customers arrive with unstructured heterogeneous research artifacts
- A schema-validated JSON handoff as the only contract between sidecar and engine (no engine modification, no shared state, no library coupling)
- Multi-source contradiction handling required (multiple research vendors disagreeing on the same field is the norm, not the exception)
- Enterprise legal review as a procurement gate (the "Flagged for Human Review" surface is what unblocks legal sign-off)
- A YC-or-similar-stage company chasing F100 logos via bespoke per-engagement work, where offloading the bespoke part lets the engine team stay focused on the moat

The corresponding pattern reference is `frameworks/architectural_patterns/calibration_sidecar.md`.
