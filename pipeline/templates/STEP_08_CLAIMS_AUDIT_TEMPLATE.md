---
type: pipeline_template
step_number: 08
pipeline_stage: 1F
step_name: forensic_claims_audit_validation_gate
runs_in: gemini_advanced
last_updated: 2026-05-19
canonical: true
key_tags:
  - validation_gate
  - load_bearing_claims
  - falsifiable_propositions
  - source_ranked_verification
  - five_minute_contradiction_simulation
  - patrick_class_misframe
  - step_1f
  - post_as_pipeline
inputs:
  - "ULTIMATE_PRD.md (Step 07 output)"
  - "intel_cleaned.md (Step 01 output)"
  - "TARGET_BRIEF.md (Step 03 output)"
  - "Master_PRD.md (Step 04 output)"
  - "dossier_*.md (Step 02 outputs)"
outputs:
  - "validation_gate.md (with verdict: CLEAR-TO-SHIP / REPOSITION-REQUIRED / KILL-AND-RESTART)"
upstream_step: step_07_architectural_synthesis
downstream_step: step_09_validation_redteam
---

# STEP 08 — FORENSIC CLAIMS AUDIT / 1F VALIDATION GATE TEMPLATE

> **Customization surfaces:** `[TARGET COMPANY]` — the prospect name.
> **Recommended runner:** Gemini Advanced (the original 1F design used Gemini's web access patterns).
> **Why this step exists:** This is the post-AS pipeline's verification gate. The Artificial Societies sprint died because one load-bearing assumption (about how AS accepts research inputs) was contradicted by their own marketing page — visible if anyone had looked. This step makes that failure mode catchable BEFORE assets are produced.

---

```
Act as a forensic claims auditor. I have an Ultimate PRD specifying a sidecar architecture for [TARGET COMPANY]. Your job is to surface every load-bearing claim — explicit and implicit — that the architecture rests on, test each against primary sources, and report contradictions before assets are produced.

ATTACHED FILES:

- ULTIMATE_PRD.md — the architecture to audit
- intel_cleaned.md — verified ground-truth substrate
- TARGET_BRIEF.md — Step 03 output (for cross-reference)
- Master_PRD.md — Step 04 output (for cross-reference)
- dossier_*.md — Step 02 output(s)

YOUR TASK — execute four passes in order. Do not skip ahead.

═══════════════════════════════════════════════════════════════
PASS 1F.0 — IMPLICIT ASSUMPTION EXTRACTION
═══════════════════════════════════════════════════════════════

For each architectural component in the Ultimate PRD, ask: "What would have to be true about [TARGET COMPANY]'s existing stack, workflow, or product for this component to be necessary?" The answer is the implicit claim. Document each.

Output as a numbered list of falsifiable propositions. Format:

"Implicit Claim N: [TARGET] currently [does not do / does not have / does not accept] X."

Example (Artificial Societies sprint, retrospectively):

"Implicit Claim 1: AS does not currently accept first-party research bundles (segmentation decks, focus group transcripts, SPSS exports, brand trackers) as direct inputs to its calibration pipeline."

The AS sprint died because this implicit claim was wrong — AS does accept these inputs, visible on their own marketing page. The 1F validation gate would have caught it.

═══════════════════════════════════════════════════════════════
PASS 1F.1 — EXPLICIT CLAIM EXTRACTION
═══════════════════════════════════════════════════════════════

Scan the Ultimate PRD for explicit assertions about the target's stack/workflow. Examples:

- "Their FDE team manually completes IT security questionnaires"
- "Their customers use SAP PM and Maximo CMMS systems"
- "Their Gemini routing currently uses model X"
- "Their support workflow takes 3+ hours per disruption"

Add each to the audit list as numbered Explicit Claim N.

═══════════════════════════════════════════════════════════════
PASS 1F.2 — SOURCE-RANKED VERIFICATION
═══════════════════════════════════════════════════════════════

For each claim (implicit and explicit, in order of stated importance to the architecture), test against priority-1 sources first:

1. Target's own marketing copy (homepage, product pages, FAQ, "How it works" pages, security/trust pages)
2. Target's own engineering or technical blog
3. Founder's own verified statements (LinkedIn posts, interview transcripts, podcast appearances)
4. Press coverage where target is quoted directly
5. Investor commentary about target

VERIFICATION VERDICTS:

- If priority-1 returns a positive match: mark ✅ VERIFIED, cite the URL, quote the verbatim supporting text.
- If priority-1 returns a contradiction: mark ❌ CONTRADICTED, cite the URL, quote the verbatim contradicting text.
- If priority-1 returns null: continue to priority-2. Document that priority-1 was searched and returned null.
- If priority-1 through priority-5 all return null: mark ⚠️ UNVERIFIABLE, document what was searched.

DO NOT cite as primary evidence:

- Vendor blogs in the same category as the proposed sidecar (these are tainted by commercial interest)
- Aggregators (Highperformr, RocketReach, Tracxn, HN aggregation pages) — verify at the article level only
- Any source already flagged in the dossier's fabrication-pattern list

═══════════════════════════════════════════════════════════════
PASS 1F.3 — FIVE-MINUTE CONTRADICTION SIMULATION
═══════════════════════════════════════════════════════════════

After the full audit completes, run a focused simulation. Take the lead architecture's central one-sentence premise (the sentence that justifies the sidecar's existence). Simulate a domain-fluent reader (CPO, CTO, or technical co-founder) with 5 minutes and access only to the target's marketing page, product page, and any public technical blog post.

Can they find a fact that contradicts the premise? Document the simulation:
- What page would they open first?
- What would they search for on the page?
- What would they find?
- How long would it take?

If the simulation surfaces a contradiction within 5 simulated minutes: this is a Patrick-class misframe (named after the Artificial Societies CPO who would have caught the AS misframe in this exact way). Route to Step 09 (1F-red) with red-flag priority.

═══════════════════════════════════════════════════════════════
OUTPUT FORMAT — validation_gate.md
═══════════════════════════════════════════════════════════════

# 1F Validation Gate — [TARGET COMPANY]

## 1F.0 Implicit Assumption Extraction

[Numbered list of implicit claims, formatted as falsifiable propositions]

## 1F.1 Explicit Claim Extraction

[Numbered list of explicit claims from the Ultimate PRD]

## 1F.2 Source-Ranked Verification

[For each claim: ✅ / ⚠️ / ❌, primary source URL, verbatim supporting/contradicting text, search trail if priority-1 was null]

## 1F.3 Five-Minute Contradiction Simulation

- Premise sentence: [the central one-sentence justification]
- Simulated reader path: [what page they open, what they search, what they find]
- Contradiction found: YES / NO
- Time to surface: [estimated seconds-to-minutes]

## Latent Bottleneck Inventory

Bottlenecks visible in priority-1/2/3 sources that the current PRD does not address. These are pivot targets if the audit surfaces an unrecoverable contradiction.

## Positioning Delta

If any ❌ exists, sentence-level edits to:
- Demo script
- Video script
- Outreach email

If 1F-sim surfaced a contradiction, rebuild the central premise here with the contradicting evidence accounted for.

## Verdict

CLEAR-TO-SHIP / REPOSITION-REQUIRED / KILL-AND-RESTART, with one-line justification

═══════════════════════════════════════════════════════════════
HANDOFF
═══════════════════════════════════════════════════════════════

Output to validation_gate.md in the repo root. Do not proceed to Step 10 (Phase 1 spec) until Step 09 (1F-red) has reviewed this file.
```

---

## Customization checklist

- [ ] `[TARGET COMPANY]` — fill in throughout the prompt
- [ ] Attach: `ULTIMATE_PRD.md`, `intel_cleaned.md`, `TARGET_BRIEF.md`, `Master_PRD.md`, `dossier_*.md`
- [ ] Verify Gemini Advanced has web access enabled
- [ ] Confirm the target's homepage URL is accessible (run a manual check first if uncertain)

## Downstream

validation_gate.md feeds Step 09 (1F-red verdict in Claude Web). Step 09 has veto authority over the sprint — if 1F audit surfaces unrecoverable contradictions, Step 09 issues KILL-AND-RESTART and the sprint returns to Step 04 with a positioning constraint document.

## Why this is non-negotiable

The Artificial Societies failure pattern: load-bearing assumption contradicted by the target's own marketing page, visible if anyone had looked. This step is the mechanism that prevents that failure. Skipping it because "the architecture looks solid" is exactly the failure mode the AS post-mortem identified.
