---
type: prospect_overview
prospect: tracelight
vertical: financial_modeling_saas
stage_at_engagement: post_seed_3_6m
outcome: lost
last_updated: 2026-05-21
canonical: true
sourcing_status: would_not_pass_current_filters
filter_failure_mode: riba_haram_vertical_entanglement
key_tags:
  - financial_modeling_saas
  - private_equity_buyers
  - management_consulting_buyers
  - dag_engine_core_ip
  - excel_add_in
  - procurement_friction_bottleneck
  - peter_fuller
  - aleksander_misztal
  - janek_zimoch
  - haram_vertical_caveat
---

# Tracelight — Overview

## Company snapshot

Tracelight is a London-based AI-powered financial modeling SaaS, post-seed at $3.6M (Chalfen Ventures led; engagement was contemporaneous with the seed announcement). The core product is an Excel add-in that parses spreadsheets into a Directed Acyclic Graph (DAG) of formulas, allowing large language models to reason over topological cell dependencies rather than flat text. This is the company's load-bearing IP: stochastic LLMs are anchored by deterministic graph structure, which is what made Tracelight credible to private equity and elite management consultancy buyers (5 of the top 10 global consultancies were in late-stage evaluation at the time of engagement).

The buyer is the modeler — analysts and associates at PE firms and consultancies who live in Excel building LBOs, DCFs, and 3-statement models. Tracelight's positioning is "coding agents inside Excel" (Aleksander Misztal's own framing), not "AI assistant" — a deliberate disavowal of the generic Copilot category.

## What Tracelight does

Three product surfaces that mattered for Anti-Replication framing at engagement time:

1. **The DAG engine** — parses Excel workbooks into a graph of cells, formulas, and inter-sheet references. Lets the LLM reason over causal structure rather than flat text. This is the core IP.
2. **Plan Mode** — the LLM co-develops a multi-step plan with the analyst before executing changes. Eliminates the prompt-engineering friction Copilot-class tools impose.
3. **Spreadsheet Compare and Change Reviews** — version-diff and change-validation surfaces ensure the analyst can verify every AI-made modification before accepting it. Recently launched at engagement time; load-bearing for the "workslop" defense.

Two further surfaces shipped during the engagement window: native shareable-webpage output from spreadsheets, and chat with native citation to the underlying Excel — both directly downstream of the DAG engine and explicitly off-limits for the sidecar (see Anti-Replication section).

## The founder team

Three founders, distinct buyer dynamics, distinct pitch hooks. The engagement was designed to satisfy all three in a single demo.

### Peter Fuller — CEO

**Background:** Ex-McKinsey Engagement Manager, ex-QuantumBlack, Oxford. Strategy-pedigreed, ROI-obsessed, written-memo-first communicator (Amazon "Six-Pager" style).

**Communication style:** Data-driven, written, focused on unit economics and sales-cycle metrics. Public LinkedIn posts are essay-style and frame product decisions through margin and enterprise-procurement-friction lenses.

**Engagement role:** Primary decision-maker for any third-party engagement at Tracelight's stage. The "Peter pitch" was wedged from his own published views.

**Citation anchors used in pitch:**
- LinkedIn post explicitly advising enterprise buyers to *"Invest in a testing set-up. Create non-sensitive test data for use cases you care about."* The entire Safe-Harbor pitch is the automation of this advice inside Tracelight's own product surface
- "Workslop" manifesto on a viral Microsoft Copilot screenshot showing catastrophically wrong financial-model outputs — this informed the deterministic-validation-as-trust-anchor framing of the demo
- "80/2 rule" — Peter's coinage that AI compresses the 80/20 rule (5 minutes of Deep Research replaces 2 weeks of McKinsey analysis). The demo's 60-second magic moment was framed as the 80/2 moment for prospect onboarding

### Aleksander Misztal — CTO

**Background:** Ex-Jane Street (OCaml, deterministic high-performance systems), ex-Nethermind (zero-knowledge cryptography), Cambridge CS. Functional-safety-pedigreed, formal-verification-minded.

**Communication style:** Quiet (mostly reposts Peter's content), but specific where engaged. Uses agent terminology deliberately ("coding agents"). 100-mile ultramarathon endurance background — pattern-matches to extreme patience and willingness to stress-test under load.

**Engagement role:** Technical credibility gatekeeper. The audit trail surface and the deterministic-Python-validator-as-trust-anchor were built specifically for his inspection lens. The PRD-vs-ship gap audit (see architecture.md) is the kind of discipline he would have respected if engagement had progressed.

**Citation anchors used in pitch:**
- His "About" framing of Tracelight as **coding agents** rather than AI assistants — the demo pitched in agent terminology throughout, with explicit agent routing and per-stage cost accounting
- Jane Street pedigree — used to justify the Pure-Python deterministic validator as a "trust anchor" rather than another LLM layer

### Janek Zimoch — CPO

**Background:** Third engineer at 11x.ai (scaled $100K to $1M ARR), ex-Standard Chartered quantitative researcher, Cambridge MPhil in Machine Learning. Hyper-growth product engineering background with hands-on multi-agent orchestration experience.

**Communication style:** Frictionless-UX-obsessed, zero-prompt interfaces. Pathologically detail-oriented (Peter publicly attests to office-plant-watering-schedule levels of precision and 2 AM Slack messages about marginal prompt-tuning gains).

**Engagement role:** Product/UX evaluator. The Sentient-UI patterns (scenario selector with archetype-aware constraint prompting, live-formula proof surface in Google Sheets) were designed for his lens. A single sloppy loading state or unhandled error would have been a fatal credibility hit.

**Citation anchors used in pitch:**
- His 11x.ai multi-agent orchestration background — the pitch deliberately exposed agent routing detail because he would have spotted cargo-culted multi-agent design instantly. The actual ship uses agent specialization where it earns its place (schema extraction vs synthetic generation vs validation) and avoids it where a single call suffices

## The bottleneck we identified

**Enterprise InfoSec procurement friction.** Peter publicly named this as the binding constraint on Tracelight's sales velocity. Prospective PE firms and consultancies operate under strict compliance regimes (SOC 2, GDPR, MNPI handling) that prohibit uploading live financial data — actual deal numbers, debt schedules, customer concentration data — to any third-party AI tool during a proof-of-concept evaluation. The procurement security review for such an upload typically takes 2-3 months. This creates a paradox: Tracelight can't be validated without data, but the data can't be provided without months of security review. Deals stall in evaluation, not in pricing.

This is the cold-start problem of enterprise AI adoption, sharpened by the regulatory weight of financial data.

The Safe-Harbor sidecar was designed to dissolve the paradox by populating the prospect's empty model template with mathematically-coherent **synthetic** data in under 90 seconds, with zero sensitive data exposure. The prospect could then evaluate Tracelight's core product immediately, with a model that *feels* real but triggers no compliance review.

## Why this passed Anti-Replication (at design time)

**Specifically NOT touched:**

- **The DAG engine** — Tracelight's core IP. The sidecar never parses formulas, builds graphs, or reasons over cell dependencies. It writes input values; Tracelight's engine consumes them
- **Plan Mode** — Tracelight's co-development planning surface. The sidecar has no "plan" — it executes a fixed pipeline (schema → generate → validate → project → score → write)
- **Spreadsheet Compare and Change Reviews** — version-diff and validation surfaces. The sidecar's validation is a one-shot deterministic check, not an iterative review workflow with diff visualization
- **Shareable webpages** — Tracelight's downstream output surface launched two weeks before our analysis. The IC Memo Synthesizer adjacent idea (proposed in initial brainstorming) was killed precisely because it would have replicated this surface
- **The Excel add-in itself** — the sidecar lives entirely outside the add-in. It's a separate web app the prospect opens in a browser; the output is an xlsx file the prospect uploads into Tracelight's add-in

The sidecar is an upstream feeder: empty template in, populated template out. If Tracelight unplugs it tomorrow, their product still works (with the original 3-month procurement-friction problem restored).

## Why this would not pass current sourcing filters

**The haram-vertical caveat — load-bearing for honest substrate framing.**

Tracelight's buyer base is private equity firms and management consultancies modeling leveraged buyouts. The LBO product itself is constructed around interest-bearing debt instruments (senior debt at 5.5-7.5%, mezzanine PIK at 9-13%), debt-to-EBITDA leverage ratios as the core return mechanism, and equity returns generated primarily by financial engineering on top of interest-rate spreads. The Tracelight product makes this modeling faster and more accurate — i.e., it directly accelerates the operational throughput of the riba-bearing instrument class.

Under current Kaide Labs sourcing rules, a prospect whose product workflow is structurally riba-entangled is killed at sourcing, before any intelligence work, demo work, or outreach is performed. Tracelight's vertical wasn't fully scoped on this dimension at the time of sourcing, and the engagement proceeded through full demo build and outreach before the filter was formalized.

The substrate records this honestly because:

1. The engagement *did* happen — code shipped, demo recorded, Vidyard sent. Pretending it didn't would corrupt the substrate's integrity as a historical record
2. The architectural lessons (the Safe-Harbor pattern itself, the two-pass constraint projection, the live-formula proof surface) are reusable in non-riba-entangled verticals where the buyer's product workflow is structurally clean
3. The filter that would prevent this engagement today was hardened *because of* the Tracelight post-mortem. Naming Tracelight as the precedent strengthens the filter's operational reach

Future Claude Code queries that retrieve Tracelight content should surface this caveat unambiguously. The pattern is reference-grade; the prospect is not.

## Outcome

See `outcome.md` (canonical, not modified). Cold email + Vidyard demo sent. Low completion rate. No response. No-follow-up discipline held. The vertical-filter learning was the most durable artifact of the engagement and now governs sourcing.
