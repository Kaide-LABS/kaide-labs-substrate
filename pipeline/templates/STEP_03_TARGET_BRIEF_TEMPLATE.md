---
type: pipeline_template
step_number: 03
pipeline_stage: 1A
step_name: target_brief_synthesis_and_proposal_generation
runs_in: gemini_advanced
last_updated: 2026-05-19
canonical: true
key_tags:
  - target_brief
  - founder_psychology
  - three_architectures
  - anti_replication
  - five_pillar_standard
  - step_1a
  - gemini_advanced
inputs:
  - "Kaide_Labs_SOP_Gemini.md (the Gemini-side SOP with benchmarks)"
  - "Kaide_Labs_Identity.md (canonical positioning)"
  - "Target Intelligence (cleaned intel + founder dossier)"
outputs:
  - "TARGET_BRIEF.md (psychological profile + 3 proposed sidecar architectures, citation-anchored)"
upstream_step: step_02_dossier_expansion
downstream_step: step_04_prd_redteam
---

# STEP 03 — TARGET BRIEF SYNTHESIS + PROPOSAL GENERATION TEMPLATE

> **Customization surfaces:** `[Company Name]`, `[Company URL]`, `[Target Industry]`, `[Core Problem]`, founder name/role/background blocks.
> **Recommended runner:** Gemini Advanced (the original Gemini SOP was designed for this surface). Deep Research is acceptable but not required at this step — the synthesis is bounded by the attached files, not by autonomous web research.
> **Dependencies:** This step requires the `Kaide_Labs_SOP_Gemini.md` and `Kaide_Labs_Identity.md` files to be attached to the Gemini session.

---

```
Act as an elite FDE, AI Solutions Architect, and B2B Sales Strategist. I am building a highly targeted product demo for a company called [Company Name] ([Company URL]). They build software for [Target Industry], targeting [Core Problem]. Their founders are:

- [Founder 1 Name] ([Role]): [Brief Background]
- [Founder 2 Name] ([Role]): [Brief Background]
[Add additional founders as needed]

I have attached three sets of files:

1. Kaide_Labs_SOP_Gemini.md (Your core instructions, strict boundaries, and benchmarks).
2. Kaide_Labs_Identity.md (Canonical Kaide Labs positioning — read this first for context on who we are and what we build).
3. The Target Intelligence (cleaned LinkedIn intel + founder dossier(s) + company context).

YOUR TASK: Read the Identity file first. Then read the SOP file (this contains the 5-Pillar Standard, the Anti-Replication Principle, and architectural benchmarks from prior Kaide Labs demos — Renlo and Mundostra). Then, synthesize the Target Intelligence. Finally, execute the tasks outlined in Part 4 of the SOP to generate the TARGET_BRIEF.md containing their psychological profile and 3 proposed sidecar architectures.

THE CITATION MANDATE (CRITICAL): When writing the psychological profiles and the framing for your 3 proposals, you MUST show your work. Explicitly cite the exact evidence, quotes, or LinkedIn posts from the Target Intelligence files that drove your architectural decisions. (e.g., "Proposal 1 is designed this way because [Founder Name] explicitly stated '[Quote]' in their recent LinkedIn post.") I need this specific evidence to use as leverage in my sales outreach.

OUTPUT STRUCTURE — TARGET_BRIEF.md:

§1 — Founder Psychology Profile (per founder)
- Career pedigree summary (2-3 sentences, citation-anchored)
- Communication style read (sharp/structured vs. relational/narrative)
- Specific operational complaints surfaced from public statements (verbatim quotes with sources)
- Buying triggers (what they care about, citation-anchored)
- Allergies (what to avoid saying)
- Net pitch susceptibility: HIGH / MEDIUM / LOW with reasoning

§2 — Three Proposed Sidecar Architectures

For each proposal:

**Proposal N: [Architecture Name]**
- The Zone: Pre-Core / Post-Core / Parallel-Adjacent
- The Bottleneck Assassinated: [specific operational pain, citation-anchored]
- The Anti-Replication Verdict: [explicit explanation of why this doesn't touch their core IP, with reference to their shipped/announced features]
- The Native Environment: [where this lives in the user's workflow]
- The Magic Moment: [what's visible on the frontend in <60 seconds]
- The System Resilience Layer: [the deterministic safety rails]
- Multi-Agent Architecture (Google Gemini stack only):
  - [Agent 1]: [Gemini model] — [role]
  - [Agent 2]: [Gemini model] — [role]
  - [etc.]
- The Strategic Hook: [why this specifically lands for THIS founder team — citation-anchored to their public statements]
- The Pitch Framing: [the exact angle/framing for outreach]

§3 — Cross-Proposal Comparison

Brief table or summary comparing the three proposals on:
- Bottleneck severity (which pain is most acute?)
- Anti-Replication safety (which has the cleanest boundary?)
- Magic Moment compelling-ness (which produces the strongest visual proof?)
- Implementation risk (which is most achievable in 48-72hrs?)
- Strategic hook strength (which best matches founder psychology?)

§4 — Recommendation

State which of the three you would advance to Step 04 (Claude red-team) and why, in 2-3 paragraphs.
```

---

## Customization checklist

- [ ] `[Company Name]` and `[Company URL]`
- [ ] `[Target Industry]` and `[Core Problem]` — one-liners
- [ ] For each founder: name, role, brief background (from Step 02 dossier)
- [ ] Attach: `Kaide_Labs_SOP_Gemini.md`, `Kaide_Labs_Identity.md`, intel + dossier
- [ ] Confirm Gemini Advanced (not Deep Research mode) is the runner

## Downstream

TARGET_BRIEF.md feeds Step 04 (Claude PRD red-team). The three proposals are NOT yet locked — they are candidates for Claude to evaluate adversarially in the next step. Expect Claude to kill 1-2 of the three.
