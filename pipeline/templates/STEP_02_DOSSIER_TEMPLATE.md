---
type: pipeline_template
step_number: 02
pipeline_stage: 0_5
step_name: founder_dossier_expansion
runs_in: gemini_deep_research
last_updated: 2026-05-19
canonical: true
key_tags:
  - founder_dossier
  - primary_source_citation
  - fabrication_pattern_guardrails
  - step_0_5
  - gemini_deep_research
inputs:
  - "*_intel_cleaned.md (output of Step 01)"
outputs:
  - "dossier_[founder_slug].md (one per founder, 7-section structure)"
upstream_step: step_01_intel_cleaner
downstream_step: step_03_target_brief_synthesis
---

# STEP 02 — FOUNDER DOSSIER EXPANSION TEMPLATE

> **Customization surfaces:** `[COMPANY NAME]`, `[N]` (founder count), `[FOUNDER NAME]` blocks, the "WHAT INTEL ALREADY COVERS" enumeration, and the "WHAT INTEL DOES NOT COVER" research targets.
> **Recommended runner:** Gemini Deep Research (Gemini Advanced with Deep Research mode enabled). The autonomous multi-source research is the load-bearing capability here.

---

```
Act as a forensic intelligence researcher. I have manually captured high-fidelity content (LinkedIn activity, role descriptions, public commentary) on [N] founder(s) of [COMPANY NAME]. I need you to expand this with web-surface content I couldn't capture manually — specifically pre-LinkedIn biographical context, press/interview content, technical artifacts, and investor commentary. Your output is a comprehensive founder dossier with strict primary-source URL preservation.

ATTACHED FILE:

intel_cleaned.md — Manually-captured intelligence on [COMPANY] founder(s). Contains verbatim LinkedIn activity (posts, reposts, likes, comments) and full Ashby/job-board JD text for all open [COMPANY] roles. Treat this as ground-truth substrate that doesn't need re-verification. Your job is to expand around it.

THE COMPANY:

[COMPANY NAME] — [one-line description, location, vertical]. [Funding stage, latest round, key investors]. [Approximate employee count]. [Key customers if known].

THE FOUNDER(S):

[For each founder, list:]
[FOUNDER NAME] — [Role] at [COMPANY]. LinkedIn: [URL]. Verified path: [Education] → [Career history with dates]. [Key public artifacts: GitHub URL, personal site URL, Twitter/X URL]. [Notable family/biographical context if relevant].

WHAT INTEL.MD ALREADY COVERS (don't duplicate):

[Enumerate what's in the manual capture, in 3-5 bullets per founder. Be specific — name actual posts, JDs, quotes, company facts that are present. Examples:]

- [FOUNDER]'s LinkedIn activity (~N posts/reactions): [list 3-5 specific themes or memorable posts]
- All [N] open [COMPANY] roles via [job board]: [list each role with salary band and key verbatim phrases]
- [COMPANY] facts: [funding rounds, employee count, customer roster, certifications, product launches]

WHAT INTEL.MD DOES NOT COVER — focus your research here:

(1) Pre-[COMPANY] biographical depth for [FOUNDER(S)]:
[List specific gaps. Examples:]
- [FOUNDER A] at [Prior Company 1] (role, tenure, projects, public commentary)
- [FOUNDER A] at [Prior Company 2] (same)
- [FOUNDER A]'s family/educational detail beyond what's verifiable from intel.md
- [FOUNDER B]'s [thesis topic, dissertation, papers if applicable]
- Any specific projects or open-source contributions from prior roles

(2) Press/interview content already referenced in intel.md but not captured in full:
[List specific named publications/podcasts. Examples:]
- [Podcast name] appearance — full transcript or detailed summary
- [Publication name] interview from [date] — full transcript or detailed summary, especially [specific topics relevant to outreach angle]
- [Other publications referenced in intel.md but not captured]

(3) [COMPANY]-side content:
- Company website (about page, blog, product pages — what's published?)
- Company "insights" or "case studies" if any
- Customer-side commentary about [COMPANY] (partner quotes in press releases, customer testimonials)

(4) [Technical founder]'s technical surface:
[If applicable — usually for a CTO/technical founder:]
- GitHub activity beyond pinned repos (recent commits, forks, contributions)
- Personal website content
- Twitter/X commentary
- Academic papers, conference talks, public webinars
- Any open-source contributions to [vertical]-relevant libraries

(5) Investor/board commentary:
- [Lead investor]'s commentary beyond funding-announcement quotes
- Other investor partner views
- Board composition / advisor list

KNOWN FABRICATION PATTERNS — do not reintroduce. Three failure-mode patterns autonomous research agents reliably exhibit:

- DOM-adjacent content on aggregator pages: multiple authors' content appearing on the same URL gets misattributed. Verify authorship at the article level, not the aggregator level.
- Namesake collision on profile aggregators: sites like Highperformr, RocketReach, or Tracxn merge multiple people's profiles together. Verify against the canonical LinkedIn URL only, not aggregator-merged profiles.
- Semantic mapping from unrelated documents: specific quantitative claims (percentages, dollar figures, performance metrics) sometimes get grafted from academic papers in unrelated domains onto founder claims. Verify quantitative claims have founder-attributed primary sources.

If your research surfaces content matching these patterns, flag it and verify before including. Plausible-sounding content that you cannot trace to a primary source attributed to the named founder must be marked [UNVERIFIED] or excluded.

OUTPUT FORMAT:

Markdown document with 7-section structure per founder:

1. Background (career history, education, family, pre-[COMPANY] context)
2. Current role and responsibilities at [COMPANY]
3. Stated public positions and thought leadership (quotes, posts, interviews — citation-anchored)
4. Technical decisions and architectural preferences (for technical founder(s) especially)
5. Communication style and apparent decision-making patterns
6. Recommended outreach angles (anchored to verified public statements)
7. Red flags / things to avoid

For each section, every claim has an inline citation in [N] format pointing to a numbered citation list at the end of the document. Citation list contains primary-source URLs only.

Sections may reference intel.md content directly with the notation [intel.md: section name]. Don't recopy intel.md content; reference it.

CRITICAL CONSTRAINTS:

- Primary sources only. If you cannot find a primary URL for a claim, mark it [UNVERIFIED] and explain what you searched. Null evidence is evidence.
- No synthesis prose without citation. Inferences like "[FOUNDER] likely values X" are excluded unless directly supported by a verified statement.
- Direct quotes must be verbatim and traceable. Paraphrases must clearly indicate they're paraphrases.
- For quantitative claims, prefer to cite the founder's own statement of the number rather than third-party reports. If only third-party reports exist, note that explicitly.
- Treat intel.md as ground-truth substrate. Don't relitigate its content; expand around it.
- For each section, indicate clearly what is intel.md-sourced vs newly-researched.
- Counterintuitive but important: fewer new claims with stronger citations is a better outcome than more new claims with weaker citations. Don't pad the dossier with marginal sources.

OUTPUT:

A single comprehensive dossier covering [the founder(s)]. This will be fed alongside intel.md into Step 03 (target brief synthesis + architecture proposal) in the next phase.
```

---

## Customization checklist

- [ ] `[N]` — number of founders (typically 1-3 for B2B AI seed-stage)
- [ ] `[COMPANY NAME]` — prospect company name
- [ ] Company one-liner, funding, employee count, customers
- [ ] For each founder: name, role, LinkedIn URL, verified career path, public artifacts (GitHub, personal site, Twitter)
- [ ] "WHAT INTEL.MD ALREADY COVERS" — be specific, name actual content
- [ ] "WHAT INTEL.MD DOES NOT COVER" — be specific about research gaps; better narrow research targets than broad ones
- [ ] Confirm Gemini Deep Research is enabled in the runner session

## Downstream

Output `dossier_[founder_slug].md` files (one per founder, or a single combined dossier) become inputs for Step 03 (Gemini target brief synthesis). The dossier is treated as primary-source-cited ground truth for architecture generation.
