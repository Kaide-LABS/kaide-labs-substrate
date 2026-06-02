---
type: prospect_outreach
prospect: matta
status: not_yet_sent
target_contact: doug_brion
channel: cold_email
last_updated: 2026-06-02
key_tags:
  - cold_email
  - doug_brion
  - vidyard_demo
  - not_yet_sent
  - no_follow_up_discipline
---

# Matta — Outreach

**Status: NOT YET SENT. Outcome open.** This file is the send plan, not a record of a
sent campaign.

## Target contact + why

**Doug Brion — Co-Founder & CEO.** He is the buyer-level nerve for this build: the person
who feels the cost of every misallocated deployment slot, who personally absorbs the
trade-show triage burden, and whose own published work on uncertainty quantification makes
him uniquely able to evaluate the briefing's confidence layer on its merits. He is
pragmatic, anti-hype, and metric-driven — the pitch must be concrete and physical-first,
not a SaaS deck.

Secondary readers to design for (not separate sends): **Damjan Denic (CTO)** will inspect
the repo for schema hygiene and transaction semantics, and **Sebastian Pattinson (Chief
Scientist)** will read the briefing artifact for whether its uncertainty is calibrated or
decorative. The email targets Doug; the demo asset and repo are built to survive both of
them.

## Channel

Cold email to Doug Brion. No confirmed warm path yet — **[VERIFY]** Brion's best email
and any warm intro through the Lakestar / Cambridge IfM network before sending. If a warm
intro is available, prefer it over cold.

## The hook (client-facing — jargon-free)

One-line wedge, built from the bottleneck + Matta's own public positioning:

> *"You deploy a new factory line in hours, but you're choosing ~two a month from a
> waitlist that's years deep — and the week before each visit, you and your team are still
> hand-assembling the 'is this one worth a slot, and what will we hit on site' research.
> We built you a tool that reads your trade-show lead list, ranks it against the lines
> you've actually deployed to, and writes the pre-visit briefing for the top candidates —
> so the triage is done before your next hire walks in. It never touches your cameras or
> models; if you don't like it, you unplug it and nothing changes."*

Keep it in Doug's lexicon — shop floor, scrap, deployment slots, plug-and-play. Lead with
deployment-velocity language he already uses publicly. **No internal vocabulary in any
client-facing copy** (no "sidecar," "Refinery" codename, "anti-replication," "DMZ,"
"1F-red," "magic moment," or any mention of prior architecture iterations).

## The demo asset

Hybrid: a short **Vidyard why-video** (the ~85-second pre-baked quickdemo run showing the
CSV → ranked top-12 → William Cook briefing materializing across Slack / CRM / Drive,
with the "this customer reference was selected deterministically, not invented by a model"
beat) **+ a hosted interactive sandbox** at the `/sandbox?mode=quickdemo` URL so Doug can
click through it himself, **+ the GitHub repo link** for Damjan to inspect schemas and the
transactional outbox directly.

The cold email points to the Vidyard timestamp where the briefing completes (per the
standard outreach mechanic). **Open question before send:** confirm the hosted sandbox is
publicly reachable and stable, or fall back to video-only — the full cold run is ~7–9
minutes, so any live click-through must use the pre-baked quickdemo path, never a cold CSV
drag. **[VERIFY] deployable** — the build runs locally via Docker Compose; a hosted,
stable, public deployment is not yet confirmed.

## Follow-up cadence

**No follow-up after no response.** Per the Tracelight learning
(`no_follow_up_discipline_preserves_optionality`, see `prospects/tracelight/outcome.md`):
after a cold email + demo, no response = no signal, and a follow-up converts ambiguity
into an explicit no while damaging re-engagement optionality. One well-built send; if Doug
engages, move to a 15-minute call; if silence, hold and preserve the relationship for a
future angle. (A single soft touch is acceptable only if a warm-path intro explicitly
invites it.)

## Open items before send

- [ ] Demo deployed to a stable, publicly-reachable host (or decision to go video-only)
- [ ] Vidyard why-video recorded, captioned, and timestamp-marked at briefing completion
- [ ] Doug Brion's email verified (and any warm Lakestar / Cambridge IfM intro explored)
- [ ] Final client-facing copy scrubbed of all internal vocabulary
- [ ] Hosted sandbox confirmed running the pre-baked quickdemo path (not a cold run)
- [ ] Hafeedh review of all five substrate files complete and push authorized
