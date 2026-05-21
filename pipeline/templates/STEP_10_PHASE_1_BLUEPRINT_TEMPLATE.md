---
type: pipeline_template
step_number: 10
pipeline_stage: 2
step_name: phase_1_technical_blueprinting
runs_in: claude_code_with_nia
last_updated: 2026-05-19
canonical: true
key_tags:
  - phase_1_spec
  - technical_blueprint
  - hyper_granular
  - pydantic_schemas
  - fastapi_signatures
  - implementation_logic
  - step_2
inputs:
  - "ULTIMATE_PRD.md (Step 07 output, validated by Steps 08-09)"
  - "positioning_final.md (Step 09 output, with verdict CLEAR-TO-SHIP or REPOSITION-REQUIRED)"
outputs:
  - "PHASE_1_SPEC.md (file-by-file, function-by-function technical requirements for Phase 1)"
upstream_step: step_09_validation_redteam
downstream_step: step_11_phase_build_execution
---

# STEP 10 — PHASE 1 TECHNICAL BLUEPRINTING TEMPLATE

/Nia

> **Customization surfaces:** None. This template runs against the validated Ultimate PRD.
> **Recommended runner:** Claude Code with Nia MCP active.
> **Prerequisite:** Step 09 must have issued CLEAR-TO-SHIP or REPOSITION-REQUIRED verdict. If KILL-AND-RESTART, do not run this step.

---

```
/Nia

Role: Act as the elite Principal Systems Architect.

Context: I am handing you a repository containing our strategic intelligence and a newly modernized Ultimate PRD that has passed the 1F validation gate. Your job is to expand the Ultimate PRD's Phase 1 section into an exhaustive, hyper-granular blueprint that a downstream execution agent (Step 11) will use to write the actual code.

YOUR INSTRUCTIONS:

1. CONTEXTUALIZE (REPO SCAN):

Use Nia MCP to scan and read through all the files in this repository. Locate the strategic context and the Ultimate PRD. Internalize:
- The target's enterprise bottlenecks (cited from context.md / intel_cleaned.md)
- The strict "Anti-Replication" boundaries (per ULTIMATE_PRD.md §6)
- The allowed Google Gemini LLM routing stack (per docs/modernization_log.md)
- Any positioning pivots from positioning_final.md (Step 09 output)

If validation_gate.md or positioning_final.md indicate REPOSITION-REQUIRED, apply the positioning edits to the language used in PHASE_1_SPEC.md's acceptance criteria — the technical spec doesn't change, but the way it's described should align with the new framing.

2. RESOURCE INDEXING (NIA MCP):

Do NOT hallucinate API syntax. Use your Nia MCP server to fetch the latest technical documentation for any specific third-party services, SDKs, or libraries mentioned in the PRD:
- Google GenAI SDK (current generate_content signature, region binding, zero-retention flags)
- FastAPI (current routing patterns, exception handling, dependency injection)
- Pydantic v2 (ConfigDict patterns, field validation, custom validators)
- SQLAlchemy 2.0 (async session, relationship patterns)
- Alembic (current migration patterns)
- Celery / asyncio (per the architecture's chosen task orchestration)
- Streamlit / Next.js / React (per the chosen frontend stack)
- Any other libraries the Ultimate PRD specifies

3. EXPAND & SAVE (THE DELIVERABLE):

Generate a new file called PHASE_1_SPEC.md. Break down Phase 1 from the Ultimate PRD into exhaustive, file-by-file, function-by-function technical requirements.

OUTPUT STRUCTURE — PHASE_1_SPEC.md:

## §0 — Phase Plan Header

Reaffirm: "This is Phase 1 of N phases in the Sprint [SPRINT_NUMBER] build." State which Ultimate PRD sections this Phase implements (e.g., "Implements ULTIMATE_PRD §3.1, §3.2, §3.10").

## §1 — Files Added or Modified

Exhaustive list of every file Phase 1 touches:
- Path
- Brief description of purpose
- Whether it's new (created) or modified (extending existing)
- The Ultimate PRD section it implements

## §2 — Pip Dependencies

Every new pip dependency with pinned version (from docs/modernization_log.md). Justify each one — what does it do, why is it needed in Phase 1 specifically.

## §3 — Pydantic Schemas

Every BaseModel Phase 1 introduces:
- Class name
- ConfigDict (must have extra="forbid")
- Field-by-field definition (name, type, validators, default if any)
- Brief description of what this schema represents
- Whether it's input/output/internal

## §4 — FastAPI Route Signatures

Every API route Phase 1 exposes:
- Path
- HTTP method
- Request schema (reference §3)
- Response schema (reference §3)
- Status codes (200, 4xx, 5xx) and conditions
- Exception handlers
- Dependencies (auth, db session, etc.)

## §5 — Alembic Migration (if applicable)

If Phase 1 introduces new database state:
- Migration name
- New tables (column-by-column with types, constraints, indexes)
- New columns on existing tables
- Foreign key relationships
- Migration ordering relative to prior migrations

## §6 — Implementation Logic Flow

For each major function or class Phase 1 introduces:
- Function/class name
- Inputs (with types)
- Outputs (with types)
- Step-by-step logic flow (numbered, plain English describing what the code does — not the code itself)
- External calls (Vertex AI, database, Slack, etc.) and retry behavior
- Error cases and how they're handled
- Idempotency strategy (if applicable)

This is the most granular section. The downstream execution agent (Step 11) should be able to write the code from this section alone.

## §7 — Cross-Phase Integration Requirements

What this phase depends on from prior phases (n/a if Phase 1). What it produces that subsequent phases will consume.

## §8 — Phase Acceptance Criteria

Specific testable criteria that must pass to consider Phase 1 complete:
- Smoke test (the single command that proves the phase ships, e.g., `docker compose up trigger=smoke_test_event`)
- Per-magic-moment timing checks (e.g., "Magic Moment 1 fires at T+8s±2s")
- Database state assertions (e.g., "outbox table has exactly N rows of type X after smoke test")
- Log assertions (e.g., "no exceptions in refinery_worker logs after smoke test completes")

## §9 — Explicit NON-GOALS

What is NOT built in Phase 1. Be explicit. This prevents the execution agent from scope-creeping into Phase 2 territory.

CRITICAL BOUNDARY:

Do NOT write the actual application code. Write the airtight, hyper-granular blueprint that the execution agent will use to write the code.

Do NOT touch Phase 2 or Phase 3 specs. Only Phase 1.

If the Ultimate PRD's Phase 1 section is too thin to expand into a complete PHASE_1_SPEC.md, halt and report — the spec authors (Step 04 + Step 07) need to revisit. Do not invent Phase 1 details that aren't grounded in the Ultimate PRD.

HANDOFF:

Acknowledge when PHASE_1_SPEC.md is successfully saved to the directory and committed. Output the commit SHA and word count.

```
Phase 1 spec: COMPLETE
File saved:   PHASE_1_SPEC.md
Word count:   <approx>
Commit:       <SHA>
Branch:       main
Nia index:    updated
Status:       Ready for Step 11 (Phase Build Execution)
```
```

---

## Customization checklist

- [ ] None — this template runs against the validated Ultimate PRD
- [ ] Verify Step 09 issued CLEAR-TO-SHIP or REPOSITION-REQUIRED (do NOT run on KILL-AND-RESTART)
- [ ] Verify Nia MCP is connected
- [ ] Confirm Opus 4.7 is the active model in Claude Code

## Downstream

PHASE_1_SPEC.md feeds Step 11 (Phase Build Execution). Step 11 reads this spec and writes the actual Phase 1 code. Step 12 reviews Step 11's output and either approves or patches, then writes PHASE_2_SPEC.md (and so on for Phase 3 through Phase N).

## Quality bar

The execution agent (Step 11) should never have to "interpret" PHASE_1_SPEC.md. If the spec has ambiguity, the execution agent makes a judgment call — and judgment calls under ambiguity are where the Matta one-shot debug tax came from. Spec tightness is the load-bearing discipline.
