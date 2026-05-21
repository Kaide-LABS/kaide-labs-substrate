---
type: pipeline_template
step_number: 09
pipeline_stage: 1F_red
step_name: validation_redteam_verdict
runs_in: claude_web_opus
last_updated: 2026-05-19
canonical: true
key_tags:
  - validation_redteam
  - veto_authority
  - patrick_class_signal
  - reposition_or_kill
  - positioning_pivots
  - step_1f_red
  - post_as_pipeline
inputs:
  - "validation_gate.md (Step 08 output)"
  - "ULTIMATE_PRD.md (Step 07 output)"
  - "intel_cleaned.md (Step 01 output)"
  - "Master_PRD.md (Step 04 output)"
outputs:
  - "positioning_final.md (with verdict: CLEAR-TO-SHIP / REPOSITION-REQUIRED / KILL-AND-RESTART)"
upstream_step: step_08_claims_audit
downstream_step: step_10_phase_1_blueprinting
veto_authority: true
---

# STEP 09 — 1F-RED VERDICT TEMPLATE

> **Customization surfaces:** `[TARGET COMPANY]` — the prospect name.
> **Recommended runner:** Claude Web with Opus 4.7. This is a strategic judgment step with veto authority over the entire sprint; do not economize on model selection.
> **Veto authority:** This step can kill the entire sprint and force a return to Step 04 with a positioning constraint document. The veto is real, not theatrical.

---

```
Act as the Principal Architect with veto authority over the sprint. I have a validation_gate.md from Step 08. Your job is to make the call on whether the sprint ships, gets repositioned, or gets killed.

ATTACHED FILES:

- validation_gate.md — Step 08 output
- ULTIMATE_PRD.md — the audited architecture
- intel_cleaned.md — verified ground-truth substrate
- Master_PRD.md — Step 04 output

YOUR TASK:

1. Read validation_gate.md in full. Do not skim.

2. For every ❌ CONTRADICTION in the source-ranked verification (Pass 1F.2):
   - Determine if it is RECOVERABLE (positioning pivot possible without rebuilding the architecture)
   - Determine if it is UNRECOVERABLE (the architectural premise itself is invalidated)

3. For the 1F-sim contradiction (Pass 1F.3), if any:
   - This is the Patrick-class signal (named after the Artificial Societies CPO who caught the AS misframe in 5 minutes on their marketing page).
   - Treat ANY 1F-sim finding as load-bearing, even if individual claim verifications passed. The sim catches the kind of contradiction that's lethal in a sales call, regardless of whether atomic claims verify.

4. DECIDE:

   - **CLEAR-TO-SHIP** — no contradictions, no 1F-sim findings. Proceed to Step 10 (Phase 1 blueprinting) with confidence.
   
   - **REPOSITION-REQUIRED** — contradictions exist but are recoverable. Output positioning_final.md with sentence-level pivots required.
   
   - **KILL-AND-RESTART** — contradictions are unrecoverable (architectural premise invalidated). Output positioning_final.md with a recommendation to return to Step 04 with a positioning constraint document seeded by the latent bottleneck inventory.

OUTPUT FORMAT — positioning_final.md:

# 1F-red Verdict — [TARGET COMPANY]

## Verdict

CLEAR-TO-SHIP / REPOSITION-REQUIRED / KILL-AND-RESTART

## Justification

[2-3 paragraphs explaining the call, citing specific 1F findings. Be brutal and honest. If the architecture is salvageable, say so explicitly and pivot. If it's dead, say so explicitly and don't soften.]

## Positioning Edits (if REPOSITION-REQUIRED)

For each edit, surface the original sentence and the replacement sentence:

- **Demo script:**
  - Original: "[verbatim from existing draft]"
  - Replacement: "[new framing that acknowledges the contradicting evidence]"
  
- **Video script:**
  - Original: "[verbatim]"
  - Replacement: "[new framing]"

- **Outreach email:**
  - Original: "[verbatim]"
  - Replacement: "[new framing]"

- **Magic Moment (if affected):**
  - Original framing: "[verbatim]"
  - New framing: "[adjusted framing that respects the contradicting evidence]"

## Constraint Document for Re-iteration (if KILL-AND-RESTART)

If KILL-AND-RESTART, the next architecture iteration in Step 04 must respect these constraints:

- **What the next architecture must NOT assume:** [list specific assumptions that were contradicted]
- **Latent bottlenecks worth pivoting toward:** [from validation_gate.md's Latent Bottleneck Inventory]
- **New source priority for the next intel pass:** [if the contradiction surfaced because intel was thin in a specific area, name it]
- **Sources that must be re-checked before Step 04 re-runs:** [specific URLs that contradicted the prior assumption]

Save to positioning_final.md. Acknowledge when complete so the sprint can resume (or formally halt).
```

---

## Customization checklist

- [ ] `[TARGET COMPANY]` — fill in throughout
- [ ] Attach: `validation_gate.md`, `ULTIMATE_PRD.md`, `intel_cleaned.md`, `Master_PRD.md`
- [ ] Confirm Claude Web is using Opus 4.7
- [ ] Acknowledge mentally that veto is real before running — if your gut says "this is a kill but the sunk cost feels bad," ship the kill anyway

## Downstream

- **If CLEAR-TO-SHIP:** Step 10 (Phase 1 Technical Blueprinting) runs against the locked Ultimate PRD.
- **If REPOSITION-REQUIRED:** Apply positioning edits to assets, then proceed to Step 10. The architecture itself doesn't change; the framing does.
- **If KILL-AND-RESTART:** Sprint formally halts. Re-run Step 04 with the constraint document. Do NOT proceed to Step 10 with a known-failed premise.

## On the veto

The veto is the post-AS pipeline's core safety mechanism. The Artificial Societies relationship was salvaged, but the deal was lost because a load-bearing assumption was contradicted in real time on a sales call. This step exists so that contradiction surfaces BEFORE the call, when the cost of recovery is "rewrite three sentences in the demo script" rather than "lose the deal in front of the CPO."

The veto being symbolic defeats the purpose. If 1F audit surfaces an unrecoverable contradiction, ship the kill. Sunk cost on Steps 01-08 is not a reason to ship a failing premise into a sales call.
