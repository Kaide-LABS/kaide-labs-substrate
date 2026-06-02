---
type: kill_log
prospect: matta
last_updated: 2026-06-02
key_tags:
  - validation_gate_evidence
  - 1f_red_in_action
  - killed_architectures
  - internal_only
  - cmms_bridge_killed
  - the_brief_killed
  - refinery_selected
---

# Matta — Kill Log

> **INTERNAL SUBSTRATE ONLY. NEVER CLIENT-FACING.** This file documents architectures we
> killed before shipping. It must never be referenced in `outreach.md`, in any cold email,
> in the Vidyard script, or in any artifact Matta sees. Disclosing that we killed two
> prior architectures on this prospect would be self-defeating. Its audience is the Kaide
> Labs strategy thread only.

## Purpose

This is the evidence that the **1F-red validation gate works**. The Refinery is the third
architecture cycle on Matta; the first two were caught and killed at the 1F-red red-team
gate *before* any sprint shipped. The gate exists because of the Artificial Societies
engagement (`prospects/artificial_societies/`) — the deal loss there triggered the
pipeline re-engineering that produced Step 1F (implicit-assumption validation) and Step
1F-red (red-team with go/no-go authority). Matta is the first prospect where 1F-red caught
**two** bad builds in a single sprint. That is the apparatus doing exactly what AS paid for
it to do.

Reconstructed faithfully from `MATTA_MASTER_PRD_v2.md §0` and
`docs/validation_gate_1f_red_v3.md` (the source repo is ground truth).

---

## Cycle 1 — CMMS Bridge (v1) — KILLED at 1F-red v1

**The architecture.** An outbound-webhook → CMMS work-order-routing sidecar. The premise:
when a Matta camera flags a defect, route a structured work order into the customer's
maintenance system (the killed schema family was `MattaDefectEvent` → `RootCauseHypothesis`
→ CMMS work order; placeholder library name "Bridge++"). Positioned as relieving an
IT-compliance / maintenance-dispatch friction.

**The 1F-red v1 verdict — KILL.** Two cascading premise failures, surfaced by Gemini's 1F
audit plus a second-pass adjudication:
1. **No evidence the burden exists.** Matta's FDE job description does not name CMMS
   integration as a current burden anywhere. The architecture solved a pain the prospect
   never expressed.
2. **It contradicted Matta's own marketing.** Matta's verbatim differentiator is that
   deployments go live in *hours / 24 hours vs the industry's ~6 months*. Repositioning
   the sidecar around "future IT-compliance friction" would have made Kaide argue *against*
   Matta's own headline speed claim — self-defeating in front of the CEO.

**Disposition.** Killed and stays killed. Preserved as a **verbal-only Phase 3 adjacent
idea** — mentionable on a later discovery call as a downstream companion for the
post-deployment workflow, never built or pitched. All `cmms_*` identifiers were
subsequently purged from the shipped Refinery codebase (the DLQ table is `outbox_dlq`, not
`cmms_outbox_dlq`; repo audit confirms zero `cmms_*` hits).

---

## Cycle 2 — The Brief (v1) — KILLED at 1F-red v2

**The architecture.** A **stateless, mobile, pre-call scoping** sidecar for the
trade-show booth — a phone-driven 60-second lookup an FDE runs while standing in front of
a prospect at a show.

**The 1F-red v2 verdict — KILL.** A four-axis "form mismatch" against the verified
operational reality, surfaced by Gemini's 1F-lite audit:
1. **Stateful, not stateless** — Matta has a multi-year waitlist; the pipeline's entities
   persist for months. A stateless-per-interaction sidecar cannot model that.
2. **Asynchronous, not real-time** — 124 leads in two days is a post-show *triage* volume,
   an async batch workflow, not a live one-at-a-time booth lookup.
3. **Desktop, not phone** — the work is CSV-driven desktop processing, not phone-driven
   floor scanning.
4. **Deep research, not 60-second lookup** — Sebastian-grade uncertainty-quantification
   rigor for a regulated-industry procurement buyer demands a deep, calibrated artifact,
   not a fast surface lookup.

**Forms A and B also rejected.** Form A (Pipeline Triage standalone) failed the
strategic-decision **ego check** — a sidecar that ranks Matta's deployment queue can be
read as overreaching into judgment Doug and Damjan reserve for themselves. Form B
(Pre-Visit Dossier standalone) failed on **Clay-class commodification** risk. **Form C —
the combined Refinery** (Stage 1 prioritization + Stage 2 dossier over one stateful spine)
— was selected, with statefulness as a deliberate, primary-source-justified deviation from
the default stateless-sidecar commitment.

---

## Cycle 3 — The Refinery — SURVIVED 1F-red v3 (for completeness)

Not a kill, recorded here because it shows the gate cutting both ways. The selected
Refinery architecture was put through a hostile Gemini 1F forensic audit that returned a
**KILL-AND-RESTART** verdict. On adjudication (`validation_gate_1f_red_v3.md`), both
load-bearing drivers of that verdict were found to be **externally verifiable errors**
(Gemini wrongly claimed gemini-3 preview models were Global-endpoint-only / EU-residency-
breaking, citing Reddit/aggregator sources over the authoritative Vertex locations table;
and it misread the stateful-Postgres stack-mirroring as "bloat" when it is the deliberate
Damjan-absorbability strategy). Verdict overturned to **REPOSITION-REQUIRED**; four genuine
compositional findings were accepted as the **five tightenings** (transactional outbox,
Redis Slack lock, byte-density gate, section-granular DS-CP, deployment-topology diagram)
that shipped in the build. The gate's value here was catching a *false kill*, not just bad
builds — and the SOP meta-finding (independently verify any ❌ on an external technical
claim before treating it as load-bearing) was carried forward.

---

## Why this file exists

Two architectures caught before ship in a single sprint; one false-kill overturned on
documented evidence. The 1F-red gate is the post-Artificial-Societies apparatus working as
designed. Cross-ref `prospects/artificial_societies/learnings.md` (origin of Steps 0b /
1F / 1F-red) and `prospects/artificial_societies/CLAUDE.md`.
