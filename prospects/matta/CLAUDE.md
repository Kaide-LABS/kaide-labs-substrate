---
type: navigation
prospect: matta
status: live_demo_built_not_sent
last_updated: 2026-06-02
key_tags:
  - industrial_ai
  - manufacturing_qa
  - defect_detection
  - pre_deployment_intelligence
  - stateful_sidecar
  - cambridge_spinout
  - lakestar_seed
  - live_demo_built_not_sent
  - validation_gate_evidence
---

# Matta — Substrate Navigation

Matta (https://www.matta.ai/) is a Cambridge spin-out building industrial AI for
factory-floor visual defect detection. The demo built for them is **The Refinery** —
a stateful, two-stage **pre-deployment intelligence sidecar** that ingests trade-show
lead lists, ranks them against Matta's deployment capacity and verified deployment
patterns, and on-demand composes audit-grade pre-visit briefings for the top-ranked
prospects. It is the **third** architecture cycle on this prospect (two prior cycles
killed at the 1F-red gate). Engagement status: **LIVE — sprint closed, demo built,
not yet sent.** Outcome OPEN.

One-line thesis: Matta deploys factory inspection in hours but has a multi-year /
~300-factory waitlist against ~two deployments a month — the bottleneck is *which leads
to deploy to next and how to prep the visit*, and The Refinery automates that triage
without ever touching Matta's detection IP.

## Files in this folder

- `CLAUDE.md` — this file (navigation, pattern signals, cross-prospect anchors)
- `overview.md` — company snapshot, founder team, the pre-deployment bottleneck, anti-replication reasoning, status stub
- `architecture.md` — full Refinery architecture (two-stage stateful pipeline, exact model strings, state mechanism, theatre, live-vs-mocked, PRD-vs-shipped audit, 5-pillar verdict)
- `outreach.md` — cold-email plan to Doug Brion (CEO); client-facing hook, demo asset, cadence, open items — *outcome open, not yet sent*
- `kill_log.md` — INTERNAL-ONLY audit trail of the two killed architecture cycles (1F-red in action)

## Pattern signals (positive comps for live prospect-matching)

Future prospects matching any of these signals should retrieve Matta/Refinery content
during architecture generation. Unlike Tracelight/Ylookup, these signals are **for
matching** (clean vertical — no sourcing caveat):

- **Capacity-bound deployment pipeline** — prospect deploys fast but is throughput-capped (N deployments/period) against a long waitlist; the scarce resource is *deployment-slot allocation*, not lead capture
- **High-volume inbound triage burden** — trade-show / waitlist inbound (100+ leads in days) that founders or a Chief-of-Staff hire personally triage by hand before any deployment decision
- **Pre-engagement scoping research** — material founder/FDE hours spent in the days before a customer visit assembling fit, comparable-customer, and integration-risk research from scattered public sources
- **Regulated/physical-procurement uncertainty rigor** — buyer-side scientist who will deconstruct any confidence claim to first principles, so surfaced uncertainty must be calibrated and honestly bounded, not decorative
- **Absorbable stack-mirror** — prospect's own backend stack (here FastAPI/Pydantic/Postgres/SQLAlchemy/Redis/Celery) can be mirrored so the CTO can absorb or unplug the sidecar with no new tooling

## Architecture pattern name

**"Stateful Pre-Deployment Intelligence Sidecar"** — a stateful, asynchronous,
desktop-anchored sidecar that sits *upstream of a deployment decision* (not upstream of
a data engine). It ranks a persistent, months-to-years-evolving prospect pipeline
against the customer's own deployment capacity and verified deployment patterns, then
composes deterministic-majority, citation-anchored briefing artifacts for the
human who makes the deployment call. Output lands in the team's native coordination
surfaces (Slack / CRM / Drive) as reversible projections; the sidecar never decides,
never touches the core product. Cross-ref `frameworks/architectural_patterns/stateful_pre_deployment_intelligence_sidecar.md`
(forward-ref — expected-missing until the engagement closes; see `kill_log.md` for why
the Matta pattern is held out of the library until outcome is known).

## Cross-prospect anchors — how this differs from the upstream-ingestion family

The Refinery is **not** an upstream-ingestion sidecar like Renlo or Ylookup, and the
distinction is load-bearing for retrieval:

- **Renlo (`prospects/renlo/`) / Ylookup** — *stateless ingestion* sidecars that sit
  upstream of a customer's data **engine**, converting unstructured field input into
  schema-validated records the engine consumes. Each interaction is independent; no
  pipeline state persists.
- **Refinery (Matta)** — *stateful intelligence* sidecar that sits upstream of a human
  **deployment decision**. Prospect entities persist in Postgres for months as the
  multi-year waitlist evolves; the sidecar re-scores and re-ranks a living queue. It
  produces decision-support artifacts for a human, not structured records for an engine.
  The "upstream" is a *go/no-go scoping moment*, not a data front door.
- **Artificial Societies (`prospects/artificial_societies/`)** — origin of the 1F-red
  validation gate that killed two Refinery predecessors. See `kill_log.md`.

`kill_log.md` is the internal 1F-red audit trail for this engagement — evidence the
validation gate works. **Internal only; never client-facing. Never referenced from
`outreach.md`.**
