---
type: pipeline_template
step_number: 07
pipeline_stage: 1E
step_name: architectural_synthesis_ultimate_prd
runs_in: claude_code_with_nia
last_updated: 2026-05-19
canonical: true
key_tags:
  - architectural_synthesis
  - frankenstein_pattern
  - ultimate_prd
  - mcp_divide_conquer
  - academic_validation
  - state_of_the_art_justification
  - step_1e
  - claude_code
inputs:
  - "Modernized Master_PRD.md (Step 05)"
  - "LATERAL_PRD_v1.md through LATERAL_PRD_v4.md (Step 06)"
  - "context.md (Source Intelligence)"
outputs:
  - "ULTIMATE_PRD.md (synthesized hybrid architecture with academic validation)"
upstream_step: step_06_lateral_exploration
downstream_step: step_08_claims_audit
---

# STEP 07 — ARCHITECTURAL SYNTHESIS / ULTIMATE PRD TEMPLATE

/Nia

> **Customization surfaces:** `[INSERT SPECIFIC TECHNICAL DOMAIN / MATH / XAI FOCUS HERE]` — the specific arXiv search target for academic validation.
> **Recommended runner:** Claude Code with multiple MCP servers active (Nia, arxiv-mcp, paper-search-mcp, academic-mcp).
> **Critical:** This step does autonomous external research via MCP. Verify all MCPs are connected and authenticated before running.

---

```
/Nia

Role: Act as the Principal Product Architect.

Context: I have a repository containing strategic intelligence, a Master_PRD.md, and four LATERAL_PRD_v#.md files. We are now consolidating our exploration into a single, lethal execution path for [TARGET COMPANY].

YOUR INSTRUCTIONS:

1. TOTAL REPOSITORY INGESTION (MANDATORY):

Before writing a single word, you MUST read every single file present in this repository.

- Read context.md to internalize the founder's specific pain points and quotes.
- Read Master_PRD.md and all four LATERAL_PRD_v#.md files to understand the core bottleneck and the proposed variations.
- Analyze any existing code or configuration files to understand the current technical state of the project.

Use Nia MCP for semantic retrieval; do not rely solely on direct file reads.

2. EXTERNAL CONTEXT & R&D INGESTION (MCP DIVIDE & CONQUER):

Do not synthesize in a vacuum. You MUST use your active MCP servers to validate your architecture, strictly following this routing:

(a) THE ENGINEERING SWEEP (Nia MCP):

Run targeted web searches for recent engineering blogs or GitHub repos to validate the Google-native software stack (Gemini, GCP best practices, Vertex AI patterns, Cloud Run deployment models). Anchor your architectural choices to what production systems are actually doing in 2025-2026, not what training data suggests.

(b) THE ACADEMIC SWEEP (arxiv-mcp, paper-search-mcp, academic-mcp):

Run a highly constrained search for recent (2024-2026) academic papers specifically regarding [INSERT SPECIFIC TECHNICAL DOMAIN / MATH / XAI FOCUS HERE — e.g., "schema-driven information extraction from heterogeneous tables," or "physics-informed reinforcement learning for industrial control," or "neuro-symbolic compliance reasoning"].

Do NOT research general theory. Find one or two specific algorithms or methodologies we can cite to prove our underlying logic is mathematically and architecturally sound.

For each paper:
- Verify the paper exists (don't hallucinate citations — verify arXiv ID, authors, abstract)
- Verify the methods section actually says what you claim it says
- Pin the exact subsection/equation reference your architecture depends on

If a paper looks promising but inaccessible (paywalled, withdrawn, in-flight), flag it ⚠️ DEFERRED rather than citing it as ✅ verified. The §4.7 Citation Verification Audit in this output (see §3 below) is non-negotiable.

3. FEATURE EXTRACTION & "FRANKENSTEIN" SYNTHESIS:

(a) The Filter: Strip out the safest, conventional ideas. Extract only the absolute highest-leverage, most distinct technical features from across ALL FIVE PRD documents (Master_PRD + 4 Laterals), backed by the external research you just conducted.

(b) The Architecture: Wire the best routing mechanisms and agent loops from the Lateral PRDs into the most stable data structures from the Master PRD. The synthesis must be defensible — for each feature included, you must be able to point to either the original PRD it came from OR the academic paper that justifies it.

(c) The Stack: Ensure the architecture is strictly Google-native. Show exactly how Gemini (specific model strings from docs/modernization_log.md) and GCP infrastructure (Vertex AI region, Cloud Run, Postgres, Redis, etc.) handle this new combined flow.

4. THE STRICT ADJACENCY GUARDRAIL:

You must verify that the combined architecture remains distinctly DIFFERENT, ADJACENT, and MODULAR. It must act as a bolt-on pre-processing lead magnet or post-processing analytics tool. Under no circumstances should it replicate [TARGET COMPANY]'s core proprietary engine.

If you find yourself wanting to add a feature that drifts toward the customer's core IP — STOP. Halt and report. That is a hard kill on the synthesis.

5. THE DELIVERABLE — ULTIMATE_PRD.md:

Output a single, highly optimized file called ULTIMATE_PRD.md with this structure:

## §1 — The FDE Thesis

A concise summary (with citations from context.md) explaining why this hybrid path is the most lethal for this target. Anchored to founder quotes and verified company bottlenecks.

## §2 — Founder Psychology Synthesis (carry forward from Master_PRD)

## §3 — The System Map

A granular look at the Gemini multi-agent routing:
- §3.1 — §3.9: Per-agent and per-stage detail (model strings, routing, retry behavior, deterministic fallbacks)
- §3.10 — Compliance Posture (data residency, zero-retention configuration, audit trail requirements, boot validators)

Every numbered subsection should be implementable from this spec alone.

## §4 — State-of-the-Art Justification

A dedicated section explicitly citing the specific blogs, repos, and academic papers you found via your MCPs that validate this specific Frankenstein data pipeline.

For each architectural claim:
- §4.X — [Claim] — backed by [primary source — paper title, arXiv ID, GitHub repo, engineering blog with date]

## §4.7 — Citation Verification Audit

NON-NEGOTIABLE. List every academic citation in §4 with verification status:
- ✅ VERIFIED — paper accessed, methods section confirmed, claim matches paper content
- ⚠️ DEFERRED — paper exists per arXiv listing but body inaccessible; flag for phase-boundary re-verification (typically at PHASE_2 and PHASE_4 boundaries)
- ❌ CONTRADICTED — paper exists but says something different than the claim relied on; this claim must be removed or amended before proceeding

For ⚠️ DEFERRED citations, specify which phase boundary triggers re-verification (Step 12 / Step 3B handles this).

## §5 — Execution Spec

A detailed Phase 1 technical blueprint for Claude Code to build this foundation:
- File-by-file scaffold
- Pydantic schemas (with ConfigDict(extra="forbid"))
- FastAPI route signatures
- Dependencies (per docs/modernization_log.md)
- Implementation logic per function
- Phase 1 acceptance criteria

## §6 — Anti-Replication Boundary Map

Explicit list of customer-side IP NOT touched. Per-feature, per-engine. With "unplug guarantee" statement.

## §7 — Killed Features (Receipts)

Features from the Master PRD or any Lateral that were considered but rejected during synthesis. Cite reason and source intelligence.

CRITICAL CONSTRAINTS:

- Do not summarize your intent or acknowledge this instruction. Just execute the MCP searches, synthesize the data, and output the complete ULTIMATE_PRD.md file to the directory.
- Citations to academic papers without §4.7 verification status = hard fail. Every paper cited gets a verification verdict.
- Do not alter the core business strategy or anti-replication boundary established in Master_PRD.md §6.

HANDOFF FORMAT:

```
Ultimate PRD synthesis: COMPLETE
Features extracted:     <count from each source PRD>
Academic citations:     <count> (✅ verified / ⚠️ deferred / ❌ contradicted)
File created:           ULTIMATE_PRD.md
Commit:                 <SHA>
Branch:                 main
Nia index:              updated
Status:                 Ready for Step 08 (Forensic Claims Audit / 1F Validation Gate)
```
```

---

## Customization checklist

- [ ] `[TARGET COMPANY]` — prospect name
- [ ] `[INSERT SPECIFIC TECHNICAL DOMAIN / MATH / XAI FOCUS HERE]` — the arXiv search target. Be specific: "schema-driven extraction" not "AI extraction"; "physics-informed RL for chemical reactors" not "AI for industry"
- [ ] Verify all 4 MCP servers connected: Nia, arxiv-mcp, paper-search-mcp, academic-mcp
- [ ] Verify Opus 4.7 is the active model

## Downstream

ULTIMATE_PRD.md is the canonical architectural document going forward. Step 08 audits its claims (1F validation gate). Step 09 makes the ship/reposition/kill call. Step 10 expands it into PHASE_1_SPEC.md.

The §4.7 Citation Verification Audit specifically feeds Step 12 (Phase Review Template) — at PHASE_2 and PHASE_4 boundaries, any ⚠️ DEFERRED citations must be re-verified before the corresponding phase spec is generated.
