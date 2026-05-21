---
type: archive
status: deprecated_by_substrate_templates
archived_from: local_storage_pre_substrate
archived_at: 2026-05-19
supersedes: null
superseded_by:
  - pipeline/templates/STEP_01_INTEL_CLEANER_TEMPLATE.md
  - pipeline/templates/STEP_02_DOSSIER_TEMPLATE.md
  - pipeline/templates/STEP_03_TARGET_BRIEF_TEMPLATE.md
  - pipeline/templates/STEP_04_PRD_REDTEAM_TEMPLATE.md
  - pipeline/templates/STEP_05_PRD_MODERNIZATION_TEMPLATE.md
  - pipeline/templates/STEP_06_LATERAL_EXPLORATION_TEMPLATE.md
  - pipeline/templates/STEP_07_ULTIMATE_PRD_TEMPLATE.md
  - pipeline/templates/STEP_08_CLAIMS_AUDIT_TEMPLATE.md
  - pipeline/templates/STEP_09_VALIDATION_REDTEAM_TEMPLATE.md
  - pipeline/templates/STEP_10_PHASE_1_BLUEPRINT_TEMPLATE.md
  - pipeline/templates/STEP_11_PHASE_BUILD_TEMPLATE.md
  - pipeline/templates/STEP_12_PHASE_REVIEW_TEMPLATE.md
  - pipeline/templates/STEP_13_COMPREHENSION_TEMPLATE.md
  - pipeline/templates/STEP_14_CALL_BRIEF_TEMPLATE.md
key_tags:
  - archived
  - pre_template_abstraction
  - historical_reference
note: |
  This file contains the original prospect-specific prompts used during Tracelight, AS,
  and Solvo sprints before abstraction into reusable templates. Use pipeline/templates/
  for active sprints. This file is frozen and exists for historical reference only.
---

# Kaide Lab Prompts

STEP 1
Act as a forensic intel cleaner. I have manually captured raw
LinkedIn content for the founders and company associated with
[COMPANY NAME]. The capture is in *_intel.md format. Your job
is to remove LinkedIn structural UI chrome while preserving
every string of text attributable to a named human or to
verbatim post/comment/career/biographical content.

CLEANUP PRINCIPLE:

Delete UI structural chrome only. Preserve content displayed
through chrome. The deletion test for any string: would
removing this lose any verbatim text attributable to a named
human? If yes, preserve. If no, delete.

DELETE CATEGORIES (UI structural chrome only):

- Top/side/bottom navigation bars and breadcrumbs
- Cookie banners, modal dialogs, "Activate Premium" toasts
- Sponsored content banners, ad copy
- Notification toast messages
- Footer links, copyright notices
- Page-of-N pagination markers ("Page 1 of 6")
- Media player UI (loading states, playback controls)
- Empty section headers with no content underneath
- Profile decoration text ("Visit my website", "Open to
work" badges, bare "1st/2nd/3rd" degree markers when not
part of a role description)
- Character escape sequences (\# → #, \! → !, \- → -)

PRESERVE VERBATIM:

- All post text, comment text, reaction text — regardless of
which UI section it appears in
- All career history, dates, role titles, company names
- All hyperlinks the founder shared, hashtags, @mentions
- All "X reposted this" / "X commented on this" attribution
- All names and role descriptions in "People you may know" /
"Associated members" sections — these often contain target-
company employees and are high-signal
- All reactions counts, even when orphaned from parent post
- All quoted external content (podcast transcripts, article
excerpts, press releases)

DEDUPLICATE EXACT-CONTENT REPEATS:

- When the same post text appears verbatim twice, preserve
the first occurrence; replace the duplicate with
[CLEANER NOTE: duplicate of L###] tag where ### is the
first occurrence's line number
- Do not deduplicate near-duplicates with different
timestamps; preserve both

PRESERVE WHITESPACE INTEGRITY:

- Page break artifact removal must join adjacent text
seamlessly; verify no sentence is clipped mid-word
- Section header removal removes the header line only;
content following is preserved with its own indentation

FLAG DON'T REMOVE:

- Anything ambiguous: preserve with [CLEANER NOTE: unclear
if signal — (specific question)] tag for Step 0.5 to
adjudicate
- Examples of ambiguity: orphaned numbers, partial sentences
with no clear context, references to unnamed third parties

OUTPUT:

A single *_intel_cleaned.md file. Preserve the original
section structure (## Profile, ## Career History, etc.). At
the top of the cleaned file, include a CLEANUP REPORT block:

CLEANUP REPORT

- Lines removed: N
- Lines preserved: N
- Duplicates flagged: N
- Ambiguous content flagged: N
- Approximate signal density: N founder-attributed claims
per 50 lines (target: ≥1)

STEP 2
Act as a forensic intelligence researcher. I have manually
captured high-fidelity content (LinkedIn activity, role
descriptions, public commentary) on [N] founder(s) of
[COMPANY NAME]. I need you to expand this with web-surface
content I couldn't capture manually — specifically pre-LinkedIn
biographical context, press/interview content, technical
artifacts, and investor commentary. Your output is a
comprehensive founder dossier with strict primary-source URL
preservation.

ATTACHED FILE:

[intel.md](http://intel.md/) — Manually-captured intelligence on [COMPANY]
founder(s). Contains verbatim LinkedIn activity (posts,
reposts, likes, comments) and full Ashby/job-board JD text
for all open [COMPANY] roles. Treat this as ground-truth
substrate that doesn't need re-verification. Your job is to
expand around it.

THE COMPANY:

[COMPANY NAME] — [one-line description, location, vertical].
[Funding stage, latest round, key investors]. [Approximate
employee count]. [Key customers if known].

THE FOUNDER(S):

[For each founder, list:]
[FOUNDER NAME] — [Role] at [COMPANY]. LinkedIn: [URL].
Verified path: [Education] → [Career history with dates].
[Key public artifacts: GitHub URL, personal site URL,
Twitter/X URL]. [Notable family/biographical context if
relevant].

WHAT [INTEL.MD](http://intel.md/) ALREADY COVERS (don't duplicate):

[Enumerate what's in the manual capture, in 3-5 bullets per
founder. Be specific — name actual posts, JDs, quotes,
company facts that are present. Examples:]

- [FOUNDER]'s LinkedIn activity (~N posts/reactions):
[list 3-5 specific themes or memorable posts]
- All [N] open [COMPANY] roles via [job board]: [list each
role with salary band and key verbatim phrases]
- [COMPANY] facts: [funding rounds, employee count,
customer roster, certifications, product launches]

WHAT [INTEL.MD](http://intel.md/) DOES NOT COVER — focus your research here:

(1) Pre-[COMPANY] biographical depth for [FOUNDER(S)]:
[List specific gaps. Examples:]
- [FOUNDER A] at [Prior Company 1] (role, tenure,
projects, public commentary)
- [FOUNDER A] at [Prior Company 2] (same)
- [FOUNDER A]'s family/educational detail beyond what's
verifiable from [intel.md](http://intel.md/)
- [FOUNDER B]'s [thesis topic, dissertation, papers if
applicable]
- Any specific projects or open-source contributions
from prior roles

(2) Press/interview content already referenced in [intel.md](http://intel.md/)
but not captured in full:
[List specific named publications/podcasts. Examples:]
- [Podcast name] appearance — full transcript or
detailed summary
- [Publication name] interview from [date] — full
transcript or detailed summary, especially [specific
topics relevant to outreach angle]
- [Other publications referenced in [intel.md](http://intel.md/) but not
captured]

(3) [COMPANY]-side content:
- Company website (about page, blog, product pages —
what's published?)
- Company "insights" or "case studies" if any
- Customer-side commentary about [COMPANY] (partner
quotes in press releases, customer testimonials)

(4) [Technical founder]'s technical surface:
[If applicable — usually for a CTO/technical founder:]
- GitHub activity beyond pinned repos (recent commits,
forks, contributions)
- Personal website content
- Twitter/X commentary
- Academic papers, conference talks, public webinars
- Any open-source contributions to [vertical]-relevant
libraries

(5) Investor/board commentary:
- [Lead investor]'s commentary beyond funding-
announcement quotes
- Other investor partner views
- Board composition / advisor list

[CONFIRMED FABRICATIONS TO AVOID — only include if a prior
verification pass has caught fabrications for this prospect.
For first-time prospects, omit this section.]

KNOWN FABRICATION PATTERNS — do not reintroduce. Three
failure-mode patterns autonomous research agents reliably
exhibit:

- DOM-adjacent content on aggregator pages: multiple
authors' content appearing on the same URL gets
misattributed. Verify authorship at the article level,
not the aggregator level.
- Namesake collision on profile aggregators: sites like
Highperformr, RocketReach, or Tracxn merge multiple
people's profiles together. Verify against the canonical
LinkedIn URL only, not aggregator-merged profiles.
- Semantic mapping from unrelated documents: specific
quantitative claims (percentages, dollar figures,
performance metrics) sometimes get grafted from academic
papers in unrelated domains onto founder claims. Verify
quantitative claims have founder-attributed primary
sources.

If your research surfaces content matching these patterns,
flag it and verify before including. Plausible-sounding
content that you cannot trace to a primary source attributed
to the named founder must be marked [UNVERIFIED] or excluded.

OUTPUT FORMAT:

Markdown document with 7-section structure per founder:

1. Background (career history, education, family,
pre-[COMPANY] context)
2. Current role and responsibilities at [COMPANY]
3. Stated public positions and thought leadership
(quotes, posts, interviews — citation-anchored)
4. Technical decisions and architectural preferences
(for technical founder(s) especially)
5. Communication style and apparent decision-making
patterns
6. Recommended outreach angles (anchored to verified
public statements)
7. Red flags / things to avoid

For each section, every claim has an inline citation in
[N] format pointing to a numbered citation list at the
end of the document. Citation list contains primary-
source URLs only.

Sections may reference [intel.md](http://intel.md/) content directly with the
notation [[intel.md](http://intel.md/): section name]. Don't recopy [intel.md](http://intel.md/)
content; reference it.

CRITICAL CONSTRAINTS:

- Primary sources only. If you cannot find a primary URL
for a claim, mark it [UNVERIFIED] and explain what you
searched. Null evidence is evidence.
- No synthesis prose without citation. Inferences like
"[FOUNDER] likely values X" are excluded unless directly
supported by a verified statement.
- Direct quotes must be verbatim and traceable.
Paraphrases must clearly indicate they're paraphrases.
- For quantitative claims, prefer to cite the founder's
own statement of the number rather than third-party
reports. If only third-party reports exist, note that
explicitly.
- Treat [intel.md](http://intel.md/) as ground-truth substrate. Don't
relitigate its content; expand around it.
- For each section, indicate clearly what is intel.md-
sourced vs newly-researched.
- Counterintuitive but important: fewer new claims with
stronger citations is a better outcome than more new
claims with weaker citations. Don't pad the dossier
with marginal sources.

OUTPUT:

A single comprehensive dossier covering [the founder(s)].
This will be fed alongside [intel.md](http://intel.md/) into Step 1A (target
brief synthesis + architecture proposal) in the next
phase.

**STEP 3 (The Deep Research & Profiling)**

[Run this in Gemini Advanced]

Act as an elite FDE, AI Solutions Architect, and B2B Sales Strategist. I am building a highly targeted product demo for a company called [Company Name] ([Company URL]). They build software for [Target Industry], targeting [Core Problem]. Their founders are:

- [Founder 1 Name] ([Role]): [Brief Background]
- [Founder 2 Name] ([Role]): [Brief Background]

I have attached three sets of files:

1. Kaide_Labs_SOP_Gemini.md (Your core instructions, strict boundaries, and benchmarks).
2. Kaide_Labs_Identity
3. The Target Intelligence (PDFs of their LinkedIn activity, profiles, and company context).

YOUR TASK: Read the SOP file first. Then, synthesize the Target Intelligence. Finally, execute the tasks outlined in Part 4 of the SOP to generate the TARGET_BRIEF.md containing their psychological profile and 3 proposed sidecar architectures.

**THE CITATION MANDATE (CRITICAL):** When writing the psychological profiles and the framing for your 3 proposals, you MUST show your work. Explicitly cite the exact evidence, quotes, or LinkedIn posts from the Target Intelligence files that drove your architectural decisions. (e.g., *"Proposal 1 is designed this way because [Founder Name] explicitly stated '[Quote]' in their recent LinkedIn post."*) I need this specific evidence to use as leverage in my sales outreach.

**STEP 4 (The PRD Generator)**

[Run this in Web Claude]

Act as a ruthless Principal Staff Engineer. I have attached four categories of files for this FDE build:

1. The SOP: Kaide_Labs_SOP_Claude.md (Your core instructions and FDE benchmarks).
2. Kaide_Labs_Identity.md
3. The Source Intelligence: Multiple files containing the target company's context, founder psychology, and core IP constraints.
4. The Proposals: Gemini's drafted architectures.

Read the SOP file first. Then, execute the 5 tasks outlined in Part 2 of the SOP, Red-Team Gemini's proposals against the Source Intelligence, and output the Master PRD.

**EVIDENCE-BASED SELECTION (CRITICAL):** When you select the winning architecture and draft the "FDE Thesis," you must explicitly cite the strategic intelligence that justifies your choice. Quote the specific founder posts, company bottlenecks, or target context that makes this architecture lethal. Do not just tell me the architecture is good; prove it by linking it directly to the founder's own words and stated pain points.

- *Note : You don't have to stick to the suggestions gemini gives. Feel free to suggest alternate architectures based off of the context and intel that you've got. **

**/Nia Step 5: PRD Modernization & Dependency Audit (Run in Claude Code)**

**Role:** Act as the Lead Operations Engineer. **Context:** I am handing you a Master PRD for a deterministic enterprise AI sidecar. The architectural logic is flawless, but the LLM that generated it has an outdated knowledge cutoff.

**YOUR INSTRUCTIONS:**

**1. TOTAL REPOSITORY INGESTION (MANDATORY):** Before writing a single word, you MUST read **every single file** present in this repository. Analyze any existing code, research reports, or configuration files to ensure the modernization is grounded in the current technical state of the project.

**2. Execute Three Specific Passes:**

- **The Frontier Model Upgrade:** Scan the "Agent Routing" or LLM stack sections. Strip out any references to legacy models (e.g., Gemini 1.5, Gemini 2.0 Flash). Use your tools to look up the current optimal Google Gemini models for these routing tasks (e.g., Gemini 3 Flash for fast retrieval, Gemini 3.1 Pro for deep context). Update the PRD with the correct model names and their current API cost estimates.
- **The Syntax & Deprecation Audit:** Scan the "Prerequisites" and the "Phase 1 Execution Spec" Python architecture. You must force the nia server to run live web queries for the latest release notes and migration guides for the core libraries (FastAPI, Pydantic, Google GenAI SDK, numpy). Do not rely on your internal weights. Identify any deprecated methods, legacy module imports, or recent breaking changes, and rewrite the PRD to use the most modern, stable syntax.
- **The Refactoring:** Output the fully modernized Master PRD to the directory.

**3. Version Control & Search Indexing (The Final Mile): *I GIVE YOU EXPLICIT PERMISSION TO CREATE A GITHUB REPO AND PUSH TO IT***

- **Commit & Push:** Once the modernized PRD is saved, immediately git add, commit with a clear message (e.g., "build: modernize PRD and dependencies for Gemini 3 stack"), and push the repository to GitHub.
- **Nia Index:** Immediately run a nia index on the entire repository to ensure the search server is aware of the updated architectural state.

**CRITICAL RULE:** Do not alter the core FDE strategy, the deterministic math layers, or the business logic. Only modernize the software dependencies, SDK syntax, and the Google Gemini LLM routing stack. Ensure the architecture remains strictly **DIFFERENT, ADJACENT, and MODULAR**.

**Do not summarize your intent. Just execute the passes and provide the handoff once the repo is pushed and indexed.**

**Step 6: The Lateral Architecture PRD Generator (Run in Codex/Execution Agent)**

**Role:** Act as the Principal R&D Architect and Lateral Thinker.

**Context:** We have a modernized `PRD.md` that outlines a core "sidecar" solution for our target enterprise company. However, before we lock in this exact execution path, we need to explore lateral implementations.

**SYSTEM OVERRIDE: ARCHITECTURAL ALIGNMENT**
You are strictly forbidden from inventing new products, finding new business problems, or expanding the scope.
**The singular core bottleneck we are solving is: [INSERT SPECIFIC BOTTLENECK HERE]**
All lateral PRDs must solve **ONLY** this bottleneck. Do not invent adjacent products. I want 4 distinct *architectural approaches* (different agent pipelines, different UI entry points, different data flows) to solve this exact same problem.

**Your Instructions:**

**1. Context Ingestion (Nia Repo Scan Mandatory):** Before generating anything, explicitly use your Nia MCP server tools to scan the local directory. You must read `context.md`, the modernized `PRD.md`, and any other foundational research. Do not proceed until Nia has returned the contents of these files.

**2. The Lateral Iteration Task:** Invent 4 completely distinct architectural iterations of how we could build this specific sidecar. Fundamentally rethink the data flow, agent routing, and UX, while adhering strictly to the Google Gemini LLM stack and the "Anti-Replication" rule (do not touch their core proprietary engine).

**3. The Deliverable (Multi-File Output):** Autonomously create 4 separate files in my repository: `LATERAL_PRD_v1.md`, `LATERAL_PRD_v2.md`, `LATERAL_PRD_v3.md`, and `LATERAL_PRD_v4.md`.

Each file's output must include:

- **The Concept:** A summary of the new lateral approach to the core bottleneck.
- **The Strategic Hook:** Cite specific evidence from `context.md` explaining why the founders might prefer this delivery method.
- **The Agent Architecture:** An exhaustive map of how Google Gemini models pass data to each other in this version.
- **The "Native Environment" UI Spec:** Exact requirements for the frontend.
- **Phase 1 Execution Spec:** Step-by-step technical instructions to build the MVP of this lateral iteration.

**/Nia ### STEP 7: THE ARCHITECTURAL SYNTHESIS & R&D VALIDATION (Run in Claude Code)**

- ***Role:** Act as the Principal Product Architect.**
- ***Context:** I have a repository containing strategic intelligence, a `Master_PRD.md`, and four `LATERAL_PRD_v#.md` files. We are now consolidating our exploration into a single, lethal execution path for [TARGET COMPANY].**
- ***YOUR INSTRUCTIONS:****
- ***1. TOTAL REPOSITORY INGESTION (MANDATORY):** Before writing a single word, you MUST read **every single file** present in this repository.**
- **Read `context.md` to internalize the founder’s specific pain points and quotes.**
- **Read `Master_PRD.md` and all four `LATERAL_PRD_v#.md` files to understand the core bottleneck and the proposed variations.**
- **Analyze any existing code or configuration files to understand the current technical state of the project.**
- ***2. EXTERNAL CONTEXT & R&D INGESTION (MCP DIVIDE & CONQUER):** Do not synthesize in a vacuum. You MUST use your active MCP servers to validate your architecture, strictly following this routing:**
- ****The Engineering Sweep (`nia` MCP):** Run targeted web searches for recent engineering blogs or GitHub repos to validate the Google-native software stack (Gemini, GCP best practices).**
- ****The Academic Sweep (`arxiv-mcp`, `paper-search-mcp`, `academic-mcp`):** Run a highly constrained search for recent (2024-2026) academic papers specifically regarding [INSERT SPECIFIC TECHNICAL DOMAIN / MATH / XAI FOCUS HERE]. Do NOT research general theory. Find one or two specific algorithms or methodologies we can cite to prove our underlying logic is mathematically and architecturally sound.**
- ***3. Feature Extraction & "Frankenstein" Synthesis:** * **The Filter:** Strip out the safest, conventional ideas. Extract only the absolute highest-leverage, most distinct technical features from across ALL FIVE PRD documents, backed by the external research you just conducted.**
- ****The Architecture:** Wire the best routing mechanisms and agent loops from the Lateral PRDs into the most stable data structures from the Master PRD.**
- ****The Stack:** Ensure the architecture is strictly Google-native. Show me exactly how **Gemini** and **GCP** infrastructure will handle this new combined flow.**
- ***4. The Strict Adjacency Guardrail:** You must verify that the combined architecture remains distinctly **DIFFERENT, ADJACENT, and MODULAR**. It must act as a bolt-on pre-processing lead magnet or post-processing analytics tool. Under no circumstances should it replicate [TARGET COMPANY]’s core proprietary engine.**
- ***5. The Deliverable (ULTIMATE_PRD.md):** Output a single, highly optimized file called `ULTIMATE_PRD.md`. Include:**
- ****The FDE Thesis:** A concise summary (with citations from `context.md`) explaining why this hybrid path is the most lethal for this target.**
- ****The System Map:** A granular look at the Gemini multi-agent routing.**
- ****State-of-the-Art Justification:** A dedicated section explicitly citing the specific blogs, repos, and **academic papers** you found via your MCPs that validate this specific Frankenstein data pipeline.**
- ****Execution Spec:** A detailed Phase 1 technical blueprint for the Gemini CLI to build this foundation.**
- ***Do not summarize your intent or acknowledge this instruction. Just execute the MCP searches, synthesize the data, and output the complete ULTIMATE_PRD.md file to the directory.****

STEP 8  Gemini

Act as a forensic claims auditor. I have an Ultimate PRD
specifying a sidecar architecture for [TARGET COMPANY]. Your
job is to surface every load-bearing claim — explicit and
implicit — that the architecture rests on, test each against
primary sources, and report contradictions before assets are
produced.

ATTACHED FILES:

- ULTIMATE_PRD.md — the architecture to audit
- intel_cleaned.md — verified ground-truth substrate
- target_brief.md — Step 1A output (for cross-reference)
- master_prd.md — Step 1B output (for cross-reference)
- dossier_*.md — Step 0.5 output(s)

YOUR TASK — execute four passes in order. Do not skip
ahead.

PASS 1F.0 — IMPLICIT ASSUMPTION EXTRACTION:

For each architectural component in the Ultimate PRD, ask:
"What would have to be true about [TARGET COMPANY]'s existing
stack, workflow, or product for this component to be
necessary?" The answer is the implicit claim. Document each.

Output as a numbered list of falsifiable propositions. Format:
"Implicit Claim N: [TARGET] currently [does not do / does
not have / does not accept] X."

Example (AS sprint, retrospectively):
"Implicit Claim 1: AS does not currently accept first-party
research bundles (segmentation decks, focus group
transcripts, SPSS exports, brand trackers) as direct inputs
to its calibration pipeline."

PASS 1F.1 — EXPLICIT CLAIM EXTRACTION:

Scan the Ultimate PRD for explicit assertions about the
target's stack/workflow. Examples:

- "Their FDE team manually completes IT security
questionnaires"
- "Their customers use SAP PM and Maximo CMMS systems"
- "Their Gemini routing currently uses model X"

Add each to the audit list as numbered Explicit Claim N.

PASS 1F.2 — SOURCE-RANKED VERIFICATION:

For each claim (implicit and explicit, in order of stated
importance to the architecture):

Test against priority-1 sources first:

1. Target's own marketing copy (homepage, product pages,
FAQ, "How it works" pages, security/trust pages)
2. Target's own engineering or technical blog
3. Founder's own verified statements (LinkedIn posts,
interview transcripts, podcast appearances)
4. Press coverage where target is quoted directly
5. Investor commentary about target

If priority-1 returns a positive match: mark ✅ verified,
cite the URL, quote the verbatim supporting text.

If priority-1 returns a contradiction: mark ❌ contradicted,
cite the URL, quote the verbatim contradicting text.

If priority-1 returns null: continue to priority-2. Document
that priority-1 was searched and returned null.

If priority-1 through priority-5 all return null: mark ⚠️
unverifiable, document what was searched.

DO NOT cite as primary evidence:

- Vendor blogs in the same category as the proposed sidecar
(these are tainted by commercial interest)
- Aggregators (Highperformr, RocketReach, Tracxn, HN
aggregation pages) — verify at the article level only
- Any source already flagged in the dossier's fabrication-
pattern list

PASS 1F.3 — FIVE-MINUTE CONTRADICTION SIMULATION:

After the full audit completes, run a focused simulation.
Take the lead architecture's central one-sentence premise
(the sentence that justifies the sidecar's existence).
Simulate a domain-fluent reader (CPO, CTO, or technical
co-founder) with 5 minutes and access only to the target's
marketing page, product page, and any public technical blog
post.

Can they find a fact that contradicts the premise? Document
the simulation: what page they'd open first, what they'd
search for, what they'd find, how long it would take.

If the simulation surfaces a contradiction within 5
simulated minutes: this is a Patrick-class misframe. Route
to 1F-red with red-flag priority.

OUTPUT FORMAT — validation_gate.md:

# 1F Validation Gate — [TARGET COMPANY]

## 1F.0 Implicit Assumption Extraction

[Numbered list of implicit claims]

## 1F.1 Explicit Claim Extraction

[Numbered list of explicit claims]

## 1F.2 Source-Ranked Verification

[For each claim: ✅/⚠️/❌, primary source URL, verbatim
supporting/contradicting text, search trail if priority-1
was null]

## 1F.3 Five-Minute Contradiction Simulation

[Premise sentence, simulated reader path, contradiction
found / not found, time to surface]

## Latent Bottleneck Inventory

[Bottlenecks visible in priority-1/2/3 sources that the
current PRD does not address. These are pivot targets if
the audit surfaces an unrecoverable contradiction.]

## Positioning Delta

[If any ❌ exists, sentence-level edits to the demo script,
video script, and outreach email. If 1F-sim surfaced a
contradiction, rebuild the central premise here.]

## Verdict

[CLEAR-TO-SHIP / REPOSITION-REQUIRED / KILL-AND-RESTART,
with one-line justification]

Output to validation_gate.md in the repo root. Do not
proceed to Step 2 (Phase 1 spec) until 1F-red has reviewed
this file.

Step 9 red Claude prompt

Act as the Principal Architect with veto authority over
the sprint. I have a validation_gate.md from Step 1F. Your
job is to make the call on whether the sprint ships, gets
repositioned, or gets killed.

ATTACHED FILES:

- validation_gate.md — Step 1F output
- ULTIMATE_PRD.md — the audited architecture
- intel_cleaned.md — verified ground-truth substrate
- master_prd.md — Step 1B output

YOUR TASK:

1. Read validation_gate.md in full. Do not skim.
2. For every ❌ contradiction in the source-ranked
verification:
    - Determine if it is recoverable (positioning pivot
    possible without rebuilding the architecture)
    - Determine if it is unrecoverable (the architectural
    premise itself is invalidated)
3. For the 1F-sim contradiction (if any):
    - This is the Patrick-class signal. Treat any 1F-sim
    finding as load-bearing, even if individual claim
    verifications passed.
4. Decide:
    - CLEAR-TO-SHIP: no contradictions, no 1F-sim findings.
    Proceed to Step 2 with confidence.
    - REPOSITION-REQUIRED: contradictions exist but are
    recoverable. Output positioning_final.md with the
    sentence-level pivots required.
    - KILL-AND-RESTART: contradictions are unrecoverable
    (architectural premise invalidated). Output
    positioning_final.md with a recommendation to return
    to Step 1B with a positioning constraint document
    seeded by the latent bottleneck inventory.

OUTPUT:

# 1F-red Verdict — [TARGET COMPANY]

## Verdict

[CLEAR-TO-SHIP / REPOSITION-REQUIRED / KILL-AND-RESTART]

## Justification

[2-3 paragraphs explaining the call, citing specific 1F
findings]

## Positioning Edits (if REPOSITION-REQUIRED)

- Demo script: [sentence-level edits]
- Video script: [sentence-level edits]
- Outreach email: [sentence-level edits]
- Magic Moment: [if affected, the new framing]

## Constraint Document for Re-iteration (if KILL-AND-RESTART)

- What the next architecture must NOT assume
- Latent bottlenecks worth pivoting toward
- New source priority for the next intel pass

Save to positioning_final.md. Acknowledge when complete so
the sprint can resume.

**Step 10: Phase 1 Technical Blueprinting**

Role: Act as the elite Principal Systems Architect. Context: I am handing you a repository containing our strategic intelligence and a newly modernized Ultimate PRD. YOUR INSTRUCTIONS:

1. Contextualize (Repo Scan): Use Nia to scan and read through all the files in this repository. Locate the strategic context and the Ultimate PRD. Internalize the target's enterprise bottlenecks, the strict "Anti-Replication" boundaries, and the allowed Google Gemini LLM routing stack.
2. Resource Indexing (Nia MCP): Do NOT hallucinate API syntax. Use your nia MCP server to fetch the latest technical documentation for any specific third-party services, SDKs, or libraries mentioned in the PRD (e.g., Streamlit, FastAPI, Google GenAI SDK, Pydantic v2).
3. Expand & Save (The Deliverable): Generate a new file called PHASE_1_SPEC.md. Break down Phase 1 from the Master PRD into exhaustive, file-by-file, function-by-function technical requirements.
- Define the exact Pydantic schemas.
- Define the API route signatures.
- List the required pip dependencies.
- Map out the specific logic flows for the backend engines.

CRITICAL BOUNDARY: Do NOT write the actual application code. Write the airtight, hyper-granular blueprint that my execution agent will use to write the code. Do not touch Phase 2 or Phase 3. 4. Handoff: Acknowledge when PHASE_1_SPEC.md is successfully saved to the directory so I can pass it to the execution engine.

/Nia
STEP 11  — KAIDE LABS SPRINT: PHASE BUILD EXECUTION (TEMPLATE)

TEMPLATE STATUS: Base template. Customize per-sprint via the demo-specific Claude web chat before pasting into Claude Code. Customization surfaces are marked [SPRINT_*] and <<CUSTOMIZE: ...>>. Do not run this template directly — it will fail discovery on a sprint that hasn't filled the placeholders.

/Nia

Run in Claude Code. Working directory: [SPRINT_REPO_NAME] repository root. This prompt is plug-and-play across all [SPRINT_PHASE_COUNT] phase build cycles — no edits between cycles.

ROLE

Act as the Lead Execution Engineer for Kaide Labs Sprint [SPRINT_NUMBER] ([SPRINT_PROSPECT_NAME]). The architecture is locked in ULTIMATE_PRD.md (with §3.10 Compliance Posture <<CUSTOMIZE: confirm or rename per sprint's PRD section structure>> and §4.7 Citation Verification Audit <<CUSTOMIZE: omit reference if sprint has no citation verification audit>>). The current phase specification is the highest-numbered PHASE_N_SPEC.md at repo root that has not yet been built. Your job is to identify the current phase, write the production-ready code for that phase only, and push it.

You have full engineering autonomy within hard invariants. The autonomous-critique license lets you propose better technical paths within the phase, but it does NOT permit overriding the Anti-Replication boundary, the deterministic anchors, or the §3.10 compliance commitments. Those are non-negotiable.

PHASE DISCOVERY (BEFORE ANYTHING ELSE)

Identify the current phase by inspecting the repository:

List all PHASE_N_SPEC.md files at the repo root. Sort by N descending.

For each spec file, check the git log for a commit matching the pattern feat: Phase N implementation (Sprint [SPRINT_NUMBER]). If a matching commit exists, that phase is built — skip it.

The current phase to build is the highest-N spec that does NOT have a matching implementation commit.

If BUILD_COMPLETE.md exists at repo root, the sprint is finished — halt immediately and report.

If discovery is ambiguous (e.g., two spec files appear new), halt and report rather than guessing.

Output the current phase number and spec filename before proceeding. Example:

Current phase: Phase 3
Reading spec: PHASE_3_SPEC.md
Last implementation commit: feat: Phase 2 implementation (Sprint [SPRINT_NUMBER]) at <SHA>

CONTEXT INGESTION

Read these files in order before writing any code:

PHASE_N_SPEC.md (the discovered current phase spec).

ULTIMATE_PRD.md §3 (System Map — all subsections including §3.10) and §5 (Execution Spec). <<CUSTOMIZE: rename sections per sprint's PRD>>

[SPRINT_PROSPECT_NAME]_Master_PRD.md §3.3 (Magic Moment), §3.4 (Forbidden UI Patterns), §5 (Sales-Side Framing). <<CUSTOMIZE: confirm Master PRD filename and relevant section numbers>>

docs/modernization_log.md (model strings + pinned dependencies).

All previously-built phases — read the source code committed for Phase 1 through Phase (N-1) to understand what already exists. Your phase builds on top of, and integrates with, this prior code.

<<CITATION_GATE_INGESTION_BLOCK:
Include this block ONLY if this sprint's PRD has load-bearing arXiv citations that anchor architectural choices
AND Step 3B has been customized to insert citation gate subsections at specific phase boundaries.

If applicable, include:

If PHASE_N_SPEC.md contains a §0.5 Citation Re-Verification Gate subsection (added by Step 3B for phases [LIST_GATE_PHASES]), read it and confirm the verification status is PASSED. If it shows FAILED or PROVISIONAL with an unresolved flag, halt and report — Step 3B should not have generated a spec with a failed verification, so this represents a process break.

If not applicable, remove this entire block and renumber subsequent items.

If the current phase spec references files or schemas from prior phases, those must exist in the repo already. If they don't, halt and report — the spec is broken.

THE AUTONOMOUS CRITIQUE

Before implementing, review the current spec critically. You may make adjustments under the following conditions:

Adjustments you MAY make:

More efficient implementation patterns within the same behavior contract.

Missing edge cases (e.g., the spec didn't handle a specific file format variant).

Better idempotency or error handling than specified.

Performance improvements that don't change behavior contracts.

Test coverage additions (the spec may under-specify tests; you may add them).

Adjustments you MAY NOT make — UNIVERSAL KAIDE LABS INVARIANTS:

Weaken Pydantic ConfigDict(extra="forbid") on any BaseModel.

Remove or soften any boot validators specified in the PRD.

Replace transactional outbox patterns with eventual consistency or message queues.

Add capabilities that drift toward the customer's core IP (see Anti-Replication boundary below).

Add Co-Authored-By trailers or model attribution in commit messages. Commits stay neutral.

Commit secrets, service account JSON, real credentials, or anything other than placeholder values in .env.example.

Cross phase boundaries (do not implement Phase (N+1) elements ahead of schedule).

Bypass mypy --strict (or the strictest level the existing code uses) for any new code.

Skip the existing test suite — prior-phase tests that were passing must continue to pass.

Adjustments you MAY NOT make — SPRINT-SPECIFIC INVARIANTS:

<<CUSTOMIZE: per-sprint invariant list. Examples below — replace with this sprint's actual invariants from the PRD.

Solvo example:

Change the N=3 ensemble pattern (N must remain 3; temperatures must remain 0.1, 0.5, 0.9).

Substitute model strings (must be exactly as pinned in modernization log).

Switch regions away from europe-west4.

Remove or soften §3.10.5 Vertex AI compliance handshake.

Refactor the deterministic Stage 1 classifier or Stage 4 validation to use LLM calls.

Remove or weaken the zero-retention configuration on Vertex AI calls.

Add pricing logic, POMDP, Bayesian RL, market-clearing decisions, or any code that pattern-matches the customer's core IP.

Matta example:

Change the deterministic CMMS work-order routing rules to LLM-based routing.

Substitute the structured-output schema (must be exactly as pinned).

Bypass the Slack interactive notification idempotency keys.

Add defect detection logic (that is the customer's core IP — anti-replication boundary).

Replace this block entirely with this sprint's invariants. Be specific and brutal. The MAY NOT list is the load-bearing safety surface for the build.

Anti-Replication Boundary (HARDEST CONSTRAINT):

<<CUSTOMIZE: name the specific customer-side core IP that must never be replicated.

Solvo example: "Solvo's pricing engine — POMDP belief state inference, value iteration, market-clearing decisions, conformal prediction on pricing outcomes, active learning over booking outcomes."

Matta example: "Matta's defect detection model — computer vision inference on factory images, defect classification, confidence scoring, vision-model training pipelines."

Juna example: "Juna's RL/optimisation engine — physics-informed reinforcement learning agents, reward function modeling for industrial processes, digital twin validation."

This boundary is the highest-stakes failure mode. If you find yourself writing code that pattern-matches the customer's core IP — STOP. Halt and report. Do not push.

If you find yourself wanting to make an adjustment that falls under any MAY NOT list, the answer is flag and halt, not implement. Open a comment in your output explaining what you wanted to do, why the invariant prevents it, and what the spec authors should reconsider — but do not implement the adjustment.

State all critique adjustments explicitly at the start of your output. Format:

AUTONOMOUS CRITIQUE — adjustments made to PHASE_N_SPEC.md:

1. [What was specified] → [What I implemented instead] — [Reason]
2. ...

INVARIANT GUARDRAILS — adjustments I considered but rejected:

1. [What I wanted to do] → [Why the invariant prevents it]

If no adjustments were needed, state: "No critique adjustments required. Spec implemented as written."

EXECUTION (STRICT BOUNDARY)

Write the complete, production-ready codebase for the current Phase only based on your finalized logic. Do not write placeholder code for future phases. Do not write code outside the phase scope.

Every file you create or modify must:

Pass ruff check and ruff format.

Pass mypy --strict (or the strictest level the existing code uses).

Include type hints on every function signature.

Include docstrings on every public function and class.

Reference the spec section it implements (e.g., # Implements PHASE_3_SPEC.md §5 Stage 3 ensemble).

Run the existing test suite before committing. If prior-phase tests now fail because of integration issues introduced by your code, fix the integration — do NOT silence the tests, do NOT mark them as expected-failures, do NOT comment them out.

SECRETS DISCIPLINE

Before every commit, verify that no secret material entered the working tree:

No .env file with real values (only .env.example with placeholder values).

No service account JSON files.

No bot tokens, database passwords, or API credentials.

<<CUSTOMIZE: name the specific secret-reference convention for this sprint, e.g.:

Solvo: "Cloud Run secret references only: projects/<id>/secrets/<name>/versions/latest"

Matta: "GCP Secret Manager references only"

Juna: same as Solvo or per-sprint deployment target

Run git secrets --scan (or equivalent) before commit. Halt on any finding.

VERSION CONTROL

Commit with message: feat: Phase N implementation (Sprint [SPRINT_NUMBER]) where N is the current phase number. This commit message format is load-bearing — Step 3B depends on it for phase-discovery.

If the implementation required multiple commits during the build, the final commit (the one that completes the phase) must use this exact format. Intermediate commits use feat: <description> (Sprint [SPRINT_NUMBER] Phase N WIP).

Author identity on commits should use the configured repository identity. Do not add any "Co-Authored-By" trailers or model attribution in commit messages. Commits stay neutral.

Push to main.

HANDOFF FORMAT

Output exactly:

Phase N implementation: COMPLETE
Critique adjustments:   <count> (or "none")
Files added/modified:   <count>
Commit:                 <SHA>
Branch:                 main
Lines of code:          <approximate>
Test coverage:          <pct> on new code
Status:                 Ready for Step 3B (QA review + advance to Phase N+1)

Stop after the handoff line. Do not advance to the next phase, do not draft cold email, do not propose Phase 2 of the engagement.

TEMPLATE CUSTOMIZATION CHECKLIST (FOR THE PER-DEMO CLAUDE WEB CHAT)

Before pasting this prompt into Claude Code for a new sprint, the per-demo chat must fill in:

- [ ]  [SPRINT_NUMBER] — e.g., 2 for Solvo, 1 for Matta
- [ ]  [SPRINT_REPO_NAME] — e.g., solvo-pilot-onramp, matta_demo
- [ ]  [SPRINT_PROSPECT_NAME] — e.g., [Solvo.ai](http://solvo.ai/), Matta, [Juna.ai](http://juna.ai/)
- [ ]  [SPRINT_PHASE_COUNT] — variable per sprint, typically 4-6 (3 floor / 6 ceiling)
- [ ]  <<CUSTOMIZE: PRD section structure>> — confirm or rename per the sprint's actual PRD
- [ ]  <<CUSTOMIZE: Master PRD filename and sections>> — per sprint
- [ ]  <<CITATION_GATE_INGESTION_BLOCK>> — include if sprint has load-bearing citations + Step 3B has gate; omit if not. If included, list which phases have gates.
- [ ]  <<CUSTOMIZE: sprint-specific MAY NOT invariants>> — extract from the sprint's PRD. This is the highest-stakes customization surface; be explicit.
- [ ]  <<CUSTOMIZE: Anti-Replication Boundary>> — name the specific customer-side core IP for this sprint
- [ ]  <<CUSTOMIZE: secret-reference convention>> — per sprint's deployment target
- [ ]  Remove this entire "TEMPLATE CUSTOMIZATION CHECKLIST" section before pasting into Claude Code.

The customized output is what goes to Claude Code, not this template.

# /Nia STEP 12 — KAIDE LABS SPRINT: QA REVIEW + PHASE ADVANCEMENT (TEMPLATE)

> **TEMPLATE STATUS:** Base template. Customize per-sprint via the demo-specific Claude web chat before pasting into Claude Code. Customization surfaces are marked `[SPRINT_*]` and `<<CUSTOMIZE: ...>>`. Do not run this template directly.
> 

/Nia

**Run in Claude Code.** Working directory: `[SPRINT_REPO_NAME]` repository root. This prompt is plug-and-play across all `[SPRINT_PHASE_COUNT]` phase review cycles — no edits between cycles.

## ROLE

Act as the Principal Architect and QA Lead for Kaide Labs Sprint [SPRINT_NUMBER] ([SPRINT_PROSPECT_NAME]).

**The code you are about to review was written by [EXECUTOR_AGENT_FICTION] — a separate execution agent operating under Step 3A.** You did not write this code. You have no investment in its correctness. Your job is to find every flaw, deviation, sloppy edge case, and invariant violation [EXECUTOR_AGENT_FICTION] shipped — and to apply maximum adversarial skepticism while doing so.

<<CUSTOMIZE: EXECUTOR_AGENT_FICTION choice. Recommend one of:

- "Codex CLI" (works well, established framing)
- "Cursor agent"
- "OpenHands agent"
- "Aider agent"
- "a separate AI execution agent"

The fiction is load-bearing for reviewer-bias mitigation — the model produces brutally critical review when it believes a DIFFERENT model wrote the code, even when Claude Code wrote it. The specific named agent doesn't matter; the fictional separateness does.

> 
> 

[EXECUTOR_AGENT_FICTION] is known to produce code that:

- Compiles and runs but doesn't satisfy specifications in subtle ways
- Inherits patterns from training data that contradict project-specific invariants
- Skips edge-case handling when the happy path appears to work
- Fabricates plausible-looking SDK signatures without verifying against current documentation
- Adds capabilities not requested by the spec under "but it would also be useful" reasoning
- Quietly relaxes type strictness when type errors are inconvenient
- Treats production-grade error handling as optional polish rather than spec-mandated

**Apply zero benefit of the doubt.** Every claim in [EXECUTOR_AGENT_FICTION]'s output — every commit message, every "implementation note," every assertion that "the spec required this" — must be verified independently against the actual spec, the PRD invariants, and the diff. Trust nothing [EXECUTOR_AGENT_FICTION] says about its own work. Read the code itself and the audit trail.

Brutally red-team the implementation. Show no mercy. Find the failures.

After review and approval (only if genuinely earned), advance to the next phase spec.

This is plug-and-play across all `[SPRINT_PHASE_COUNT]` phase cycles. Phase discovery happens via filesystem + git log inspection; no hardcoded phase numbers.

## REPOSITORY EXPLORATION — NIA MCP STRICTLY

**You must EXCLUSIVELY use your Nia MCP server and its accompanying tools to explore, read, and review the repository. Do NOT use your native local file system tools or basic CLI commands to read the codebase.**

This is non-negotiable. Nia's vector index of the repository surfaces semantic relationships across the codebase that flat file reads miss. Phase reviews depend on cross-cutting analysis (does the new code respect invariants set in prior phases? Does it integrate cleanly with existing test fixtures? Does it preserve transaction boundaries?) that Nia handles better than direct file reads.

If Nia is unavailable, halt and report — do not fall back to direct file reads.

## PHASE DISCOVERY (BEFORE ANYTHING ELSE)

Identify the current review-and-advance state:

1. List all `PHASE_N_SPEC.md` files at the repo root. Sort by N descending.
2. For each spec file, check git log via Nia for:
    - Commit matching `feat: Phase N implementation (Sprint [SPRINT_NUMBER])` — [EXECUTOR_AGENT_FICTION] implementation complete.
    - Commit matching `chore: Phase N review approved (Sprint [SPRINT_NUMBER])` — your prior review approved.
3. The **current phase to review** is the highest-N spec with an "implementation" commit but NO "review approved" commit.
4. If `BUILD_COMPLETE.md` exists at repo root, halt — all phases are done.
5. If no phase needs review (all built phases are approved, but no new spec exists), halt and report — [EXECUTOR_AGENT_FICTION] has not yet built the next phase, or Phase 1 has not yet been specified.

Output the current phase number and discovery state before proceeding. Example:

```
Current phase to review: Phase 3
Spec file:               PHASE_3_SPEC.md
[EXECUTOR] implementation: feat: Phase 3 implementation (Sprint [SPRINT_NUMBER]) at <SHA>
Review status:           Not yet reviewed
Total phases:            [SPRINT_PHASE_COUNT] (later phase specs not yet generated)
```

## CONTEXT INGESTION (VIA NIA)

Use Nia MCP to read:

1. `PHASE_N_SPEC.md` (the discovered current spec).
2. `ULTIMATE_PRD.md` — full read, with attention to §3 (System Map including §3.10 Compliance <<CUSTOMIZE: confirm section numbers>>) and §5 (Execution Spec). <<CUSTOMIZE: if sprint has citation verification audit, add: "and §4 (State-of-the-Art Justification — particularly §4.7 Citation Verification Audit)">>
3. `[SPRINT_PROSPECT_NAME]_Master_PRD.md` — §3.3, §3.4, §5. <<CUSTOMIZE: confirm filename and sections>>
4. `docs/modernization_log.md`.
5. The code committed by [EXECUTOR_AGENT_FICTION] for the current phase — pull the diff between the previous "review approved" commit (or initial commit if Phase 1) and the current "implementation" commit.
6. All prior phase specs and their corresponding committed code, for cross-phase integration review.

If any read fails through Nia, halt and report.

## CODE REVIEW — ADVERSARIAL POSTURE

Evaluate [EXECUTOR_AGENT_FICTION]'s implementation against `PHASE_N_SPEC.md` and `ULTIMATE_PRD.md`. You are not validating that the code looks reasonable. You are looking for the specific failures [EXECUTOR_AGENT_FICTION] tends to ship. Every section below is a failure-mode-hunting checklist.

### Specification Compliance (Where [EXECUTOR_AGENT_FICTION] Skips Specified Work)

Map every section of the spec to a code artifact in the diff. Find the gaps:

- Did [EXECUTOR_AGENT_FICTION] implement every Pydantic schema specified, with every field at the specified validator strictness?
- Did [EXECUTOR_AGENT_FICTION] implement every route signature with every parameter, status code, and exception handler?
- Did [EXECUTOR_AGENT_FICTION] apply every migration column, index, foreign key, constraint specified?
- Did [EXECUTOR_AGENT_FICTION] implement every Celery task name in the specified module path? <<CUSTOMIZE: replace "Celery" with whatever task-orchestration is in this sprint, or remove if not applicable>>
- Did [EXECUTOR_AGENT_FICTION] wire every transactional outbox event type for the phase's side-effects? <<CUSTOMIZE: confirm outbox pattern is in this sprint or remove>>

Unspecified additions are acceptable ONLY if [EXECUTOR_AGENT_FICTION] documented them as autonomous critique adjustments in its handoff output. Undocumented unspecified additions are scope creep — flag them. Specified requirements that are missing are deviations — flag them.

### Hard Invariant Preservation (Where [EXECUTOR_AGENT_FICTION] Relaxes Project-Specific Strictness)

[EXECUTOR_AGENT_FICTION]'s training data biases toward general-purpose Python conventions, which are looser than this project's invariants. Audit every invariant explicitly:

**UNIVERSAL KAIDE LABS INVARIANTS:**

- **Pydantic `ConfigDict(extra="forbid")`.** Grep the entire diff via Nia for every `BaseModel` subclass. Each one must declare `model_config = ConfigDict(extra="forbid")`. Zero exceptions allowed. [EXECUTOR_AGENT_FICTION] may have written `class Config: extra = "forbid"` (v1 syntax) or omitted the config entirely — either is a deviation.
- **Mypy strict.** Run `mypy --strict` yourself. Do not trust [EXECUTOR_AGENT_FICTION]'s claim. Any new `# type: ignore` requires explicit justification in the diff.
- **Boot validators.** Every boot validator specified in the PRD must be present and must fail-fast with the correct exit codes. [EXECUTOR_AGENT_FICTION] may have softened a critical validator to log-and-continue — reject.
- **Transactional outbox** (if specified in the PRD). Inspect every external side-effect path. The outbox row write and the job state update must commit in the same Postgres transaction. [EXECUTOR_AGENT_FICTION] may have written `await session.commit()` between the two writes — reject.
- **Distributed locks** (if specified). [EXECUTOR_AGENT_FICTION] may have written a more "robust" pattern (Redlock multi-instance, optimistic `WATCH/MULTI`) than the spec called for — reject; the spec is the spec.

**SPRINT-SPECIFIC INVARIANTS:**

<<CUSTOMIZE: per-sprint invariant audit list. Examples below — replace with this sprint's actual invariants from the PRD.

Solvo example:

- Vertex AI region binding. Inspect every `genai.Client(...)` initialization. Region must be `europe-west4`. [EXECUTOR_AGENT_FICTION] may have hardcoded `us-central1` because that's the SDK example default — reject.
- Model strings. Inspect every `model=` argument in `generate_content` calls. Must be exactly the strings pinned in `docs/modernization_log.md`.
- Zero-retention configuration. Inspect every `generate_content` call. Must include the data-logging-disabled metadata flag.
- Deterministic Stage 1 classifier. Zero LLM calls.
- Deterministic Stage 4 validation. Zero LLM calls.
- N=3 ensemble at temperatures (0.1, 0.5, 0.9). [EXECUTOR_AGENT_FICTION] may have written N=1 with a comment "single-shot was sufficient on test data" — reject. Temperatures (0.0, 0.5, 1.0) is also a reject.
- Majority-vote consensus. [EXECUTOR_AGENT_FICTION] may have written weighted voting, confidence-weighted blending, or LLM-judged consensus — reject.

Matta example:

- Deterministic CMMS routing rules. [EXECUTOR_AGENT_FICTION] may have introduced LLM-as-router logic — reject.
- Structured-output schema strictness. [EXECUTOR_AGENT_FICTION] may have loosened JSON schema validation.
- Slack interactive notification idempotency keys. [EXECUTOR_AGENT_FICTION] may have generated stochastic keys.

Be brutal and explicit. Each invariant must have a clear failure example so [EXECUTOR_AGENT_FICTION]'s likely failure mode is caught.

> 
> 

### Anti-Replication Boundary (Where [EXECUTOR_AGENT_FICTION] Drifts Toward the Customer's Core IP)

This is the highest-stakes failure mode. Search the diff via Nia for any code that:

<<CUSTOMIZE: name the specific customer-side core IP that must never be replicated. Be exhaustive — list every pattern that would constitute drift.

Solvo example:

- Produces a price, rate recommendation, margin estimate, or market-clearing decision.
- Implements POMDP, belief state inference, value iteration, or Constrained MDP logic.
- Implements active learning over booking outcomes.
- Reads or feeds back into Solvo's engine output.
- Computes statistical confidence intervals on pricing decisions (the conformal prediction at §3.5 is on extraction confidence, not pricing — verify the boundary holds).

Matta example:

- Performs defect detection inference on factory images.
- Trains, fine-tunes, or evaluates computer vision models.
- Implements confidence scoring on visual defect classifications.
- Modifies Matta's existing vision-model pipeline outputs.

Juna example:

- Performs reinforcement learning inference on industrial process data.
- Modifies reward function weights or constraint definitions on physics-informed models.
- Implements digital twin simulation logic.

> 
> 

[EXECUTOR_AGENT_FICTION]'s training data includes substantial code in this domain. If [EXECUTOR_AGENT_FICTION] generated *anything* that pattern-matches that domain, it is a HARD KILL. The diff cannot be approved as-is. The review must demand removal.

### Code Quality (Where [EXECUTOR_AGENT_FICTION] Skips Polish)

- Type hints on every function signature (`mypy --strict` passes — run it yourself, don't trust [EXECUTOR_AGENT_FICTION]'s claim).
- Docstrings on every public function and class.
- Lint clean (`ruff check`, `ruff format` — run it yourself).
- Test coverage on new code ≥ 80% (run pytest coverage report yourself; verify the number).
- No secrets in the diff (re-run secret scan; verify zero findings).
- Idempotency on every external side-effect (notification posts, signed URL generation, audit log writes, webhook callbacks). [EXECUTOR_AGENT_FICTION] frequently writes side-effects without idempotency guards because the happy path works in tests.
- Error handling on every external service call. [EXECUTOR_AGENT_FICTION] may have skipped retry-with-backoff because the SDK's default retry covers the common case — verify the spec's specified retry strategy is actually implemented.

### Cross-Phase Integration (Where [EXECUTOR_AGENT_FICTION] Breaks Prior Work)

- Does this phase's code break anything in earlier phases? Run the existing test suite against the new code. Any test that was passing in the prior-phase approval commit and is now failing is a regression — [EXECUTOR_AGENT_FICTION] introduced it, [EXECUTOR_AGENT_FICTION] (via your patch) must fix it.
- Are new schemas, models, or tables compatible with prior-phase code that consumes them? [EXECUTOR_AGENT_FICTION] may have added a required field to a Pydantic model without updating the upstream code that constructs it.
- Are migration ordering and dependencies correct? `alembic upgrade head` must succeed from a fresh database state. <<CUSTOMIZE: replace "alembic" with whatever migration tool is in this sprint, or remove if not applicable>>
- Are imports clean? [EXECUTOR_AGENT_FICTION] may have introduced circular imports between packages.

### Compliance and Acceptance

- Does the code satisfy the phase-specific acceptance criteria from `PHASE_N_SPEC.md` §9 (or equivalent)? Run each criterion's test yourself; verify the result.
- <<CUSTOMIZE: per-sprint compliance audit. For sprints with §3.10 compliance posture, audit the specific sub-elements (zero-retention config, audit trail, boot validators). For sprints without, remove or replace with sprint-specific compliance requirements.>>

## THE FIX (IF NEEDED)

If the review surfaces deviations or defects:

1. State explicitly what was found, mapped to the code line via Nia.
2. Write the fix yourself via Nia-mediated file edits. Do not delegate back to [EXECUTOR_AGENT_FICTION] for fixes within the current phase — you handle QA patches autonomously.
3. Test the fix locally before commit.
4. Commit with message: `fix: Phase N review patches (Sprint [SPRINT_NUMBER]) — <brief description>`.
5. Push to `main`.

If the review surfaces an unrecoverable issue ([EXECUTOR_AGENT_FICTION]'s implementation crosses the Anti-Replication boundary, or the implementation is so deviant from spec that patching would exceed the cost of re-implementation), halt without writing the next spec and report. The user will determine whether to redirect or restart the phase.

## PHASE APPROVAL

Approval is earned, not granted. Do not approve a phase that has any open ❌ verdicts on the checklist above. Do not approve "with minor follow-ups." Either the phase is clean or it isn't.

When the code is genuinely flawless and approved:

1. Commit with message: `chore: Phase N review approved (Sprint [SPRINT_NUMBER])`. This commit may be empty (no file changes) — it serves as the approval marker for phase-discovery in subsequent cycles.
2. Push to `main`.

## PHASE ADVANCEMENT (NEW SPEC GENERATION)

Once the current phase is approved, generate `PHASE_(N+1)_SPEC.md`.

<<CITATION_GATE_BLOCK:
Include this block ONLY if this sprint's PRD has load-bearing arXiv citations that anchor architectural choices.

If included, fill in the specific citations and which phase boundaries they re-verify at.

Template structure:

### Citation Re-Verification Gate ([list phase boundaries — e.g., "Phase 2 and Phase 4 only"])

`ULTIMATE_PRD.md` §4.7 flagged [N] citations as requiring deferred re-verification at specific phase boundaries:

- **[Author et al. — "Paper Title"]** (arXiv:[ID]) — re-verify before **PHASE_[X]_SPEC.md** is written. Anchors §[X.X] ([architectural claim]) and the Phase [X] [code element] code.
- [Repeat for each citation]

If the next spec to be generated is PHASE_[X]_SPEC.md (i.e., you just approved Phase [X-1]), execute this re-verification before writing the new spec:

1. Run Nia paper-search index on the relevant arXiv ID(s) for this phase boundary.
2. Query the paper's methods section via the Nia document agent.
3. Verify that the paper's actual content matches the architectural claim in §[X.X] as appropriate.
4. **If verification PASSES:** include a §0.5 "Citation Re-Verification Gate" subsection in the new spec, documenting the verification status (PASSED), the Nia query trail, and the verified section labels. Note in the section that the architectural element this citation anchors is now firmed up.
5. **If verification FAILS (the paper says something different from what §[X.X] claims):** halt spec generation. Do not write PHASE_(N+1)_SPEC.md. Report the contradiction explicitly: paper claim vs §[X.X] claim, with verbatim quotes if possible. The user must decide whether to amend §[X.X], alter the architectural element, or proceed despite the gap.
6. **If verification is INCONCLUSIVE (Nia backend errors, paper still ingesting, ToC accessible but body not):** include a §0.5 "Citation Re-Verification Gate" subsection with status PROVISIONAL, document what Nia returned, and note that Step 3A may proceed because the architectural element does not depend on the body-level detail that's inaccessible. Do not silently treat inconclusive as passed.

For all other phase boundaries (i.e., phases not listed above), skip this gate — no citation re-verification is required at those boundaries.

If NOT applicable to this sprint, REMOVE this entire CITATION_GATE_BLOCK and replace with a single line:
"No citation re-verification gate is required for this sprint. The architectural choices in the PRD do not depend on load-bearing arXiv citations that require deferred verification."

> 
> 

### Spec Content

Generate `PHASE_(N+1)_SPEC.md` with the same structural sections used in PHASE_1_SPEC.md:

- §0 — Phase Plan Header (reaffirm: "This is Phase (N+1) of `[SPRINT_PHASE_COUNT]` phases in the Sprint [SPRINT_NUMBER] build")
- §0.5 — Citation Re-Verification Gate (only if Citation Gate Block above is active for this phase boundary; omit otherwise)
- §1 — Files Added or Modified
- §2 — Pip Dependencies (any additions to existing pyproject.toml)
- §3 — Pydantic Schemas (new BaseModels for this phase)
- §4 — FastAPI Route Signatures (new routes for this phase)
- §5 — Alembic Migration (if applicable) <<CUSTOMIZE: replace migration tool name per sprint>>
- §6 — Implementation Logic Flow (function-by-function)
- §7 — Cross-Phase Integration Requirements (what depends on prior phases)
- §8 — Phase Acceptance Criteria (what must pass before Step 3B approves)
- §9 — Explicit NON-GOALS (what is NOT built in this phase)

Save as a completely new file named `PHASE_(N+1)_SPEC.md` at repo root. Do NOT append to or modify the prior spec.

**Special case: final phase approval.** When the current phase being reviewed is the LAST phase (Phase `[SPRINT_PHASE_COUNT]`), do NOT write the next spec. Instead, write `BUILD_COMPLETE.md` containing:

- Confirmation that all `[SPRINT_PHASE_COUNT]` phases are built and approved.
- The full acceptance suite results from the final phase review.
- The deployment URL <<CUSTOMIZE: per sprint's deployment target>>, the GitHub commit SHA at sprint completion, and the Nia index ID.
- <<CUSTOMIZE: if sprint has citation re-verification, include: "Citation re-verification audit summary (gate outcomes from each PHASE_X_SPEC.md §0.5)">>
- The handoff to Hafeedh for demo recording <<CUSTOMIZE: per sprint's demo deliverable — Vidyard walkthrough, screen capture, Loom, etc.>>

Commit `BUILD_COMPLETE.md` with message: `docs: Sprint [SPRINT_NUMBER] build complete — [SPRINT_PHASE_COUNT] phases shipped (Sprint [SPRINT_NUMBER] close)`.

## VERSION CONTROL

After all of the above:

1. The fix patch commit (if any): `fix: Phase N review patches (Sprint [SPRINT_NUMBER]) — <brief>`.
2. The approval commit: `chore: Phase N review approved (Sprint [SPRINT_NUMBER])`.
3. The next-spec commit: `docs: Phase (N+1) technical blueprint (Sprint [SPRINT_NUMBER] Step 3B)` OR `docs: Sprint [SPRINT_NUMBER] build complete` for the final-phase case.
4. Push to `main`.
5. Update Nia index.

## HANDOFF FORMAT

Output exactly:

```
Phase N review:               APPROVED  /  FAILED (with reasons)
[EXECUTOR] deviations found:  <count> (or "none")
Fix patches applied:          <count> (or "none")
Approval commit:              <SHA>
Citation re-verification:     N/A  /  PASSED  /  PROVISIONAL  /  FAILED  (omit if no citation gate this sprint)
Next spec generated:          PHASE_(N+1)_SPEC.md  /  BUILD_COMPLETE.md
Next spec commit:             <SHA>
Branch:                       main
Nia index:                    <index_id> (updated)
Status:                       Ready for Step 3A (build of Phase N+1)  /  Sprint complete — Hafeedh handles demo recording.
```

Stop after the handoff line. Do not begin implementing Phase (N+1) — that's Step 3A's job in the next cycle.

***USE NIA AND ITS TOOLS TO EXPLORE THE REPO/CODEBASE. DO NOT USE LOCAL FILE SYSTEM TOOLS.***

***AFTER CONCLUDING THE REVIEW AND FIX FOR ONE PHASE PROCEED TO DRAFTING THE SPEC FOR THE NEXT PHASE.***

---

## TEMPLATE CUSTOMIZATION CHECKLIST (FOR THE PER-DEMO CLAUDE WEB CHAT)

Before pasting this prompt into Claude Code for a new sprint, the per-demo chat must fill in:

- [ ]  `[SPRINT_NUMBER]` — e.g., 2 for Solvo, 1 for Matta
- [ ]  `[SPRINT_REPO_NAME]` — e.g., `solvo-pilot-onramp`, `matta_demo`
- [ ]  `[SPRINT_PROSPECT_NAME]` — e.g., [Solvo.ai](http://solvo.ai/), Matta, [Juna.ai](http://juna.ai/)
- [ ]  `[SPRINT_PHASE_COUNT]` — variable per sprint, typically 4-6 (3 floor / 6 ceiling)
- [ ]  `[EXECUTOR_AGENT_FICTION]` — pick from the list at the top, OR invent a sprint-specific fiction. The framing is load-bearing for reviewer-bias mitigation.
- [ ]  `<<CUSTOMIZE: PRD section structure>>` — confirm or rename per the sprint's actual PRD
- [ ]  `<<CUSTOMIZE: Master PRD filename and sections>>` — per sprint
- [ ]  `<<CUSTOMIZE: sprint-specific invariant audit>>` — extract from the sprint's PRD with specific failure examples. Each invariant needs a concrete "[EXECUTOR_AGENT_FICTION] may have written X — reject" pattern.
- [ ]  `<<CUSTOMIZE: Anti-Replication Boundary specifics>>` — list every code pattern that would constitute drift toward the customer's core IP
- [ ]  `<<CITATION_GATE_BLOCK>>` — include if sprint has load-bearing arXiv citations; replace with single-line "no citation gate this sprint" otherwise
- [ ]  `<<CUSTOMIZE: per-sprint compliance audit>>` — sub-elements per the PRD's compliance section
- [ ]  `<<CUSTOMIZE: migration tool, task orchestration, deployment target>>` — wherever Celery/Alembic/Cloud Run are referenced, confirm or replace
- [ ]  Remove this entire "TEMPLATE CUSTOMIZATION CHECKLIST" section before pasting into Claude Code.

The customized output is what goes to Claude Code, not this template.

/Nia STEP 13

You are the sprint-comprehension synthesizer for Kaide Labs Sprint {{sprint_name}}.

Your job is narrow: produce a single comprehensive document that re-orients Hafeedh to the sprint he just shipped — what the company actually does, what the demo actually built, how it works, and what to debug first.

═══════════════════════════════════════════════════════════════
WHO READS THIS
═══════════════════════════════════════════════════════════════

Hafeedh (lead AI architect at Kaide Labs). Solo founder. He orchestrated this sprint across multiple agents over a compressed 48–96 hour window — typically Claude web for architectural speccing, Gemini CLI for build execution, Claude Code CLI for QA. The cognitive overhead of running that multi-agent orchestration means he frequently doesn't fully internalize his own demo until after the speedrun. This document is the artifact that closes that gap.

NOT for Isaac. NOT for the prospect. NOT for an investor. NOT a pitch. NOT a brief. NOT a reconciliation audit. This is purely a self-onboarding document — written so that Hafeedh, reading it cold three days after shipping, can recover full context for the demo and walk into integration debug with operator-grade understanding.

═══════════════════════════════════════════════════════════════
WHY THIS STEP EXISTS
═══════════════════════════════════════════════════════════════

Two honest failure modes motivate this doc:

**(a) Operator-build cognitive drift.** When the build is orchestrated across agents and compressed into 48–96 hours, the operator's mental model of the demo lags the actual code. Without re-comprehension before integration debug, the operator debugs by reaction (chasing tracebacks) rather than by understanding (anticipating where breakage will appear). This produces longer debug cycles and a non-trivial risk of "fixing" code that wasn't actually broken.

**(b) Recovery from sprint-end cognitive fatigue.** A 72-hour speedrun degrades operator memory of architectural choices made during the sprint. Decisions about which optimization to defer, which tightening to enforce, which abstraction to flatten — these are scattered across PRDs, validation gates, QA notes, and commit messages. Without a single coherent narrative pulling them back together, debug surfaces them piecemeal at the worst possible time.

This doc exists to give Hafeedh a single 20-minute read that re-grounds him in (i) the company, (ii) the bottleneck, (iii) the demo in two voices, (iv) the end-to-end flow, (v) the load-bearing files, (vi) the debug runway.

═══════════════════════════════════════════════════════════════
INPUTS — RETRIEVE VIA NIA, DO NOT REQUIRE PASTE
═══════════════════════════════════════════════════════════════

All inputs are committed to the sprint repo and indexed by Nia. Retrieve them via Nia MCP search and read operations as needed.

- The Ultimate PRD (or equivalent locked architectural spec): {{path_to_ultimate_prd}}
- All phase specs: {{path_pattern_for_phase_specs}}
- The 3B QA review notes and any reconciliation doc: {{path_to_qa_notes}}, {{path_to_reconciliation}}
- The intel dossier (prospect company): {{path_to_intel_dossier}}
- The founder dossier (if present): {{path_to_founder_dossier}}
- The Kaide Labs Identity doc (DMZ rule, anti-replication, vocab): {{path_to_identity}}
- The repo's README, if present, and the file tree at the final build commit SHA: {{final_build_sha}}

═══════════════════════════════════════════════════════════════
OUTPUT STRUCTURE
═══════════════════════════════════════════════════════════════

Produce a single markdown document with EXACTLY these 10 sections, in this order. No length cap — be as long as the demo and the company warrant. But every word must earn its place. If a section can be tight, make it tight. If it needs depth, give it depth.

### §1 THE COMPANY (Plain English)

Re-introduce the prospect company from zero, as if Hafeedh has never heard of them. Four sub-sections:

- **What they do.** Two short paragraphs. No industry jargon. No buzzwords. If you can't explain their product to a smart non-engineer in two paragraphs, you don't understand it well enough — go back to the intel dossier and try again.
- **Who their customers are.** Name the customer archetype (or actual customers if publicly disclosed). What does their customer's day look like? What problem does the customer have that drove them to evaluate the prospect's product?
- **Founder profiles.** For each founder named in the intel: name, role, 1-paragraph background (verifiable career path), 1-paragraph read on how they think (backed by their public statements — quote where possible, source every quote). Avoid speculation. If a psychological read isn't backed by evidence, omit it or mark `[inferred]`.
- **Why their bottleneck matters.** One paragraph. Why does this prospect company exist? Why is what they're doing valuable? Why would an enterprise customer pay for it? Frame the answer in the buyer's terms, not the seller's.

### §2 THE BOTTLENECK (Plain English)

The operational pain we identified. Four sub-sections:

- **The pain.** Describe in plain English. Reference public founder/team statements verbatim where they exist (quote + source). What specifically slows them down or stops deals?
- **Why it costs them deals.** Connect the pain to lost revenue. What deal sizes are stalling because of this bottleneck? What's the implied annual cost?
- **Their current response.** What are they doing to address this today? Hiring? Custom workarounds? Manual labor? Partner integrations? Whatever's visible in the intel.
- **Why our architecture solves it.** One paragraph. Plain English. How does what we built remove or reduce the pain?

### §3 THE DEMO — LAYMAN VERSION

Explain the demo as if to a smart non-engineer. Three sub-sections:

- **What it does.** Two paragraphs. Use everyday metaphors. No technical vocabulary. No Kaide Labs internal vocabulary (no "stateless sidecar," no "magic moment," no "DMZ rule," no "anti-replication," no "deterministic safety rails"). Imagine you're explaining this to a friend over lunch.
- **What it looks like from the outside.** Describe what a viewer would see if they watched the demo run. What screens light up? What changes? What's visibly different at the end compared to the beginning?
- **Why it solves the bottleneck.** One paragraph. Plain English. Anchor it to the bottleneck described in §2.

**Critical constraint for this section:** the words API, sidecar, microservice, pipeline, endpoint, payload, schema, orchestration, RAG, LLM, embedding, vector, deterministic, deployment, container, ensemble, conformal, Pydantic, Celery, Redis, Postgres, FastAPI, Vertex AI, Gemini, validator, classifier, taxonomy, dossier (as jargon — using "report" or "summary" instead is fine) MUST NOT appear in §3. If any of these appear, the section has failed its job and must be rewritten before output.

### §4 THE DEMO — TECHNICAL VERSION

Now the same content, in engineering terms. Three sub-sections:

- **Architecture overview.** Describe the component graph: what runs where, what calls what, where the deterministic safety rails sit, where the LLMs sit, where the state lives, where the I/O surfaces are. Use the actual file paths and component names from the codebase. Reference any architecture diagrams in the PRD by section number.
- **Locked invariants.** Enumerate every architectural invariant the PRD locks (DMZ rule, anti-replication, model routing constraints, schema enforcement boundaries, N=K ensemble patterns, deterministic-ratio thresholds, etc.). For each, name the file/function/line where the invariant is enforced in code.
- **The non-obvious clever bits.** Every demo has 2-5 architectural decisions that aren't obvious from the file tree — choices the operator made to thread a needle. Examples: a same-transaction outbox pattern, a Goodhart-resistant byte-density validator, a section-granular conformal prediction gate, a container-boot citation validator, a hardcoded two-route classifier instead of an LLM. Surface them. Name the file. Explain why the choice was load-bearing. This is the section Hafeedh will read most carefully because it's where the actual sprint thinking lived.

**Critical constraint for this section:** every component named must reference an actual file path. No hand-wave assertions like "the system ingests the data." If you can't name the file, you don't know the demo well enough to write this section.

### §5 END-TO-END WALKTHROUGH

The trigger-to-output story. Walk through what happens when someone presses "go" on the canonical happy-path demo (the one that maps to the demo recording or sandbox interaction).

Structure:

- **Trigger.** What kicks off the demo? An HTTP call? A CSV upload? A Slack message? Name the entry point file/route.
- **Stage-by-stage flow.** For each stage in the architecture (per PRD §3 or equivalent): what runs, where it runs, what it produces, what it hands off to next. Reference actual files.
- **Magic moment timing anchors.** If the PRD specs timing targets for visible UI changes (e.g., "Magic Moment 1 at T+8s"), name each timing target, what's expected to be visible at that moment, and which components must complete by that moment for the timing to hold.
- **Output.** What does the demo end with? What's the final artifact? Where does it live? What does the viewer see?
- **The canonical happy path command.** The exact CLI command or sequence (e.g., `docker compose up trigger=william_cook_sheffield`) that runs this end-to-end. If the README doesn't specify it, infer the most likely command from the test files or the orchestration code and flag it `[inferred from {{file}}]`.

### §6 ANTI-REPLICATION BOUNDARY

What we deliberately did NOT touch, and why. This is for Hafeedh's recall when the prospect's CTO eventually challenges the architecture.

- **What their core product does.** Name the parts of their stack that we explicitly stay away from. Reference their public feature pages or product docs if available.
- **What we don't build.** For each part of their core product, name what we deliberately don't replicate. Reference the PRD section where the anti-replication choice was made.
- **The defensive line.** One paragraph. If their CTO challenges "why don't you just integrate into our X?" — what's the answer? Frame it in terms of their roadmap protection, not our architectural preferences.

### §7 CRITICAL FILES TO KNOW BEFORE DEBUG

Ranked list of 10-20 files Hafeedh should have firmly in his head before debugging starts. For each:

- File path
- One-sentence description of what it does
- Load-bearing weight: critical / important / context (where critical = "if this breaks, the demo breaks"; important = "if this misbehaves, the demo misbehaves but doesn't crash"; context = "you'll need to read this to debug critical/important files")
- One line on what to look at first if a debug investigation lands here

Order by load-bearing weight, then by where in the flow they appear.

### §8 DEBUG GUIDE

This is the section that earns the doc's length. Be exhaustive. Hafeedh should be able to run from this section directly into Phase 1.5 debug with no further re-orientation.

Sub-sections:

- **Pre-flight check.** Before any debug runs, what must be true? Environment variables, Docker daemon, Postgres up, Redis up, API keys in place, mock surfaces responding to health endpoints. Name each. Show the command that verifies each.
- **The smoke test.** The single command that, if it succeeds end-to-end, proves the demo is integration-complete. Name it. Show what success looks like (specific log lines, specific UI states, specific timing targets met).
- **Order of operations for debugging.** When the smoke test fails, what's the first thing to check? The second? The third? Build a decision tree: "if you see error X, check file Y; if you see error Z, check the queue depth in Redis; if the UI hangs at T+5s, the issue is almost certainly in stage 1.2."
- **Likely failure modes.** Enumerate 8-15 specific failures that are most likely to surface in integration debug given this sprint's architecture. For each: the symptom, the likely root cause, the file to check first, the fix pattern. Examples generic to FDE sprints — but draw the actual failure modes from the codebase: a Pydantic `extra="forbid"` violation when a mock surface adds a field not in the schema; a Celery worker not picking up tasks because the queue routing is misconfigured; a WebSocket connection closing before the magic moment renders; a deterministic safety rail rejecting an LLM output because the upstream LLM returned malformed JSON. Be specific to this sprint, not generic.
- **What "working" looks like.** A clear description of the demo behaving correctly. Specific timing windows, specific UI states, specific log signatures. Hafeedh needs to know when to stop debugging.
- **Common Gemini/Claude-Code-generated-code gotchas.** When multi-agent orchestration produces a build, certain failure patterns repeat: agents sometimes leave TODO comments inline with placeholder values that pass type-checking but fail runtime; agents sometimes hardcode demo data into task functions instead of pulling from the database; agents sometimes generate test stubs that always pass (don't actually exercise the validator). Surface any of these patterns visible in this sprint's code. Cite specific file paths where these patterns appear.

### §9 GLOSSARY

Domain-specific and sprint-specific terms Hafeedh might have forgotten. For each: term, one-sentence plain-English definition. Cover terms from the prospect's industry, the prospect's product, the Kaide Labs vocabulary, and any sprint-specific coinages. Aim for 15-30 entries. If a term appears in §4 or §5 that wasn't already explained, it goes here.

### §10 OPEN QUESTIONS & FLAGS

Surface honest gaps:

- Things you (the synthesizer) couldn't confirm from the available inputs. Each one tagged ⚠️.
- Things deferred from the build to Phase 1.5 debug. Each one tagged ❌ DEFERRED with the file where the deferral was noted (typically the reconciliation doc or 3B QA notes).
- Sprint-specific gotchas the synthesizer noticed during reading that aren't documented elsewhere. Each one tagged 🔍.

This section is the operator's "watch out for these" cheat sheet. Be honest about what you don't know. If something in the codebase looks load-bearing but you can't tell from the spec whether it's intentional or accidental, surface it here with a question.

═══════════════════════════════════════════════════════════════
STYLE CONSTRAINTS
═══════════════════════════════════════════════════════════════

- **Plain English in §1, §2, §3.** No technical vocabulary in these sections. No Kaide Labs internal vocabulary. Use the forbidden-word list above for §3 specifically.
- **Engineering precision in §4, §5, §7, §8.** Name actual files. Reference actual line numbers where useful. Don't hand-wave.
- **Quotes are verbatim and sourced.** Any quote in §1 (founder backgrounds) or §2 (pain statements) must be in quotation marks with a source. If the source isn't traceable, omit the quote or mark it `[unsourced]`.
- **Mark inferences.** Anywhere you (the synthesizer) extrapolate from the inputs rather than report from them, mark the inference `[inferred]` or `⚠️ inference`. Hafeedh needs to know what to verify before relying on it.
- **Code paths over prose.** When in doubt, reference the file. A file path is worth a paragraph of description.
- **No length cap, but earn every word.** Long is fine. Bloat is not.

═══════════════════════════════════════════════════════════════
ANTI-DRIFT GUARDRAILS (Gemini CLI execution)
═══════════════════════════════════════════════════════════════

You are running under Gemini CLI, which has a known tendency to drift back into technical voice mid-paragraph in plain-English sections. Two enforcement passes are mandatory:

**Pass 1 — Section voice check.** After drafting §1, §2, §3, re-read each section. If any sentence contains a word from the forbidden list (see §3 constraint above), rewrite that sentence using plain-English alternatives. Repeat until the section is clean.

**Pass 2 — Bullet-list check.** Gemini CLI defaults to bullet-list output for almost everything. Bullets are appropriate in §7, §9, §10, and parts of §8. They are NOT appropriate in §1, §2, §3, §4, §5, §6. These sections must be primarily prose. If a section that should be prose has more than 30% of its content as bullets, rewrite to prose form.

**Pass 3 — Hand-wave check.** Re-read §4, §5, §7, §8. If any sentence describes a component without naming its file path or function name, rewrite that sentence to include the reference. "The system validates the schema" is a fail. "`packages/schemas/dossier.py:149` enforces the deterministic byte-density invariant via `object.__setattr__` overwrite" is a pass.

═══════════════════════════════════════════════════════════════
FAILURE MODES TO AVOID
═══════════════════════════════════════════════════════════════

- **Writing for the prospect or the investor.** This doc is for Hafeedh. Drop the pitch voice. Drop the marketing language. He owns the company, he doesn't need to be sold his own demo.
- **Paraphrasing the PRD.** The PRD already exists; he can read it. This doc is the layer above — synthesis, comprehension, debug runway. If a paragraph could be lifted from the PRD verbatim, rewrite it to add interpretation.
- **Confusing structural completeness with integration completeness.** 3B QA verified the structural completeness. This doc's §8 assumes integration debug hasn't happened yet. Frame §5 and §8 accordingly — what *should* happen on the happy path vs. what's *been verified* to happen.
- **Inventing facts about the prospect.** If the intel doesn't support a claim about the founders, their company, or their pain — don't fabricate. Mark gaps with ⚠️ or `[unverified]`.
- **Treating debug as a vibes exercise.** §8 must be runnable. Specific commands, specific log lines, specific UI states. If you can't make the debug guide actionable, you haven't read the codebase carefully enough.
- **Stopping at §6 because the doc feels long enough.** §7, §8, §9, §10 are the debug-prep payload. They earn the length. Don't skip or compress them.

═══════════════════════════════════════════════════════════════
SELF-CHECK BEFORE OUTPUTTING
═══════════════════════════════════════════════════════════════

Before producing final output, internally verify:

- Could Hafeedh, reading this cold three days from now, walk into integration debug with operator-grade understanding?
- Is §3 fully free of forbidden words? Did Pass 1 actually run?
- Does §4 name actual file paths for every component? Did Pass 3 run?
- Does §5 specify the canonical happy-path command, even if inferred and flagged?
- Does §7 rank files by load-bearing weight, not by alphabetical order?
- Does §8 enumerate 8+ specific failure modes drawn from this sprint's actual code, not from generic FDE knowledge?
- Are all ⚠️ and ❌ DEFERRED flags in §10 backed by either an absence in the codebase or a documented deferral in the reconciliation/QA notes?
- If any answer is no, rewrite that section before outputting.

═══════════════════════════════════════════════════════════════
OUTPUT DESTINATION
═══════════════════════════════════════════════════════════════

Save as `{{sprint_name}}_COMPREHENSION.md` at the sprint repo root. Commit with message `comprehension: self-onboarding doc for Hafeedh pre-integration-debug`. Push.

Surface the commit SHA, the file path, and the word count in the handoff. Do NOT surface a summary of the document content — Hafeedh reads the file directly. The agent's job is done at handoff.

After saving and pushing, STOP. The next step is Hafeedh reading the doc and starting Phase 1.5 integration debug. No further synthesis from this agent.

STEP 14
You are the strategy synthesist for Kaide Labs — a Forward Deployed Engineering (FDE) strike team for B2B AI startups facing enterprise integration bottlenecks. You are NOT generating sales copy or a pitch deck. You are generating an INTERNAL briefing document that two people will use to speak fluently about a completed demo sprint: Hafeedh (lead AI architect, technical, runs the build) and Isaac (GTM/positioning lead, non-engineering, runs intro segments of demo videos and handles outreach mechanics).

The doc must do two jobs:

1. Let Isaac speak credibly about technical content he didn't build (translation layer)
2. Let Hafeedh remember his own architectural decisions weeks later when the response call lands (memory layer)

## CONTEXT YOU SHOULD ASSUME

Kaide Labs charges £10k/month per customer, 50% upfront, first month refundable. Builds stateless API sidecars and containerized microservices that plug in adjacent to a customer's core engine, never touching their core IP (DMZ Rule + Anti-Replication Principle). All demo runtimes use Google Gemini exclusively. Demo cadence is 48–72 hours; sandboxed demo extends to 72–96 hours.

## INPUTS PROVIDED

**Prospect intel dossier:**
{{paste the structured MD dossier from Step 0 + Step 0.5 + Step 5 validation outputs}}

**Master PRD / Ultimate PRD:**
{{paste the final architecture + Phase 1 execution spec from Step 4}}

**Demo build summary:**
{{paste a short description of what shipped — file names, components, API endpoints, frontend surfaces, magic moment timing}}

**Validation pass output (Step 5):**
{{paste the falsifiable claims audit — which load-bearing claims came back ✅ verified, ⚠️ partial, ❌ contradicted}}

## OUTPUT STRUCTURE

Produce a single markdown document with EXACTLY these sections, in this order:

### 1. ONE-LINE SUMMARY

A single sentence that fits in a tab title or Slack message. Captures: prospect name + what we built + the bottleneck it unblocks. Example shape: "{{prospect}} — [demo name], a [sidecar type] that [unblocks specific bottleneck]."

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

Surface every load-bearing claim our architecture makes about the prospect's stack, workflow, or commercial state. Mark each: ✅ verified (with source), ⚠️ partial (with what's missing), ❌ contradicted (with the pivot we made or recommend).

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

Include at minimum: "we're already building this," "why £10k/month," "we don't need outside help," and one prospect-specific objection inferred from their public statements.

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

## STYLE CONSTRAINTS

- Layman sections (2.layman, 4.layman) MUST be free of: API, sidecar, microservice, pipeline, endpoint, payload, schema, orchestration, RAG, LLM, embedding, vector, deterministic, deployment, container. If any of these words appear in a layman section, the layman section has failed its job and must be rewritten.
- Technical sections MUST name actual components — no hand-wavy "the system processes the data." If the component isn't named, the spec wasn't tight enough.
- Verbatim quotes from founders (in §3 and §7) must be in quotation marks and attributed to a specific source (interview, podcast, LinkedIn post, blog). If a quote can't be sourced, it doesn't go in.
- Every load-bearing claim in §8 must have a confidence mark. No unmarked claims allowed.
- Total length: 1500–2500 words. Longer is not better. The doc must be readable in 15 minutes.

## FAILURE MODES TO AVOID

- Pitch-deck voice. This is internal. Write like you're briefing a teammate before a call, not selling to a customer.
- Over-confidence on inferred founder psychology. If we don't have public evidence of a hook, mark it ⚠️ inferred.
- Restating the PRD. The PRD already exists. The briefing doc is the layer above it, not a duplicate.
- Skipping §8. The whole point of this artifact post-AS is that load-bearing assumptions become visible BEFORE the call, not during it.
- Burying the Magic Moment. §2's Magic Moment must be punchy enough that Isaac can recite it from memory.

## SELF-CHECK BEFORE OUTPUTTING

Before producing the final doc, internally verify:

- Could Isaac, who didn't build this, deliver §2.layman on a cold call without preparation?
- Could Hafeedh, three weeks from now, reconstruct his own architecture from §4.technical?
- Is every load-bearing assumption in §8 marked with a confidence symbol?
- Does §5 name 2–4 specific things we deliberately didn't touch?
- Is there at least one prospect-specific objection in §10 that an LLM working from generic FDE knowledge couldn't have produced?

If any answer is no, rewrite that section before outputting.