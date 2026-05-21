---
type: pipeline_template
step_number: 06
pipeline_stage: 1D
step_name: lateral_architecture_exploration
runs_in: codex_cli
last_updated: 2026-05-19
canonical: true
key_tags:
  - lateral_thinking
  - architecture_variants
  - divergent_thinking
  - codex_cli
  - cross_model_bias_mitigation
  - step_1d
inputs:
  - "Modernized Master_PRD.md (Step 05 output)"
  - "context.md (Source Intelligence)"
outputs:
  - "LATERAL_PRD_v1.md, LATERAL_PRD_v2.md, LATERAL_PRD_v3.md, LATERAL_PRD_v4.md"
upstream_step: step_05_prd_modernization
downstream_step: step_07_architectural_synthesis
---

# STEP 06 — LATERAL ARCHITECTURE EXPLORATION TEMPLATE

> **Customization surfaces:** `[INSERT SPECIFIC BOTTLENECK HERE]` — fill in the one-sentence bottleneck statement from the Master PRD.
> **Recommended runner:** Codex CLI. The point of this step is cross-model divergent thinking; running it in Claude Code (the same model that wrote the Master PRD) defeats the purpose.
> **Why Codex specifically:** Different training distribution means different stylistic and architectural priors. Codex surfaces lateral options Claude wouldn't because Claude pattern-matches to a stronger central tendency.

---

```
Role: Act as the Principal R&D Architect and Lateral Thinker.

Context: We have a modernized Master_PRD.md that outlines a core "sidecar" solution for our target enterprise company. However, before we lock in this exact execution path, we need to explore lateral implementations.

SYSTEM OVERRIDE: ARCHITECTURAL ALIGNMENT

You are strictly forbidden from inventing new products, finding new business problems, or expanding the scope.

The singular core bottleneck we are solving is: [INSERT SPECIFIC BOTTLENECK HERE — copy verbatim from Master PRD §1 FDE Thesis]

All lateral PRDs must solve ONLY this bottleneck. Do not invent adjacent products. I want 4 distinct architectural approaches (different agent pipelines, different UI entry points, different data flows) to solve this exact same problem.

YOUR INSTRUCTIONS:

1. CONTEXT INGESTION (NIA REPO SCAN MANDATORY):

Before generating anything, explicitly use your Nia MCP server tools to scan the local directory. You must read:
- context.md (Source Intelligence)
- The modernized Master_PRD.md
- Any other foundational research files

Do not proceed until Nia has returned the contents of these files.

2. THE LATERAL ITERATION TASK:

Invent 4 completely distinct architectural iterations of how we could build this specific sidecar. Fundamentally rethink:
- Data flow (event-driven vs. polling vs. user-initiated vs. scheduled)
- Agent routing (orchestrator + specialists vs. peer-to-peer vs. pipeline vs. consensus)
- UX entry point (Slack vs. embedded panel vs. mobile PWA vs. CLI vs. email-shaped vs. webhook-only)
- Data structures (transactional outbox vs. event sourcing vs. stream processing vs. batch)
- Theatre surface (real-time dashboard vs. async report vs. ambient notification vs. on-demand pull)

Adhere strictly to:
- The Google Gemini LLM stack (no OpenAI, no Anthropic, no other providers in customer-facing demo code)
- The "Anti-Replication" rule (do not touch their core proprietary engine)
- The Native Environment principle (UI must live where the user already works)

Iterations should differ meaningfully — variants that only swap one button color or change a model from Flash to Pro don't count. Real architectural differentiation.

3. THE DELIVERABLE (MULTI-FILE OUTPUT):

Autonomously create 4 separate files in the repository: LATERAL_PRD_v1.md, LATERAL_PRD_v2.md, LATERAL_PRD_v3.md, and LATERAL_PRD_v4.md.

Each file's output must include:

## §1 — The Concept

A summary of the new lateral approach to the core bottleneck. One paragraph. State the architectural shift compared to Master_PRD.md.

## §2 — The Strategic Hook

Cite specific evidence from context.md (Source Intelligence) explaining why the founders might prefer this delivery method over the Master PRD shape. Quote founder statements verbatim where possible.

## §3 — The Agent Architecture

An exhaustive map of how Google Gemini models pass data to each other in this version:
- Event/trigger
- Agent 1 (model, role, input, output)
- Agent 2 (model, role, input, output)
- [...for each agent]
- Synthesis or output handling
- Deterministic safety rails

## §4 — The "Native Environment" UI Spec

Exact requirements for the frontend in this variant:
- Primary surface(s)
- Theatre elements visible to which buyer (per dual-buyer dynamics if applicable)
- Magic Moment timing

## §5 — Phase 1 Execution Spec

Step-by-step technical instructions to build the MVP of this lateral iteration:
- Files to create
- Pydantic schemas
- Route signatures
- Dependencies
- Acceptance criteria

## §6 — Trade-offs vs. Master_PRD.md

Explicit comparison:
- What this iteration does better than the Master PRD
- What this iteration does worse
- Implementation cost delta (faster/same/slower to build)
- Risk delta (safer/same/riskier execution)

This section is mandatory. Without it, Step 07 (Architectural Synthesis) can't intelligently select which features to "Frankenstein" from each variant.

HANDOFF FORMAT:

```
Lateral exploration: COMPLETE
Variants produced:   4
Files created:       LATERAL_PRD_v1.md, LATERAL_PRD_v2.md, LATERAL_PRD_v3.md, LATERAL_PRD_v4.md
Commits:             <SHAs>
Branch:              main
Nia index:           updated
Status:              Ready for Step 07 (Architectural Synthesis — Frankenstein into Ultimate PRD)
```
```

---

## Customization checklist

- [ ] `[INSERT SPECIFIC BOTTLENECK HERE]` — copy verbatim from Master_PRD.md §1 FDE Thesis
- [ ] Confirm Codex CLI is the runner (not Claude Code)
- [ ] Verify Nia MCP is accessible from Codex CLI
- [ ] Verify GitHub auth is configured for the multi-file push

## Downstream

The 4 LATERAL_PRD_v#.md files plus the modernized Master_PRD.md become inputs for Step 07 (Architectural Synthesis — Ultimate PRD generation). Step 07 selects the best features from across all five PRDs and synthesizes them into a single ULTIMATE_PRD.md.
