---
type: prospect_architecture
prospect: tracelight
vertical: financial_modeling_saas
demo_name: safe_harbor_synthetic_financial_data_fabric
sprint_duration: 72_hours_plus_post_prd_extensions
last_updated: 2026-05-21
canonical: true
architecture_pattern: safe_harbor_synthetic_data_fabric
shipped_components:
  - safe_harbor_primary_sidecar
  - launcher_unified_landing
not_shipped_designed_only:
  - shield_wall_parallel_sidecar
key_tags:
  - synthetic_data_fabric
  - schema_extraction
  - constraint_aware_generation
  - deterministic_validation
  - two_pass_constraint_projection
  - fixed_point_iteration_lbo_solver
  - archetype_conformance_scoring
  - row_map_template_abstraction
  - google_sheets_proof_surface
  - upstream_sidecar
  - gemini_3_pro_synthesis
  - gemini_3_flash_schema
  - haram_vertical_engagement_record
---

# Tracelight — Architecture: Safe-Harbor Synthetic Financial Data Fabric

## Sprint scope

72-hour high-impact prototype solving Tracelight's enterprise procurement-friction bottleneck — the inability of PE firms and consultancies to upload live financial data during proof-of-concept evaluation. The shipped artifact populates an empty xlsx model template with mathematically-coherent synthetic data in 60-90 seconds, with all algebraic accounting identities preserved, zero sensitive data exposure, and a clickable live-formula proof surface inside a real Google Sheet.

**Two adjacent sidecars were proposed in the initial PRD:**

1. **Safe-Harbor** (primary, pre-core, upstream of the DAG engine) — **shipped as functional code**
2. **Shield-Wall** (parallel, internal ops vendor-questionnaire auto-populator) — **designed but never coded**

The launcher card for Shield-Wall renders explicitly as "COMING SOON" / "Architecture Designed" (`launcher/src/App.jsx`, lines 73-95). The docker-compose.yml declares the service, the CI/CD workflows reference it, the PHASE_2_SPEC.md describes it in full implementation detail, but no `shield-wall/` directory exists in the repo. **All architectural detail below refers to Safe-Harbor unless otherwise noted.** Shield-Wall design notes warrant a separate substrate file (see Forward-reference notes).

A third adjacent idea — an IC Memo Synthesizer — was killed at red-team because Tracelight had shipped spreadsheet-to-shareable-webpages two weeks before the analysis, making the synthesizer a direct core-IP replication risk.

## Anti-Replication boundary

The sidecar operates strictly **upstream** of Tracelight's DAG engine. The boundary is sharp by design:

**Specifically NOT touched:**

- The DAG engine (the parsed-formulas-as-graph IP — Tracelight's central IP)
- Plan Mode (Tracelight's collaborative planning surface)
- Spreadsheet Compare (version-diff feature)
- Change Reviews (AI-change validation surface)
- The native shareable-webpages output surface
- Chat-with-citation against the underlying Excel
- The Excel add-in itself

The sidecar is a separate web application. It accepts an empty xlsx template, returns a populated xlsx, and links the user to Tracelight's product as the next step. If Tracelight unplugs it tomorrow, their product still works with the original procurement-friction problem restored.

## The architectural thesis

Five sequential stages (with one parallel sub-pipeline) and a strict separation between stochastic generation and deterministic certification:

1. **Schema Extraction** — Gemini 3 Flash Preview parses the template structure into a typed `TemplateSchema` Pydantic model (model classification, industry inference, currency detection, inter-sheet references, column constraint inference). Single-source row resolution via `row_map.py` canonical-key abstraction (eliminates hardcoded row numbers; works across both verbose and compact LBO templates).

2. **Scenario-Aware Synthetic Generation** — Gemini 3.1 Pro Preview generates cell values in JSON, chunked at 25 cells per call, parallelized sheet-by-sheet, with archetype-specific constraint prompts (LBO general / distressed turnaround / high-growth tech / mature cash-cow). Income Statement and Debt Schedule generated first to establish prior values; remaining sheets generated in parallel using IS/DS context.

3. **Post-Processing Fixed-Point Solver** — Pure-Python Banach fixed-point iteration (`post_processor.py`) resolves the LBO interest-NI-repayment circular reference. Simulates the full IS → DS → CF → BS chain in memory; iterates senior and mezzanine repayments until convergence (typically <10 iterations, max 100). Writes only to designated input cells; never overwrites formulas. Sign-convention normalization (D&A positive, repayments positive) applied prior to iteration.

4. **Deterministic Validation** — `DeterministicValidator` (`validator.py`) — Pure Python, no LLM. Six algebraic rules with zero tolerance. Plug-account corrections on failure (Cash for BS imbalance, Other CF Items for CF mismatch, force-match for NI linkage, cap-at-ceiling for depreciation, repayment-adjustment for debt rollforward).

5. **Two-Pass BS Constraint Projection** — `bs_plug.py`, post-xlsx-write. Uses the `formulas` Python library to evaluate the actual xlsx workbook formulas (the truth, not a Python mirror), reads back computed Total Assets and Total L+E per period, computes the residual imbalance, and writes a corrective plug to "Other Long-Term Assets" (fallback: "Other Long-Term Liabilities" if Other LT Assets would go negative). Implements the Coletta et al. (2023) "constraint projection" pattern: generate first, project onto the constraint surface using the truth.

6. **Archetype Conformance Scoring** — `archetype_validator.py`, READ-ONLY. Extracts realized KPIs from the validated payload (entry/exit revenue growth, EBITDA margin trajectory, gross margin, leverage ratios, IRR, MOIC) and scores against locked parameter ranges for the selected archetype. Citations baked into the report (Guo et al. 2011, Axelson et al. 2013, Bain PE Report 2024, RL Hulett 2024). Never modifies cells; produces a graded conformance report (PASS / WARN / FAIL per metric) that surfaces in the audit trail and the Google Sheets ✓ Validation tab.

The discipline: every stochastic stage (schema extraction, generation) is bounded by a deterministic certification stage (validation, projection, conformance). The validator is the only thing that can certify output as shippable. If validation fails after `max_retries`, the pipeline raises rather than degrading silently.

## The six deterministic algebraic rules

Verified against `backend/agents/validator.py`. Each rule operates per-period and produces a `ValidationRuleResult`:

| Rule | Assertion | Failure response |
|---|---|---|
| `_rule_balance_sheet_identity` | Total Assets == Total Liabilities + Total Equity, tolerance 0.01 | Plug Cash by delta amount |
| `_rule_cash_flow_reconciliation` | Ending Cash == Beginning + Net Change in Cash | Plug Other CF Items, fallback force-Ending-Cash |
| `_rule_net_income_linkage` | P&L Net Income == CF Net Income (when both sheets contain NI cells) | Force CF NI to match P&L |
| `_rule_margin_bounds` | Gross margin [0,1]; EBITDA margin [-0.5, 0.8]; Net margin [-1, 0.5] | Signal regeneration of offending line item (no plug) |
| `_rule_depreciation_constraint` | Cumulative D&A ≤ Cumulative CapEx + Opening PP&E | Cap depreciation at ceiling |
| `_rule_debt_schedule_integrity` | Ending Debt == Beginning + Drawdowns − Repayments, per tranche | Adjust repayments to force identity |

Validation status is one of `PASSED`, `PASSED_WITH_PLUGS`, or `FAILED`. The orchestrator runs up to `max_retries` (default 3) generate-validate loops; on persistent failure it raises and the WSEvent surfaces as `error`.

## The model stack

Verified against `backend/config.py` and the agent files:

| Stage | Model | Provider | Role |
|---|---|---|---|
| Schema Extraction (primary) | `gemini-3-flash-preview` | Google Vertex AI | Sheet classification, column typing, inter-sheet ref detection, constraint inference |
| Schema Extraction (fallback) | `gpt-4o` | OpenAI | Activates only after 2 Gemini failures or malformed JSON |
| Synthetic Generation (primary) | `gemini-3.1-pro-preview` | Google Vertex AI | Scenario-aware cell value generation, JSON output, temperature 0.3, thinking budget 512 |
| Validation | Pure Python | n/a | Six algebraic rules; zero LLM |
| Post-Processor (circular ref) | Pure Python | n/a | Banach fixed-point iteration |
| BS Plug (two-pass) | Pure Python + `formulas` lib | n/a | Real formula evaluation, constraint projection |
| Archetype Conformance | Pure Python | n/a | KPI extraction, range comparison, scored report |

**Cost figures (per `cost_tracker.py` pricing table):**
- Gemini 3.1 Pro Preview: $2.00 / 1M input tokens, $12.00 / 1M output
- Gemini 3 Flash Preview: $0.15 / 1M input, $0.60 / 1M output
- GPT-4o (fallback only): $2.50 / 1M input, $10.00 / 1M output

Typical end-to-end generation cost for a full LBO template (283 input cells across 5 sheets): approximately $0.08 per run, accumulated across schema extraction, ~12 chunked generation calls, and conformance scoring.

## Theatre surfaces — what the prospect actually sees

The active demo flow (`frontend/src/App.jsx`):

1. **UploadZone** — drag-and-drop xlsx + a **scenario selector** (4 LBO archetypes: General / Distressed Turnaround / High-Growth Tech / Mature Cash-Cow, each with a short description of the parameter envelope). Sample template buttons (LBO enabled; DCF and 3-Statement gated as "COMING SOON"). Template preview opens the empty template as a real Google Sheet so the buyer can verify it's actually empty before submitting.

2. **SchemaTerminal** — dark-themed live terminal feed during processing. Renders the WebSocket event stream in order: parse → schema extract → generate → validate → conformance → write. Each event is a single line, prefixed with `[SCAN]`, `[MAP]`, `[LINK]`, `[TYPE]`, etc. Auto-scrolls. Blinking cursor for liveness. Filters out audit-trail-specific events (those surface in the AuditTrail panel, not the terminal).

3. **SpreadsheetViewer** (the actual proof surface) — on the `complete` WebSocket event, the SchemaTerminal component transitions inline to render `SpreadsheetViewer`. This component `POST`s to `/api/sheets/{job_id}`, the backend creates a real Google Sheet via the Sheets API, writes all generated data including formulas, **adds a separate "✓ Validation" tab with live formulas** proving every check (BS Identity per period, Margin Analysis with computed Gross/EBITDA/Net, Revenue Growth YoY, CF Reconciliation, Debt Schedule Rollforward per tranche, Cross-Sheet Linkage between IS and CF/DS, Statistical Distribution of revenue, Leverage Ratios, and the full Archetype Conformance scorecard). The Google Sheet is made publicly viewable and embedded in an iframe.

4. **The validation is clickable.** The buyer can click any cell in the ✓ Validation tab and see the actual live formula computing the check. This is the trust anchor that replaced the originally-designed VerdictBadge.

5. **AuditTrail** (collapsible panel) — displays phase timings, deduped audit log entries, plug adjustments (target cell, original value, adjusted value, delta, reason), model pipeline cost breakdown per agent, and generation metadata.

## PRD design intent vs shipped state

Substrate-grade discipline: the demo path diverges from the PRD path in several material ways. Documenting them honestly.

**1. Synthetic generation model.** PRD specified `GPT-4o` with Structured Outputs (`PRD.md`, `PHASE_1_SPEC.md`, `CONTEXT.md` all reference GPT-4o as the generator). Actual ship uses `gemini-3.1-pro-preview`. Why: Google released the gemini-3 preview series mid-build; the team migrated to align with the Google-only-stack mandate that crystallized post-PRD. Cost economics favor Gemini at the volume of chunked generation calls.

**2. Schema extraction model.** PRD specified `gemini-2.0-flash`. Actual ship uses `gemini-3-flash-preview`. Same migration rationale as above. Functional behavior is preserved.

**3. Shield-Wall sidecar — designed but not coded.** PHASE_2_SPEC.md is a complete implementation specification (multi-agent questionnaire-parser → telemetry-agent → policy-agent → synthesis-agent → drift-detector). The launcher displays a Shield-Wall card with the button text "Architecture Designed" and a "COMING SOON" badge (`launcher/src/App.jsx`). `docker-compose.yml` declares a `shield-wall-backend` service. `infra/cloudbuild-shield-wall.yaml` and CI/CD workflows reference it. **No `shield-wall/` directory exists in the repo.** Shield-Wall is a designed-but-not-built adjacent idea, surfaced verbally in the demo as a future capability.

**4. VerdictBadge is built but not in the active flow.** PRD's "Magic Moment" was a full-screen green-checkmark verdict badge. `frontend/src/components/VerdictBadge.jsx` exists and is functional (it shows validation proof rows, plug adjustments, model metadata, three CTAs). But App.jsx never transitions to a `VERDICT` phase in the shipped flow — SchemaTerminal transitions directly to SpreadsheetViewer on the `complete` event. The "verdict" lives inside the Google Sheet's ✓ Validation tab as live formulas. This is arguably a stronger theatre than the originally-designed badge (clickable formula evaluation beats a marketing-style "✓ PASSED" badge for the technical buyer), but it's an undocumented divergence from the PRD and the FOUNDERS_BRIEF's "Magic Moment" description. [VERIFY] — whether VerdictBadge can still be reached via any user action in the current code path is unclear; it may be dead code preserved for fallback.

**5. Magic Moment timing.** PRD and FOUNDERS_BRIEF claim "30-second magic moment." Actual generation timing for the LBO template (283 input cells, 5 sheets, ~12 chunks at 25 cells per chunk, phased generation with IS+DS first then parallel third phase) is closer to **60-90 seconds end-to-end** depending on Vertex AI latency. The backend `generation_timeout_s` is set to 600 seconds. The 30-second claim is aspirational PRD-marketing language; the actual 60-90s magic moment is still dramatic relative to the 3-month procurement-friction status quo.

**6. Scenario Engine — addition beyond the PRD.** The original PRD specified generic "realistic financial data" generation. The actual ship adds a **4-archetype scenario selector** (general / distressed_turnaround / high_growth_tech / mature_cashcow) with full constraint prompts citing academic LBO literature (Guo et al. 2011 *J. Finance*, Axelson et al. 2013 *J. Finance*, Bain PE Report 2024, RL Hulett Q3 2024). This was a post-PRD enhancement that substantially upgraded the demo's credibility to a PE buyer — synthetic data isn't just "realistic numbers," it's archetype-conformant numbers obeying recognizable industry conventions.

**7. Archetype Conformance Validator — addition beyond the PRD.** A second validator (`archetype_validator.py`) operates READ-ONLY after the algebraic validation passes. It scores realized KPIs (revenue growth, margin trajectory, leverage ratios, IRR, MOIC) against locked archetype ranges and surfaces a PASS/WARN/FAIL scorecard. This layer doesn't exist in the PRD and is a substantive uplift in demo credibility.

**8. Two-Pass BS Plug — addition beyond the PRD.** The original validation used only an in-memory Cash plug (`validator.py`). The actual ship adds a **second pass** (`bs_plug.py`) that runs after the xlsx is written: uses the `formulas` library to evaluate the workbook's actual formula DAG, reads back computed Total Assets and Total L+E per period from the truth (Excel-formula-evaluation), and writes a residual plug to Other LT Assets (or fallback to Other LT Liabilities). This implements Coletta et al. (2023) "constraint projection." It's a more rigorous correction mechanism than the PRD specified and meaningfully closes the gap between Python's mathematical mirror and Excel's actual formula evaluation.

**9. Pydantic `extra="forbid"` boundary discipline.** Tracelight's Pydantic models in `models/schemas.py` do **not** consistently use `extra="forbid"` at API boundaries. This is a discipline that crystallized in later Kaide sprints (notably the Matta PRD's 4-invariant standard). Tracelight's repo predates this discipline; the gap is acknowledged but was not retroactively fixed because the engagement closed.

**10. Continuous validation vs single-pass.** Tracelight uses single-pass Pydantic validation at major stage boundaries (TemplateSchema at extraction, SyntheticPayload at generation), then transitions to dict-based processing in the post-processor and bs_plug stages. This is less rigorous than continuous-validation-at-every-boundary discipline; the gap was not retroactively closed.

**11. Retry budget and error envelope.** Tracelight has `max_retries=3` for generate-validate loops but no explicit "partial success" envelope (no equivalent of a `PartialCalibrationResponse` typed status with bounded degradation). Failures cascade to `status="error"` with a WSEvent error and a job-level `error_message`. The pipeline is binary: either complete or error.

## End-to-end flow with magic moment

For an LBO template (the only template enabled in the active demo flow):

1. **t = 0s** — User uploads xlsx, selects scenario archetype (e.g., "Distressed Turnaround"), submits
2. **t = 0-3s** — Parse phase: openpyxl walks the workbook, identifies input cells vs formula cells, extracts inter-sheet refs, produces the parsed-template dict
3. **t = 3-15s** — Schema extraction: Gemini 3 Flash Preview classifies sheets in parallel (one call per sheet), produces the TemplateSchema. WebSocket events stream into SchemaTerminal as each sheet completes
4. **t = 15-70s** — Generation phase: Income Statement and Debt Schedule generated first (sequentially, to establish prior values). Remaining sheets generated in parallel using IS/DS context. Each sheet's cells are streamed to the frontend as cell_update events for the data-waterfall theatre
5. **t = 70-72s** — Post-processor: in-memory fixed-point iteration converges the LBO circular reference (typically <10 iterations)
6. **t = 72-74s** — Validation: six algebraic rules run; plug adjustments applied where needed
7. **t = 74-75s** — Archetype conformance scoring: KPIs extracted, ranges checked, scorecard built
8. **t = 75-76s** — Excel writer: writes synthetic values to input cells (preserves formulas). bs_plug.py runs second-pass: evaluates real formulas, computes BS imbalance, writes corrective plug
9. **t = 76s** — `complete` WebSocket event fires
10. **t = 76-80s** — SchemaTerminal transitions to SpreadsheetViewer. POST to `/api/sheets/{job_id}`. Backend creates Google Sheet, writes all data, builds the ✓ Validation tab with live formulas, makes the sheet publicly viewable
11. **t = ~80s** — Google Sheet iframe loads. The buyer sees the populated model, can navigate to the ✓ Validation tab, click any cell, and watch the live formula evaluate

**The magic moment is the ✓ Validation tab.** It's not a green-checkmark badge. It's a sheet full of clickable formulas where each formula evaluates against the populated model in real time, and every check returns "PASS" because the deterministic validator already guaranteed it would. The buyer's trust isn't asked for — it's earned by an artifact they can interrogate.

## 5-Pillar verdict

1. **Bottleneck Assassin:** ✅ — directly solves enterprise procurement friction; automates Peter Fuller's own published advice ("Invest in a testing set-up. Create non-sensitive test data.") into Tracelight's product surface
2. **Anti-Replication:** ✅ at design time — operates strictly upstream of the DAG engine; specifically avoids the recently-shipped shareable-webpages surface, Plan Mode, and Spreadsheet Compare. See haram-vertical caveat in `overview.md` for why this engagement would not happen today regardless
3. **Native Environment:** ✅ — handoff into Tracelight's product is a populated xlsx the buyer can immediately upload into the Excel add-in. Google Sheets embed in the demo viewer is itself a familiar spreadsheet surface, not an unfamiliar custom UI
4. **Magic Moment:** ✅ — 60-90 second wall-clock generation with the ✓ Validation live-formula proof surface. Stronger than the originally-designed verdict badge for the technical buyer
5. **System Resilience and Immunity:** ✅ — deterministic Pure-Python validator as trust anchor; six algebraic rules with zero LLM in the certification path; two-pass BS projection using real formula evaluation; archetype conformance scoring as a second-tier read-only audit; transparent plug-adjustment audit trail with deltas surfaced to the buyer

## What was specific to Tracelight vs reusable

**Specific to Tracelight (not reusable as-is):**

- The LBO / DCF / 3-Statement schema definitions and the verbose-vs-compact template support
- The six financial-modeling algebraic rules (BS identity, CF reconciliation, NI linkage, margin bounds, depreciation constraint, debt schedule integrity)
- Cash / Other CF Items / Other LT Assets / Other LT Liabilities as the plug accounts
- The LBO archetype profiles (general, distressed turnaround, high-growth tech, mature cash-cow) and the academic citation framework (Guo, Axelson, Bain, Hulett)
- The fixed-point iteration solver for the LBO debt-interest-NI-repayment circular reference specifically
- The "Start Testing in Tracelight" marketing CTA copy and the tracelight.ai handoff link
- The financial-jargon glossary baked into the schema-extraction prompts (LBO, DCF, NNN, Cap Rate, etc.)

**Reusable at the pattern level (apply to non-financial domains with translation):**

- **The Safe-Harbor Synthetic Data Fabric pattern itself** — schema extraction → constraint-aware generation → deterministic validation → constraint projection → archetype conformance → handoff. Generalizes to any vertical where the buyer's compliance regime prevents real data uploads and the cold-start problem dominates the sales cycle
- **Validator-as-only-certification-gate discipline** — the deterministic validator is the only thing that can certify output as shippable. Generation is allowed to be stochastic; certification is not
- **Two-pass constraint projection** (Coletta et al. 2023) — generate first, evaluate the artifact's actual computation graph (formulas, business rules, dependency DAG), compute the residual, project onto the constraint surface via a designated plug variable. Applies to any structured-artifact domain where the artifact has its own evaluation engine (Excel, contract templates, BOM systems, compliance reports)
- **Constraint-aware regeneration with bounded retries** — the plug-adjustment mechanism generalizes: when validation fails on a structural constraint, regenerate the offending element with the constraint-violation surfaced back to the LLM. Max retries cap the cost
- **Row-map / canonical-key abstraction** (`row_map.py`) — for any template-driven domain where headers and row numbers vary across template instances, build a canonical-key-to-row lookup at parse time. Eliminates hardcoded positional references; supports verbose-vs-compact template variants from a single processing pipeline
- **Schema Terminal + live progress feed** — terminal-style WebSocket event stream during processing builds buyer trust by exposing the pipeline's internal state. Generalizes to any multi-stage pipeline demo
- **Live-formula proof surface as trust anchor** (in lieu of verdict badge) — embed validation logic as live, clickable formulas inside the deliverable artifact rather than separating it into a "trust me" badge. The buyer interrogates the formula; the formula evaluates correctly because the deterministic validator already enforced it. Applies to any domain where the artifact's own computation engine can host verification logic
- **Domain-archetype-aware generation with post-hoc conformance scoring** — generalize the LBO-archetype pattern to: (a) buyer selects a domain archetype at submission time, (b) generator receives archetype-specific constraint prompts citing domain conventions, (c) a read-only post-validation scorer reports conformance to archetype parameter ranges with academic/industry citations. Applies to any vertical where output realism is judged against recognizable category templates
- **PRD design intent vs shipped state documentation discipline** — every substantive architecture.md should include an audit section documenting where the shipped path diverges from the PRD path and why. This discipline is substrate-grade; the audit's existence is more important than the gaps themselves being closed

## Stack

- **Backend:** Python 3.12, FastAPI (async), WebSockets, openpyxl, pandas, numpy, Pydantic 2.10, `formulas` library (for two-pass BS plug), google-genai SDK (Vertex AI), openai SDK (fallback only), Google Sheets API via google-api-python-client
- **Frontend:** React 18 + Vite + Tailwind CSS, native WebSocket hook for live event stream, Google Sheets iframe embed for the proof surface
- **Infrastructure:** Google Cloud Run (Safe-Harbor backend, multi-stage Docker), Vercel (three frontends: launcher, safe-harbor/frontend, shield-wall/frontend — last one references a non-existent backend), Cloud Build via `infra/cloudbuild-safe-harbor.yaml`, GitHub Actions CI/CD

## Forward-reference notes

The following substrate files do not yet exist and warrant separate authoring as the operational record matures:

- `prospects/tracelight/shield_wall_design_notes.md` — full design captured in `PHASE_2_SPEC.md` in the repo, but never coded. Worth a separate substrate record because the design (questionnaire-parser → telemetry-agent → policy-agent + RAG → synthesis-agent → drift-detector) is a reference pattern for vendor-questionnaire-automation that could be triggered for a future (non-haram-vertical) prospect. Documents what was specified vs what would need to be built to ship it
- `prospects/tracelight/post_engagement_diagnostic.md` — analysis of why demo completion was low and why no response came back, beyond what's already in `outcome.md`. Specifically: was the first 60 seconds of the Vidyard recording load-bearing-enough? Did the scenario-selector concept land in the cold-watch context, or did it confuse a first-time viewer? This is operationally useful for future outreach discipline even though the prospect is closed
- `frameworks/architectural_patterns/safe_harbor_synthetic_data_fabric.md` — the pattern file itself, abstracted from Tracelight's instance. Should be authored when the second instance of this pattern is built; Tracelight's architecture.md is the first instance and serves as the empirical anchor
- `frameworks/sourcing_filters/riba_haram_vertical_filter.md` — the post-engagement filter that prevents Tracelight-shaped engagements from happening today. Should be authored to make the filter operational across future sourcing pipelines, with Tracelight named as the historical precedent that motivated it

These are surfaced here rather than written into architecture.md because each is operationally distinct and warrants its own file with its own retrieval anchors.
