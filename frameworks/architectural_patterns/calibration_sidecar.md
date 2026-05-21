---
type: architectural_pattern
pattern_name: calibration_sidecar
canonical_one_liner: "Stateless upstream sidecar that ingests a messy enterprise research bundle, runs specialist extraction agents in parallel against a Pydantic-anchored field-state plan, validates outputs through a deterministic rules engine plus a family-scoped semantic vocabulary check, and emits a schema-validated calibration JSON the target's automated engine can consume as-is — with a Deterministic Refusal Surface that renders what the system declined to assert as the load-bearing theatrical move."
status: v1.1
version_first_shipped: 2026-04
prior_worked_examples:
  - artificial_societies
last_updated: 2026-05-21
key_tags:
  - upstream_sidecar
  - user_uploaded_ingest
  - parallel_specialist_extractors
  - deterministic_refusal_surface
  - evidence_bounded_confidence
  - family_scoped_vocabulary
  - local_first_retrieval
  - structured_output_discipline
  - tolerant_coercion_at_boundary
signal_checklist:
  - target_has_automated_downstream_engine_with_unautomated_intake
  - customer_inputs_are_messy_heterogeneous_formats
  - hallucination_cost_is_brand_risk_not_nuisance
  - target_publicly_claims_automation_in_marketing
  - output_schema_is_the_handoff_contract
  - sub_90_second_magic_moment_is_achievable
  - native_environment_is_target_own_application
related_patterns:
  - safe_harbor_synthetic_data_fabric
  - voice_to_structured_action_pwa
  - multi_agent_parallel_triage
position_relative_to_target_core: upstream
llm_stack_default: vertex_ai_gemini
llm_stack_fallback: openai_structured_outputs_for_extraction_only
---

# Calibration Sidecar

*Stateless upstream sidecar that turns the customer's messy research warehouse into the structured calibration JSON the target's automated engine already accepts — with a Deterministic Refusal Surface that makes what the system declined to assert the most credible thing on screen.*

---

## 1. Pattern name + one-liner

**Calibration Sidecar.** The customer uploads heterogeneous research artifacts — segmentation studies, raw survey exports, qualitative transcripts, brand decks, CRM CSVs, occasionally regulatory filings — and the sidecar emits a single Pydantic-validated payload the target's engine can boot from in under 90 seconds. Specialist agents run in parallel against a field-state plan, the rules engine has hard authority over what counts as valid, and the synthesized output renders both the calibration and the refusals side by side.

---

## 2. When to reach for this pattern

All of the following should be true:

- The target ships an automated downstream engine (simulation, scoring, ranking) but the **intake to that engine is still hand-rolled** — solutions engineers translate customer artifacts into the engine's input contract over days or weeks.
- The customer's inputs arrive in **at least four distinct formats** (PDF, .sav, .csv/.xlsx, .docx, .qsf, images, plain text) and a single-LLM pass over the union of them either hallucinates or runs out of context.
- The cost of a hallucinated field is **brand risk for the customer**, not a nuisance — a wrong segment weight or a fabricated psychographic key would end the contract.
- The target **publicly markets the workflow as automated** in their landing-page or YC copy. (This is the pre-flight check that prevents the AS-class failure documented in §8.1.)
- The output is a **structured JSON contract** the engine already consumes from internal sources. The sidecar extends the engine's input surface upstream; it does not replace any internal pipeline.
- A **sub-90-second magic moment** is achievable on representative fixtures with parallel extraction and a deterministically-bounded synthesis step.
- The natural rendering surface is **the target's own application** — same nav, same tokens, same auth.

If even one of these is false, reach for a different pattern. The pattern's strength is concentrated in the combination; partial fit yields a worse demo than no demo.

---

## 3. The problem shape

The bottleneck this pattern attacks is **upstream of automation**, not inside it. The target has invested years in a downstream engine — a multi-agent simulator, a scoring model, a network propagation algorithm — and the engine is genuinely good at what it does. What the target has not built, and structurally cannot prioritize building, is the messy translation layer between *the customer's research warehouse* and *the engine's input contract*. That translation is consulting work today: a solutions engineer reads the customer's segmentation study, hand-extracts the segments, normalizes demographics, picks representative verbatims, drops everything into a JSON form, and runs the engine. It takes days. It does not scale past the target's headcount. It is the reason a 30-second simulation has a 24-hour turnaround.

The shape of the messy input is the design constraint. A single SPSS file has variable labels and value labels embedded in metadata, not in the row data. A Qualtrics `.qsf` export is a JSON tree with question IDs that map to nothing else in the corpus. A focus-group transcript has segments named in paragraph form. A brand tracker CSV has demographic distributions in columns but uses non-canonical column names. A naive single-pass LLM solution either (a) burns tokens reconciling all of this in one context window and hallucinates when the contradictions get sharp, or (b) uses brittle format-specific scripts that break the moment a customer sends a real-world artifact.

The pattern's design wager is that **structured-output extraction agents, run in parallel against a field-state plan that knows what the schema still needs, can carve up the messy input by specialty** (segments here, verbatims there, demographics elsewhere, brand tone, campaign benchmarks), and that a deterministic rules engine plus a family-scoped vocabulary check can validate the outputs without giving the LLM authority to certify itself. The synthesis step does final reconciliation; the rules engine has terminal authority on what ships. Whatever the engine declined to assert is rendered as a first-class element of the output, not hidden in a confidence score.

---

## 4. Technical architecture

### Component graph

```
┌──────────────────────────────────────────────────────────────────────┐
│  CUSTOMER drag-drops heterogeneous artifacts                          │
│  (PDF, .sav, .csv, .xlsx, .docx, .qsf, .txt, images, SEC filings)     │
└──────────────────────────────┬───────────────────────────────────────┘
                               │ multipart/form-data
                               ▼
┌──────────────────────────────────────────────────────────────────────┐
│  STATELESS CONTAINERIZED SIDECAR (FastAPI on Cloud Run)              │
│                                                                       │
│  [1] INGESTION — format detection + per-format parsers                │
│      Pure Python except for vision-fallback on scanned PDFs/images.   │
│      Output: List[SourceArtifact] with raw_text + tables + metadata.  │
│                                                                       │
│  [2] TRIAGE — single Flash-Lite call classifies each artifact         │
│      into one of N canonical artifact types. Filename heuristic       │
│      fallback when client is unavailable (regulatory filings have     │
│      stable filename patterns; rely on those before LLM disambig).    │
│                                                                       │
│  [2b] FIELD-STATE ENGINE — pure Python, no LLM                        │
│      Walks the schema, registers every leaf field as UNKNOWN.         │
│      Maps triage manifest to per-agent extraction assignments.        │
│      Each artifact_type routes to a specific set of extractors.       │
│      This is the routing gate; getting it wrong silently drops        │
│      whole extraction paths (see anti-pattern §8.8).                  │
│                                                                       │
│  [3] PARALLEL EXTRACTION (asyncio.gather)                             │
│      Six specialist agents, all on Flash with thinking_level=LOW,     │
│      all using Pydantic response_schema for native structured output: │
│        segment_extractor                                              │
│        verbatim_distiller                                             │
│        demographic_normalizer  (Flash-Lite, thinking=MINIMAL)         │
│        behavioral_extractor                                           │
│        brand_tone_extractor                                           │
│        campaign_benchmark_extractor                                   │
│                                                                       │
│      Each agent ONLY processes the artifacts the field-state plan    │
│      assigned to it. No agent sees the full corpus. Continuous        │
│      Pydantic validation fires immediately on the agent's output;    │
│      failures don't crash the pipeline — they set validation_passed  │
│      = false and the agent returns an empty ExtractionResult.        │
│                                                                       │
│  [3a] RETRIEVAL LAYER (optional, flag-gated)                          │
│      Local-first vector store, Gemini text-embedding-001 embeddings.  │
│      Per-engagement collection indexed at pipeline start, torn down   │
│      at end. Three agents (segment, verbatim, demographic) can query  │
│      it for focused chunks instead of dumping raw_text[:50000].       │
│      Falls back transparently to truncation if retrieval is disabled  │
│      or the indexing call fails. See §8.6 for the migration scar      │
│      that drove this layer local instead of vendor-hosted.            │
│                                                                       │
│  [4] EVIDENCE MERGER — pure Python, no LLM                            │
│      Deduplicates citations by (artifact_id, locator). Detects        │
│      contradictions across agents for the same field. Ranks nodes     │
│      by source authority (primary_research > secondary > inferred).   │
│      Reduces token load on the synthesis step by ~40% on              │
│      representative fixtures.                                         │
│                                                                       │
│  [5] SYNTHESIS — Gemini Pro with thinking_level=HIGH, location=global │
│      Consumes the merged evidence graph (not the raw artifacts).      │
│      Resolves contradictions. Emits draft calibration with citations  │
│      back to source artifacts. Pydantic-validates at the boundary.    │
│      (See §8.7: demo configurations may run this on Flash+LOW for     │
│      latency reasons; the pattern remains Pro+HIGH.)                  │
│                                                                       │
│  [6] DETERMINISTIC VALIDATION — rules engine, pure Python             │
│      Eight rules (segment weights sum to 1.0, every attribute has     │
│      ≥1 citation, ≥5 verbatims per segment, family-scoped canonical   │
│      vocabulary check, evidence-bounded confidence ceiling, no PII    │
│      in verbatims, provenance completeness, brand constraints have    │
│      examples). LLM never self-certifies. Rules engine is the gate.   │
│                                                                       │
│  [7] OUTPUT + REFUSAL SURFACE                                         │
│      Schema-validated calibration JSON + coverage_gaps[] + per-field  │
│      validation states + segments flagged requires_human_review.      │
│      SSE event stream renders the pipeline in real-time.              │
└──────────────────────────────────────────────────────────────────────┘
                               │ HTTP 200
                               ▼
┌──────────────────────────────────────────────────────────────────────┐
│  TARGET'S AUTOMATED ENGINE — boots from the calibration JSON,        │
│  runs whatever it was already going to run. Sidecar never touches    │
│  the engine, the engine's data, or the engine's UI.                  │
└──────────────────────────────────────────────────────────────────────┘
```

### Component routing table

| Component | Model | Thinking | Why |
|---|---|---|---|
| Triage | Flash-Lite | MINIMAL | N-class classification; sub-second; cheapest tokens; filename fallback for offline mode |
| Segment extractor | Flash | LOW | Structured extraction with citations; not reasoning-heavy |
| Verbatim distiller | Flash | LOW | Quote selection + sentiment tagging |
| Demographic normalizer | Flash-Lite | MINIMAL | Pure mechanical field-name mapping |
| Behavioral extractor | Flash | LOW | Constrained to canonical vocabulary in prompt |
| Brand tone extractor | Flash | LOW | Constraint-type classification with examples |
| Campaign benchmark extractor | Flash | LOW | Numeric metric extraction |
| Synthesis | **Pro** | **HIGH** | Long-context reconciliation, contradiction resolution, evidence ranking. See §8.7 for the demo-cadence override that runs this on Flash+LOW when latency budget is tight. |
| Targeted retry | Pro | MEDIUM | Narrow field-level rewrites against specific rule violations |

### Sub-pattern: the Validator/Retrieval Split

**The rule:** semantic validation and corpus retrieval are *different problems with different scale curves*. Combining them under one vendor creates a hidden coupling that breaks under load.

The pattern keeps them split. **Semantic validation** runs against a small, stable, pre-indexed canonical vocabulary (~67 keys across five families, ~350 aliases total). This is a *tiny* corpus, indexed once, queried thousands of times. A hosted semantic-search vendor with a good reranker is the right tool here — the corpus fits inside whatever inline-payload ceiling the vendor enforces, and the reranker quality is what you're paying for. **Corpus retrieval** runs against the *current engagement's* uploaded artifacts. This is potentially millions of characters, indexed once per engagement, queried six to eight times per pipeline run. Hosted vendors hit throughput ceilings on F100-scale corpora that you can't audit from outside (see §8.6). Run this layer local: vector store on disk, Gemini embeddings, your own infrastructure.

The pattern documents both layers independently. The default canonical retrieval is **local vector store with Gemini embeddings** — vendor-agnostic, no hidden throughput ceiling. ChromaDB is the reference implementation for the prior worked example; any local vector store works.

### Sub-pattern: Tolerant Coercion at the Lowest Boundary

**The rule:** strict-everywhere Pydantic is correct at five out of six LLM boundaries, and the sixth is the boundary where the LLM's field-naming jitter actually lives. Make that *one* type tolerant — with a coercion helper sitting beside it — and keep every higher-level type strict.

In practice the jitter consolidates at the citation layer. Six extraction agents emit citation dicts; their internal "locator" field comes back as `locator`, `page_locator`, `page`, `row`, `section_id`, `section`, `cell`, `question_id`, depending on artifact type and the agent's prompt. Forcing every agent to emit the same key via prompt discipline alone is a tax that compounds: every prompt gets longer, every agent gets brittler, and one prompt drift breaks the boundary.

The pattern resolves this by configuring `SourceCitation` with `extra="ignore"` and a `populate_by_name=True` validator, paired with a `coerce_citations()` helper that walks the alias list and synthesizes a fallback citation tied to each assigned artifact when the model omits the field entirely. Every type *above* `SourceCitation` — `BehavioralAttribute`, `PersonaSegment`, `RadiantPersonaCalibration` — stays `extra="forbid"`. The strictness budget concentrates where it matters; the brittleness budget concentrates where the LLM's noise actually lives.

This is a positive design decision, not a workaround. The rule generalizes: when prompt-discipline-alone is the wrong tool for one specific kind of jitter, name the boundary where the jitter lives and isolate the tolerance there.

### Sub-pattern: Family-Scoped Vocabulary Check

The canonical vocabulary is partitioned by family (demographic, psychographic, behavioral, brand_constraint, campaign_benchmark). The validator never cross-validates: a demographic key like `age_range` is invalid as a behavioral attribute, even though the lexical similarity is high. The vendor reranker is scoped to one family's source corpus per call. Wrong-family matches fail closed.

### Sub-pattern: Evidence-Bounded Confidence Ceiling

The rules engine enforces `confidence ≤ sqrt(n_citations / 10) + 0.1`. A single-source attribute caps at ~0.42, regardless of what the LLM claimed. Two sources caps at ~0.55, four sources at ~0.73, ten or more at 1.0. The synthesis agent cannot override this — the rule fires after synthesis, on the synthesized object, and any field exceeding the bound becomes a coverage gap.

### LLM stack discipline

The canonical pattern routes every agent through the Google ecosystem via Vertex AI Gemini. Single-vendor discipline matters here because the synthesis step relies on long-context reasoning that varies meaningfully across providers. Documented fallback: `openai_structured_outputs_for_extraction_only` (Flash-class extraction agents only, never synthesis or rules retry), enabled only if Vertex is unavailable mid-engagement. The validator and retrieval layers stay on Google embeddings regardless.

---

## 5. Anti-replication boundary

The sidecar does not touch:

- The target's automated engine (simulator, scorer, ranker, network propagator).
- The target's persona database, scoring database, or any internal source-of-truth.
- The target's rendering UI for engine outputs.
- The target's authentication, billing, or tenant-scoping layer.
- The target's product roadmap. The sidecar fills an *intake gap* that the target has chosen not to staff. It does not compete for the target engineer's time.

The defensive-line quote, usable verbatim when the target's CTO challenges the boundary: *"We don't run your simulation. We just turn the customer's research warehouse into the JSON your engine already accepts. The handshake is one HTTP call. If you decide to build the same intake internally in six months, you sever the API and the sidecar disappears — no residual debt in your repo."*

The structural test is the **unplug guarantee**: removing the sidecar's API endpoint should leave the target's engine running on its existing inputs with zero downstream breakage. If unplugging it breaks anything in the target's main path, the pattern was implemented wrong.

---

## 6. Theatre approach

The pattern's load-bearing theatrical innovation is the **Deterministic Refusal Surface**: the synthesized calibration is rendered side-by-side with everything the system declined to assert, and the refusal is framed as the most credible artifact on screen.

### Magic moment timing anchors

On a representative 5-artifact fixture (segmentation study, survey responses, transcripts, brand brief, screener):

- **T+0** — Drop zone glows, files chip in.
- **T+1.5s** — Triage events stream: "Classified `[filename]` as `[artifact_type]`" per file.
- **T+3** — Field-state engine reports total fields tracked and zero populated.
- **T+5–12** — Six extraction agents fire in parallel; each emits an event when it starts and when it finishes.
- **T+13** — Evidence merger event: dedup stats + field-state transition (unknown → candidate).
- **T+15** — Synthesis starts. (Demo configurations may finish here in ~50s; production Pro+HIGH may extend to ~80s. See §8.7.)
- **T+65–85** — Synthesis returns; rules engine runs; refusals appended.
- **T+90 max** — Calibration ready. "Load into Simulation →" button illuminates.

The 90-second ceiling is the demo gate. Anything above it loses the room.

### Three rendering surfaces

| Surface | Purpose | Viewer |
|---|---|---|
| **Drop zone + file chips** | Captures the operator gesture; one tap from "I have research" to "system is working." | Operator (the customer's research lead) |
| **Pipeline activity stream** | SSE-streamed proof-of-work. Every event is a real Gemini call or Python validation. Expandable to show model, token counts, latency. | Technical buyer (CTO, lead engineer) |
| **Synthesized calibration + Flagged for Human Review** | The calibration on top; the Deterministic Refusal Surface below, with refusals categorized (vocabulary, ambiguity, evidence, missing data) and grouped where the same root cause repeats. | Commercial buyer (CPO, head of product) |

The third surface is the pattern's distinctive move. Most demos hide refusals in error states or low confidence scores. This pattern renders them prominently with operator-grade copy: *"This calibration declined to assert claims it could not verify. Each item below was caught by the deterministic validation layer."* When ten evidence-bound violations fire on the same calibration, they collapse to a single grouped line — "10 attributes claim confidence above their evidence bound (single-source ceiling 0.42)" — with the operator-grade subtitle explaining the mechanic. The buyer who reads this comes away believing the system is more trustworthy than the buyer who saw zero refusals.

### Theatre-side decisions worth canonicalizing

- **Refusal grouping over refusal listing.** Don't show 22 raw violations. Group by category, collapse duplicates, surface the count.
- **Operator-grade copy on the refusal panel.** No technical jargon, no Pydantic error strings. Translate.
- **The refusal panel is below the calibration, not in a tab.** Tab-hiding the refusals defeats the entire move.
- **One coral accent line per refusal**, matching the brand. The visual budget says "this matters" without screaming.

---

## 7. Prior worked examples

**Artificial Societies / RCS (Radiant Calibration Sidecar), Q2 2026.** Target: AS, YC W25, $5.35M seed (Point72-led), London/SF. The sidecar ingested research bundles (segmentation studies, surveys, transcripts, briefs, optionally regulatory filings) and emitted Radiant-ready calibration JSON in under 90 seconds. Validator layer used Nia against the canonical_vocabulary.json. Retrieval layer migrated to ChromaDB after the Unilever 20-F stress test surfaced a vendor-side indexing ceiling (see §8.6).

Engagement outcome: relationship win, deal loss. Patrick Sharpe (CPO) engaged, replied with structured feedback ("smart insight, not exactly the way our system works but it's the right idea"), and opened a long-term door. The deal lost on a load-bearing assumption miss documented in §8.2 — the demo's framing implied a bottleneck on AS's side that contradicted AS's own public marketing.

Cross-references: `prospects/artificial_societies/overview.md`, `prospects/artificial_societies/outcome.md`; full `prospects/artificial_societies/architecture.md` pending backfill — see overview.md and outcome.md for engagement context.

---

## 8. Anti-patterns and failure modes

### 8.1 Public-Marketing Contradiction

**The scar.** The pitch claimed the target's own workflow contained a manual bottleneck. The target's public marketing copy explicitly claimed that workflow was automated. The CPO had personally signed off on the public copy. The contradiction surfaced on first contact, the pitch reframed under pressure, and the deal was lost cleanly.

**The rule.** Before any pitch with a falsifiable claim about the target's internal workflow, **read every public surface the target controls** (landing page, YC application, podcast appearances, LinkedIn posts by the founding team) and confirm the claim doesn't contradict it. If it contradicts, find a different framing — the bottleneck is almost always one step *outside* the target's automated surface (in the customer's data preparation, in the format-translation layer, in the integration to a downstream system), not inside it.

**Why this is the load-bearing lesson.** It generalized into Pipeline Step 1F (implicit assumption validation gate) and Step 1F-red (red-team with go/no-go authority). This is the most expensive lesson in the substrate. Do not soften this entry.

### 8.2 Unverified Buyer Bottleneck Assumption

**The scar.** The architecture solved a bottleneck the prospect didn't actually have. The bottleneck was inferred from public materials, never validated with a direct contact, and the demo's first ninety seconds asserted the bottleneck as if it were established fact.

**The rule.** Every architecture pitch contains at least one load-bearing assumption about the target's internal workflow. Name it explicitly during planning. Verify it through at least one of: a direct conversation with someone at the target, a granular technical post the target wrote (engineering blog, careers page job spec), or a credible analyst report. Inferring from marketing copy alone is not validation — see §8.1.

### 8.3 Magic Moment Without a Refusal Surface

**The scar.** Early iterations rendered the synthesized calibration prominently and buried refusals in a developer-facing JSON field. Demo viewers walked away believing the system was confident about claims it had explicitly declined to make. When pressed, the operator had to manually surface the refusals — which both broke flow and lost the credibility move.

**The rule.** The Deterministic Refusal Surface is not optional. Every calibration sidecar demo must render the refusals as a first-class, prominently-placed element, with operator-grade copy explaining what was declined and why. If §6's third surface is missing or hidden, the pattern was implemented wrong.

### 8.4 Family-Scoped Validation as Afterthought

**The scar.** A first-pass implementation used a single global canonical vocabulary across all attribute families. The validator passed `age_range` as a behavioral attribute because lexical similarity was high; the rules engine never caught it because the rule was scoped globally too. Synthesis emitted a behavioral attribute that was structurally a demographic. The customer's downstream engine choked.

**The rule.** Family scoping is a foundational design constraint, not a tuning parameter. The canonical vocabulary is partitioned at index time. The validator queries one family's corpus per call. The rules engine asserts family scoping on every attribute. Cross-family matches fail closed by construction. If the validator is making this decision at runtime, the architecture is wrong.

### 8.5 Strict-Everywhere Pydantic at the LLM Boundary

**The scar.** Pre-coercion, six extraction agents emitted citation dicts with seven different locator field names. Strict `extra="forbid"` on `SourceCitation` caused 30–50% of citations to fail validation, which cascaded — segments lost their citation chain, the `every_attribute_has_citation` rule fired catastrophically, synthesis got a malformed evidence graph and produced fabricated segments to fill the gap.

**The rule.** Make the `SourceCitation` boundary tolerant with `extra="ignore"`, pair it with a coercion helper that handles the locator alias set (`locator`, `page_locator`, `page`, `row`, `section_id`, `section`, `cell`, `question_id`), and provide a fallback synthetic citation per artifact when the model omits the field entirely. Keep every type *above* `SourceCitation` strict. See §4 sub-pattern. The lesson generalizes: when prompt-discipline-alone is the wrong tool for one specific kind of jitter, isolate the tolerance at the lowest boundary where the jitter lives.

### 8.6 Vendor Indexing Throughput as Hidden Ceiling

**The scar.** The first retrieval layer used a hosted vector-search vendor (Nia) for both the validator vocabulary and the per-engagement corpus. The vocabulary corpus (~350 strings) worked fine — that's what the vendor's reranker was good at. The engagement corpus on representative fixtures (≤5MB) also worked. The F100 stress test on the Unilever 20-F (14M characters, ~14MB) triggered an `index_error` during upload because the vendor's inline payload exceeded a server-side filter. A chunked-upload fix attempt succeeded at the upload layer — the chunked POST returned HTTP 200 in ~53s — but the *vendor's downstream indexer* failed to flip the source to `indexed` within 15 minutes. The chunked-upload fix was reverted; the migration went local.

**The rule.** Any hosted vector store has at least three throughput ceilings you can't audit from outside: inline payload limit on upload, indexer throughput on the vendor's side after upload succeeds, query-time fan-out limit. The first one is visible; the other two are not. If the target's F100-scale corpora are non-negotiable, run the retrieval layer local from day one: PersistentClient vector store, Gemini embeddings, your own disk. The validator vocabulary stays hosted (small, stable, reranker quality is the value). The retrieval layer goes local (large, per-engagement, throughput is the value). See §4's Validator/Retrieval Split sub-pattern.

**The deeper rule.** Splitting "semantic search" into two problems with two different scale curves and two different infrastructure choices is the architectural move, not a workaround. The pattern documents this split as canonical, not as a scar.

### 8.7 The Demo-Cadence Override

**The scar.** The pattern's canonical synthesis step is Gemini Pro with thinking_level=HIGH, with a two-attempt targeted retry loop on Pro+MEDIUM for rule violations. Production discipline says: Pro+HIGH for synthesis, retry where rules fail, ship coverage gaps for what doesn't repair. Demo discipline says: 90-second ceiling, no exceptions. On the AS / RCS engagement, the demo configuration ran synthesis on Flash+LOW and disabled the retry loop entirely (`retry_budget = 0`). The demo shipped under the ceiling. The quality discount was real but uncalibrated.

**The rule.** Two parts.

*First:* the pattern's canonical synthesis is Pro+HIGH with retry. Production engagements ship this. The retry loop is the pressure-release valve that prevents one rule violation from cascading; disabling it is not a generalizable choice.

*Second:* demo configurations have permission to deviate from canonical for latency reasons, but the deviation must be **named, time-bounded, and documented in the prospect's architecture file**. Synthesis on Flash+LOW with retry disabled is a known and documented quality discount, taken knowingly, reversible within one engagement. It is not a permanent architectural choice. The rules engine still has terminal authority; what changes is the model that produces the draft the rules engine validates.

The deeper lesson: when production discipline and demo cadence conflict, name the conflict. The opposite anti-pattern — silently letting demo configurations contaminate production canonical — is more expensive than the visible compromise.

### 8.8 Type-Registry Routing Gap

**The scar.** A new prospect uploaded SEC filings (10-K, 10-Q, 8-K, DEF14A) for an investor-relations calibration. Triage classified them as `OTHER` because the `ArtifactType` enum had no entry for regulatory filings. The field-state planner routed `OTHER` to only two extractors (behavioral and brand_tone). Three of the six extractors (segment, verbatim, demographic) never ran. The dry-run produced 2/11 fields validated despite 1.6M characters of substantive material in the corpus. The retrieval layer was healthy; ChromaDB indexed and queried correctly. The pipeline silently dropped the work because the routing gate at step 2b had nowhere to send the artifacts.

**The rule.** The extraction plan derives from `artifact_type`. If the enum doesn't cover what the corpus actually contains, the planner silently routes the artifact to a subset of extractors and entire extraction paths fall off a cliff with no error. Triage's classifier is only as good as the enum it can pick from. When entering a new vertical or a new artifact category, audit the type registry first: does an existing enum value capture this artifact, and if not, does adding the value require routing it into more extractor buckets than the default `OTHER` fallback does? The fix on RCS added a `REGULATORY_FILING` enum value, a filename-regex triage heuristic for SEC form names, and routed the new type into segment/verbatim/demographic extractors alongside the existing behavioral/brand_tone routing. Field validation jumped from 2/11 to 10/11 on the same corpus.

**The structural test.** Before any engagement on a new artifact class, run the triage agent on the prospect's corpus, inspect the resulting manifest, and confirm the field-state plan routes those artifacts to the extractors that should see them. If the planner is dropping artifacts into a sparse extractor set, fix the enum before running the rest of the pipeline.

---

## 9. When NOT to reach for this pattern

- **The target's downstream engine itself is the bottleneck.** If the engine is slow, the calibration upstream of it doesn't change anything. The customer's experience is still slow. Wrong pattern; look at the downstream-sidecar family instead.
- **The customer's inputs are uniform.** If every customer sends the same one file in the same format, the parallel-extraction architecture is overkill. A single-pass extraction agent with a tight Pydantic schema is the correct shape.
- **Hallucination is cheap.** If wrong outputs are nuisance-grade rather than brand-risk-grade — internal dashboards, exploratory tools, things a human will review before any external use — the deterministic rules engine is overhead the demo doesn't earn from. Simpler patterns work.
- **The target's intake is already API-callable from the customer's source systems.** If the customer can pipe their CRM directly into the engine without translation, there's no gap for the sidecar to fill. Look for the gap one layer further out (compliance, scoring, reporting).
- **The engine is itself the pitch.** If the target's narrative is *"our engine is the differentiator,"* upstream sidecars compete for that narrative oxygen. Pick a pattern that lives downstream and amplifies the engine instead.
- **No sub-90-second magic moment is feasible.** If the corpus is so large or the validation so deep that the demo can't finish in 90 seconds even with parallel extraction, the pattern's theatre breaks. Either compress the demo to a single artifact type (lose the multi-format story) or reach for a different pattern entirely.

---

## Cross-references

**Prior worked examples:**
- `prospects/artificial_societies/overview.md` — AS company snapshot, founder dynamics (James He CEO, Patrick Sharpe CPO, Tom Whittle CTO), Radiant product, the automated-calibration public-marketing positioning.
- `prospects/artificial_societies/architecture.md` — RCS architecture as it actually shipped, including the Nia validator + ChromaDB retrieval split and the demo-cadence overrides on synthesis. **Pending backfill** — see overview.md and outcome.md for engagement context.
- `prospects/artificial_societies/outcome.md` — engagement outcome (relationship win, deal loss), Vidyard analytics (~6% completion from a UK-based view), the load-bearing-assumption miss that drove Pipeline Steps 1F and 1F-red into existence.

**Adjacent patterns:**
- `frameworks/architectural_patterns/safe_harbor_synthetic_data_fabric.md` — the sensitive-data cousin; same Pydantic structured-output discipline, same upstream position, different theatre (Two-Pass BS Plug rather than Deterministic Refusal Surface).
- `frameworks/architectural_patterns/voice_to_structured_action_pwa.md` — the real-time mobile cousin; canonicalizes Sentient UI as its load-bearing theatrical innovation, structurally parallel to this pattern's Deterministic Refusal Surface.
- `frameworks/architectural_patterns/multi_agent_parallel_triage.md` — the downstream cousin; same parallel-specialist-agent shape, different position relative to core (downstream not upstream), canonicalizes Dual-Audience Theatre as its load-bearing surface.

**Pipeline templates referenced:**
- Step 1F (implicit assumption validation gate) — non-negotiable post-AS lesson.
- Step 1F-red (red-team with go/no-go authority) — has veto authority; sunk cost on prior steps is not a reason to ship a failing premise.
- Demo recording playbook — applies to the three-surface theatre in §6.

**Identity doc references:**
- 5-Pillar Standard: this pattern hits all five. Bottleneck Assassin = the customer's hand-rolled translation layer. Anti-Replication = strictly upstream of the engine. Native Environment = inside the target's own application. Magic Moment = sub-90s drop-to-calibration. System Resilience = deterministic rules engine has terminal authority over LLM output.
- Tech Stack Mandate: canonical Vertex AI Gemini, validator on hosted semantic search, retrieval local-first.
- DMZ Rule: the sidecar holds no engine state; statelessness is structural.

**Academic anchors:**
- Schema-optimized extraction with reflection-based guardrails (the PARSE pattern from EMNLP 2025 Industry Track) validates the continuous-validation + targeted-retry architecture.
- Native provider-level structured output (the JSONSchemaBench finding) validates the `response_schema=PydanticClass` discipline at every extraction agent.
- Deterministic compilation of multi-step LLM pipelines (PlanCompiler) validates the field-state-plan-before-execution gate.
