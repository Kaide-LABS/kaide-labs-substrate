---
type: architectural_pattern
pattern_name: safe_harbor_synthetic_data_fabric
canonical_one_liner: "Compliance-safe synthetic data generation that unblocks enterprise trials of a data-hungry core engine."
status: v1
version_first_shipped: 2026-04
prior_worked_examples:
  - tracelight
last_updated: 2026-05-21
key_tags:
  - synthetic_data
  - structured_outputs
  - compliance_unblock
  - enterprise_trial_acceleration
  - deterministic_validator
  - financial_modeling
  - upstream_sidecar
  - two_pass_constraint_projection
  - fixed_point_iteration
signal_checklist:
  - target_engine_needs_real_data_to_demonstrate_value
  - prospect_cannot_or_will_not_share_real_data_during_trial
  - target_engine_consumes_well_specified_schema
  - schema_has_algebraic_invariants_a_validator_can_assert
  - trial_window_is_the_binding_sales_constraint
related_patterns:
  - calibration_sidecar
position_relative_to_target_core: upstream
llm_stack_default: vertex_ai_gemini
llm_stack_fallback: openai_gpt4o_structured_outputs
---

# Safe-Harbor Synthetic Data Fabric

## §1. Pattern name + one-liner

**Safe-Harbor Synthetic Data Fabric.** A stateless containerized sidecar that generates compliance-safe, schema-conformant, mathematically valid synthetic data for a target engine, eliminating the data-acquisition bottleneck that blocks enterprise trials.

The one-line pitch shape: *"Your prospect can't share real [data type] with you during the trial. We generate fake data that exercises your engine end-to-end, so the trial converts on capability — not on whether the prospect's legal team has cleared a data-share."*

The pattern's load-bearing claim is that an LLM-generated payload is acceptable only when an external deterministic system can prove its correctness against the schema's own invariants. The LLM is the generator; the validator is the trust anchor.

## §2. When to reach for this pattern

Reach for Safe-Harbor when all of the following are true:

- The target's core engine **requires substantive input data** to demonstrate its value (financial models, simulations, ML pipelines, optimization solvers, BI dashboards).
- Prospects in the target's pipeline **cannot share real data during the trial window** — sensitivity, regulatory constraints (GDPR, HIPAA, SOX, MiFID II), competitive secrecy, or procurement latency on data-share agreements.
- The target's engine **consumes a well-specified input schema** (Excel template structure, JSON contract, database schema, file format). The schema is recoverable from a public artifact or an empty template.
- The schema has **algebraic invariants a deterministic validator can assert** — accounting identities, conservation laws, type bounds, referential integrity, sum-equals constraints. If you can't validate the synthetic output without running the customer's engine, this pattern fails.
- The **trial window is the binding sales constraint** — the deal slips because the prospect can't get their data into the target's product in time, not because the product itself is unconvincing.

Reach for it especially when the founder's own public writing names the bottleneck. Tracelight's CEO Peter Fuller explicitly told prospects "Invest in a testing set-up. Create non-sensitive test data for use cases you care about." The pattern automates that founder-prescribed workaround.

## §3. The problem shape

The target's core engine produces high-value output but only when fed substantive structured input. Real input is expensive to acquire during a trial because the prospect's legal/InfoSec team controls it and operates on a 2-12 week clock that's longer than the trial.

The bottleneck is not technical; it is procedural. Engineering effort spent on the target's core product cannot fix it because the blocker lives outside the target's product boundary, inside the prospect's procurement process.

The wedge: synthetic data that is *plausible enough to exercise the engine* and *structurally valid enough to survive technical scrutiny* lets the prospect run the trial during the InfoSec review, not after it. Sales cycles compress because the conversion event (the prospect seeing value in the engine) moves earlier in time.

The Anti-Replication boundary is sharp here: the sidecar produces only the input file. It never touches the target's engine, never replicates its output format beyond the input handoff, never competes with anything the target's engineering team builds. The sidecar's value proposition is *displaced into the procurement timeline*, not into the product surface.

## §4. Technical architecture

### Component graph

```
[Empty template] → Parser → Schema Extractor → Synthetic Generator → Post-Processor → Validator → Excel Writer → Two-Pass BS Plug → Conformance Validator → [Filled template + live validation tab]
                                                                          ↑                                          ↓
                                                                          └─ retry (max 3) ←────────── validation FAILED
```

Every arrow is async. Every payload between components is a Pydantic model with `extra="forbid"` discipline. WebSocket streams per-cell progress events to the frontend for the theatrical layer (§6).

### Agent routing

| Component | Tech | Role |
|---|---|---|
| **Parser** | Python (`openpyxl`) | Extracts sheet structure, headers, input cells, formula refs, named ranges, inter-sheet dependencies. Detects section headers vs. data rows. Detects whether the template is empty (rejects templates with >15% populated input cells). |
| **Row Map Builder** | Python (canonical aliases) | Universal lookup that maps parser output to canonical keys (`bs_total_assets`, `is_ebitda`, `ds_senior_repay`). Eliminates all hardcoded row numbers downstream. Sheet-scoped overrides resolve cross-sheet collisions. |
| **Schema Extractor** | Vertex AI Gemini Flash (primary) → GPT-4o Structured Outputs (fallback) | Classifies model type, industry, currency, column data types, temporal ranges, inter-sheet refs. Chunked: one call to classify model + one parallel call per sheet. GPT-4o fallback only when Gemini fails reliability check. |
| **Synthetic Generator** | Vertex AI Gemini Pro | Generates values per sheet in chunks of ≤25 cells (small chunks = LLM never skips). 3-phase ordered generation: IS first (revenue/cost baseline) → DS (needs IS context) → everything else in parallel. Backfills missing keys. Cross-sheet value context injected as prior_sheets dict. Temperature 0.3. |
| **Post-Processor** | Pure Python | Fixed-point iteration solver for the Interest → NI → CF → Repayment circularity in LBO models. Template-driven via row_map. Sign-convention fixes. Cross-period linkages (begin = prev-end). Writes only to input cells, never formula cells. |
| **Deterministic Validator** | Pure Python | Six hardcoded rule classes (§4.3). The trust anchor — zero LLM in the validation path. Produces ValidationRuleResult per (rule × period), plug adjustments, regeneration signals. |
| **Excel Writer** | Pure Python (`openpyxl`) | Writes synthetic values to input cells only. Preserves all formulas, formatting, named ranges. |
| **Two-Pass BS Plug** | Pure Python (`formulas.ExcelModel`) | Anchors to Coletta et al. (2023) arXiv:2307.01717 "Constrained Time-Series Generation": generate first, project onto the constraint surface. Pass 1 writes to xlsx; Pass 2 evaluates the template's own formulas as ground truth, computes the (Assets − L − E) delta per period, and writes a plug adjustment to a designated input cell (Other LT Assets primary, Other LT Liabilities fallback). |
| **Conformance Validator** | Pure Python | READ-ONLY layer. Compares extracted KPIs against locked parameter ranges per archetype (general, distressed_turnaround, high_growth_tech, mature_cashcow). Cites academic sources (Guo 2011, Axelson 2013, Bain 2024, RL Hulett 2024). Never modifies cells. Scored PASS/WARN/FAIL with 15% tolerance band. |

### The six deterministic validation rules

These are the rule classes the validator enforces. The exact constraints vary by template type; the *shape* of the rule is the canonical part.

1. **Balance Sheet Identity** — `Total_Assets ≡ Total_Liabilities + Total_Equity` per period. Tolerance 0.01. Plug: Cash, with delta logged.
2. **Cash Flow Reconciliation** — `Ending_Cash ≡ Beginning_Cash + Net_Change_in_Cash` per period. Plug: Other_CF_Items if present, else force Ending_Cash directly.
3. **Net Income Linkage** — `P&L.Net_Income ≡ CF.Net_Income`. Cross-sheet integrity. Force CF to match P&L.
4. **Margin Bounds** — EBITDA margin ∈ [-50%, 80%], gross margin ∈ [0, 1], net margin ∈ [-100%, 50%]. Violations trigger regeneration, not plug.
5. **Depreciation Constraint** — cumulative D&A ≤ cumulative CapEx + opening PP&E per period. Plug: cap depreciation at the ceiling.
6. **Debt Schedule Integrity** — per tranche: `Ending_Balance ≡ Beginning + Drawdowns - Repayments`. Plug: adjust repayments.

The split between *plug* and *regenerate* matters. Plug is acceptable when the violation is a small algebraic gap a single delta can close. Regenerate is required when the value itself is structurally wrong (negative gross margin means revenue or COGS is wrong, not that an adjustment cell can absorb it).

### Infrastructure

- **Containerization**: backend FastAPI container, frontend nginx container, both deployed to Google Cloud Run. Stateless. Two-pass BS plug uses local tmp for the formula-evaluation pass.
- **Persistence**: in-memory job dict for the demo; production version uses Cloud SQL Postgres for job state.
- **Streaming**: WebSocket from backend orchestrator to frontend, one event per cell update plus phase markers.
- **Auth**: Google service account or OAuth user credentials for Sheets/Drive API access (required for the Magic Moment §6).

### LLM stack discipline

The pattern's first shipped version (Tracelight, April 2026) used GPT-4o Structured Outputs for the synthetic generation step because Gemini's structured-output reliability at the time wasn't validated for the financial-data use case. The production architecture as of May 2026 inverts that: **Gemini Pro is the default for synthesis, GPT-4o Structured Outputs is the fallback only**. This brings the pattern into Anti-Replication compliance for Google-ecosystem-mandated engagements while preserving the structured-output guarantee on the fallback path.

Today's default for new Safe-Harbor instances: Vertex AI Gemini Pro for generation, Vertex AI Gemini Flash for schema extraction, GPT-4o Structured Outputs only as a documented fallback when Gemini's `application/json` response_mime_type fails schema conformance more than 3 times in a single generation attempt.

## §5. Anti-replication boundary

The sidecar's surface is hard-bounded at the input handoff:

- **Never touches the target's core engine.** Tracelight's DAG engine, formula generator, Plan Mode, Spreadsheet Compare, Change Reviews — all untouched. The sidecar terminates when it hands the populated .xlsx back.
- **Never produces target-engine output.** No synthetic memos, no synthetic analysis, no synthetic anything the target's product produces. The sidecar produces *input only*.
- **Never builds anything on the target's product roadmap.** This is the audit that killed the IC Memo Synthesizer proposal in the Tracelight engagement — Peter Fuller had announced spreadsheet-to-webpage two weeks before the proposal, and the State Observer Agent that read finalized DAG nodes literally interfaced with their proprietary graph engine. Anti-Replication caught it; the pattern survives because the audit is non-negotiable.
- **Stays upstream of the trial flow.** The prospect's first interaction with the target's actual product happens *after* Safe-Harbor's verdict screen, when they click "Start Testing." The sidecar is invisible from inside the target's app.

The defensive line if the target's CTO challenges: *"Our sidecar produces an .xlsx and stops. Your engine consumes the .xlsx. There is no integration with your DAG. There is no shared state. There is no closed loop. If you turned our sidecar off tomorrow, your product would still work exactly as it does today; you'd just lose the procurement-cycle compression."*

## §6. Theatre approach

The pattern's strongest theatrical move is the **live Google Sheets validation tab** — every algebraic check is a clickable formula in a Google Sheet the customer's browser is rendering. Not a claim. The CTO can click any cell and see the live formula evaluate.

### Magic moment timing

The shipped Tracelight version has these timing anchors:

- **T+0** — Prospect uploads empty template (or selects sample). Schema discovery starts immediately, terminal-style feed shows sheet mapping. Visible computation, not a spinner.
- **T+8** — Schema extraction complete. Model classified (LBO/DCF/3-statement). Inter-sheet links discovered and printed: `[LINK] P&L.Net_Income → CF.Net_Income ✓`. Generation begins.
- **T+30 to T+90** — DataWaterfall: per-cell stream into a tabbed grid. Cells flash green as values land. Validation pass markers appear in the status bar: `✓ Balance Sheet Balanced (Year 1)`. Plug adjustments appear transparently: `⚡ Adjusted Cash by +$142K to force BS equilibrium (Year 3)` — this transparency BUILDS trust by showing the system is honest about corrections.
- **T+90+** — Verdict screen with the validation summary, then click into the Spreadsheet Viewer which uploads to Google Sheets via the Sheets API and embeds it. The embedded sheet has a `✓ Validation` tab with section-by-section live formulas covering BS identity, margin analysis, revenue growth, CF reconciliation, debt schedule rollforward, cross-sheet linkage, statistical distribution, leverage ratios, and archetype conformance.

### Rendering surfaces

Three surfaces, each tuned to a different buyer:

| Surface | Audience | What it proves |
|---|---|---|
| **DataWaterfall grid** | Operator / business buyer | Speed and scope — "this took 90 seconds, 84 cells populated across 5 sheets." |
| **VerdictBadge with proof drawer** | Decision maker | Algebraic correctness — "6/6 validation rules passed across all periods, with deltas visible." |
| **Google Sheets ✓ Validation tab** | CTO / quant | Cryptographic-grade trust — "every check is a live cell formula they can click." |

The third surface is the load-bearing one for a CTO buyer. Without it, the validator's claims are unverifiable from outside the sidecar. With it, the customer's own Google account is doing the validation in their browser.

### Cost ticker as defensive theatre

The shipped version logs API costs per agent (`cost_tracker.py`). The verdict screen surfaces this as "$0.037 per generation." Two purposes: (1) it's a closer for the business buyer — "your sales cycle just got 8 weeks shorter for four cents," and (2) it preemptively answers the unit-economics question that any sophisticated CTO will ask within 60 seconds of seeing the demo.

## §7. Prior worked examples

**Tracelight (April 2026).** Cold-pitched Peter Fuller, CEO. Architecture shipped as the Safe-Harbor Synthetic Financial Data Fabric. 3 templates supported (LBO, DCF, 3-statement; DCF and 3-statement marked "Coming Soon" on the live demo). 4 archetypes (general, distressed_turnaround, high_growth_tech, mature_cashcow) with parameter ranges grounded in Guo et al. (2011), Axelson et al. (2013), Bain & Company (2024), RL Hulett (2024). Two adjacent proposals surfaced as a future capabilities menu — Shield-Wall (InfoSec questionnaire automation, demoted to "Coming Soon") and an IC Memo Synthesizer (killed for replicating Tracelight's shipped spreadsheet-to-webpage feature).

See: `prospects/tracelight/outcome.md` (engagement summary, Vidyard analytics, ghosting, no-follow-up discipline held); full `prospects/tracelight/architecture.md` pending backfill.

## §8. Anti-patterns and failure modes

These are the failure modes the Tracelight build either survived or was redesigned to avoid. Treat them as a checklist when customizing the pattern for a new prospect.

**1. Stochastic LLM doing math.** The original Gemini Web proposal called for a GAN/Diffusion model to generate financial time series. Killed in red-team — training a TabGAN takes weeks, not the 72-hour sprint window, and the output guarantees aren't strong enough to survive a quant CTO's audit. The shipped version replaced this with `LLM-generates-numbers + Python-validates-and-plugs`. The LLM is the generator; the validator is the trust anchor. Never let an LLM be both.

**2. Replicating the target's roadmap.** The IC Memo Synthesizer proposal would have rebuilt Tracelight's just-shipped spreadsheet-to-webpage feature. Anti-Replication audit caught it. When customizing this pattern: before locking the architecture, search the target's last 90 days of public posts (Twitter, LinkedIn, company blog) for any feature announcements that overlap. If overlap exists, kill that proposal and pivot.

**3. Formula-evaluation drift.** Early shipped versions mirrored Excel formulas in Python to predict what the template would evaluate to. This drifts the moment the template's formulas change. The fix was the **Two-Pass BS Plug**: write to xlsx, evaluate the template's own formulas as ground truth (via `formulas.ExcelModel`), then project onto the constraint surface. Coletta et al. (2023) is the academic anchor. Always let the template's own formulas be the truth.

**4. Fake formulas that break the parser.** Some templates have placeholder strings like `= Formula (auto-calculated)` that aren't valid Excel formulas. `formulas.ExcelModel` chokes on these. The fix was the sanitization step in `bs_plug.py` — validate every formula with `formulas.Parser().ast()` before evaluation; convert invalid formulas to plain strings.

**5. LLM skipping cells on large generation requests.** Initial generation batched all cells per sheet in one LLM call; the model would silently drop 5-15% of cells on long requests. The fix was `MAX_CELLS_PER_CHUNK = 25` plus a missing-key backfill pass. Smaller chunks → LLM never skips. Always size chunks so the output token budget is dominated by per-cell content, not by structural overhead.

**6. Cross-sheet inconsistency.** Net Income on the P&L diverging from Net Income on the Cash Flow Statement. The fix was 3-phase ordered generation (IS first → DS second with IS context → everything else in parallel with both) plus cross-sheet value context injection (`_extract_cross_sheet_values` builds a dict of revenue/EBITDA/NI/PP&E values per period and passes it to downstream sheets as `prior_sheets`).

**7. Plug-account abuse.** Every algebraic identity has a designated plug account that can absorb deltas (Cash for BS, Other_CF for CF, Repayments for debt). If a plug adjustment exceeds 5% of the underlying account or breaks a separate constraint (negative cash, negative debt repayment), regenerate instead of plugging. The shipped validator has a fallback chain — Other_LT_Assets → Other_LT_Liabilities — that gracefully degrades when the primary plug can't absorb.

**8. Hardcoded row numbers.** First versions had `IS_REVENUE_ROW = 6` constants scattered across post-processor and validator. Every new template type required surgery. The fix was `row_map.py` — universal canonical-key lookup with sheet-scoped overrides. Now new templates require only adding aliases to `CANONICAL_ALIASES`, not patching downstream logic.

**9. Validation theatre that doesn't validate.** It's tempting to make the verdict screen claim more than the validator proves. The discipline: every visible green check on the verdict screen must correspond to a ValidationRuleResult with `passed=True`. Every plug must be visible as a `PlugAdjustment` with reason text. Trust compounds when the system is honest about what it adjusted; it collapses the first time a CTO catches a discrepancy.

## §9. When NOT to reach for this pattern

- **Target's engine doesn't need substantive input data to demo well.** If a prospect can experience the engine's value through a 5-minute click-through with no data upload, Safe-Harbor solves a non-problem.
- **No algebraic invariants in the schema.** If you can't write a deterministic validator that asserts correctness without running the target's engine, the pattern fails because there's no trust anchor. (Counter-example: a CRM data fabric where the only "validity" check is that fields are non-null — too weak to survive scrutiny.)
- **Prospect's actual blocker is engine quality, not data acquisition.** If the target's CTO knows the product is losing trials because the engine is shaky, fixing the data side is theatre. Safe-Harbor needs the trial-blocking to live in procurement, not in product.
- **Target's product already ships synthetic-data generation.** Replication check. If the target's engineering team has shipped (or has on their roadmap) any synthetic-data feature, kill the architecture and pivot.
- **The target is in a vertical where synthetic-data tooling is commodity** (e.g., generic mock-data libraries for software testing). The wedge requires the synthesis to be domain-aware (LBO archetypes, healthcare ICD codes, derivatives mark-to-market, etc.). If domain expertise isn't load-bearing, the prospect can stitch together open-source tooling and the sale collapses.
- **Trial windows are not the binding sales constraint.** If deals are slipping on price, engine accuracy, integration complexity, or buying-committee dynamics — Safe-Harbor doesn't address any of those. Verify the bottleneck before pitching the pattern.

## Cross-references

- Prior worked example: `prospects/tracelight/outcome.md` (engagement notes); full `prospects/tracelight/architecture.md` pending backfill
- Adjacent pattern: `frameworks/architectural_patterns/calibration_sidecar.md` (AS / RCS — calibration takes messy real data and produces structured representation; Safe-Harbor takes schema-only and produces synthetic data. Both run upstream of a target engine. Both validate deterministically. Different input shapes, different output meanings.)
- Pipeline templates referenced: `pipeline/templates/STEP_04_PRD_REDTEAM_TEMPLATE.md` (anti-replication audit), `pipeline/templates/STEP_09_VALIDATION_REDTEAM_TEMPLATE.md` (validation rule design)
- Identity reference: `identity/identity_doc.md` §5 (Technical Constraints — Google-ecosystem mandate with documented OpenAI fallback for structured-output guarantee)
- Academic anchors: Coletta et al. (2023) arXiv:2307.01717 (constrained time-series generation); Guo, Hotchkiss & Song (2011) J. Finance 66(2); Axelson, Jenkinson, Strömberg & Weisbach (2013) J. Finance 68(6); Bain & Company (2024) Global Private Equity Report; RL Hulett (2024) Software & Tech-Enabled Services M&A Update Q3 2024
