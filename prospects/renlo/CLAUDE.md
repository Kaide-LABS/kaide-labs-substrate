---
type: navigation
prospect: renlo
last_updated: 2026-05-19
key_tags:
  - proptech
  - cre
  - voice_to_deal
  - mobile_first
  - completed_demo
---

# Renlo — Substrate Navigation

Renlo is a closed-loop prospect engagement. The Voice-to-Deal: Field Commander PWA was the demo built and pitched.

## Files in this folder

- `overview.md` — company snapshot (who they are, what they do, the bottleneck we addressed)
- `architecture.md` — the Voice-to-Deal PWA architecture in full (multi-agent stack, four-phase feature build, Magic Moment definition)
- `outcome.md` — *(awaiting population — engagement outcome and learnings)*

## Pattern signals (when this prospect's shape is relevant)

Future prospects matching any of these signals should retrieve Renlo content during architecture generation:

- **Mobile-first user** — primary user spends material time away from desk/keyboard
- **CRM-shape pain** — friction between unstructured field data (photos, voice, PDFs) and structured system-of-record
- **Voice-to-structured-data wedge** — bottleneck is unstructured-input ingestion, not the engine that consumes the structured output
- **PropTech / verticalized SaaS** — vertical AI startup with strong domain positioning but a known mobile/input gap

## Architecture pattern name

**"Voice-to-Structured-Action PWA"** — multi-agent ingestion pipeline that converts unstructured field input (voice + image) into structured records, embedded as a PWA that lives where users already work (mobile field environments). Defers all engine-side decisioning to the customer's core product. Theatre layer: Sentient UI that adapts to incoming data shape rather than displaying rigid forms.
