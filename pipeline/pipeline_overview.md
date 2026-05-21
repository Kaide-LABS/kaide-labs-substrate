---
type: pipeline_overview
last_updated: 2026-05-17
---

# Post-AS Pipeline Overview

The pipeline was re-engineered after the Artificial Societies sprint, which failed due to an unverified load-bearing assumption. Steps 0b and 1F were added as verification gates.

## Step 0 — Manual intel gathering
**Operator only.** No model. Hafeedh scrapes LinkedIn profiles, captures founder career paths verbatim, pulls Companies House / Crunchbase data, builds a structured MD intel file. Typically 2-3 hours per prospect. The intel file is the foundation for all downstream stages.

## Step 0b — Intel cleanup (Gemini Web)
Removes LinkedIn UI chrome, deduplicates noise, normalizes formatting. Output is a clean intel file ready for Step 0.5.

## Step 0 fit assessment — Judgment-mode evaluation (Claude Code + Nia)
**Run on indexed prospect repository.** Job A judgment-mode verdict: GO / CONDITIONAL GO / NO-GO. Hard kill filters + bottleneck-fit window + anti-replication surface + founder profile + load-bearing questions. Output drives whether to commit Step 0b cleanup hours.

## Step 0.5 — Founder dossier expansion (Gemini DR)
Deepens founder profiles with public-source enrichment. Discovers commercial allergies, communication style, prior controversies, pitch susceptibility signals.

## Step 1A — Three architecture generation (Gemini DR)
Produces three distinct multi-agent workflow candidates. Each adheres to anti-replication principle, DMZ rule, 5-pillar standard.

## Step 1B — Architecture red-team (Claude Web)
Kills proposals on verification failures, anti-replication violations, magic-moment thinness. Picks the surviving architecture. **Claude Web is the right tool because cross-prospect substrate matters.**

## Step 1F — Falsifiable claim audit (Claude Web)
**Added post-AS.** Every load-bearing claim in the architecture pick must be falsifiable from public sources. Audit before commit.

## Step 2 — Foundational build (Claude Code)
PRD-driven build of the core sidecar. Phased, not one-shot. Each phase has its own PHASE_N_SPEC.md.

## Step 3 — Lateral feature exploration (Codex CLI)
Divergent thinking pass. What features should we add that the customer doesn't know to ask for?

## Step 3A — Autonomous build executor (Gemini CLI 3 Flash/Pro or Sonnet 4.6)
Phase-by-phase build against PHASE_N_SPEC.md. Greenfield: try Gemini CLI. Iterative: always Sonnet 4.6.

## Step 3B — QA + phase advance (Claude Code)
Verifies phase outputs meet spec. Writes PHASE_N_QA.md. Approves advance to next phase.

## Step 3C — Sprint comprehension doc (Gemini CLI 3.1 Pro)
Self-onboarding for operator: company plain English, bottleneck plain English, demo layman + technical, end-to-end walkthrough, debug runway. Re-grounds before integration debug.

## Step 4 — Ultimate PRD synthesis (Claude Web)
Final canonical PRD reflecting actual built system. Cross-references between sections.

## Step 5 — Falsifiable validation gate (Gemini DR)
Every claim in Ultimate PRD audited against external sources. ✅ / ⚠️ / ❌.

## Step 5b — Validation red-team (Claude Web)
Red-team the validation gate output. Are there claims marked ✅ that shouldn't be?

## Step 5C / Step 6 — Call-prep briefing + asset production
- Why-video script
- Sandboxed demo recording
- Cold email language
- Vidyard upload
- Outreach send

## Discipline

Steps cannot be skipped. The pipeline exists because previous skips (most notably the AS load-bearing assumption miss) cost real sprints. The 1F gate specifically exists to catch what AS caught us on.
