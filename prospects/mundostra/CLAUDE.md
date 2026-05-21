---
type: navigation
prospect: mundostra
last_updated: 2026-05-19
key_tags:
  - corporate_travel
  - travel_os
  - no_markup_model
  - multi_agent_support
  - completed_demo
  - dual_founder_dynamics
---

# Mundostra — Substrate Navigation

Mundostra is a closed-loop prospect engagement. The Multi-Agent "Self-Healing Support" demo was built and pitched.

## Files in this folder

- `overview.md` — company snapshot (Travel OS positioning, no-markup model, dual-founder dynamics)
- `architecture.md` — full Self-Healing Support architecture (Orchestrator + Research + Policy + Comms multi-agent stack)
- `outcome.md` — *(awaiting population — engagement outcome and learnings)*

## Pattern signals (when this prospect's shape is relevant)

Future prospects matching any of these signals should retrieve Mundostra content during architecture generation:

- **Event-driven support workflow** — webhook fires, system must triage + decide + act in real-time
- **Multi-agent parallel decisioning** — research + policy + communication are separable concerns that can run in parallel before synthesis
- **Cost-pressure on human ops** — business model can't afford human-loop on every event (or human-loop is the explicit competitive disadvantage)
- **Dual-founder dynamics** — business co-founder + technical co-founder with distinct buying triggers (ROI/margins vs. DX/architecture)
- **CFO-skepticism on a transparency claim** — buyer needs *automated audit trails* to validate a commercial promise

## Architecture pattern name

**"Multi-Agent Parallel Triage + HITL Escalation"** — webhook-triggered orchestrator that dispatches specialist agents (research, policy, communications) in parallel, synthesizes with a confidence score, and escalates to human only below threshold. Theatre layer: real-time agent activity stream + cost ticker + JSON inspector designed for *both* a business buyer (ROI) and a technical buyer (architecture proof).
