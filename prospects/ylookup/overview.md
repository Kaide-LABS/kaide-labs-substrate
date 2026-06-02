---
type: prospect_overview
prospect: ylookup
vertical: audit_tech_fintech
stage_at_engagement: pre_seed_early
engagement_date: 2026-03-16
outcome: lost_engagement_model_mismatch
sourcing_status: would_not_pass_current_filters
filter_failure_mode: riba_haram_vertical_entanglement
last_updated: 2026-06-02
canonical: true
key_tags:
  - audit_tech
  - private_equity
  - tax_audit_deals_advisory
  - excel_workflow_augmentation
  - unstructured_ingestion
  - non_technical_founders
  - haram_vertical_caveat
  - riba_entangled
  - pre_filter_engagement
---

# Ylookup — Overview

## Company snapshot

Ylookup (founded late 2025) is an agentic workflow engine built for tax, audit, and deals advisory teams. Its core thesis is **augmentation over replacement**: it accelerates the Excel-based reconciliation workflows accountants already perform rather than forcing a new operational paradigm. Positioning is explicitly practitioner-built — "by practitioners, for practitioners" — where the moat is domain expertise, not underlying engineering.

The platform is structured around four functional pillars that can run standalone or chain into multi-step workflows:

- **Import** — ingests data from forms, agreements, statements, and financial reports
- **Analyze** — populates standardized templates, generates analytical insight, reorganizes datasets
- **Reconcile** — the proprietary core engine; compares datasets, pinpoints discrepancies/missing records, generates reconciliation summaries
- **Review** — human-in-the-loop surface for detecting errors, spotting calculation inconsistencies, and asking context-aware questions against working papers

Differentiation from VDR/M&A-software incumbents (Ansarada, Datasite, DealRoom) is deliberate: those host documents and track engagement; Ylookup operates on the mathematical reality inside the spreadsheet. In-app audit trails and user validation flows are first-class, reflecting a regulated-finance posture where defensible human judgment is mandatory.

## Founder team

> Profiles below are factual summaries for engagement context. The pitch choreography used to engage these founders is intentionally **not** recorded in the substrate.

**Daniel Fraai — CEO.** Ex-Motive Partners, ex-EQT Ventures; Copenhagen Business School dropout. Profile: high risk-tolerance, unconventional path, systems-thinker who values demonstrated execution over credentials. Communication style is direct and pragmatic. Public-statement anchor used in the pitch: his critique of horizontal AI developers — that the firms "chasing horizontal AI" have no feel for the niche, regulated, unglamorous finance workflows where the trapped value actually sits (the "nobody at Anthropic can point to Luxembourg on a map" critique). Engagement role: primary decision-maker; the call was booked with Fraai, and he is the one who preferred to hire rather than outsource.

**Matthew Hill — institutional anchor.** Ex-PwC Senior Manager, Chartered Accountant (ex-BDO). Profile: the risk-aversion / defensibility / standardization counterweight to Fraai. Communication style emphasizes rigor and audit-trail discipline. Two public-statement anchors used in the pitch: his admission that **"neither of us can write code,"** and his **"we went and sat with the people we're building for"** empirical, user-observation approach to product. Engagement role: the rigor gate — the persona the deterministic-safety framing of UDINA was built to satisfy.

**The "code gap."** Both founders are non-technical and rely entirely on external engineering. This was the engagement hook — and, in hindsight, the seed of the outcome: a founder who places high trust in demonstrated solo execution and has no in-house engineering will rationally prefer to *acquire* the engineer rather than *rent* an outsourced engagement. (See `outcome.md`.)

## The bottleneck we identified

The **Import-phase dependency on unstructured data.** Ylookup's engine needs structured input to function, but in PE and audit reality the data arrives as chaos: scanned 8-Ks, 10-Ks, confidential information memorandums (CIMs), heavily annotated debt schedules, diverse ERP exports. Today, highly-paid analysts act as expensive document scanners — manually extracting tables and figures to feed the engine. This pre-processing friction sits strictly upstream of Ylookup's core IP: it is the front door to the engine, not the engine itself. (CONTEXT.md, "The Strategic Vulnerability.")

## Why this passed Anti-Replication (at design time)

UDINA is a stateless pre-processor that converts unstructured financial PDFs into Ylookup-ready structured JSON/CSV. It deliberately does **not** touch:

- **The Reconcile engine** — Ylookup's proprietary core; UDINA never reconciles, compares datasets, or generates reconciliation summaries
- **The four-pillar core** — UDINA feeds the Import phase; it does not replace Import/Analyze/Reconcile/Review as Ylookup's product
- **The in-app audit trails / user validation flows** — Ylookup's own Review surface for reconciliation is untouched

If Ylookup unplugged UDINA tomorrow, their product still works — with the original manual document-scanning friction restored. UDINA pre-processes and feeds; it never replaces.

**Boundary tension worth flagging (substrate-grade honesty).** UDINA contains its own GAAP normalization step (Analyze-adjacent) and a confidence-scored HITL review surface (Review-adjacent). The objects differ — UDINA's "review" scores *extraction confidence* (did we read the PDF correctly), whereas Ylookup's Review scrutinizes *reconciliation*; UDINA's normalization maps headers to a GAAP taxonomy for ingestion, not analytical insight. So the boundary holds. But the *cosmetic* resemblance (a "Review" panel, an "Analyze"-like mapping) is a pitch risk: a domain-expert CTO could misread adjacency as encroachment. The cleanest, least-contestable anti-replication core of this build is the **extraction-and-structuring** step; the normalization and HITL layers should be framed explicitly as ingestion-prep, not analysis. [VERIFY: no record that this tension was raised on the call.]

## Why this would not pass current sourcing filters

Ylookup's customer base is private equity firms and Big 4 audit teams, and its workflows center on NAV reconciliation for funds, leveraged-buyout due diligence, and Quality-of-Earnings analysis on debt-financed deals. This is structurally **riba-entangled** — arguably more directly than Tracelight.

Under current Kaide Labs sourcing rules, a prospect whose customer workflows are structurally riba-entangled is **killed at the riba filter at sourcing**, before any intelligence, demo, or outreach work. This engagement (2026-03-16) predates the formalization of that filter — the filter was hardened by the Tracelight post-mortem — so it is a **pre-filter engagement, not a filter violation**, the same posture as Tracelight. The architecture is preserved for the lesson; the prospect would not be sourced today.

## Outcome

Closed, lost — on engagement-model mismatch, not on demo quality. See `outcome.md`.
