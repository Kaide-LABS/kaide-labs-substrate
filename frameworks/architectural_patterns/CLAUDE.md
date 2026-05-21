---
type: navigation
domain: architectural_patterns_library
last_updated: 2026-05-19
key_tags:
  - architectural_patterns
  - reusable_sidecar_shapes
  - cross_prospect_retrieval
---

# Architectural Patterns — Substrate Navigation

Reusable sidecar architecture shapes abstracted from shipped Kaide Labs engagements. Each pattern documents the conditions under which it applies, the technical architecture, the anti-replication boundary, and prior worked examples.

When generating architecture for a new prospect (Step 03 in the pipeline), retrieve patterns from this folder FIRST. Pattern-match the new prospect's bottleneck shape against existing patterns before designing from cold. Compounding mechanism: 60% of architecture is pre-decided when a pattern matches.

## Patterns filed

| Pattern | Prior worked example | When to reach |
|---|---|---|
| `calibration_sidecar.md` | Artificial Societies / RCS | Customer has core engine requiring structured input; current ingestion is operator hand-translation or services-led onboarding |
| `safe_harbor_synthetic_data_fabric.md` | Tracelight | Customer's enterprise deals stall in InfoSec because prospects refuse to share real production data during evaluation |
| `voice_to_structured_action_pwa.md` | Renlo | Customer has mobile-first users who generate unstructured field input (voice/photo) that must become structured database records |
| `multi_agent_parallel_triage.md` | Mundostra | Event-driven workflow with separable decisioning concerns (research + policy + comms) that can run in parallel before synthesis |

## Patterns deferred

- **Bridge++** — webhook → CMMS routing → notification (Matta sprint). Deferred until Matta engagement closes. Writing the pattern from an in-flight engagement risks contamination; will file after demo records, cold email sends, and outcome is known.

## How to add a new pattern

When a sprint closes with a novel architectural shape worth abstracting:

1. Verify the shape is genuinely novel (not a customization of an existing pattern)
2. Create `[pattern_name].md` in this folder following the 9-section structure (see any existing pattern as canonical reference)
3. Update the "Patterns filed" table above
4. Cross-reference from the prior worked example's `prospects/[name]/architecture.md`

The substrate compounds only if patterns are filed as engagements close. There is no "I'll file it later."
