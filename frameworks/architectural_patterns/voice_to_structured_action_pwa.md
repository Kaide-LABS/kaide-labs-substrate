---
type: architectural_pattern
pattern_name: voice_to_structured_action_pwa
canonical_one_liner: "Mobile-first multimodal capture that turns unstructured field input into structured records and ready-to-send actions for a downstream system of record."
status: v1
version_first_shipped: 2026-01
prior_worked_examples:
  - renlo
last_updated: 2026-05-21
key_tags:
  - voice_to_structured
  - multimodal_fusion
  - mobile_first_pwa
  - sentient_ui
  - pydantic_structured_outputs
  - downstream_action_generation
  - market_intelligence_grounding
  - upstream_sidecar
  - localstorage_memory
signal_checklist:
  - mobile_first_user_in_the_field
  - unstructured_capture_must_become_structured_record
  - target_engine_decisions_on_structured_records_once_ingested
  - friction_in_manual_entry_is_the_binding_retention_constraint
  - target_does_not_own_the_capture_surface
related_patterns:
  - safe_harbor_synthetic_data_fabric
  - calibration_sidecar
position_relative_to_target_core: upstream
llm_stack_default: vertex_ai_gemini
llm_stack_fallback: openai_whisper_plus_gpt4o
---

# Voice-to-Structured-Action PWA

## §1. Pattern name + one-liner

**Voice-to-Structured-Action PWA.** A mobile-first installable web app that captures messy field input (voice, photo, or both), fuses it into an enriched transcript, extracts a structured record via constrained LLM output, and pre-generates ready-to-send downstream actions — all without touching the target's system of intelligence.

The pattern's one-line pitch shape: *"Your users live in the field. Your CRM/database lives at a keyboard. We close that gap with a single tap-and-talk surface that produces a structured record and the next three things they'd otherwise type by hand."*

The load-bearing claim is that mobile field workers will not use a tool that's slower than the alternative (often Excel or scrap paper). The sidecar's job is to be measurably faster than the manual baseline while producing output that is structurally compatible with the target's data model.

## §2. When to reach for this pattern

Reach for Voice-to-Structured-Action when all of the following are true:

- The target's users are **mobile-first and field-deployed** — brokers, inspectors, drivers, field reps, technicians, scouts, claims adjusters. They spend significant time away from a keyboard.
- The user's primary capture artifact is **unstructured** — spoken observations, photos of physical objects (signs, equipment, damage, documents), voice memos, dictated notes.
- The target's core engine **performs decisions on structured records** once captured — CRM intelligence, routing, pricing, dispatch, underwriting, recommendation.
- **Manual data entry is the binding retention constraint** — users churn back to Excel, paper, or no-tool baselines because typing into the target's interface is slower than their alternative.
- The target **does not own the capture surface** — they ship a web/desktop product that assumes data is already structured, and the upstream "how did this data get into the system" is unaddressed.

Reach for it especially when the founder's public posts complain about user-friction-on-data-entry, or when their competitor analysis names "too many clicks" as the legacy product's failure mode. Renlo's founder publicly described the legacy CRM (Apto) as "clunky" and "antique" with "far too many clicks." The pattern automates the founder's own diagnosis.

## §3. The problem shape

The target ships a sophisticated decisioning engine that delivers value only when the user has populated structured records inside it. But the user's most valuable observations happen in contexts where structured entry is impossible — driving between sites, walking a property, standing in front of equipment. The user's choices in that moment are: (a) try to type into a mobile interface that wasn't designed for one-handed field use, (b) record a voice memo for later transcription, (c) write on paper, (d) skip the capture entirely.

Choice (d) is the failure mode. Data loss at capture time compounds — every observation that doesn't make it into the target's database is a downstream decision the engine cannot inform. The target's "intelligence" becomes proportional not to its engine quality but to its capture surface, which is the part of the product it least controls.

The wedge: a tap-and-talk capture surface that takes 5-10 seconds of voice (optionally enriched with a photo) and produces a structurally complete record the user can save with one more tap. The cognitive load of "what fields does the form need" is fully absorbed by the LLM extractor. The user's job is reduced to *speaking what they observed*.

The Anti-Replication boundary is sharp: the sidecar produces the record and hands it off via the target's API. It does not store the record. It does not query it. It does not replicate the target's intelligence layer. The capture surface and the decisioning surface remain separate products that share only a data contract.

## §4. Technical architecture

### Component graph

```
[Tap mic / camera] → [Voice + Image capture] → [Multimodal fusion] → [Extractor LLM] → [Sentient deal card] → [Action generators]
                                                       ↓                       ↓                                ↓
                                                       └──→ [Market intel] ←──┘                          [LOI / Email / etc]
```

The pipeline is sequential with per-stage progress streamed to the UI. Each stage degrades gracefully — if image analysis fails, fall back to voice-only; if market intel fails, hide the badge.

### Component routing

| Component | Tech | Role |
|---|---|---|
| **PWA shell** | React + Vite + Tailwind, Workbox for service worker, manifest for installability | Hold-to-talk mic button, camera button (rear-facing via `capture="environment"`), captured-image thumbnail, deal card render surface. Mobile-first layout; works one-handed. |
| **Audio capture** | MediaRecorder API with codec fallback chain (`audio/webm;codecs=opus` → `audio/webm` → `audio/mp4` → browser default) | Records to local Blob. Detects browser MIME differences (Safari ≠ Chrome). Emits 1-second timeslices for progressive chunks. Releases mic on stop. |
| **Image capture** | Hidden file input with `accept="image/*"` and `capture="environment"` | Triggers native OS camera UI on mobile. In-browser canvas compression to ≤1MB JPEG before upload. Base64 encoding for inline API transport. |
| **Transcriber** | Vertex AI Gemini (audio input, native) → OpenAI Whisper (fallback) | Receives audio blob via multipart upload. Returns transcript + duration. Fallback path activates if Gemini latency or transcription quality fails per-call thresholds. |
| **Image analyzer** | Vertex AI Gemini Pro (multimodal) → GPT-4o Vision (fallback) | Receives base64 image + transcript-as-context. Extracts visible data (phone numbers, broker names, company names, addresses, building-type indicators). Returns a Pydantic `SignData` model. |
| **Multimodal fuser** | Pure Python orchestration | Appends image-extracted context to transcript as `"Visual Context from photo: {fields}"`. Keeps the API contract on the extractor unchanged — extractor still takes a single transcript string. |
| **Extractor** | Vertex AI Gemini Pro structured outputs → GPT-4o `beta.chat.completions.parse` (fallback) | Receives enriched transcript + Pydantic schema. Returns a populated `Deal` model with `extra="forbid"` discipline. Generates not just structured fields but also `headline`, `summary`, and `highlights` (see §6 Sentient UI). |
| **Market Intel agent** | Vertex AI Gemini with Google Search grounding | Receives subset of extracted fields (address, asset class, asking price). Runs grounded search. Returns `comparison` (below_market/at_market/above_market), `percentage`, `market_range`, `explanation`, and `sources`. Runs async and pops into UI when ready. |
| **Action generators** | Vertex AI Gemini Pro (default) → GPT-4o (fallback) | Receives extracted deal + draft_type (LOI, follow_up_email, property_summary). Returns generated text. Pre-generated optimistically after extraction completes so the action buttons "light up" without latency on tap. |
| **Local memory** | `localStorage` (deal history, last 20 entries) | Demo-tier persistence. Production version uses Cloud SQL or the target's API. Memory is per-device not per-user. |

### The structured-output schema discipline

The extractor produces a Pydantic model with three layers:

1. **Strict structured fields** — `property`, `space`, `financials`, `contact`, `deal_context`, `confidence`. These map 1:1 to the target's CRM schema. Conservative extraction — only fields explicitly mentioned, others remain `null`.
2. **Sentient presentation fields** — `headline` (3-6 words, punchy), `summary` (1-2 sentence broker pitch), `highlights` (list of `{category, detail}` pairs). These are *generated*, not extracted — the LLM curates a presentation layer over the raw fields. This is the load-bearing innovation that separates the pattern from a generic form-filler (see §6 and §8).
3. **Confidence metadata** — `confidence.overall` (0-1 float), `confidence.fields_needing_review` (list of field names). Drives the Missing Field detector and the per-field uncertainty markers.

### Progressive rendering and graceful degradation

The pipeline takes 8-15 seconds end-to-end. Three disciplines keep it feeling instant:

- **Per-stage progress events** stream to the UI as `processingStage` transitions: `transcribing` → `analyzing_image` → `extracting`. The user sees activity, not a spinner.
- **Optimistic surfacing.** The deal card POPs as soon as extraction completes. Market intel runs in parallel and appears as a badge when ready (often 2-4 seconds after the card). Action buttons light up immediately because drafts are pre-generated.
- **Per-stage fallback.** If image analysis fails, the pipeline continues with voice-only — the captured image is preserved but the visual_context section is omitted from the enriched transcript. If market intel fails, the badge silently doesn't render. The user is never blocked by a non-critical agent failure.

### LLM stack discipline

The Renlo build shipped OpenAI-primary (Whisper for transcription, GPT-4o for extraction and drafts, Gemini for market intel only). This was the right call at sprint time — GPT-4o vision was best-in-class for sign OCR at the time, and Pydantic structured outputs via `beta.chat.completions.parse` was the most reliable schema-conformance path.

The pattern's canonical default going forward is **Gemini-primary**: Gemini Pro for native audio + image + text input, Gemini Pro structured outputs for extraction, Gemini with Google Search for market intel. OpenAI fallback only when Gemini multimodal fails schema conformance or quality thresholds. This brings the pattern into Anti-Replication compliance for Google-ecosystem-mandated engagements. Customizing for a new prospect: start with Gemini-only, document the per-call quality thresholds that would trigger OpenAI fallback, and only enable the fallback path if production data shows the threshold being hit.

## §5. Anti-replication boundary

The sidecar's surface is hard-bounded at the **record handoff**:

- **Never touches the target's system of intelligence.** Renlo's "Unified Relationships" engine, deal-history analytics, prospect-scoring, automated follow-up reminders — all untouched. The sidecar produces records; the target's engine consumes them.
- **Never replicates the target's existing product.** Renlo had announced spreadsheet-to-webpage conversion before pitch time. The original brainstorm included an "Instant Deal Room" microsite generator — Anti-Replication check killed it because Renlo's own roadmap had marketing-collateral generation in flight. The shipped build deliberately stays at capture and skips the publish-side entirely.
- **Never stores records in its own database.** `localStorage` is per-device demo memory. Production records flow directly to the target's API. The sidecar has no persistent state of its own to manage, no privacy boundary to maintain, no migration to coordinate.
- **Never owns the user identity.** Authentication is the target's. The sidecar receives the user's session token (or runs in the target's authenticated shell) and acts on their behalf.

The defensive line if the target's CTO challenges: *"Our PWA captures field input and emits records via your API. We don't store, query, or analyze the records. If you turned our sidecar off tomorrow, your users would lose a faster capture surface; your engine would keep working with the records already in your database. There is no integration with your intelligence layer. There is no shared state."*

## §6. Theatre approach

The pattern's strongest theatrical move is the **Sentient UI**: the LLM doesn't just extract fields, it curates a *presentation* of the deal. A raw voice memo like *"high rise office building in Ikoi, 10,000 square feet, $120k monthly, lots of competition, urgent"* becomes a card with:

- **Headline:** "Urgent Waterfront High-Rise in Ikoi"
- **Smart badges:** ⚡ Urgent, 🔥 Hot Deal, 🌊 Waterfront
- **Narrative summary:** "A rare 10,000 SF waterfront high-rise in Ikoi with motivated seller facing multiple offers. Act fast — this won't last."
- **Structured fields** below the fold (address, size, rent, contact)
- **Market intel badge** (after async ground-search): "📈 12% above market — typical office rent in Ikoi is $95-$110/SF"

The hero element is not the structured data. The hero is the curated voice — making the user feel like they have a research analyst riding shotgun, not a form they have to feed. The form-feeling is the failure mode the pattern is designed to escape.

### Magic moment timing

- **T+0** — User taps mic. Hold-to-talk activates.
- **T+5–10** — User releases. Audio uploads. Transcription begins. Status: "Transcribing audio..."
- **T+8–12** — Transcript appears. If image was captured, image analysis runs in parallel. Status: "Reading sign..."
- **T+12–18** — Extraction completes. Sentient deal card materializes — headline, badges, summary, fields. Status fades. This is the Magic Moment.
- **T+15–22** — Market intel badge pops in (if applicable).
- **T+18–25** — Quick action buttons (Draft LOI, Follow-up, Property Summary) become tappable. Drafts are pre-generated on first availability.
- **T+anywhere** — User taps "Save." Record posts to target API. Done.

### Rendering surfaces

Three surfaces tuned to three buyer angles:

| Surface | Audience | What it proves |
|---|---|---|
| **The deal card hero** | Operator / business buyer (broker, founder watching the demo) | Speed + intelligence — "10 seconds of speech → curated listing." |
| **The market intel badge** | Strategic buyer (CRO, founder) | Real-time grounding — "AI estimate backed by live Google Search, not stale training data." |
| **The Quick Actions drawer** | Decision-maker | ROI compression — "the next three things they'd type manually are already written, ready to copy." |

### Theatre-side decisions worth canonicalizing

- **The camera button sits next to the mic, not behind a menu.** Multimodal capture has to feel like a single gesture. Hide-and-reveal kills it.
- **The transcript is collapsed under a `<details>`, not the hero.** Showing the transcript prominently makes the demo feel like a transcription tool. Hiding it under "View transcript" makes the curated deal card the headline.
- **Missing fields surface as a toast, not a modal.** Modal interrupts the magic moment. Toast says "you're missing X, tap to add" without breaking flow.
- **Pre-generate drafts; don't wait for tap.** Tapping "Draft LOI" and waiting 3 seconds breaks the impression that the system has already done the work. Pre-generation makes the actions feel pre-existing.

## §7. Prior worked examples

**Renlo (January 2026).** Pitched to the technical co-founder of Renlo, a commercial real-estate "Modern OS" startup in Early Access. Architecture shipped as a Voice-to-Deal Field Commander PWA: mobile-first installable web app, Whisper transcription, GPT-4o vision for sign capture, GPT-4o structured outputs for `Deal` extraction with Pydantic schema discipline, Gemini Pro with Google Search for market intelligence grounding, GPT-4o for draft generation (LOI + follow-up email). Two adjacent ideas surfaced and deferred — Owner Detective (LLC-to-human resolution via state business registry scraping; killed for legal scraping risk after Gemini consultation), Total Recall (vector-search over historical voice notes; scoped down to localStorage for sprint).

See: `prospects/renlo/architecture.md` (full PRD with founder psychology, competitive landscape, three-phase rollout), `prospects/renlo/overview.md` (engagement state, PropTech context).

## §8. Anti-patterns and failure modes

These are the build scars from the Renlo sprint. Treat them as a checklist when customizing the pattern for a new field-worker prospect.

**1. Rigid form-like UI.** The first cut of the deal card was a strict-fields-only layout — address, size, rent, contact, in a vertical stack. Hafeedh flagged it mid-build: *"It's still kind of rigid... we're building a freaking intelligence robot here."* The fix was the Sentient UI — adding `headline`, `summary`, and `highlights` to the Pydantic model so the extractor produces presentation language alongside structured data. Pattern lesson: if the deal card feels like a form, the demo will land as a form, no matter how good the AI is. The LLM has to curate, not just extract.

**2. Skeleton-endpoint hangover.** Early builds had `/api/transcribe` returning hardcoded mock strings while the frontend built capture features against it. Frontend looked done; backend was vapor. The fix was to save uploaded files to disk immediately and verify with playback before integrating Whisper. Pattern lesson: real I/O on day 1 or you cannot diagnose what's actually broken.

**3. MIME-type mismatch across browsers.** Safari emits `audio/mp4` or `audio/x-m4a`; Chrome emits `audio/webm`. Naive backend code that hardcoded "audio/webm" 500'd on iPhone uploads. The fix was a codec-detection chain on the frontend (preferred → fallback → browser default) and content-type logging on the backend. Pattern lesson: detect MIME, don't assume. Mobile field users are heavily iPhone-weighted.

**4. Sequential pipeline failure cascade.** The original flow was strict sequence — transcribe → analyze image → extract. If image analysis 500'd, the whole pipeline died and the user got a generic error. The fix was per-stage graceful degradation: image analysis failure logs a warning and continues with voice-only. Pattern lesson: every non-critical agent in the pipeline must have a documented fallback path that preserves the magic moment.

**5. Scraping proprietary data sources.** The original brainstorm included an "Owner Detective" agent that would scrape state business registries and LinkedIn to resolve LLC owners. Killed after legal-risk review — CoStar and LinkedIn both litigate scrapers aggressively. Pattern replacement: use AI estimates with explicit "AI Market Estimate" labeling, surfaced via Gemini with Google Search grounding. Pattern lesson: never replicate proprietary data sources that have legal teams. Use grounded LLMs with citations instead.

**6. Over-engineering memory.** The original brainstorm included "Total Recall" — semantic vector search over historical voice notes via a vector DB. Scoped down to `localStorage` for the 72-hour sprint. The localStorage version covered 95% of the demo value (browsing recent deals, reloading old captures) at 1% of the engineering cost. Pattern lesson: faked-it-with-simpler-tech is correct during sprints. Add real infrastructure only after the demo validates the wedge.

**7. TTS interrogation loops.** The brainstorm included "Interrogation Mode" — AI asks follow-up questions aloud when fields are missing. Scoped down to a silent toast notification because TTS roundtrip latency (2-3 seconds) breaks the magic-moment timing, and synthesized voice talking back to a broker driving a car is creepy more than helpful. Pattern lesson: any feature that adds a synchronous loop after the user has stopped talking will be killed by the timing budget. Use silent surfaces for follow-up prompts.

**8. CRM API not actually wired.** Demo-tier shipped the "Save to Renlo" button as a localStorage write. Production version would POST to the target's API, which requires their auth flow, schema mapping, and error-state handling — none of which is sprint-feasible without target cooperation. Pattern lesson: be explicit with the prospect about what's demo-tier and what's production-tier. The capture pipeline is real; the persistence layer is mock until the target gives access.

**9. Multi-agent provider drift.** The Renlo build uses three providers (OpenAI Whisper, OpenAI GPT-4o, Google Gemini). Each has different auth, rate limits, pricing, and failure modes. Cost per voice note is ~$0.004 across all three; if any one provider rate-limits, the user-facing pipeline degrades unpredictably. Pattern lesson: for the canonical version, consolidate to a single provider where possible. Today's Gemini Pro handles audio + image + structured outputs + search natively — there's no longer a multi-provider justification for greenfield builds.

## §9. When NOT to reach for this pattern

- **Users are desk-bound.** If the target's users live at keyboards (analysts, accountants, customer success), voice capture is a solution to a problem they don't have. A faster form is the right wedge, not a voice surface.
- **Target's data model is too freeform to validate.** The pattern depends on a Pydantic-shaped schema with named fields. If the target's "record" is a freeform text blob with no required structure, the extractor has nothing to anchor to and the demo collapses to "we transcribed your voice memo."
- **Field capture is not the binding constraint.** If users are happily typing into the target's interface and retention is fine, the pattern solves a non-problem. Verify the manual-entry friction is real and named in user research before pitching.
- **Target's product already ships voice or multimodal capture.** Replication check. If the target has a mobile app with voice notes (even a poor one) or has shipped vision features, the pattern needs to pivot — possibly to a higher-end multimodal capability the target doesn't yet have, possibly to a downstream action layer instead of the capture layer.
- **Regulated capture domains.** Voice in legal-discovery, medical, or financial-advisory contexts carries recording-consent and retention requirements that a 72-hour sprint cannot address responsibly. Punt the pattern, or scope the engagement to non-sensitive capture only.
- **Network latency on the user's side is unreliable.** The pattern assumes 8-15 second roundtrip latency for the LLM pipeline. Users on intermittent connectivity (rural field workers, international travelers) will see the magic moment break. Either ship offline-capture-with-batch-sync (much harder) or qualify the target's user base to connected-urban field roles.

## Cross-references

- Prior worked example: `prospects/renlo/architecture.md`, `prospects/renlo/overview.md`
- Adjacent patterns:
  - `frameworks/architectural_patterns/safe_harbor_synthetic_data_fabric.md` — both run upstream of a target engine, both use Pydantic-shaped structured outputs as the trust anchor, both maintain the LLM-as-generator / deterministic-validator-or-curator split. Safe-Harbor generates *synthetic* data for an empty schema; Voice-to-Structured-Action *extracts* data from messy input. Different input shapes, same upstream-sidecar discipline.
  - `frameworks/architectural_patterns/calibration_sidecar.md` — also takes unstructured input (research bundles for AS, voice for Renlo) and produces validated structured payload for a target engine. Calibration uses deterministic rules + Nia vocabulary check; Voice-to-Structured-Action uses LLM curation + grounded market intel. Same shape, different validation strategies.
- Pipeline templates referenced: `pipeline/templates/STEP_04_PRD_REDTEAM_TEMPLATE.md` (anti-replication audit, killed Owner Detective and Instant Deal Room), `pipeline/templates/STEP_06_LATERAL_EXPLORATION_TEMPLATE.md` (surfaced the Sentient UI pivot mid-sprint)
- Identity reference: `identity/identity_doc.md` §5 (Technical Constraints — canonical pattern is Gemini-primary; OpenAI fallback acceptable only with documented quality-threshold justification)
- Mobile-first reference: PWA installability, `capture="environment"` for rear-camera, MediaRecorder codec fallback chain. These are pattern-level technical decisions, not Renlo-specific.
