---
type: pipeline_template
step_number: 04
pipeline_stage: 1B
step_name: prd_redteam_and_master_prd_generation
runs_in: claude_web_opus
last_updated: 2026-05-19
canonical: true
key_tags:
  - prd_redteam
  - master_prd
  - architecture_selection
  - anti_replication_audit
  - five_pillar_audit
  - step_1b
  - claude_web_opus
inputs:
  - "Kaide_Labs_SOP_Claude.md (the Claude-side SOP with benchmarks)"
  - "Kaide_Labs_Identity.md (canonical positioning)"
  - "Source Intelligence (cleaned intel + founder dossier + company context)"
  - "TARGET_BRIEF.md (Step 03 output — Gemini's 3 proposals)"
outputs:
  - "Master_PRD.md (the surviving lethal architecture with full execution spec)"
upstream_step: step_03_target_brief_synthesis
downstream_step: step_05_prd_modernization
---

# STEP 04 — PRD RED-TEAM + MASTER PRD GENERATION TEMPLATE

> **Customization surfaces:** Minimal — this template runs against attached files. The customization is in the inputs (the SOP, identity, intel, dossier, target brief).
> **Recommended runner:** Claude Web with Opus 4.7. This is the highest-leverage strategic judgment moment in the pipeline; do not economize on model selection.
> **Dependencies:** `Kaide_Labs_SOP_Claude.md`, `Kaide_Labs_Identity.md`, intel + dossier + TARGET_BRIEF.md all attached.

---

```
Act as a ruthless Principal Staff Engineer. I have attached four categories of files for this FDE build:

1. The SOP: Kaide_Labs_SOP_Claude.md (Your core instructions and FDE benchmarks — Renlo and Mundostra reference architectures).
2. Kaide_Labs_Identity.md (Canonical Kaide Labs positioning — the 5-Pillar Standard, Anti-Replication Principle, DMZ Rule).
3. The Source Intelligence: Multiple files containing the target company's context, founder psychology, and core IP constraints (cleaned intel + founder dossier).
4. The Proposals: Gemini's drafted architectures (TARGET_BRIEF.md from Step 03).

Read the Identity file first to internalize positioning. Then the SOP file (this contains the 5-Pillar audit framework and architectural benchmarks). Then execute the 5 tasks outlined in Part 2 of the SOP, red-team Gemini's proposals against the Source Intelligence, and output the Master PRD.

EVIDENCE-BASED SELECTION (CRITICAL): When you select the winning architecture and draft the "FDE Thesis," you must explicitly cite the strategic intelligence that justifies your choice. Quote the specific founder posts, company bottlenecks, or target context that makes this architecture lethal. Do not just tell me the architecture is good; prove it by linking it directly to the founder's own words and stated pain points.

OVERRIDE AUTHORITY: You don't have to stick to the suggestions Gemini gives. Feel free to surface alternate architectures based on the context and intel you've got. The Gemini proposals are candidates, not constraints. If all three are weak, kill all three and propose your own architecture — but cite the same source intelligence rigor.

EXECUTION TASKS (from SOP Part 2):

1. The Source Check: Analyze the target's context. Did Gemini's proposals miss massive red flags or fail to exploit macroeconomic/operational threats? Surface what was missed.

2. The Ego Check (Anti-Replication audit): Do any of Gemini's workflows get too close to the target's Core IP? If an architecture looks like something their own engineers are already building natively, flag it and kill it. Reference the target's specific shipped/announced features.

3. The 5-Pillar Audit: For surviving proposals, evaluate against:
   - Bottleneck Assassin (does it solve a real, expensive, citation-anchored pain?)
   - Anti-Replication (is it strictly outside their core IP?)
   - Native Environment (does the UI live where users already work?)
   - Magic Moment (is there a tangible ROI moment in <60 seconds?)
   - System Resilience (deterministic safety rails present?)

4. The Final Verdict: Select the absolute strongest, most lethal architecture. Refine the multi-agent routing to be completely bulletproof. If you're surfacing your own architecture (not from Gemini's set), make this explicit and explain why.

5. The Master PRD Deliverable: Generate the Master Product Requirements Document.

OUTPUT FORMAT — Master_PRD.md (inside a markdown code block):

# Master PRD — [TARGET COMPANY]

## §1 — The FDE Thesis

A concise summary (3-5 paragraphs) of how this architecture hits the 5-Pillar Standard, with citations from the Source Intelligence justifying every claim.

## §2 — Founder Psychology Synthesis

Brief recap of the founder profiling, with emphasis on how it shaped the architectural choice. Cite Step 02 dossier.

## §3 — System Architecture & Agent Routing

A detailed map of the data flow and LLM routing (restricted to Google Gemini models — Vertex AI region binding required for EU prospects).

- Event/trigger: [what initiates the workflow]
- Agent 1: [Gemini model] — [exact role, input, output, retry/fallback behavior]
- Agent 2: [Gemini model] — [same]
- [... for each agent]
- Synthesis layer: [how outputs combine, confidence scoring, escalation logic]
- Deterministic safety rails: [the hardcoded Python rules engine layer that prevents hallucination]
- Output surface: [where the result lands — Slack, Drive, CRM, etc.]

## §4 — The "Native Environment" UI Spec

Exact requirements for the frontend "Theater" to ensure visual proof:
- Surface 1: [Slack canvas / CRM / Drive panel / etc.] — [what renders, when, why visible]
- Surface 2: [next surface]
- [...]
- Magic Moment timing: T+Xs / T+Ys (precise seconds for each magic moment to fire)
- Theatre layer requirements: [Cost Ticker / Agent Activity Stream / JSON Inspector / etc. — per the dual-buyer dynamics from the founder profile]

## §5 — Anti-Replication Boundary

Explicit named list of customer-side IP that the sidecar MUST NOT touch:
- [Specific feature 1] (operated by their core engine — citation to their feature page)
- [Specific feature 2] (operated by their core engine — citation)
- [...]

Plus the explicit "what happens if customer unplugs us tomorrow" answer.

## §6 — Phase 1 Execution Spec

Exact, step-by-step technical instructions to build the minimum viable foundation:
- Files to create
- Pydantic schemas
- FastAPI route signatures
- Required pip dependencies
- Implementation logic flow per function
- Acceptance criteria (smoke tests that must pass to ship Phase 1)

This Phase 1 spec is what Step 05 modernizes and Step 10 expands into PHASE_1_SPEC.md.

## §7 — Killed Proposals (Receipts)

For each of Gemini's proposals that you killed:
- Proposal name
- Reason for kill (which filter triggered)
- Specific source intelligence that made the kill necessary

This section is non-negotiable — it proves rigor and builds credibility internally.
```

---

## Customization checklist

- [ ] Attach `Kaide_Labs_SOP_Claude.md`
- [ ] Attach `Kaide_Labs_Identity.md`
- [ ] Attach Source Intelligence: `*_intel_cleaned.md` + `dossier_*.md`
- [ ] Attach `TARGET_BRIEF.md` (Step 03 output)
- [ ] Confirm Claude Web is using Opus 4.7 (not Sonnet 4.6)
- [ ] No `[CUSTOMIZE]` placeholders to fill in the prompt itself — the customization is in the inputs

## Downstream

Master_PRD.md feeds Step 05 (PRD Modernization & Dependency Audit). The Master PRD's architectural logic is locked at this point — Step 05 only modernizes SDK syntax and model strings, not the architecture itself.

## Override Authority Reminder

This is the strategic judgment moment. Don't rubber-stamp Gemini's proposals. If all three fail filters, kill all three and propose your own. The substrate prefers a sprint that ships fewer architectures with higher conviction over a sprint that ships a Gemini-anchored architecture because "we already have three to choose from."
