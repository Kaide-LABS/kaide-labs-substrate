---
type: pipeline_template
step_number: 05
pipeline_stage: 1C
step_name: prd_modernization_dependency_audit
runs_in: claude_code_with_nia
last_updated: 2026-05-19
canonical: true
key_tags:
  - prd_modernization
  - dependency_audit
  - model_string_freshness
  - sdk_freshness
  - nia_powered
  - step_1c
  - claude_code
inputs:
  - "Master_PRD.md (Step 04 output)"
  - "Existing repo (code, research reports, configuration)"
outputs:
  - "Master_PRD.md (modernized, committed and pushed to GitHub)"
  - "Nia index update"
upstream_step: step_04_prd_redteam
downstream_step: step_06_lateral_exploration
---

# STEP 05 — PRD MODERNIZATION & DEPENDENCY AUDIT TEMPLATE

/Nia

> **Customization surfaces:** None. This template runs against the existing repository.
> **Recommended runner:** Claude Code with Nia MCP enabled. The Nia repository scan is non-negotiable for this step.
> **Critical rule:** Do NOT alter the architectural strategy. Only modernize SDK syntax and LLM routing.

---

```
/Nia

Role: Act as the Lead Operations Engineer.

Context: I am handing you a Master PRD for a deterministic enterprise AI sidecar. The architectural logic is flawless, but the LLM that generated it has an outdated knowledge cutoff.

YOUR INSTRUCTIONS:

1. TOTAL REPOSITORY INGESTION (MANDATORY):

Before writing a single word, you MUST read every single file present in this repository. Analyze any existing code, research reports, or configuration files to ensure the modernization is grounded in the current technical state of the project.

Use Nia MCP for repository exploration. Do not rely on direct file reads alone — Nia's semantic chunking surfaces dependencies and architectural patterns that flat file reads miss.

2. EXECUTE THREE SPECIFIC PASSES:

(a) THE FRONTIER MODEL UPGRADE:

Scan the "Agent Routing" or LLM stack sections of the Master PRD. Strip out any references to legacy models (e.g., Gemini 1.5, Gemini 2.0 Flash). Use your tools to look up the current optimal Google Gemini models for these routing tasks:
- For fast retrieval / structured outputs: identify the current Gemini Flash variant (e.g., Gemini 3 Flash)
- For deep context / orchestration: identify the current Gemini Pro variant (e.g., Gemini 3.1 Pro)
- For multimodal / audio: identify the current multimodal Gemini variant

Update the Master PRD with:
- The correct current model names (canonical model strings as they appear in the Vertex AI API)
- Current API cost estimates per call (read from Google's published pricing as of today's date)
- Region binding: confirm europe-west4 for EU prospects, us-east1 for US prospects (or per the prospect's data-residency requirement)

Save the pinned model strings + cost estimates to docs/modernization_log.md so they're locked for downstream phase specs.

(b) THE SYNTAX & DEPRECATION AUDIT:

Scan the "Prerequisites" and the "Phase 1 Execution Spec" Python architecture. You must force the Nia server to run live web queries for the latest release notes and migration guides for the core libraries:
- FastAPI (current stable version, breaking changes)
- Pydantic (v2 syntax, ConfigDict patterns)
- Google GenAI SDK (current version, generate_content signature, zero-retention configuration)
- NumPy (current version)
- [Any other libraries the Master PRD specifies]

Do not rely on your internal weights — library APIs change faster than training cutoffs. Use Nia-powered live web queries.

Identify any deprecated methods, legacy module imports, or recent breaking changes, and rewrite the affected sections of the Master PRD to use the most modern, stable syntax. Document each rewrite in docs/modernization_log.md.

(c) THE REFACTORING:

Output the fully modernized Master PRD to the directory. Preserve §1-§7 structure; only update the technical content within sections.

3. VERSION CONTROL & SEARCH INDEXING (THE FINAL MILE):

I GIVE YOU EXPLICIT PERMISSION TO CREATE A GITHUB REPO AND PUSH TO IT.

- Commit & Push: Once the modernized Master PRD is saved, immediately git add, commit with a clear message (e.g., "build: modernize Master PRD and dependencies for Gemini 3 stack"), and push the repository to GitHub.
- Nia Index: Immediately run a Nia index on the entire repository to ensure the search server is aware of the updated architectural state.

CRITICAL RULE:

Do not alter:
- The core FDE strategy
- The deterministic math layers
- The business logic
- The Anti-Replication boundary
- The Magic Moment timing
- The 5-Pillar adherence

Only modernize:
- Software dependencies (versions, syntax)
- SDK syntax (generate_content signatures, region bindings, retention flags)
- Google Gemini LLM routing stack (model strings, cost estimates)

Ensure the architecture remains strictly DIFFERENT, ADJACENT, and MODULAR. If you find yourself wanting to alter the strategy or business logic — STOP. Halt and report. That is Step 04's job, not Step 05's.

Do not summarize your intent. Just execute the passes and provide the handoff once the repo is pushed and indexed.

HANDOFF FORMAT:

```
Master PRD modernization: COMPLETE
Model string updates:     <count>
SDK syntax updates:       <count>
Deprecated patterns fixed: <count>
docs/modernization_log.md: created/updated
Commit:                   <SHA>
Branch:                   main
Nia index:                <index_id> (updated)
Status:                   Ready for Step 06 (Lateral Architecture Exploration)
```
```

---

## Customization checklist

- [ ] None — this template runs autonomously against the existing repo
- [ ] Verify Nia MCP is connected before running
- [ ] Verify Claude Code has GitHub auth configured
- [ ] Confirm Opus 4.7 is the active model

## Downstream

Modernized Master_PRD.md + docs/modernization_log.md become inputs for Step 06 (Lateral Architecture Exploration in Codex) and ultimately Step 07 (Ultimate PRD synthesis).
