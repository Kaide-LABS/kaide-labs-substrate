---
type: navigation
prospect: ylookup
status: closed_lost
engagement_date: 2026-03-16
last_updated: 2026-06-02
key_tags:
  - fintech_ai
  - audit_tech
  - private_equity
  - unstructured_ingestion
  - document_to_schema
  - completed_demo
  - closed_lost
  - engagement_model_mismatch
  - haram_vertical_caveat
  - riba_entangled
  - pre_filter_engagement
---

# Ylookup — Substrate Navigation

Ylookup is a closed, lost prospect engagement. The **UDINA** demo (Unstructured Data Ingestion and Normalization Agent) was the asset built and pitched — a pre-processing sidecar converting messy financial PDFs into Ylookup-ready structured data. The pitch landed well enough to book a call with the CEO, but the engagement died on an **engagement-model mismatch**: Fraai preferred to hire the engineer outright rather than run an outsourced FDE engagement; a follow-up pilot-sprint proposal received no response. Demo quality was not the failure mode.

**HARAM-VERTICAL CAVEAT (load-bearing).** Ylookup serves private equity firms and Big 4 audit teams; its core workflows are NAV reconciliation for funds, leveraged-buyout due diligence, and Quality-of-Earnings analysis on debt-financed deals — structurally riba-entangled, arguably more directly than Tracelight. **Under current Kaide Labs sourcing rules this engagement would be killed at the riba filter at sourcing, before any intelligence, demo, or outreach work.** It is preserved here only because it predates the filter: the engagement is dated 2026-03-16, before the riba/haram-vertical sourcing filter was formalized (the filter was hardened by the Tracelight post-mortem). This is a pre-filter engagement, not a filter violation — the same posture as Tracelight. The architecture pattern is reference-grade; **the prospect is not**.

## Files in this folder

- `overview.md` — company snapshot, founder dynamics, the bottleneck addressed, anti-replication reasoning, the riba/sourcing caveat, and the outcome stub
- `architecture.md` — the UDINA four-agent pipeline in full, the four build phases, PRD-design-intent-vs-shipped-state audit, the OpenAI historical-stack-note, the "specific vs reusable" split
- `outcome.md` — engagement outcome (closed, lost on engagement-model mismatch) and generalizable learnings

## Pattern signals (when this prospect's shape is relevant)

> **Sourcing note on every signal below:** a prospect matching this shape would be **killed at sourcing under current rules** if its customer workflows are riba-entangled (PE/fund/LBO/QoE finance). These signals are preserved for the *architectural* lesson — the unstructured-ingestion sidecar — **NOT for prospect-matching**. Do not surface Ylookup as a positive comp for a live prospect.

- **Unstructured-input bottleneck upstream of a structured engine** — the pain is converting chaos (PDFs, scanned filings, CIMs) into engine-ready data, not the engine that consumes it
- **Domain-expert, non-technical founders with a "code gap"** — strong vertical positioning, explicit reliance on external engineering; high trust placed in demonstrated execution
- **Verticalized SaaS with a behavioral-augmentation thesis** — the product accelerates an existing workflow (Excel reconciliation) rather than replacing the user's tools
- **Engagement-model risk with talent-hungry founders** — a founder who values demonstrated solo execution AND has a code gap may want to *acquire* the engineer (FTE hire) rather than *rent* the FDE engagement; the same thing that makes the demo land can sink the deal model
- **Riba-entangled customer base** — PE/audit/fund-finance verticals; the kill-at-sourcing trigger

## Architecture pattern name

**"Unstructured-to-Structured Ingestion Sidecar"** — a stateless pre-processing pipeline that converts unstructured documents into schema-conformant, engine-ready structured data, with a confidence-scored human-in-the-loop gate, deployed strictly upstream of the customer's core engine. Defers all engine-side decisioning (reconciliation, analysis, routing) to the customer's product.

Cross-reference: `frameworks/architectural_patterns/unstructured_to_structured_ingestion_sidecar.md` *(forward-reference — file not yet created)*

## Cross-prospect retrieval anchors

- **Renlo** (`prospects/renlo/`) — sibling pattern: "Voice-to-Structured-Action PWA." Both are ingestion sidecars converting unstructured input (voice/field vs. document/PDF) into structured records upstream of the customer's engine, both defer all decisioning to the core product. UDINA is the document-ingestion variant of the same meta-pattern.
- **Tracelight** (`prospects/tracelight/`) — the closest posture comp: pre-filter riba-entangled engagement preserved for the architecture, killed-at-sourcing under current rules. See Tracelight for the riba-filter post-mortem that hardened the rule. Tracelight also established the "designed-but-not-built warrants a forward-reference file" treatment (Shield-Wall) applied here to QoE Anomaly Hunter and NAGATA.
- **Riba filter** — `frameworks/sourcing/riba_haram_vertical_filter.md` *(forward-reference if not already created by Tracelight)*
