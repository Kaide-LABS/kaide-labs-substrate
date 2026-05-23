---
type: navigation
prospect: artificial_societies
last_updated: 2026-05-21
key_tags:
  - yc_w25
  - enterprise_audience_calibration
  - multi_agent_extraction
  - calibration_sidecar
  - cpo_engagement
  - relationship_win
  - deal_loss
  - post_engagement_pipeline_re_engineering
---

# Artificial Societies — Substrate Navigation

Artificial Societies is the single most load-bearing prospect in the substrate. The deal was lost (CPO Patrick Sharpe rejected the architecture in the post-demo conversation), the relationship was retained, and the post-mortem triggered the pipeline re-engineering that produced Step 0b (LinkedIn cleanup), Step 1F (implicit assumption validation gate), and Step 1F-red (red-team with go/no-go authority). Every future prospect's intel + architecture passes through gates that exist because of this engagement.

The RCS (Radiant Calibration Sidecar) was the artifact built and shipped — a stateless upstream sidecar that converts heterogeneous F100 research artifacts (PDF decks, SPSS .sav, .qsf, CSV, transcripts) into the schema-validated calibration JSON Radiant's simulation engine consumes. It is the reference implementation of the "Calibration Sidecar" pattern.

## Files in this folder

- `CLAUDE.md` — this file (navigation, pattern signals, cross-prospect retrieval anchors)
- `overview.md` — company snapshot, founder dynamics, the bottleneck identified, anti-replication reasoning at design time, the load-bearing assumption that failed
- `architecture.md` — full RCS architecture (6 parallel extractors, 8 deterministic rules, 5-family semantic validation, Nia + ChromaDB dual substrate, demo-cadence overrides, theatre layer)
- `outcome.md` — engagement outcome (deal loss, relationship retention, generalizable learnings) — *canonical, do not rewrite*
- `learnings.md` — pipeline learnings (origin of Steps 0b, 1F, 1F-red) — *canonical, do not rewrite*

## Pattern signals (when this prospect's shape is relevant)

Future prospects matching any of these signals should retrieve AS content during architecture generation:

- **Heterogeneous-artifact ingestion bottleneck** — prospect's core engine accepts a structured input but their customers arrive with unstructured/semi-structured research artifacts (PDFs, CSVs, SPSS exports, transcripts, brand decks). The wedge is upstream ingestion, not the engine.
- **Schema-validated downstream handoff** — the sidecar's only contract with the core engine is one validated JSON payload; ingestion failure must surface as structured "needs review" rather than corrupt the engine with hallucinated fields.
- **Multi-source contradiction surface** — multiple input documents will conflict (e.g., a Kantar tracker disagreeing with an Ipsos segmentation study), and the system must surface contradictions deterministically rather than silently picking one.
- **Long-context synthesis bounded by deterministic gates** — small fast models extract in parallel; one larger model synthesizes; deterministic Python rules cap confidence to evidence density and reject schema violations before the engine sees them.
- **YC-stage company chasing F100 logos** — the prospect is doing bespoke per-engagement work today (24hr human turnaround on each F100); the sidecar offloads the bespoke part so the engine team stays focused on core IP.

## Architecture pattern name

**"Calibration Sidecar"** — stateless upstream ingestion sidecar that converts heterogeneous source artifacts into a single schema-validated payload for a downstream simulation/decisioning engine. Parallel extraction agents per attribute family, deterministic evidence merger, long-context synthesizer, hardcoded rule engine as the only certification gate. Theatre layer streams SSE events plus a Field-State Engine showing per-field lifecycle (unknown → candidate → validated/blocked) so the buyer watches the schema fill in real time.

See `frameworks/architectural_patterns/calibration_sidecar.md` for the pattern reference.

## Cross-prospect retrieval anchors

The AS-specific learnings other prospects pattern-match against:

- **Load-bearing assumption verification before sprint commit** — every architectural proposal must surface its falsifiable claims and audit them against the customer's own public materials (Step 1F)
- **Marketing-page-contradicts-assumption pattern** — vendor marketing pages emphasize capability breadth while architectural proposals tend to assume capability narrowness; the contradiction is often visible on the homepage and must be read adversarially
- **Relationship-win vs deal-win distinction** — deal loss does not equal relationship loss; track `outcome` and `relationship_status` separately in frontmatter
- **Vendor-corpus retrieval throughput ceiling** — Nia's `/v2/sources` indexing pipeline did not flip a 14MB Unilever 20-F to `indexed` within 15 minutes; ChromaDB with local `PersistentClient` + Gemini embeddings sidesteps the bottleneck for F100-shape corpora (see `productization_v2.md` post-mortem)
- **Family-scoped semantic validation** — canonical-vocabulary validation must scope per attribute family (demographic / psychographic / behavioral / brand_constraint / campaign_benchmark) because the same key string (e.g., "age_range") is valid in one family and non-canonical in another
- **Evidence-bounded confidence cap** — confidence ≤ sqrt(n_citations/10) + 0.1, capping single-source attributes at ~0.42; prevents the LLM from claiming high confidence on thin evidence (see `rules_engine.confidence_monotonic_with_sources`)
- **Demo-cadence override discipline** — the PRD designed synthesis as Gemini Pro with HIGH thinking + 2 targeted retries; the demo ships Flash/LOW with retry_budget=0 to hold sub-90s SLA. Document the override; don't pretend the PRD shipped as written.
