---
type: navigation
prospect: tracelight
last_updated: 2026-05-21
status: lost
key_tags:
  - financial_modeling_saas
  - synthetic_data_fabric
  - schema_extraction
  - deterministic_validation
  - constraint_projection
  - lbo_archetype_engine
  - google_sheets_proof_surface
  - upstream_sidecar
  - completed_demo
  - haram_vertical_engagement_record
  - pre_riba_filter_engagement
---

# Tracelight — Substrate Navigation

Tracelight is a closed-loop prospect engagement with two load-bearing framing items. **First**, the engagement was lost (cold email + Vidyard demo, low completion rate, no response, no-follow-up discipline held — see `outcome.md`). **Second, and substrate-critical**: Tracelight's vertical (AI-powered financial modeling SaaS, serving private equity and management consultancy buyers) was flagged post-engagement as having haram-vertical entanglement risk that wasn't fully scoped at sourcing. Under current Kaide Labs sourcing rules, prospects matching this shape would be killed at the riba filter before any engagement work begins. The architectural artifacts are preserved here as a reference pattern record, **not** as a template for prospect-matching.

## Files in this folder

- `overview.md` — company snapshot (Tracelight product, the DAG-engine IP, founder team dynamics, the procurement-friction bottleneck, Anti-Replication reasoning at design time, the haram-vertical caveat that retroactively disqualified the engagement at sourcing)
- `architecture.md` — full technical breakdown of Safe-Harbor (the only sidecar actually shipped), including the schema extraction → scenario-aware generation → deterministic validation → two-pass BS projection → archetype conformance pipeline, PRD-vs-ship gap audit, theatre surfaces, and reusability separation
- `outcome.md` — engagement outcome and learnings (canonical, do not overwrite — records the no-follow-up discipline and the haram-vertical post-mortem that hardened the riba filter at sourcing)

## Pattern signals (when this prospect's shape is relevant)

**NOTE — important framing.** Under current Kaide Labs sourcing filters, any prospect matching the signals below would be killed at the riba filter at sourcing. These pattern signals are preserved for the **architectural lesson** they encode, not for prospect-matching. Future Claude Code retrieval should treat Tracelight as a reference for the Safe-Harbor Synthetic Data Fabric pattern only; surface this caveat in any answer that names Tracelight as precedent.

Future architectural-pattern lookups (not prospect-matching lookups) may legitimately retrieve Tracelight content when:

- **Enterprise SaaS with InfoSec-procurement friction blocking POCs** — buyer can't upload real data to evaluate the product; the cold-start problem dominates the sales cycle
- **Customer's core IP is a graph/DAG engine over structured data** — Tracelight's DAG-over-spreadsheet-formulas is structurally analogous to graph engines over any structured artifact (financial models, legal contracts, supply-chain BOMs, compliance reports). The sidecar pattern feeds the engine; never replaces it
- **Constraint-aware synthetic data generation** — output must satisfy deterministic invariants (algebraic identities, structural constraints, domain conventions) that stochastic generation can't reliably hit on its own. Pattern: generate-then-project-onto-constraint-surface
- **Domain-archetype-aware generation** — buyer expects synthetic output to conform not just to broad realism but to recognizable domain archetypes (LBO categories, deal types, segment profiles) that map to academic or industry-standard parameter ranges
- **Live-formula proof surface** — buyer's trust requires being able to inspect the verification logic, not just take a checkmark badge at face value. Pattern: embed validation as live formulas in the deliverable artifact, not as a separate "trust me" report

## Architecture pattern name

**"Safe-Harbor Synthetic Data Fabric"** — a multi-stage upstream sidecar that ingests an empty structured-artifact template (here: an xlsx financial model), extracts its semantic schema, generates domain-conformant synthetic data via a scenario-aware LLM with strict constraint prompting, validates against deterministic algebraic identities, projects any residual imbalance onto a constraint surface via a second-pass plug mechanism, scores the output against domain-archetype expectations (read-only), and hands the populated artifact to the customer's core engine. Theatre layer: live terminal feed during processing, then a live-formula proof surface (Google Sheets ✓ Validation tab with clickable formulas) instead of a marketing-style verdict badge.

Cross-reference: `frameworks/architectural_patterns/safe_harbor_synthetic_data_fabric.md` (forward-reference — pattern file to be authored separately; this prospect record is the first instance).

## Cross-prospect retrieval anchors

Tracelight-specific learnings that should surface in operational queries:

- `no_follow_up_discipline_preserves_optionality` — cold email + demo with no response converts to no signal; follow-up converts ambiguity to explicit no and damages re-engagement optionality. Cross-references: outreach discipline framework
- `low_demo_completion_signals_misfit` — Vidyard completion rate below ~25% suggests the demo content didn't hook in the first 60 seconds. The first 60 seconds of every demo recording is load-bearing
- `pre_riba_filter_engagement_record` — Tracelight is the historical engagement that hardened the riba/haram-vertical filter at sourcing. Any query about "have we built in financial services" should retrieve Tracelight AND surface the post-engagement filter that prevents the same engagement from happening today
- `safe_harbor_pattern_archetype` — the synthetic-data-fabric pattern itself, available for retrieval when an architectural lookup matches the signal shape above (NOT when a prospect-matching lookup is happening)
- `prd_vs_ship_gap_documentation_discipline` — Tracelight's architecture.md includes a load-bearing "PRD design intent vs shipped state" section. This discipline should be applied to all future prospect architecture.md files where the demo path differs from the PRD path
