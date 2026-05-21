---
type: prospect_kill_log
prospect: prometheux
vertical: financial_services_ai
geography: italy
killed_at_stage: step_0_fit_assessment
filter_fired: riba
secondary_filter: anti_replication
killed_at: 2026-05-XX
generalizable_learnings:
  - financial_services_kill_at_sourcing_not_dossier
  - knowledge_graph_engine_absorbs_dmz_pattern
---

# Prometheux — Kill Log

## What they do

Vadalog Parallel knowledge graph engine. Primary commercial vertical: financial services (Banca Sella credit risk, Revolut, Central Bank of Italy ties, "Financial Data That Thinks" keynotes).

## Why considered

Surfaced during sourcing pass. Technical depth was real. EU-based.

## Why killed

**Primary kill (riba filter):** Their commercial vertical is unambiguously conventional financial services — banking customers, credit risk, central bank engagement. Building procurement-grade infrastructure that runs through their core engine would mean directly enabling interest-bearing financial operations. Hard kill on Salafi observance constraints.

**Secondary kill (anti-replication):** Their Vadalog Parallel engine absorbs upstream and downstream surfaces by design. The knowledge-graph reasoning encompasses what would otherwise be sidecar territory. Plus they already have 4 in-house FDEs handling deployment work. The DMZ shrinks to almost nothing. Even without the riba kill, the anti-replication kill would have fired.

## Generalizable learnings

### Financial services kill at sourcing, not dossier

The riba filter must fire at sourcing-stage 5-minute screening, not after 2-3 hours of Step 0 intel work. Add explicit "primary customer base in financial services?" check at sourcing. The 5-minute screen should explicitly include banking/insurance/lending/payments as immediate kills regardless of how interesting the technology is.

### Knowledge graph engine absorbs DMZ pattern

Companies positioning as "knowledge graph engines" or "data engines" tend to absorb DMZ territory by architectural design. Their value prop *is* the orchestration layer that an FDE sidecar would otherwise occupy. Apply this caution to similar-shaped prospects: if the product positioning IS the orchestration/encoding layer, the anti-replication boundary likely fails regardless of vertical.
