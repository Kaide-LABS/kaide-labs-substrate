---
type: identity
domain: pricing_operational_depth
last_updated: 2026-05-21
canonical: true
supersedes: null
key_tags:
  - pricing
  - deal_mechanics
  - refund_clause
  - step_ups
  - per_sprint_fee
  - pricing_objections
  - investor_pitch_economics
  - walk_away_signals
  - sprint_scoping
cross_references:
  - identity/identity_doc.md §10 (canonical pricing summary)
  - pipeline/templates/STEP_14_CALL_BRIEF_TEMPLATE.md (call brief structure for response calls)
---

# **KAIDE LABS PRICING MODEL — OPERATIONAL DEPTH**

> Read `identity/identity_doc.md §10` for the high-level pricing summary. This file is the operational reference for actually conducting pricing conversations with prospects, handling objections, scoping sprints, and making flex-vs-hold decisions.

## **1. THE TWO-COMPONENT STRUCTURE**

Kaide Labs charges in two components. Both must be understood and named explicitly in every pricing conversation.

### **Component A — Base Subscription (£10,000/month per deployed sidecar)**

Covers standing capacity for one deployed sidecar:

* **Standing capacity** — Kaide Labs reserves engineering bandwidth so that when the customer's sidecar needs attention (a bug surface, a model deprecation, a schema change in their upstream API), we respond within 24-48 hours rather than queueing behind other work.
* **Monitoring** — health checks on the sidecar's Cloud Run deployment, Vertex AI quota consumption, error rate dashboards, log retention.
* **Maintenance** — security patches, dependency updates, model string updates when Google deprecates a Gemini version, region rebinding if the customer's data residency requirements change.
* **Minor adjustments** — small schema additions to existing endpoints, prompt refinements, retry/backoff tuning, log format adjustments. Bounded at roughly 8 engineering hours per month before triggering a sprint conversation.

### **Component B — Per Net-New Sidecar Sprint (£15,000–£25,000 one-time)**

Charged on delivery of each net-new sidecar Kaide Labs architects and ships. Pricing within the band is determined by:

* **£15,000 floor** — Net-new sidecar with familiar architectural pattern (matches one of the four canonical patterns: calibration sidecar, safe-harbor synthetic data fabric, voice-to-structured-action PWA, or multi-agent parallel triage). 48-72 hour sprint cadence. Single magic moment, single primary surface.
* **£20,000 mid** — Net-new sidecar with novel architectural elements (the architecture extends an existing pattern in non-trivial ways, OR introduces a new compliance posture like ISO 27001 attestation, OR requires deep integration with a customer-specific legacy system).
* **£25,000 ceiling** — Net-new sidecar requiring substantial pre-engagement R&D (a new pattern entirely, OR a sprint where the customer wants to compress 96-hour work into 60 hours, OR a sprint that requires dedicated co-located time with the customer's engineering team).

A "net-new sidecar" is defined operationally in §6.

### **The economic logic of the split**

The two-component structure does three jobs:

1. **Defends against the agency frame.** Agencies charge for hours; we charge for capacity (Component A) plus delivered artifacts (Component B). Two distinct economic logics, neither of which is "bodies for hire."
2. **Creates fundable investor optics.** On the books, Component A reads as services revenue (recurring, high-margin, customer-locked-in). Component B reads as product revenue (one-time, per-unit, scaling). The mix matters for fundraising — too much Component A reads as a services business; too much Component B reads as a project shop. The current target mix is roughly 60/40 base/sprint, shifting toward 70/30 as multi-sidecar customers compound.
3. **Aligns incentives with the customer.** Customer pays for what they actually use. One sidecar deployed = £10k/month. Three sidecars deployed = step-up rate (see §3), reflecting genuine operational load increase, not arbitrary upsell.

## **2. KICKOFF TERMS**

Standard structure for first engagement with a new customer:

### **50% upfront on signed engagement**

* **Triggers the build.** Kaide Labs does not begin a sprint without 50% in escrow or paid. This is non-negotiable. Sprints that start on a handshake produce sunk-cost dynamics if the customer disengages mid-build.
* **Reserves the strike team.** Other prospects are told "we have capacity in [N] weeks" rather than "we'll squeeze you in." This is the operational discipline that makes the 48-72 hour sprint cadence possible.
* **Payment via wire or invoice + Stripe.** Wire preferred for >£10k transactions (lower fees, faster settlement). Stripe acceptable for sub-£10k or for customers who require credit card payment for procurement reasons.

### **50% on deployment**

* **Triggers final handoff.** The containerized sidecar deploys to the customer's chosen environment (their Cloud Run, their Kubernetes cluster, or Kaide Labs' Cloud Run with the customer as the only consumer). The Slack interactive notification or Vidyard walkthrough that documents the magic moment is shipped. Final 50% invoices.
* **No deployment, no payment.** If a sprint runs into unresolvable blockers (typically: customer's stated integration surface turns out to be different from what they described, OR the deployment environment has constraints we couldn't have discovered pre-sprint), the second 50% is renegotiated rather than collected.

### **First month fully refundable**

* **Trust accelerator for cold prospects.** Reduces the perceived risk of engaging an unknown FDE team. Customers who've never heard of Kaide Labs need a structural reason to take the £10k bet.
* **What "unsatisfied" means.** The customer is the sole arbiter. If they tell us the sidecar isn't producing the value they expected, we refund the full first month within 14 days. No litigation, no negotiation on the refund mechanics. The reputational cost of fighting a refund exceeds the cash recovery.
* **When the refund clause drops.** After 2-3 closed engagements with positive testimonials (Renlo + Mundostra + one more would clear this threshold), the refund clause is removed from new contracts. Customers who came in under the refund clause keep it for their first month; future customers get the standard contract without it.

## **3. VOLUME STEP-UPS**

When a single customer adopts multiple sidecars, the base subscription steps up to reflect operational load.

### **3+ sidecars per customer → £13,000–£15,000/month base**

* Triggers when the customer has 3 or more sidecars in deployment simultaneously (not cumulative — if 2 prior sidecars were sunset, they don't count).
* The £2-5k uplift reflects increased monitoring surface, increased context-switching cost during maintenance, and increased coordination overhead when changes ripple across sidecars.
* Per-sprint fees (Component B) remain at standard £15-25k band — step-up is on Component A only.

### **5+ sidecars per customer → £18,000–£20,000/month base**

* Triggers at 5 or more sidecars deployed simultaneously.
* The further uplift reflects the customer becoming a "key account" requiring dedicated attention. At this scale, Kaide Labs treats the customer as a co-located embedded team rather than a transactional vendor.
* Step-ups stop here. Customers beyond 5 sidecars typically negotiate custom enterprise terms — flat annual fee, dedicated engineering capacity, custom SLA. These are bespoke conversations at the founder level.

### **Why step-ups, not unit pricing**

We don't charge "£10k per sidecar per month" because that incentivizes the customer to consolidate sidecars (bad for our revenue) or unsubscribe weaker sidecars (bad for both of us). Step-ups create a smoother cost curve — the marginal cost of the 4th sidecar is lower than the 1st, which is honest about the operational reality and creates a structural reason to expand the relationship.

## **4. DEAL MECHANICS**

### **What's included in the £10k base**

* Up to 8 engineering hours/month for minor adjustments and bug surfaces
* 24-48 hour response time on Slack or email for production issues
* Monthly health check report (deployment status, Vertex AI usage, error rates)
* Model deprecation handling (Google sunsets a Gemini version; we migrate at no additional cost)
* Security patches and dependency updates

### **What triggers a per-sprint fee**

* Any new sidecar (different bottleneck, different agent topology, different magic moment)
* Major architectural changes to an existing sidecar (e.g., adding a new agent to the routing, changing the structured output schema in ways that require downstream consumer updates)
* New integration surface (sidecar previously talked to SAP PM; customer now wants it to also talk to IBM Maximo — that's a new sprint, not a minor adjustment)
* Customer-driven scope expansion that exceeds the 8-hour monthly bandwidth allocation

### **Contract length**

* **Default: 6-month initial term, auto-renewing month-to-month thereafter.** The 6-month term protects against customers who churn after the first sprint ships before the standing-capacity model has earned its value.
* **Exit clause: 30 days written notice after the initial term.** Customer can exit cleanly with notice; we don't litigate the exit. The reputational cost of a contentious exit exceeds the recovered revenue.
* **No long-term lock-ins.** 12-month or annual contracts are available for customers who want predictability, but Kaide Labs doesn't push them — the standing-capacity model should earn renewal monthly.

### **Payment terms**

* **Net 30** standard for invoiced customers
* **Net 15** for new customers without billing history
* **Late payment** triggers a pause on standing-capacity work (Component A) after 45 days. Production support continues; new feature work pauses. Communicated explicitly in the contract.
* **No discounting for prepayment.** Customers who want to prepay 12 months get gratitude and a clean invoice; they don't get a 10% discount. Discounting prepayment signals we don't believe in our own monthly value.

## **5. PRICING DECISION TREE**

When a pricing conversation gets messy, work through these questions in order:

### **Q1 — Is the prospect in the ICP (per identity_doc.md §6)?**

If no → polite decline. Don't price your way into a fit problem.

If yes → continue.

### **Q2 — Has the prospect signaled a budget range?**

* **Budget signal at £8k or below per month** → the prospect's procurement reality is incompatible with our model. Two options: walk away cleanly, OR offer a single-sprint engagement (£15-25k one-time, no Component A) to demonstrate value before they commit to monthly. Most should walk away — the budget signal usually predicts future friction even if we engage.
* **Budget signal at £10-15k per month** → the standard model fits. Quote £10k base + per-sprint as standard.
* **Budget signal at £20k+ per month** → either the prospect expects more than one sidecar in the first 6 months (which the step-ups already model), OR they're testing whether we'll inflate. Quote the standard model. If they push for more, name the step-ups explicitly as the upgrade path.

### **Q3 — Is the prospect pushing for non-cash arrangements?**

* **Equity instead of cash** → polite decline. Kaide Labs takes cash exclusively at the sidecar engagement level. Equity arrangements are reserved for strategic partnerships and are negotiated at the founder level, separate from sidecar engagements.
* **Revenue share instead of cash** → polite decline. Revenue share creates incentive alignment problems and bookkeeping complexity that's not worth the upside at our current scale.
* **Discounted cash plus equity** → still a polite decline. Either it's a sidecar engagement (cash terms) or it's a strategic partnership (different conversation entirely).
* **Deferred payment** → no. Even "we'll pay 30 days after deployment" violates the upfront-50% discipline. The standing-capacity model only works when capacity is paid for, not when capacity is extended on credit.

### **Q4 — Is the prospect pushing for hourly billing?**

* **"We want to pay by the hour"** → name explicitly that Kaide Labs does not bill hourly. The standing-capacity model is structurally different from hourly billing. Offer to send the canonical pricing structure in writing. If they keep pushing for hourly, that's a walk-away signal — they're shopping for an agency, not an FDE strike team.

### **Q5 — Is the prospect pushing for a smaller pilot?**

* **"Can we do a smaller proof-of-concept first?"** → the demo video already serves this function. The video is the proof-of-concept; the £15-25k sprint is the production build. Reframe: "The demo you watched is the PoC. The sprint is the deployment-ready build. We don't run smaller pilots between those two states."
* **"Can we do a £5k 2-week mini-engagement?"** → no. The 48-72 hour sprint cadence is what makes our economics work. Half-sprints don't exist. If the prospect can't commit to the full sprint, the engagement isn't ready.

## **6. SPRINT SCOPING — WHAT COUNTS AS A "NET-NEW SIDECAR"**

The Component B per-sprint fee is triggered by net-new sidecars. The operational definition matters because customers will reasonably ask "is this a new sprint or a maintenance adjustment?"

### **Net-new sidecar (£15-25k sprint fee)**

* Different bottleneck (e.g., the existing sidecar handles invoice ingestion; the new sidecar handles contract renewal automation)
* Different agent topology (different orchestration shape, different model routing, different deterministic safety rails)
* Different magic moment (different visible end-state in the customer's surface)
* New integration surface (the existing sidecar talks to system A; the new sidecar talks to system B)

### **Maintenance adjustment (included in £10k base, capped at 8 hours/month)**

* Same sidecar, same bottleneck, minor schema additions
* Same sidecar, prompt refinements that don't change the architecture
* Same sidecar, model string updates due to Google deprecations
* Same sidecar, retry/backoff tuning
* Same sidecar, log format adjustments
* Same sidecar, observability dashboard tweaks

### **Edge cases requiring judgment**

* **Same bottleneck, but the agent topology changes substantially.** This is a sprint, not a maintenance adjustment. Even if the magic moment surface looks the same to the user, restructuring the internal architecture is sprint-grade work.
* **Different bottleneck, but reuses 80% of an existing sidecar's code.** Still a sprint at the £15k floor. Code reuse is internal efficiency; the customer is paying for the new bottleneck addressed.
* **Adding a new tenant to an existing sidecar (multi-tenancy work).** Sprint at the £15k floor. Multi-tenancy introduces new failure modes and isolation requirements that exceed maintenance bandwidth.
* **Cosmetic UI changes to the demo theatre surface.** Maintenance. Even a 4-hour UI refresh stays in the £10k base unless it triggers schema or integration changes.

When in doubt, err toward calling it a sprint. The customer pays for value delivered; we pay for ambiguity in the form of erosion of the standing-capacity model.

## **7. PRICING OBJECTIONS — CANONICAL HANDLES**

These are the objections that recur most often. Each has a canonical response that should be ready before a pricing conversation begins.

### **"£10k/month is too expensive for our stage"**

> "The base subscription is positioned against the cost of the enterprise deal it unblocks. If a £300k contract is stuck in procurement because of a custom data ingestion requirement, the sidecar that closes that deal returns 30x in the first month. For prospects where £10k feels expensive, the underlying issue is usually that the bottleneck we'd solve isn't tied to enterprise revenue — in which case Kaide Labs probably isn't the right fit."

### **"Our enterprise clients want to see hourly billing for compliance"**

> "Our customers don't pay enterprise clients on our behalf — we charge Kaide Labs subscriptions to your company directly. Your enterprise clients pay you, and your contracts with them can be structured however your procurement team prefers. Our pricing structure is separate from yours."

### **"We want to start with a smaller engagement and grow into the full model"**

> "The demo video you watched is already the smaller engagement. It's the proof-of-concept that established Kaide Labs can build what you need. The £15-25k sprint is the production build of that proof. We don't run an intermediate step between those two — it's video, then sprint, then standing capacity."

### **"What if we want to bring this in-house after one sprint?"**

> "Then you do. The sidecar is containerized and stateless. After the sprint ships, you have the source code, the deployment configuration, and the documentation. The standing capacity subscription is what we charge if you want us to keep maintaining it. If you'd rather your team take over, we offboard cleanly. We've never litigated a customer's decision to in-house something."

### **"Can we do equity instead of cash for the first 6 months?"**

> "Kaide Labs takes cash at the sidecar engagement level. Equity arrangements happen at the strategic partnership level — different conversation, different counterparty (founder-to-founder rather than procurement-to-vendor). If you'd like to explore a strategic partnership separately, that's a real conversation, but it doesn't replace the sidecar engagement fee."

### **"Your competitors charge less"**

> "Our competitors are agencies that bill hourly. The economic shape is different — they sell hours, we sell capacity. The comparison isn't price-per-hour; it's outcome-per-month. If hourly billing fits your procurement model better, the right call is probably to engage one of those agencies. If outcome-per-month is the model that matches your bottleneck, we're the right fit."

### **"Can you do a discount for paying 12 months upfront?"**

> "No discount, but I appreciate the offer. Discounting prepayment would signal we don't believe in our own monthly value. We're confident in the monthly cadence — it should earn renewal organically."

## **8. INVESTOR PITCH ECONOMICS**

When discussing Kaide Labs' pricing model with investors (vs. customers), the framing shifts.

### **The bookkeeping story**

* Component A (base) = recurring services revenue, high margin, customer-locked
* Component B (per-sprint) = product revenue, one-time per unit, scaling with adoption
* Together, the mix produces a "services-revenue-funding-product-substrate" shape that investors associate with fundable companies (Vercel, Retool, even early-stage Stripe before they fully separated services from product)

### **The unit economics**

* Customer LTV at single-sidecar level: £10k × 24 months avg + £20k sprint = £260k
* Customer LTV at 3-sidecar level: £14k × 24 months avg + £60k sprints = £396k
* Customer LTV at 5-sidecar level: £19k × 36 months avg + £100k sprints = £784k

These numbers are speculative until validated by actual customer retention data. By Q3 2026 (3+ engagements closed), we'll have real retention curves.

### **The "why this is a startup, not a consultancy" argument**

The substrate is the moat. As of V1 (May 2026), the substrate compounds across engagements via Claude Code + Nia retrieval. The marginal cost of serving customer N+1 decreases as the substrate accumulates patterns, killed-engagement learnings, and architectural references. By customer 10, the substrate is doing measurable lift on architecture generation, fit assessment, and pricing decisions.

This is what separates Kaide Labs from "Acme FDE Consulting LLC." The consulting LLC's marginal cost per customer is flat. Kaide Labs' marginal cost per customer decreases. The decreasing curve is the business.

## **9. WALK-AWAY SIGNALS**

Some prospects shouldn't be priced — they should be declined. Pricing signals worth walking away on:

* **Budget anchor below £8k/month** — incompatible with our model; will produce friction during the engagement and post-sprint upsell conversations.
* **Insistence on hourly billing** — shopping for an agency, not an FDE team.
* **Refusal of upfront 50%** — signals either cash flow issues or a procurement process incompatible with our sprint cadence.
* **Equity-only or revenue-share-only proposals** — wrong economic shape at the engagement level.
* **Pressure to discount before any value has been demonstrated** — predicts persistent discount pressure throughout the engagement.
* **Procurement process longer than 60 days** — incompatible with the 48-72 hour sprint cadence; we'd lose more in standing-capacity opportunity cost than we'd gain in revenue.

When walking away, do it cleanly: "Based on what you've shared, I don't think Kaide Labs is the right fit for what you're trying to accomplish. I'd recommend [specific alternative — agency name, in-house engineering hire, no-code platform] for your situation." Then disengage. Don't litigate the walk-away.

## **10. WHEN TO CHANGE THIS MODEL**

The current pricing model is V1 (May 2026). Conditions that trigger a revision:

* **3+ closed engagements with consistent positive feedback on pricing** → drop the first-month refund clause for new contracts (target Q3 2026)
* **First customer reaches 3 sidecars deployed** → validate the step-up actually maps to operational reality; adjust if needed
* **First customer reaches 5 sidecars deployed** → custom enterprise terms become a real conversation rather than a hypothetical
* **First objection pattern we don't have a canonical handle for** → add to §7 and update this file
* **First time Kaide Labs has more demand than capacity** → consider raising the floor of the per-sprint band from £15k to £18k

Until those conditions trigger, the model is locked. Don't experiment with pricing on individual engagements — pricing inconsistency destroys substrate value (Claude Code can't make pricing recommendations if every prior engagement priced differently).

## **11. CROSS-REFERENCE**

* `identity/identity_doc.md §10` — canonical pricing summary
* `pipeline/templates/STEP_14_CALL_BRIEF_TEMPLATE.md` — call brief structure, including pricing objections section
* `frameworks/sourcing_framework_v1.md` — ICP filters (apply before pricing conversation)
* `prospects/[name]/outreach.md` — per-prospect pricing context (when filed)
