---
type: prospect_overview
prospect: artificial_societies
vertical: synthetic_societies_ai
geography: san_francisco_yc
stage_at_engagement: yc_w25_seed
funding_state: "YC W25 batch, seed-equivalent"
employee_count_at_engagement: ~10
outcome: lost
sprint_status: closed
killed_reason: null
archived_reason: null
key_tags:
  - yc_batch_prospect
  - cpo_engagement
  - load_bearing_assumption_failure
generalizable_learnings:
  - primary_source_verification_before_sprint_commit
  - marketing_page_contradicts_assumption_pattern
  - relationship_win_vs_deal_win_distinction
related_prospects:
  - solvo
  - matta
created: 2026-03-XX
last_updated: 2026-05-21
---

# Artificial Societies

## One-paragraph summary

YC W25 batch company building synthetic-society research AI. Engaged with CPO Patrick Sharpe over multiple touchpoints. Demo was sent. Conversation went well. Deal did not close because the architecture rested on a load-bearing assumption about AS's product capabilities that was directly contradicted by AS's own marketing page — a verification that would have killed the proposal before sprint commit if Step 1F had existed at the time.

## Company snapshot

Artificial Societies is a YC W25 batch company headquartered in San Francisco with a London engineering presence, building synthetic-society simulation infrastructure for enterprise market research and strategic communications. The company raised approximately $5.35M in seed funding, led by Point72 Ventures with angel participation from operators connected to DeepMind, Strava, and Sequoia Scout.

In Q1 2026, James He publicly announced that AS would discontinue public access to the consumer Artificial Societies platform and focus entirely on bespoke enterprise services. The flagship product going forward is **Radiant** — a simulation engine that constructs interactive social graphs of 300–5,000 LLM-backed personas, drawn from a database of 2.5M+ persona profiles, and runs message-propagation simulations against a target audience for F100 strategic communications, investor relations, and policy affairs teams. The self-reported accuracy claim is 95% versus human self-replication on >18M synthetic responses. The named F100 case study is Teneo, where Radiant simulated ~180,000 perspectives across specialized synthetic societies (consumers, industry peers, policymakers).

The company's intellectual moat is the social-graph simulation methodology — the network dynamics, not isolated persona interrogation (which is what single-persona competitors like Ditto do). That moat is grounded in James He's published research on artificial collective behavior. Radiant's promise to F100 customers is a 24-hour turnaround on bespoke simulations.

## Founder team

The pitch was tailored across three founders with distinct buying triggers.

### James He — Founder & CEO

Cambridge Behavioural Sciences (First Class Honours, dissertation top 10%). Lead author of the peer-reviewed paper underwriting Radiant's methodology. Left Sichuan at 14 to study independently in New Zealand; founded the largest Go school in NZ during high school; rejected a 5M-RMB VC offer for an AI EdTech concept while still a teenager. His communication on LinkedIn is deliberately provocative and macro — he frames Radiant as a "wind tunnel" for public policy and explicitly challenges the market to "quit McKinsey, BCG and Bain" to join AS. His own Hacker News post on the consumer Societies launch named accuracy and UI as the two biggest scaling challenges. Engagement vector: macro-vision framing, institutional disruption thesis, automated strategic synthesis as MBB replacement.

### Patrick Sharpe — Founder & CPO

MSc Behavioural Economics (cum laude, Erasmus School of Economics). Behavioural science career at Swiss Re and ReAssure before AS — extensive corporate-experimentation experience inside heavily regulated financial institutions. Public posture is workflow-efficiency-first: his repeated framing is that Radiant compresses traditional market-research timelines by 100× ("Imagine running customer research 100s faster"). He is the natural internal champion for any architecture that removes onboarding friction or introduces deterministic safety, because his regulated-industry background makes him acutely sensitive to generative AI's failure modes in corporate settings. Engagement vector: speed-to-value, user-experience optimization, friction removal. **The primary commercial contact for the AS engagement.**

### Tom Whittle — Co-Founder & CTO

Forbes 30 Under 30 (2026). Non-traditional path into engineering — Le Wagon coding bootcamp into KEY ESG and Stotles before AS. Public reputation describes "natural presence/gravitas" in senior corporate meetings, a "sponge for learning," and a stated motivation that he "loves building beautiful things." Stack: TypeScript, React, Next.js, Node, Python, AWS/GCP. He is the architectural arbiter — any proposal that overlaps with shipped IP or feels like a marketing demo will fail his ego-check immediately. Engagement vector: zero-debt sidecar architecture, modular boundaries, explicit "Unplug Guarantee," aesthetic respect for the existing stack.

## The bottleneck identified

The architectural thesis was that Radiant's 24-hour bespoke turnaround promise is structurally bottlenecked not by simulation runtime (which is 30 seconds to 2 minutes) but by the pre-field manual data ingestion phase — translating each F100 customer's heterogeneous research warehouse (Kantar/Ipsos exports in SPSS, Dovetail transcripts, Qualtrics .qsf files, CRM segment CSVs, ethnography PDFs, brand-guideline decks) into the structured calibration JSON Radiant's simulation engine actually consumes. Industry-standard custom-segment stand-up is 6–12 weeks; AS's six-person team handles this as bespoke consulting work, which is why they could only serve a handful of named F100 logos at engagement time.

The Teneo case study was the citation anchor — to deliver 180,000 simulated perspectives across multiple specialized synthetic societies, somebody on the AS team had to manually transform Teneo's raw research artifacts into a structured calibration payload. That manual translation step was the target.

## Why this passed Anti-Replication (at design time)

The RCS sidecar was scoped to operate strictly upstream of Tom Whittle's core engine and to emit a single validated JSON payload that the engine's existing audience-build path consumes as-is. Specifically NOT touched:

- The 2.5M-persona database
- The multi-agent network simulation engine (1000+ agent simulation runtime)
- The 95%-accuracy methodology and the published research underlying it
- The Pulsar social-listening data pipe (which remains the dynamic input feed; RCS is the static enterprise-artifact feed and is explicitly complementary)
- The existing network-visualization UI
- The Mirror World product (LinkedIn URL → persona chat — a separate product line and a different input shape)
- The Reach consumer product (sunset in Q1 2026)
- The audience-build path inside Radiant; RCS terminates at JSON handoff

If AS unplugged the sidecar tomorrow, their product still works — the team just reverts to manual artifact ingestion as the bottleneck. The "Unplug Guarantee" was explicit in the architectural framing for Tom.

The Ego Check passed on paper across all seven risk vectors (UI replication, simulation-engine modification, product overlap with shipped features including Reach/Mirror World/Pulsar, persona-DB competition).

## The load-bearing assumption that failed

The architecture rested on a load-bearing assumption about AS's data-intake requirements that was directly contradicted by AS's own marketing page. The contradiction was visible to anyone who read the page adversarially. It was not surfaced during Step 0.5 dossier construction, not caught during Step 1A research, and not caught during the then-existing Step 1B red-team — which focused on architectural soundness rather than on falsifiable load-bearing claims. The first time the assumption was tested against AS's primary materials was during the post-demo conversation with Patrick Sharpe, at which point the proposal was structurally unrecoverable.

Patrick was generous about the gap. The conversation stayed productive. The relationship was retained. The deal was not.

The pipeline consequence:

- **Step 0b** was added — intel cleanup as a discrete stage to strip aggregator noise and LinkedIn UI chrome before downstream reasoning
- **Step 1F** was added — falsifiable claim audit, requiring every load-bearing claim in an architecture proposal to be verified against the customer's own public primary sources before sprint commit (~30-minute cost, prevents the AS failure mode)
- **Step 1F-red** was added — red-team with go/no-go authority over the architecture proposal, scoped to falsifiable claims rather than purely architectural soundness ("what would kill this if surfaced in the sales conversation?")

Every prospect engagement filed after AS passes through these gates. The cost is ~1 hour total per engagement. The benefit is preventing the AS failure mode from recurring.

## Outcome

See `outcome.md`. Result: deal loss, relationship retention (Patrick Sharpe remains a dormant-warm contact). Generalizable learnings filed and applied across the substrate.
