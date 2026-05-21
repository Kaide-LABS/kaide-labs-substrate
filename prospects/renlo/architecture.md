---
type: prospect_architecture
prospect: renlo
vertical: proptech_cre
demo_name: voice_to_deal_field_commander_pwa
sprint_duration: 72_hours
last_updated: 2026-05-19
canonical: true
architecture_pattern: voice_to_structured_action_pwa
key_tags:
  - pwa
  - multimodal_input
  - multi_agent
  - gemini_multimodal_audio
  - gemini_flash_structured_outputs
  - gemini_pro_grounding
  - sentient_ui
  - cre_jargon_handling
  - upstream_sidecar
---

# Renlo — Architecture: Voice-to-Deal: Field Commander PWA

## Sprint scope

72-hour high-impact prototype solving the "Mobile Void" left by legacy CRMs.

**The pitch:** "Brokers spend half their day in cars, but CRM data entry requires a keyboard. I'll build a 'Voice Button' PWA where a broker taps, speaks, and a new Deal record appears instantly — zero typing."

## Anti-Replication boundary

The sidecar operates strictly **upstream** of Renlo's core System of Intelligence engine. It is a data-ingestion layer that converts unstructured field input (voice, images, hands-free) into structured Deal records consumable by Renlo's existing data model.

**Specifically NOT touched:**
- Renlo's relationship-management prediction layer
- Renlo's cross-system orchestration ("the brain")
- Renlo's CRM data model or storage
- Any decisioning or routing logic Renlo's product performs on Deal records after ingestion

The sidecar is a containerized API endpoint. Renlo plugs it in; if they unplug it tomorrow, their product still works (with the original keyboard friction restored).

## Technical architecture

### Stack

**Frontend (Progressive Web App):**
- React + Vite + Tailwind CSS
- Hosted on Netlify
- Workbox for offline PWA capabilities
- Native browser APIs (MediaRecorder for audio capture, getUserMedia for camera)
- Designed to feel like a native mobile app — bypasses App Store friction

**Backend:**
- Python FastAPI
- Hosted on Google Cloud Run (serverless)
- Acts as orchestration layer for the AI models

**AI Multi-Agent Engine (all via Google Vertex AI):**

| Agent | Model | Job |
|---|---|---|
| Audio Transcription | Gemini Multimodal Audio | Direct audio stream transcription with robust CRE-jargon handling ("NNN lease", "Cap Rate", "TIs") |
| Structured Extraction | Gemini Flash (structured outputs) | Parses raw transcript + images into strict validated JSON schema representing a CRE deal — extracts square footage, asking rent, rent type, owner sentiment, etc. |
| Live Market Oracle | Gemini Pro with Google Search grounding | Performs real-time grounded market research — compares the extracted deal against current commercial rental rates in the specific neighborhood |

## The Super Broker pipeline — four-phase feature build

The demo evolved from a simple dictaphone into a highly advanced, multi-modal "Super Broker" tool across four major phases:

### Phase 1 — Multimodal Input ("Detective Mode")

Brokers can snap a photo of a "For Lease" sign or a building facade while simultaneously recording a voice note.

The backend uses Gemini's vision capabilities to merge the visual data (reading the broker's name and phone number off the sign) with the spoken audio context — pre-filling the deal card with information the user didn't even explicitly speak.

**Why it works as theatre:** the broker sees the deal card auto-populate with text from the sign that they never dictated. Visible proof of multimodal extraction in under 10 seconds.

### Phase 2 — The "Sentient" UI

Instead of displaying a rigid, boring form (Address / Price / Size fields), the React frontend dynamically adapts to the incoming data:

- **Dynamic Headlines** — Gemini generates a punchy title for the property (e.g., "Urgent Waterfront High-Rise in Ikoi")
- **Smart Badges** — visual tags dynamically generated based on sentiment and data extracted (e.g., red flame icon for "Urgent", blue badge for "Waterfront")
- **Narrative Summary** — auto-generated 2-sentence "Broker Pitch" summarizing the deal context at the top of the card

**Theatre value:** the UI itself becomes evidence of the AI's understanding. The form *thinks*.

### Phase 3 — "The Oracle" (Live Market Intelligence)

This is where the multi-agent architecture compounds. While Gemini Flash extracts the data, the backend fires off a *parallel* request to Gemini Pro with Google Search grounding.

Gemini Pro performs a live Google Search for current commercial rental rates in the specific neighborhood. Result: a badge appears on the deal card telling the broker immediately whether the property is overpriced or a steal (e.g., "📉 15% Below Market Avg for Office Space in this area").

**Theatre value:** instant market intelligence layered on top of the extraction. The broker sees competitive context before they even step out of the car.

### Phase 4 — Instant Actions & Artifacts ("The Closer")

The moment the voice note finishes processing, the app doesn't just save the deal — it automatically generates business artifacts using the extracted JSON:

- **Draft LOI (Letter of Intent)** — pre-filled, ready for review
- **Listing Blurb** — marketing copy formatted for LoopNet or CoStar
- **PDF Generation** — uses `jspdf` to generate a branded PDF of the LOI client-side, allowing the broker to download and email an offer before they drive away from the property

**Theatre value:** the voice memo doesn't just create a record — it produces *deliverables*. A 15-second voice memo turns into a complete deal package.

### Phase 5 — Verification & Coaching

- **Audio Verification** — embedded audio player lets the paranoid broker play back the original recording to ensure the AI didn't hallucinate numbers
- **"The Coach" (Missing Fields)** — if the broker mumbles a note but forgets square footage or rent, the UI pops up a toast notification: "⚠️ Missing Square Footage. Tap to add."

**Theatre value:** the AI knows what it doesn't know. Trust accelerator for the skeptical CRE professional.

### Phase 6 — The "Kill Shot": The Deal Room

To simulate Renlo's "Network Effect," the app features a **"Share Deal Room"** button. Instead of emailing a clunky 50MB PDF, this generates a simulated public link (`renlo.site/deals/{id}`). Clicking it opens a beautiful, public-facing landing page for that specific property — effectively acting as an instant website builder for the broker's new listing.

**Theatre value:** the broker can drive away from a property and email a *website* to the prospect 30 seconds later. Hours of administrative work collapsed into a 15-second voice memo + a single share button.

## Magic Moment definition

**The 60-second Magic Moment:** Broker taps the voice button, speaks for 15 seconds describing a property (with optional photo of the lease sign), and within the next ~45 seconds:

1. A fully-populated Deal card appears with dynamic headline, smart badges, narrative summary
2. A live market-intelligence badge attaches with the over/under-priced verdict
3. A Draft LOI, listing blurb, and downloadable PDF are generated
4. A Share Deal Room link is ready to send

The "magic moment" is the visible collapse of what currently takes 30 minutes of keyboard typing into a 60-second hands-free workflow.

## 5-Pillar verdict

1. **Bottleneck Assassin:** ✅ — directly solves the "brokers default to Excel" / "Mobile Void" problem Renlo's product is structurally bottlenecked by
2. **Anti-Replication:** ✅ — operates strictly upstream of the System of Intelligence; never touches Renlo's core engine
3. **Native Environment:** ✅ — PWA on the broker's mobile device, the actual environment where field data is generated
4. **Magic Moment:** ✅ — 15-second voice memo → complete deal package visible in under 60 seconds
5. **System Resilience & Immunity:** ✅ — deterministic structured-output validation via Gemini Flash; missing-fields coach as deterministic fallback; audio verification for hallucination check

## Summary

The prototype is a masterclass in combining modern browser APIs with orchestrated LLMs. By utilizing Gemini's structural reliability and real-time search grounding, the demo proves to Renlo that the data-ingestion friction can be entirely eliminated upstream of their core product — providing an "11/10" experience that turns hours of administrative work into a 15-second voice memo, without touching their System of Intelligence layer.

## Reusability for future prospects

This architecture is a reference pattern for any future prospect matching:
- Mobile-first user (field workers, brokers, sales reps, inspectors, drivers)
- Unstructured field input that must become structured database records
- A core engine that performs decisioning on structured records once ingested
- A clear upstream wedge for voice/photo/multimodal capture
