---
type: prospect_architecture
prospect: ylookup
demo_name: udina
architecture_pattern: unstructured_to_structured_ingestion_sidecar
engagement_date: 2026-03-16
shipped_components:
  - extraction_agent_gemini_2_5_flash
  - semantic_gaap_mapping_gemini_3_flash_preview
  - confidence_audit_gpt_5_2
  - deterministic_nextjs_orchestrator
  - hitl_review_surface
  - in_memory_audit_trail
  - json_csv_export
designed_only:
  - qoe_anomaly_hunter
  - nagata_narrative_agent
historical_stack_note: openai_used_pre_gemini_only_mandate
last_updated: 2026-06-02
key_tags:
  - unstructured_ingestion
  - document_to_schema
  - gaap_normalization
  - confidence_scored_hitl
  - deterministic_orchestrator
  - prd_vs_shipped_audit
  - haram_vertical_caveat
---

# Ylookup — Architecture (UDINA)

## Sprint scope

CONTEXT.md proposed three adjacent workflows. **Only one was built.**

- **Built — UDINA** (Workflow 1, CONTEXT.md "Pre-Diligence Unstructured Data Ingestion and Normalization Agent"). The full four-phase build shipped (see PRD.md).
- **Designed, not coded — QoE Anomaly Hunter** (Workflow 2, CONTEXT.md "Autonomous Quality of Earnings (QoE) Anomaly Hunter"). A ReAct-style ledger-sweep agent proposing EBITDA add-backs into Ylookup's Review module. Specified in CONTEXT.md; no code in repo.
- **Designed, not coded — NAGATA** (Workflow 3, CONTEXT.md "Post-Reconciliation Narrative Generation and Audit Trail Agent"). An Evaluator-Optimizer report drafter with a Numerical Fidelity Auditor. Specified in CONTEXT.md; no code in repo.

QoE Anomaly Hunter and NAGATA are treated the way Tracelight's Shield-Wall was — documented as designed-not-built and warranting forward-reference files (see Forward-reference notes).

## Anti-Replication boundary

UDINA operates strictly upstream of Ylookup's engine. It does **not** touch the **Reconcile** engine (core IP), does not replace the **Import/Analyze/Reconcile/Review** four pillars as a product, and does not touch Ylookup's in-app reconciliation audit trails. UDINA converts unstructured PDFs into structured, engine-ready data and hands off. (See `overview.md` for the Analyze/Review cosmetic-adjacency tension flagged honestly.)

## The bottleneck addressed

Unstructured document chaos feeding the Import phase — analysts as expensive document scanners. UDINA autonomously converts 10-Ks, trial balances, debt schedules, and scanned filings into Ylookup-ready structured output.

## System architecture — the shipped pipeline

A four-stage pipeline orchestrated by deterministic Next.js API routes (no LLM in the orchestrator).

**Stage 1 — Extraction (Python/FastAPI backend).** `backend/extractor.py` + `backend/main.py`.
- Model string (exact, from code): **`gemini-2.5-flash`**, via the `google-genai` client.
- Approach: the **entire PDF** is sent in a single request as `types.Part.from_bytes(..., mime_type="application/pdf")` plus an extract-all prompt; Gemini returns `{document_type, tables[]}` as JSON. 3× retry with linear backoff; markdown-fence stripping; JSON parse with graceful failure.
- A `MOCK_EXTRACTION=true` mode returns a hardcoded income-statement fixture for demo reliability.
- Endpoint: **synchronous** `POST /extract-tables` + `GET /health`. CORS `*`.

**Stage 2 — Semantic GAAP mapping (Next.js API route).** `src/app/api/normalize/route.ts`.
- Model string (exact): **`gemini-3-flash-preview`**, via `@google/genai`, structured output through the SDK `Type` enum `responseSchema`, `temperature: 0.1`, 3× retry with 3s backoff.
- Approach: `cheerio` extracts unique header/row-label strings from all tables, sends them as **one batched call** (not per-table), maps each to a GAAP canonical term against `src/lib/gaap-ontology.ts` (20-term taxonomy across Income Statement / Balance Sheet / Cash Flow), then applies mappings locally with `cheerio`. Preserves sub-item granularity (e.g. "Net sales: Products" → "Revenue: Products").

**Stage 3 — Confidence audit (Next.js API route).** `src/app/api/audit/route.ts`.
- Model string (exact): **`gpt-5.2`**, via the OpenAI SDK, with forced function-calling (`tool_choice` on `score_cells`).
- Approach: `cheerio` parses the normalized table into structured cells; GPT is asked to return **only flagged cells** (confidence < 95) — a bandwidth optimization over scoring every cell — with a hardcoded rule to suppress dollar-sign false positives. Flagged cells get `data-*` attributes and inline color styling injected via `cheerio` for the heatmap; average confidence is computed deterministically.

**Stage 4 — Orchestrator (deterministic).** Next.js API routes route data between stages and manage pipeline state. No LLM. Matches PRD intent.

**HITL review surface.** `ConfidenceHeatmap.tsx` renders the audited HTML; clicking a flagged cell opens `ReviewPanel.tsx` (slide-in) with Accept / Reject / Edit + reviewer note. `PipelineProgress.tsx` shows the three-step status. Sanitization via `isomorphic-dompurify` throughout.

**Audit trail.** `src/lib/audit-trail.ts` — an in-memory, module-level array of `ReviewAction` records (timestamped). PRD acknowledges this is demo-grade; production would persist to a database.

**Export.** `src/app/api/export/route.ts` — JSON (Ylookup ingestion schema) and CSV (flat + appended audit trail), both `cheerio`-parsed from audited HTML, cross-referenced against the audit trail. No LLM.

**The four build phases (PRD.md):** Phase 1 extraction + UI; Phase 2 GAAP mapping; Phase 3 confidence scoring + HITL; Phase 4 export + pipeline polish. All four shipped.

## PRD design intent vs shipped state

Substrate-grade discipline: where the code diverges from PRD.md, recorded honestly.

- **Marker was never used.** PRD §2 declares "Marker (LOCKED IN)" — `datalab-to/marker` with `TableConverter`, Gemini-hybrid table extraction, built-in FastAPI server. The shipped backend uses **no Marker** (`requirements.txt`: `google-genai`, `PyMuPDF`, fastapi, uvicorn, python-multipart). Extraction is **raw Gemini 2.5 Flash full-PDF vision** in a single request. [VERIFY: the Marker→raw-Gemini pivot rationale is undocumented in the repo.]
- **Async polling is vestigial.** PRD §5 specs an async `/extract-tables` returning a `job_id` plus `/status/{job_id}` polling. The shipped backend is **synchronous** with no `/status` endpoint. The Next proxy `src/app/api/status/[jobId]/route.ts` and the polling logic in `ProcessingStatus.tsx` exist but are dead paths — `UploadZone.tsx` calls extract synchronously and waits.
- **Normalize model differs.** PRD specifies Gemini 2.5 Pro; shipped uses **`gemini-3-flash-preview`**. [VERIFY: intentional upgrade vs. availability-driven; rationale unstated.]
- **Audit model differs.** PRD specifies GPT-5-mini; shipped uses **`gpt-5.2`**. [VERIFY: rationale unstated.]
- **Audit optimized to flagged-only.** PRD §7 scores *every* cell with band-based CSS; shipped returns only flagged cells and injects inline styles. A defensible bandwidth/latency optimization, not a regression.
- **Auto-advance not built.** PRD §8.3 marks pipeline auto-advance as OPTIONAL ("watch the pipeline work"). `page.tsx` requires **manual** "Step 2: Normalize" and "Step 3: Audit" clicks. This partially undercuts the PRD's envisioned seamless-flow Magic Moment.
- **Next.js version.** PRD §3 states Next.js 16 (Turbopack); shipped is **Next 14.2.x** (lockfile 14.2.35).

**Load-bearing historical-stack-note — OpenAI / `gpt-5.2`.** Stage 3 routes a reasoning agent through OpenAI, which **violates the current Google-only LLM mandate.** This engagement (2026-03-16) **predates that mandate**, so it is recorded as a pre-mandate historical artifact, not airbrushed. A production rebuild would substitute a Gemini reasoning model (e.g. a Gemini 3 Pro-class model) for the confidence-audit stage to bring the stack fully inside the Google ecosystem. Documented honestly per substrate discipline.

## Magic Moment

Upload a messy financial PDF → watch it become Ylookup-ready structured JSON. PRD's timing target is **"< 60 seconds for a 10-page document"** (PRD §8.8 acceptance criteria and §10 verification checklist). This is an **aspirational target, not a measured benchmark** — the repo contains no timing instrumentation proving it, and the shipped pipeline is three sequential LLM round-trips (full-PDF extraction + normalize + audit), each with retries, gated by manual step clicks rather than auto-advance. Treat the <60s figure as a design goal pending measurement.

## 5-Pillar verdict

- **Bottleneck Assassin** — yes. Eliminates the analyst-as-document-scanner friction at the Import front door.
- **Anti-Replication** — yes at the extraction core; **flagged tension** at the normalization/HITL layers (cosmetic adjacency to Analyze/Review — see `overview.md`).
- **Native Environment** — partial. UDINA is a standalone web pipeline branded for Ylookup; it outputs to Ylookup's ingestion schema but does not embed inside the user's existing Excel/Ylookup surface. Weaker than Renlo's PWA-in-the-field.
- **Magic Moment** — present but timing is aspirational and auto-advance (the "watch it flow" effect) was not built.
- **System Resilience** — partial. Deterministic orchestrator and a deterministic dollar-sign suppression rule anchor the LLM stages; the confidence-audit stage still leans on an LLM (`gpt-5.2`) rather than a hardcoded rules engine for anomaly detection, so the determinism is thinner than the PRD's "hardcoded Python rules engines" ideal.

## What was specific to Ylookup vs reusable

**Ylookup-specific (do not generalize):**
- The 20-term **GAAP taxonomy** (`gaap-ontology.ts`) and sub-item-preservation mapping rules
- The financial-PDF document types (10-K, trial balance, debt schedule) and the income-statement mock fixture
- The exact three-model pipeline tuning and the riba-entangled customer framing

**Pattern-level reusables:**
- The **unstructured-to-structured ingestion sidecar** — convert unstructured documents into schema-conformant engine-ready data, strictly upstream, deferring all engine decisioning
- The **confidence-scored HITL review surface** — flag-only audit, click-to-review (Accept/Reject/Edit), timestamped audit trail; reusable wherever extraction needs a human gate
- The **deterministic-orchestrator-over-LLM-agents** discipline — LLMs do extraction/generation; routing, state, and (where possible) validation stay deterministic
- The **batched-single-call normalization** optimization (extract clean strings with cheerio, map all terms in one call) — cheaper, faster, lower-hallucination than per-table raw-HTML calls (origin: `Gemini_context.md` Phase 2 review)

## Forward-reference notes

Files that should exist later:
- `frameworks/architectural_patterns/unstructured_to_structured_ingestion_sidecar.md` — the pattern, with Renlo's "Voice-to-Structured-Action PWA" as the sibling voice/field variant
- `prospects/ylookup/qoe_anomaly_hunter_design.md` — Workflow 2 design notes (designed-not-built; Shield-Wall treatment)
- `prospects/ylookup/nagata_design.md` — Workflow 3 design notes (designed-not-built; Shield-Wall treatment)
- `frameworks/sourcing/riba_haram_vertical_filter.md` — the riba/haram-vertical sourcing filter (forward-reference if not already created by Tracelight)
