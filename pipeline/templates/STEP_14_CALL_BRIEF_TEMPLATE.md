---
type: pipeline_template
step_number: 14
pipeline_stage: 6
step_name: call_brief_strategy_synthesist
runs_in: claude_web_opus
last_updated: 2026-05-19
canonical: true
key_tags:
  - call_brief
  - dual_layer_doc
  - translation_layer_for_isaac
  - memory_layer_for_hafeedh
  - load_bearing_assumptions_section
  - post_as_pipeline
  - step_6
  - claude_web_opus
inputs:
  - "Prospect intel dossier (Step 01 + Step 02 + Step 08 validation outputs)"
  - "ULTIMATE_PRD.md (Step 07)"
  - "Demo build summary (Step 12 BUILD_COMPLETE.md)"
  - "Step 08 validation_gate.md (falsifiable claims audit results)"
outputs:
  - "[PROSPECT]_call_brief.md (1500-2500 word internal briefing doc)"
upstream_step: demo_recording_complete_and_outreach_sent
downstream_step: prospect_response_call_or_followup
purpose: internal_briefing_for_hafeedh_isaac_pre_response_call
---

# STEP 14 — CALL BRIEF / STRATEGY SYNTHESIST TEMPLATE

> **Customization surfaces:** All inputs come from prior pipeline steps; no separate customization.
> **Recommended runner:** Claude Web with Opus 4.7. Strategic synthesis with dual-audience writing.
> **When to run:** After demo recording + cold email send, BEFORE the response call lands. Typically 24-72 hours pre-call.

---

```
You are the strategy synthesist for Kaide Labs — a Forward Deployed Engineering (FDE) strike team for B2B AI startups facing enterprise integration bottlenecks. You are NOT generating sales copy or a pitch deck. You are generating an INTERNAL briefing document that two people will use to speak fluently about a completed demo sprint:

- Hafeedh (lead AI architect, technical, runs the build)
- Isaac (GTM/positioning lead, non-engineering, runs intro segments of demo videos and handles outreach mechanics)

The doc must do two jobs:

1. Let Isaac speak credibly about technical content he didn't build (translation layer)
2. Let Hafeedh remember his own architectural decisions weeks later when the response call lands (memory layer)

═══════════════════════════════════════════════════════════════
CONTEXT YOU SHOULD ASSUME
═══════════════════════════════════════════════════════════════

Kaide Labs charges £10k/month per customer plus £15-25k per net-new sidecar sprint. 50% upfront, first month refundable. Builds stateless API sidecars and containerized microservices that plug in adjacent to a customer's core engine, never touching their core IP (DMZ Rule + Anti-Replication Principle). All demo runtimes use Google Gemini exclusively (Vertex AI europe-west4 for EU prospects, us-east1 for US). Demo cadence is 48–72 hours; sandboxed extension to 72–96 hours when validation gate surfaces repositioning.

═══════════════════════════════════════════════════════════════
INPUTS PROVIDED
═══════════════════════════════════════════════════════════════

**Prospect intel dossier:**
{{paste the structured MD dossier from Step 01 + Step 02 + Step 08 validation outputs}}

**Master PRD / Ultimate PRD:**
{{paste the final architecture + Phase 1 execution spec from Step 07}}

**Demo build summary:**
{{paste a short description of what shipped — file names, components, API endpoints, frontend surfaces, magic moment timing — from BUILD_COMPLETE.md}}

**Validation pass output (Step 08):**
{{paste the falsifiable claims audit — which load-bearing claims came back ✅ verified, ⚠️ partial, ❌ contradicted}}

═══════════════════════════════════════════════════════════════
OUTPUT STRUCTURE
═══════════════════════════════════════════════════════════════

Produce a single markdown document with EXACTLY these sections, in this order:

### 1. ONE-LINE SUMMARY

A single sentence that fits in a tab title or Slack message. Captures: prospect name + what we built + the bottleneck it unblocks. Example shape:

"{{prospect}} — [demo name], a [sidecar type] that [unblocks specific bottleneck]."

### 2. THE DEMO

Three sub-sections:

- **What it does (technical):** 2–3 sentences using accurate vocabulary. Naming the actual components, agents, models, endpoints, deterministic rules engines. This is for Hafeedh's recall.

- **What it does (layman):** 2–3 sentences using a non-engineer analogy. No acronyms, no architecture words, no "API" or "sidecar." Example translation pattern: "Their product does X. The bottleneck is that customers need Y before buying. We built a small extra piece that handles Y, so the prospect's team doesn't have to stop working on their main product to build it." This is for Isaac. He has to be able to deliver this version on a cold call without stumbling.

- **The Magic Moment:** The single visible moment in the first 60 seconds that proves the demo works. Describe what the screen shows, what changes, why it's tangible. Both Hafeedh and Isaac must be able to recreate this verbally if asked.

### 3. WHY WE BUILT IT

The bottleneck thesis in 3 parts:

- The specific operational pain we identified (in the prospect's words wherever possible — quote founders verbatim from intel)
- Why this pain costs them enterprise deals
- Why our architecture solves it without threatening their roadmap

### 4. HOW WE BUILT IT

Two sub-sections:

- **Architecture (technical):** The agent flow, model routing, deterministic safety rails, integration surface. Reference the Master PRD's data flow, not a re-explanation.

- **Architecture (layman):** Same explanation, but as a metaphor a non-engineer would grasp. "Think of it like a translator that sits between two systems that don't speak the same language" — that kind of register.

### 5. ANTI-REPLICATION BOUNDARY

What we deliberately did NOT touch and why. This is load-bearing for the call — if the prospect's CTO challenges the architecture, the answer "we never go near your X because Y" must be ready. List 2–4 specific things we stayed away from, and the public evidence (their feature pages, founder statements, shipped product) that informed each kill.

### 6. THE COMPANY

- One-paragraph snapshot: what they do, who they sell to, stage, funding state, employee count, compliance status (e.g., SOC 2, ISO 27001), known/inferred customers
- Their core product and the part of their stack that's load-bearing (the thing we explicitly do NOT replicate)
- Their customers' integration realities — what tech their customers run, what file formats / APIs / EDI standards they use, where the data lives

### 7. THE FOUNDERS

For each founder, in this structure:

- **Name + role**
- **Background:** 2–3 sentence pedigree summary
- **Psychology hooks:** what to lean into in conversation (verified from public statements where possible — flag inferred vs verified)
- **Communication style:** how to pitch to them (sharp/structured vs. relational/narrative)
- **Allergies:** what to avoid saying — words, framings, comparisons that would land badly given their background

### 8. LOAD-BEARING ASSUMPTIONS

Surface every load-bearing claim our architecture makes about the prospect's stack, workflow, or commercial state. Mark each:
- ✅ verified (with source)
- ⚠️ partial (with what's missing)
- ❌ contradicted (with the pivot we made or recommend)

This section is non-negotiable. The Artificial Societies sprint died because one load-bearing assumption (about how AS accepts research inputs) was contradicted by their own marketing page — visible if anyone had looked, invisible because the PRD made the misframe look reasonable until Patrick caught it. This section is the mechanism that prevents that failure.

### 9. TALKING POINTS

Three sub-sections:

- **Phrases to USE:** Kaide Labs vocabulary (stateless sidecar, revenue unblocking, zero technical debt, unplug guarantee, anti-replication, DMZ rule)

- **Phrases to AVOID:** agency, consultancy, dev shop, custom software, hourly rate, freelancer, outsource — and any prospect-specific allergies surfaced in §7

- **Specific quotes / framings for this prospect:** sharp lines that match the founders' own vocabulary, pulled from their verbatim statements

### 10. ANTICIPATED OBJECTIONS

List 4–6 specific objections this prospect's founders are most likely to raise, each with:

- The objection in their likely wording
- The honest answer (not a deflection)
- The fallback if the honest answer doesn't land

Include at minimum:
- "We're already building this"
- "Why £10k/month"
- "We don't need outside help"
- One prospect-specific objection inferred from their public statements

### 11. WHAT TO DO IF THEY ENGAGE

If the prospect responds positively:

- Concrete next step (call scope, who joins, what we send next)
- What the first month of paid engagement actually delivers — specific artifacts, not vague promises
- Refund clause framing if they push on risk

### 12. SOURCES & METADATA

- Date this brief was compiled
- Primary sources for every non-obvious claim (link or document reference)
- What still requires verification before the demo ships
- Which sections are most likely to need updating if outreach is delayed >2 weeks

═══════════════════════════════════════════════════════════════
STYLE CONSTRAINTS
═══════════════════════════════════════════════════════════════

- Layman sections (2.layman, 4.layman) MUST be free of: API, sidecar, microservice, pipeline, endpoint, payload, schema, orchestration, RAG, LLM, embedding, vector, deterministic, deployment, container. If any of these words appear in a layman section, the layman section has failed its job and must be rewritten.

- Technical sections MUST name actual components — no hand-wavy "the system processes the data." If the component isn't named, the spec wasn't tight enough.

- Verbatim quotes from founders (in §3 and §7) must be in quotation marks and attributed to a specific source (interview, podcast, LinkedIn post, blog). If a quote can't be sourced, it doesn't go in.

- Every load-bearing claim in §8 must have a confidence mark. No unmarked claims allowed.

- Total length: 1500–2500 words. Longer is not better. The doc must be readable in 15 minutes.

═══════════════════════════════════════════════════════════════
FAILURE MODES TO AVOID
═══════════════════════════════════════════════════════════════

- **Pitch-deck voice.** This is internal. Write like you're briefing a teammate before a call, not selling to a customer.

- **Over-confidence on inferred founder psychology.** If we don't have public evidence of a hook, mark it ⚠️ inferred.

- **Restating the PRD.** The PRD already exists. The briefing doc is the layer above it, not a duplicate.

- **Skipping §8.** The whole point of this artifact post-AS is that load-bearing assumptions become visible BEFORE the call, not during it.

- **Burying the Magic Moment.** §2's Magic Moment must be punchy enough that Isaac can recite it from memory.

═══════════════════════════════════════════════════════════════
SELF-CHECK BEFORE OUTPUTTING
═══════════════════════════════════════════════════════════════

Before producing the final doc, internally verify:

- Could Isaac, who didn't build this, deliver §2.layman on a cold call without preparation?
- Could Hafeedh, three weeks from now, reconstruct his own architecture from §4.technical?
- Is every load-bearing assumption in §8 marked with a confidence symbol?
- Does §5 name 2–4 specific things we deliberately didn't touch?
- Is there at least one prospect-specific objection in §10 that an LLM working from generic FDE knowledge couldn't have produced?

If any answer is no, rewrite that section before outputting.
```

---

## Customization checklist

- [ ] Paste prospect intel dossier (cleaned intel + Step 02 founder dossier + Step 08 validation_gate.md)
- [ ] Paste ULTIMATE_PRD.md (or relevant sections of it)
- [ ] Paste demo build summary from BUILD_COMPLETE.md
- [ ] Paste validation gate falsifiable-claims-audit results from Step 08
- [ ] Confirm Claude Web is using Opus 4.7 (this is high-leverage strategic writing)

## Downstream

The call brief sits in the prospect's folder as `prospects/[name]/call_brief.md`. It's the load-bearing document for any response call.

If the prospect doesn't respond within 2 weeks: update §12's metadata section but don't regenerate the brief.

If the prospect responds and a call is scheduled: re-read the brief 24 hours before the call. Pay specific attention to §8 (load-bearing assumptions) and §10 (anticipated objections).

## Why this template exists in the post-AS pipeline

The Artificial Societies sprint died because Hafeedh + Isaac walked into a call without §8 having surfaced the contradicted assumption. Patrick caught it in real time. The deal was lost; the relationship was salvaged. This template makes that failure mode catchable before the call lands.

Skipping this template because "the demo was strong" is exactly the failure mode the AS post-mortem identified. The demo being strong is necessary but not sufficient — the call brief verifies what the demo's framing depends on.
