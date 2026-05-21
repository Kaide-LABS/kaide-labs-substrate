---
type: prospect_architecture
prospect: mundostra
vertical: corporate_travel_expense
demo_name: multi_agent_self_healing_support
sprint_duration: 72_hours
last_updated: 2026-05-19
canonical: true
architecture_pattern: multi_agent_parallel_triage_hitl_escalation
key_tags:
  - multi_agent
  - parallel_dispatch
  - orchestrator_specialist_pattern
  - gemini_pro_orchestrator
  - gemini_flash_specialists
  - confidence_score_hitl
  - cost_ticker
  - json_inspector_theatre
  - downstream_sidecar
  - webhook_triggered
---

# Mundostra — Architecture: Multi-Agent "Self-Healing Support"

## Sprint scope

72-hour high-impact prototype solving Mundostra's biggest operational pain point — support latency — by resolving flight disruptions in under 60 seconds for pennies on the dollar.

**Dual-purpose demo:** provides Mundostra with a functional prototype of an autonomous support system, while simultaneously proving the value of an AI-enhanced development sprint methodology to a founder team that respects technical depth (Piyush) and operational ROI (Vinuta).

## Anti-Replication boundary

The sidecar operates strictly **downstream** of Mundostra's core Travel OS infrastructure. It consumes events emitted by the booking system, queries existing data sources, and produces communications and authorization updates — but never modifies the underlying booking infrastructure or virtual card issuance logic.

**Specifically NOT touched:**
- Mundostra's booking inventory connections (airlines, hotels, GDS)
- Mundostra's identity management and traveler-state infrastructure
- Mundostra's virtual card issuance system (the sidecar updates *authorizations* on existing cards, doesn't issue cards)
- Mundostra's policy engine itself (the sidecar *reads* policy JSON, doesn't define or evaluate policy primitives)
- The "Travel OS" architecture

The sidecar is a containerized API endpoint Mundostra plugs into their webhook stream. If they unplug it tomorrow, their booking system still works (human ops returns to 3+ hour triage latency).

## The architectural thesis

The system uses a multi-agent architecture, assigning specific Google Gemini models to roles based on their absolute strengths. All routing stays entirely within the Google ecosystem (Vertex AI).

### The four agents

| Agent | Model | Role | Why this model |
|---|---|---|---|
| **Orchestrator** | Gemini Pro (Vertex AI) | Receives the event, decomposes into tasks, dispatches specialists in parallel. Synthesizes results, ranks options, makes final decision with confidence score (0-1). If confidence < 0.7, escalates to HITL (human-in-the-loop). | Reasoning depth — must integrate research + policy + traveler context and make a defensible decision. |
| **Research Agent** | Gemini Flash (Vertex AI) | Queries mock flight inventory APIs for alternatives. Checks traveler's calendar for conflicts. Compares prices. | Rapid information synthesis. Large context handling for inventory comparisons. |
| **Policy Agent** | Gemini Flash (Vertex AI) | Evaluates flight alternatives against the company's travel policy JSON. Checks budget caps, role-based thresholds, auto-approval limits. | Fast, structured rule-following. Cheap to run on every event. |
| **Communications Agent** | Gemini Flash (Vertex AI) | Generates the traveler-facing message (Slack notification). Ensures empathetic, professional tone. Translates complex flight data into clear options. Formats output (Slack Block Kit). | Tone calibration + structured output formatting. |

## End-to-end scenario (the "Nitty Gritty" flow)

The demo scenario is a real-shape flight cancellation:

1. **Trigger** — webhook fires indicating Sarah Chen (VP of Engineering)'s 4:00 PM flight from SFO to JFK is cancelled. She has a 10:00 AM meeting tomorrow.

2. **Plan** — Orchestrator receives the event and simultaneously dispatches the Research and Policy agents.

3. **Parallel execution:**
   - Research (Gemini Flash) finds 7 alternative flights, checks Sarah's calendar, narrows it down to 3 viable options
   - Policy (Gemini Flash) reads the "Executive" tier policy and determines the $800 budget cap and $100 auto-approve delta

4. **Synthesis** — Orchestrator merges the data. Sees UA105 is $420, no calendar conflicts, well under auto-approve limit. Scores confidence at 0.94 and selects it.

5. **Comms** — Gemini Flash drafts a Slack message explaining the cancellation and offering the UA105 alternative.

6. **Action** — Slack message sent. Virtual card authorization automatically updated. Audit log written.

**End-to-end metrics:** ~34 seconds total. ~$0.02 in API calls.

The number Mundostra cares about: a process that takes 3+ hours of human ops time today happens in under 60 seconds for two cents.

## The Theatre — dual-buyer dashboard

The frontend is a Next.js real-time command center designed to visually prove the architecture to *both* founders simultaneously. The dashboard is the centerpiece of the demo because it lets each founder see the dimension they care about most:

### For Vinuta (CEO/Product — margin-driven)

- **Cost Ticker** — live-updates the API cost during agent execution. Kills the "AI is expensive" objection in real time. Vinuta sees $0.02 accumulate while a human ops person would cost $X for the same triage.
- **Agent Activity Stream** — shows the latency collapse. Vinuta sees the 34-second wall-clock time vs. the 3+ hour status quo.
- **Synthesis verdict with confidence score** — shows the auto-approval logic firing. Vinuta sees the no-human-loop economics in action.

### For Piyush (Tech/Co-Founder — architecture-driven)

- **Agent Activity Stream & Timeline** — visualizes the *parallel* execution of Research and Policy agents. Critically proves the architecture isn't a sequential chain (which would be slower and more expensive).
- **Model Usage** — shows the exact model calls routed through Google Cloud (which model for which task, with cost-per-call). Piyush sees the model-selection rationale.
- **JSON Inspector** — built specifically for Piyush. He can click any step in the timeline to view the raw JSON input/output payloads. Proves it's real code and not a hardcoded slide deck.

### Why this dual-buyer dashboard works

Most demos optimize for either a business buyer (slick outcomes, no internals) or a technical buyer (raw architecture, no story). Mundostra's founder team requires both simultaneously. The dashboard exposes both surfaces at once — Vinuta watches the Cost Ticker and outcome; Piyush watches the parallel execution timeline and JSON payloads. Same demo. Two satisfied viewers.

## Technical stack

**Backend:**
- Python 3.12+
- FastAPI (async-native, WebSocket support for the real-time dashboard)

**Frontend:**
- Next.js 14
- Tailwind CSS
- Framer Motion (for agent activity animations — load-bearing for the theatre)
- Native WebSockets (for real-time updates)

**Infrastructure:**
- Google Cloud Run (serverless containers — sidecar deploys as a single container)
- Google Vertex AI (Gemini Pro + Gemini Flash APIs, region europe-west4 for EU prospects or us-east1 for US deployments)

## Magic Moment definition

**The 60-second Magic Moment:** Webhook fires (simulated flight cancellation), Orchestrator dispatches in parallel, Research and Policy agents run simultaneously (visible on dashboard timeline), synthesis happens with visible confidence score, Slack message draft appears, virtual card authorization update fires, audit log entry is written. Wall-clock time: ~34 seconds. Cost Ticker stops at ~$0.02.

The "magic moment" is the visible collapse of 3+ hours of human ops into 34 seconds, with the cost theatre running in parallel so the viewer sees exactly *how cheap* the autonomous workflow is.

## 5-Pillar verdict

1. **Bottleneck Assassin:** ✅ — directly solves support latency, the structurally-load-bearing pain point of the no-markup business model
2. **Anti-Replication:** ✅ — operates strictly downstream of the Travel OS; never modifies booking infrastructure or virtual card issuance
3. **Native Environment:** ✅ — Slack (where corporate travelers actually receive operational comms) + the operations team's dashboard
4. **Magic Moment:** ✅ — 3+ hours collapses to 34 seconds, with cost theatre visible in real time
5. **System Resilience & Immunity:** ✅ — confidence-score HITL escalation (below 0.7 → human review); deterministic policy-evaluation layer; audit log on every action

## Summary

The Self-Healing Support demo proves to Mundostra that their structural margin disadvantage (can't afford call centers because of the no-markup model) can be converted into a competitive advantage (autonomous triage cheaper and faster than competitors' human ops). The multi-agent architecture is deliberately not just "one big LLM call" — the parallel orchestration is load-bearing for both the cost story (Gemini Flash for 3 of 4 agents) and the architectural credibility (Piyush needs to see specialist agents, not a monolith).

## Reusability for future prospects

This architecture is a reference pattern for any future prospect matching:
- Event-driven support or operational workflow (webhooks, alerts, status changes)
- Multiple separable decisioning concerns that can run in parallel before synthesis (research + policy + communications, or any analogous decomposition)
- Cost pressure on human ops (business model can't afford human-loop on every event)
- Dual-buyer founder dynamics requiring both ROI theatre and architecture theatre simultaneously
- A clear downstream wedge (the core engine has already produced an event; the sidecar handles what happens *next*)
