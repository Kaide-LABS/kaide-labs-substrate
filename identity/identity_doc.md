---
type: identity
domain: canonical_positioning
last_updated: 2026-05-19
canonical: true
supersedes: null
key_tags:
  - fde_strike_team
  - b2b_ai
  - stateless_sidecars
  - anti_replication
  - dmz_rule
  - 5_pillar_standard
  - substrate_strategy
  - halal_vertical_filter
  - zero_debt_revenue_unblocking
---

# **KAIDE LABS: CORE IDENTITY & POSITIONING MANIFESTO**

*The anchor document for all outreach, engineering, and AI prompts. Every research prompt, red-team audit, and demo architecture must be grounded in this document.*

## **1. WHO WE ARE (The Identity)**

We are a **Forward Deployed Engineering (FDE) Strike Team**.
We operate at the intersection of AI Engineering, Systems Architecture, and Enterprise Sales.
**We are NOT an agency. We are NOT a dev shop. We do NOT sell engineering hours.**

## **2. THE PROBLEM WE SOLVE (The Pain)**

AI Startups want to close Enterprise/B2B deals. But Enterprise clients demand bespoke, peripheral features (custom data ingestion, InfoSec compliance, legacy formatting) before they buy.
If the startup pulls their core engineering team off the main product to build these edge-cases, their roadmap dies. If they don't build them, the deal dies.

## **3. OUR SOLUTION (The Offer)**

**Zero-Debt Revenue Unblocking.**
We build the peripheral edge-cases so the core team doesn't have to.

## **4. OUR ARCHITECTURE (The Technical Boundary)**

We build **Stateless API Sidecars** and **Containerized Microservices**.

* **Distinct, Adjacent, Modular:** We NEVER touch, rewrite, or integrate directly into a client's core proprietary engine.
* **The DMZ Rule:** We operate strictly upstream (pre-processing/data ingestion) or downstream (post-processing/reporting).
* **Zero Technical Debt:** We hand the client's CTO a fully containerized API endpoint. They plug it in. If they don't like it, they unplug it. We leave no messy code in their repository.
* **Deterministic Safety:** We use LLMs for extraction/generation, but we always anchor them with hardcoded, deterministic Python rules engines to eliminate hallucination risk.

## **5. THE ANTI-REPLICATION PRINCIPLE (The Ego Check)**

This is the single most important rule governing what we build.

* **Never build something the client's engineers are already building or would feel threatened by.** If our sidecar looks like it competes with a feature on their roadmap, we kill it — even if the architecture is sound.
* **The Ego Check:** Before committing to any demo architecture, verify that the proposed workflow does not replicate, replace, or interfere with any shipped or announced feature. If the client's CTO could look at our demo and think "my team is already doing this," the proposal is dead on arrival.
* **Kill fast, kill publicly.** When a proposal fails the Ego Check, document why it was killed and reference it in the pitch. This builds credibility — it proves we did the research and respect their engineering team's work.

## **6. THE IDEAL CUSTOMER PROFILE (ICP)**

### **Who We Target:**

* **Stage:** Seed to Series B (post-funding, pre-scale). Sweet spot: 12-30 months post-seed, with strong bias toward seed-aging-toward-Series-A.
* **Type:** B2B, enterprise-facing AI/tech startups.
* **Product:** Must have a shipped product that is actively being sold to enterprise clients.
* **Pain:** Must be experiencing enterprise integration bottlenecks — deals stalling due to InfoSec compliance, legacy system bridges, custom data ingestion, bespoke reporting, or vendor security questionnaires.
* **Signal:** Hiring for "Solutions Engineer," "Forward Deployed Engineer," or "Enterprise Integration" roles. Recent enterprise partnership announcements. SOC 2 certification in progress or recently completed.
* **Vertical:** Must pass the halal-vertical whitelist (see §11). Industrial AI, logistics/supply chain AI, healthcare AI (non-insurance), construction/AEC AI, cybersecurity AI (non-finance-specific), among others.

### **Who We Do NOT Target:**

* B2C companies or prosumer tools.
* Pre-product startups (alpha, waitlist, no shipped product).
* Companies with no enterprise sales motion (pure PLG/self-serve with no named enterprise clients).
* Companies where the integration surface is too shallow to justify an FDE engagement (e.g., simple CRM API connectors).
* Companies that have pivoted more than twice (unstable product direction).
* Companies whose primary customer base is in haram verticals (see §11 Operating Constraints): fintech, banking, lending, payments processing, insurance, asset management, mortgage technology, gambling, alcohol/tobacco/cannabis vertical, adult content, conventional financial services infrastructure.

## **7. THE 5-PILLAR DEMO STANDARD**

Every demo Kaide Labs builds must pass all five pillars. If a proposed demo fails any pillar, it is redesigned or killed. Applied at Step 1B (architecture red-team) of the post-AS pipeline.

1. **Bottleneck Assassin:** Does it solve a specific, expensive, real operational pain that is currently blocking enterprise deals? Evidence must come from the founder's own public statements, job postings, or product gaps — not from our assumptions.
2. **Anti-Replication:** Is it completely outside their core IP? Does it pass the Ego Check? Would their CTO look at this and feel relieved rather than threatened?
3. **Native Environment:** Does the UI live where the users already work? (Slack, Excel, Jira, their existing portal.) It must feel like a native feature, not a bolted-on external tool.
4. **Magic Moment:** Is there a single, tangible, instant-ROI moment visible on the frontend? The prospect or internal user must see the value in under 60 seconds. This is what gets recorded in the demo video.
5. **System Resilience & Immunity:** Does it handle chaos? Does it auto-repair? Does it fail safely? The deterministic validation/fallback layer must be present — LLMs generate, but hardcoded rules verify.

## **8. OUR VOCABULARY (The CTO Shield)**

**Never use:** *Outsource, Agency, Dev Shop, Custom Software, Hourly Rate, Consultant, Freelancer.*
**Always use:** *Stateless Sidecar, Microservice, Pre-Core Pipeline, Post-Core Pipeline, Deterministic Fallback, Revenue Unblocking, Containerized Endpoint, Strike Team, Zero Technical Debt, Unplug Guarantee.*

## **9. THE SALES PSYCHOLOGY (The Pitch Arc)**

1. **Value First (The Magic Moment):** Show the weapon working immediately. Prove we can build enterprise-grade software. The demo video leads with the screen, not a pitch deck.
2. **The Pain Second:** Rub salt in the wound. Remind them of the enterprise deals they are losing because of this bottleneck.
3. **The FDE Framing:** Reassure the CTO that we are an adjacent strike team, not an invasive outsourcing firm. Reference the Anti-Replication Principle explicitly.
4. **Hit or Miss:** We do not chase. We drop the asset, leave the door open, and walk away. High status only. No needy follow-ups.

### **Outreach Mechanics:**

* **One email to all founders.** Address both the business founder (revenue nerve) and the technical founder (architecture nerve) with tagged lines in the same email.
* **Demo delivery via Vidyard video link.** Prospects don't click live demo links. They watch videos. 3-5 minutes max.
* **Timestamp trick:** Include a timestamp in the email pointing to the Magic Moment (e.g., "Skip to 0:40 for the live demo").
* **Adjacent ideas are verbal only.** Tease 1-2 additional architectures at the end of the video to hook a second call. Never put the full architecture in writing — prevents founders from stealing the design and assigning it to their own engineers.
* **CTA:** "If the architecture looks right, let's grab 15 minutes to scope the production build. If not, no follow-up from me."
* **Follow-up (telemetry-based):** If they viewed the video → one soft nudge. If they didn't → one "buried in inbox" re-send. Then done.

## **10. PRICING**

Two-component pricing finalized after the AS sprint and the Matta cycle clarified what we're actually selling.

### **Base + Per-Sprint Structure**

* **Base Rate:** **£10,000/month** per deployed sidecar. Covers standing capacity, monitoring, maintenance, and minor adjustments for one sidecar.
* **Per Net-New Sidecar Sprint:** **£15,000–£25,000**, one-time, charged on delivery, for each new sidecar Kaide Labs architects and ships (each 48-72hr build).

### **Kickoff Terms**

* **50% upfront** to reserve the strike team and begin the build.
* **50% upon deployment.**
* **First month fully refundable** if the client is unsatisfied. This is the trust accelerator for cold prospects who have never heard of Kaide Labs. Refund clause drops after 2-3 closed deals.

### **Volume Step-Ups**

* **3+ sidecars per customer:** base steps to **£13,000–£15,000/month**.
* **5+ sidecars per customer:** base steps to **£18,000–£20,000/month**.

Reflects the operational load of maintaining multiple sidecars in parallel.

### **Positioning**

The base rate is positioned against the cost of the enterprise deal it unblocks. If a £300K contract is stuck in procurement, £10K/month is a 30x ROI.

The two-component structure also matters for **investor optics**: the base + per-sprint split reads as services-revenue + product-revenue on the books — a fundable shape. It also defends against the agency frame: agencies charge for hours; we charge for capacity (base) plus delivered artifacts (per-sprint). Two distinct economic logics, neither of which is "bodies for hire."

## **11. OPERATIONAL CONSTRAINTS**

These constraints shape every architecture decision, model selection, and prospect targeting.

### **Team**

* **Hafeedh Balogun** — Founder, Lead AI Architect, Sales, Outreach.
* **Isaac** — Employee #1, GTM and positioning lead, demo production support. Equity to be formalized at: first signed customer, OR company registration, OR 60 days from May 2026 (whichever comes first). Externally: name only, no title, until formalization.

### **Geography & Timezone**

* **HQ:** Lagos, Nigeria (GMT+1).
* **Prospect targeting:** Weighted 70% UK/EU (GMT to CET) and 30% US East Coast. US-headquartered companies are viable if they have a European arm or the engagement is async-friendly. West Coast US and APAC excluded (timezone gap too large).

### **Halal Vertical Filter (Hard-Kill)**

Non-negotiable. Apply at sourcing stage, not dossier stage. Hard-kill any prospect whose primary customer base or vertical is:

* Conventional fintech, banking, lending, payments processing, BNPL, neobanks, mortgage technology
* Asset management, hedge funds, wealth-tech, conventional financial services infrastructure
* Insurance, insurtech of any kind (including health insurance tech)
* Gambling, sports betting, casino-tech, prediction markets
* Alcohol, tobacco, cannabis vertical
* Adult content, dating-tech

When in doubt, exclude. The 5-minute hard-kill screen at sourcing stage prevents wasted Step 0 intel hours on candidates that will fail filters downstream. (See `frameworks/sourcing_framework_v1.md` and `frameworks/vertical_whitelist.md` for full sourcing methodology.)

### **LLM Stack — Client-Facing**

Client demo runtimes use **Google (Gemini) ecosystem ONLY**. No OpenAI, no Anthropic/Claude, no AWS Bedrock in any client-deliverable artifact.

* Architectures route agents exclusively through Google models (Gemini 3 / 2.5 Flash, Pro).
* Vertex AI europe-west4 region binding for client demo deployments (data residency for EU prospects).
* Zero-retention configuration mandatory on all Vertex AI calls.

### **LLM Stack — Internal Orchestration**

Multi-model stack for internal sprint execution. Decoupled entirely from client-facing constraint.

* **Opus 4.7 (Claude web + Claude Code):** Architectural prompt-writing, phase specs, build execution, debugging, strategic judgment, cross-prospect reasoning, dossier writing, outreach drafting. Default model for all high-leverage cognitive work.
* **Sonnet 4.6 (Claude Code):** Fallback for Opus rate-limit windows. Was the debug workhorse on the 5x plan; now backup-only on the 20x plan.
* **Gemini Deep Research (Gemini Web):** Step 0 fit assessments, Step 1A three-architecture generation, Step 5 falsifiable claims audit. Capability slot — multi-source autonomous research with citation discipline.
* **Codex CLI:** Step 3 lateral feature exploration. Divergent thinking slot — cross-model bias mitigation for architectural exploration.
* **Claude Code + Nia MCP:** Substrate retrieval. Per-prospect repository indexing. Step 0 fit assessment (Claude Code version), Step 0.5 dossier expansion, indexed query against accumulated learnings.

### **Demo Format Standard**

* Voiceover narrated by the Lead Architect (Hafeedh).
* Cold-open with the Magic Moment in the first 10-15 seconds.
* Full technical walkthrough follows. Screen recording throughout; no on-camera presenter.
* Total runtime: 3-6 minutes.
* Vidyard delivery with timestamp hint in email.

### **Demo Sprint Cadence**

Each demo is a proof-of-concept built in approximately **48-72 hours**. It is not production-ready software — it is evidence of architectural competence designed to earn a paid engagement. Sprints follow the post-AS pipeline (Step 0 through Step 6, with verification gates at 0b, 1F, and 5). See `pipeline/pipeline_overview.md`.

Sprints are **phased, not one-shot**. Each phase produces a `PHASE_N_SPEC.md` with explicit invariants and acceptance criteria. Step 3A executes the phase; Step 3B reviews and advances. One-shot building was attempted on Matta and produced 30% of the build needing extended integration debug — the tax is real, the phasing discipline is locked.

## **12. SUBSTRATE STRATEGY (THE MOAT)**

This is what makes Kaide Labs a startup, not a consultancy.

### **The Wedge vs the Moat**

The services motion (£10k/month + per-sprint fees) is the **wedge** — it funds the company, validates the thesis with paying customers, generates referenceable case studies, and produces the artifacts the substrate compounds against.

The **substrate** is the moat — the indexed knowledge layer that compounds across engagements via Claude Code + Nia. Each prospect engagement deposits artifacts (intel, architecture, build, debug learnings, outcome) into the substrate. Future prospects' architecture decisions, fit assessments, and pitch language draw from the accumulated substrate.

### **Why It's Not An Agency**

The economic wedge: we charge £10k/month base per customer (plus per-sprint fees) while our marginal cost per customer (cloud infra + human-in-the-loop QA) is well under £1.5k. We can serve N customers with the unit economics of M humans, where N is much bigger than M. That gap is the business.

The substrate is the mechanism that drives N/M apart over time. Each new prospect benefits from the accumulated context of all prior ones. Each new architecture decision references prior architectural patterns. The infrastructure is Claude Code + Nia for indexing and retrieval; the inputs are the artifacts produced in every engagement.

Three demos in, the substrate is doing measurable lift on every new prospect assessment. By the time we're applying to YC (target: February 2027), the substrate is the technical differentiation that distinguishes us from "FDE-as-a-service" boutiques.

### **The Investor Pitch**

> *"B2B AI startups need procurement-grade enterprise artifacts to close deals. We deliver standing FDE capacity at £10k/month per customer plus per-sprint build fees. Our substrate — the indexed knowledge layer and the agent orchestration stack — lets us serve N customers with the unit economics of M humans, where N is much bigger than M."*

Customer first, substrate as the mechanism. Not: substrate first, services as funding. The order matters.

### **Operational Discipline**

The substrate compounds only if artifacts are filed as they land. Sprints that don't file artifacts to the substrate produce no compounding value. Every Matta artifact, every Solvo decision, every Juna intel finding either lands in `prospects/[name]/` or it doesn't compound. There is no "I'll file it later" — later means it decays.

## **13. CROSS-REFERENCE**

This is the canonical identity. For operational depth on adjacent topics:

* **Pipeline mechanics:** `pipeline/pipeline_overview.md` (Step 0 through Step 6 with verification gates)
* **Sourcing methodology:** `frameworks/sourcing_framework_v1.md`
* **Halal vertical whitelist:** `frameworks/vertical_whitelist.md`
* **Pricing model detail:** `identity/pricing_model.md` (this file references; pricing_model.md has detailed deal-structure language)
* **Per-prospect engagement records:** `prospects/[name]/`
