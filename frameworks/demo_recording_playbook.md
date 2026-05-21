---
type: framework
domain: demo_recording_operations
last_updated: 2026-05-19
canonical: true
key_tags:
  - demo_recording
  - vidyard
  - cold_open
  - magic_moment_timing
  - paper_script_fallback
  - completion_rate_economics
  - deferral_discipline
  - audio_cleanup
  - capcut_assembly
sources:
  - "BRAINSTORMING 1.1 (AS / RCS demo recording — late April / early May 2026)"
  - "BRAINSTORMING 1.0 (Tracelight Safe-Harbor recording sprint)"
  - "Earlier Tracelight + WeFitLabs recording iterations"
---

# DEMO RECORDING PLAYBOOK

The operational reference for every Kaide Labs demo video. Captures what was learned across the Tracelight, WeFitLabs, and Artificial Societies recording sprints. Compounds across future sprints — each new recording should leave behind any pattern that's not yet documented here.

## Why this exists

The demo video is the load-bearing artifact of every Kaide Labs cold outreach. The architecture is real, the build is real, but the prospect's first contact with all of it is a 3-7 minute screen recording. The video has to clear three filters in sequence: get clicked, get watched past minute 2, produce a response.

Demo recording quality is sensitive to operator energy, location reliability, and tool stack maturity. The patterns below exist because each one cost real time to discover (multiple deferral cycles, wasted takes, fatigue-degraded recordings, tool failures mid-shoot). Filing these patterns is how each new sprint avoids paying those costs again.

## Locations

### Primary — PAU library right-wing patio

**Window:** 7:00–8:00am Lagos time. Library proper opens at 8:00; before that, the patio is empty. After 8:00 foot traffic increases and takes get harder.

**Why it works:**
- Quiet (campus not awake yet)
- Glass-panel reflection provides academic environment signal
- Natural morning light
- No need to arrange access — outdoor public space

**Verification before recording day:** Walk past the patio the evening before. Confirm it's accessible at 7am (sometimes locked overnight, sometimes early-morning cleaning crews). If patio is unavailable, fall back to dorm/room or defer.

**Setup:**
- Eye-level camera position (laptop on books or boxes — never recording from below)
- Camera lens height aligned with eye line, not chin
- Phone or laptop teleprompter positioned just above the camera so eye direction reads as eye contact

### Backup for calls (not recordings) — Upstairs library lounge

The patio works for recording. The upstairs lounge works for taking calls when the response lands. Different acoustic profile, more controlled environment, available during library hours.

### Deprecated locations

- **Friend's apartment.** Implicit obligation concerns tied to accepting favors. Salafi observance discernment correctly identified this as wrong even though the friend offered. Don't reconsider.
- **Dorm/personal room.** Tested, didn't work (specific reasons vary — noise, roommate, lighting, clutter). Re-evaluate only if the patio is permanently unavailable.

## Pre-recording preparation (day before)

The night before recording:

1. **Walk past the patio.** Confirm 7am accessibility.
2. **Install or verify the teleprompter tool.** PromptSmart Pro didn't work on Hafeedh's setup. Speakflow is an alternative worth re-testing. **Default fallback: paper script.** Print the script on paper, position it just above the camera. Loss of "natural eye movement" is offset by zero risk of tool failure mid-shoot.
3. **Iron the shirt.** Red shirt was the AS choice — strong against library glass background, reads professional without feeling overdressed. Whatever the shirt, iron it the night before.
4. **Charge laptop to 100%.** Patio has no outlets. 90-minute recording session needs full battery plus margin.
5. **Sleep by 11pm.** Six and a half hours before a 6:30am alarm.
6. **Set alarm for 6:30am.** Be at patio by 7am. Done before campus wakes.

## Recording session structure (the 60-minute window)

7:00am arrival. Setup takes 5-10 minutes. Recording window is 7:10–8:00am.

**Three takes maximum.** First take is the warmup. Second take is usually the keeper. Third take is the safety net if take 2 had a verbal stumble or framing issue.

**Do not iterate setup mid-shoot.** If lighting is wrong in take 1, fix it once before take 2 and lock the setup. Changing setup between every take is the failure pattern that produces drained takes by attempt 4.

**Do not record when physiologically drained.** Signal: eyes flat, hand on temple, exhausted expression. Different person than the one who showed up at 7:00. If this state appears, defer to next morning. Recording in drain produces takes that show the drain regardless of what the camera sees.

## Video structure (the canonical 5-segment template)

This structure is locked across Kaide Labs demos. Adjust per-prospect content; do not adjust the segment ordering.

### 0:00–0:25 — The Empathy Hook

**Visual:** On camera, headshot. No screen share.

Open with a direct reference to the founders by name. Quote their own words verbatim where possible — recent LinkedIn post, podcast appearance, or product statement. The structure:

> *"Hey [Founder 1], [Founder 2] — [voice] here from Kaide Labs. [Founder 1], you recently posted about [specific pain in their words]. We know [the structural problem this causes]. So over the past couple days we [built the specific sidecar]."*

The hook does two jobs: signals you've actually researched them (not generic outreach), and frames the demo as a direct response to *their* stated need.

### 0:25–2:30 — The Magic Moment (Screen Recording)

**Visual:** Pre-recorded screen captures spliced in. Voice continues over the screen.

This is where the sidecar runs end-to-end. Pre-record the screen sequence the day before — do NOT live-screen-record during the talking take. Pre-recording lets you control timing, cuts, and pacing precisely. Live screen capture during the voice take introduces too many variables.

The magic moment fires in the first 60 seconds of this segment. The viewer must SEE something tangible happen — extracted JSON populating, dashboard updating, structured output appearing. Verbal description of magic without visual proof does not land.

### 2:30–3:15 — Technical Depth (Voice + Screen)

For the technical co-founder. Name the actual architecture: agent routing, deterministic safety rails, integration surface. Use acronyms the audience will recognize (DAG, VPC, etc.) — using them correctly signals operator-tier credibility, omitting them signals outsider.

This is where Aleks-shaped buyers (technical co-founders) lean in. Keep it tight — 30-45 seconds of architectural depth, with screen showing the components named.

### 3:15–3:40 — The FDE Framing

Back to camera or screen overlay. Reference the Anti-Replication Principle explicitly:

> *"This sits entirely upstream of your core engine. We never touch [their specific IP]. Containerized, stateless. If you unplug it tomorrow, your product still works."*

This is the CTO Shield — the moment that converts the demo from "competitive threat" to "adjacent helper." Load-bearing for technical-buyer trust.

### 3:40–4:15 — The Adjacent Ideas Menu (Verbal Only)

Tease 1-2 additional architectures verbally. Do NOT show them on screen. The structure:

> *"There are a couple other angles we considered — [Adjacent Idea 1, one sentence]. And [Adjacent Idea 2, one sentence]. Happy to walk through either of those if it's useful."*

Verbal-only is deliberate. If the architectures are in the video, the founder can stop watching and assign one to their own team. Verbal teases create a reason to take the call.

### 4:15–end — The Closer

Brief. Direct. CTA:

> *"If the architecture looks right, let's grab 15 minutes to scope the production build. If not, no follow-up from me."*

The "no follow-up" line is non-negotiable. It signals high status, eliminates the chase, and converts the founder's mental frame from "managing a salesperson" to "deciding whether to engage."

## Tool stack

### Recording

**Windows Game Bar** has been the reliable option when other recording tools failed. Built into Windows 10/11, no install, no paywall.

If primary recording tool fails the morning of the shoot, Game Bar is the fallback. Verify it works on your machine the night before, not the morning of.

### Audio cleanup — Adobe Podcast

Browser-based, free tier sufficient for cold pitch videos. AI-powered noise removal works even on outdoor patio recordings with light ambient sound. Drop the raw audio in, download the cleaned version. ~2 minutes per pass.

This is the tool that makes 7am patio recordings sound studio-clean. Non-optional.

### Video assembly — CapCut

Free tier with paywall risk on export. Watch this — if CapCut wants to charge for a feature mid-shoot (transition effect, specific export resolution), the moment-of-friction may force tool swap to Davinci Resolve or similar. Have a backup option identified before the first export attempt.

CapCut workflow:
1. Import the two-take raw video files
2. Cut to the best lines from each take
3. Splice in the pre-recorded screen captures with audio ducked
4. Add System Note captions (text overlays for technical clarification points)
5. Export at 1080p

**Minimize re-exports.** Each export cycle is a chance for something to break (paywall surface, codec issue, audio sync drift). Get the cut right, export once.

### Hosting — Vidyard

Free starter plan has known bugs (encountered during AS sprint — specifics in BRAINSTORMING 1.1). Budget for the $200/yr proper plan once first £10k clears.

Vidyard provides view analytics — view percentage, geographic origin. The AS send showed a UK-based view at ~6% completion (~25 seconds, through the cold open and magic moment). That single data point is more useful than any guess about whether the email was opened.

### Teleprompter

**Primary attempt:** PromptSmart Pro. Did not work on Hafeedh's setup during AS sprint.
**Secondary:** Speakflow. Not yet tested.
**Fallback:** Paper script. Print on A4, position just above the camera. Loss of natural eye movement is offset by zero risk of mid-shoot failure.

Paper script has been the actual production tool for the past two recording sprints. Treat it as the default; teleprompter apps are upgrades, not requirements.

## Editing workflow

Same-day editing matters. Don't let the raw footage sit overnight — energy for editing decays fast, and 24-hour-old footage becomes harder to remember the intent of.

Workflow:

1. **7:00–8:00am** — record at the patio
2. **8:00–8:30am** — transit home, eat properly, hydrate
3. **9:00–11:00am** — first editing pass (cut + splice in screen captures)
4. **11:00am–12:00pm** — audio cleanup via Adobe Podcast, second pass cut
5. **12:00–1:00pm** — System Note captions, export, upload to Vidyard
6. **1:00pm onward** — email draft, send window

**Send window:** Tuesday–Thursday mornings UK time. Lagos is GMT+1, UK is BST in May (GMT+1 same as Lagos) — send between 8am and 11am UK to hit UK/EU inboxes when founders are at desk.

## Email mechanics

The email is the click-conversion artifact. The video can't do its job if the email doesn't get the click.

### Tagged lines

Address business and technical co-founders separately within one email. Example structure:

> *Hey [Founder 1], [Founder 2] —*
>
> *[Founder 1]: [revenue-nerve framing — what enterprise deal this unblocks, dollar value if known]*
>
> *[Founder 2]: [architecture-nerve framing — stateless sidecar, deterministic validator, containerized]*
>
> *Walkthrough of [the sidecar] — [Vidyard link]*
>
> *Skip to 0:40 for the live demo.*
>
> *If the architecture looks right, let's grab 15 minutes. If not, no follow-up from me.*
>
> *— [voice], Kaide Labs*

Single email to all founders is the canonical Kaide Labs pattern. Do not send separate emails to business and technical founders.

### The timestamp trick

"Skip to 0:40 for the live demo." Points the founder directly to the magic moment. Reduces the bounce-out risk in the empathy hook minute by giving them a fast-forward option that lands them at the strongest section.

### CTA

"If the architecture looks right, let's grab 15 minutes to scope the production build. If not, no follow-up from me."

This is the Kaide Labs locked CTA. Do not rewrite it. The "no follow-up" line is the load-bearing element — it inverts the sales-call dynamic and produces higher response rates than any iteration tested.

### What NOT to include

- Don't add duration to the email ("3-minute walkthrough"). Vidyard shows runtime once they click; pre-loading the length sets a precision expectation that's often missed (AS came in at 7:07 vs the planned 5:00).
- Don't add caveats about video quality. The video is what it is; weakening the framing reduces conversion.
- Don't tell the founder you're a solo founder, a student, or in Lagos. None of this is hidden — it surfaces if they ask — but pre-loading it in the cold email reads as discounting your own work.
- Don't parenthetically attribute tools to other people you're trying to connect with separately. "(Arlan's tool)" was specifically killed during AS sprint for this reason.

## Length targets

**Target window: 3:00–6:00.** Sweet spot 5:00–5:30.

**Vidyard data:** Completion rate drops measurably between minute 5 and minute 7. ~15-20% drop in average completion. AS came in at 7:07 and shipped anyway — workable but not optimal.

**Why optimal is 5:00–5:30:**
- Empathy hook + magic moment + technical depth fit comfortably in first 3:00
- FDE framing + adjacent ideas + closer fit in 2:00–2:30
- Founders who care will watch the whole thing; founders who don't will bail by minute 3 regardless of total runtime

**Why over 7:00 hurts:**
- Average completion rate matters less than response rate, but very-long videos signal "this person doesn't respect my time"
- Adjacent ideas tease in minute 4-5 lands stronger when the founder hasn't already lost attention

**Delivery pace is 25-30% slower than script-writing pace.** A script that reads at 5:00 in your head will deliver at 6:15–6:30. Plan accordingly.

## Voice selection — Hafeedh or Isaac

Each demo's voice should match the founder psychology being pitched.

**Hafeedh (lead architect voice):**
- Use when the prospect's founders are themselves architects/engineers (e.g., AS — James and Patrick are technical)
- Use when the pitch is architecture-heavy (multi-agent routing, deterministic validation, technical depth)
- Use when authenticity matters more than polished delivery

**Isaac (operator/GTM voice):**
- Use when the prospect's lead founder is a business operator (e.g., Tracelight — Peter Fuller is operator-shape)
- Use when the pitch is outcome-heavy (deals unblocked, dollar value, ROI math)
- Use when polished delivery matters more than architectural depth

When in doubt, Hafeedh is the default. Isaac is the upgrade when operator-shape framing is the right register.

## Failure patterns to avoid

These have all happened. The discipline below prevents recurrence.

### Iterating setup permutations across days

The fatigue pattern: patio → upstairs → wardrobe change → friend's apartment → window orientation → recording tool research. Each step is individually reasonable. Cumulative cost is operator drain before takes begin.

**Discipline:** Pick the setup the day before. Execute it. Iterate only if a hard blocker surfaces (location unavailable, tool failure), not if a soft preference shifts.

### Recording when drained

Signal: eyes flat, hand on temple, exhausted expression. The person on camera at attempt 4 is not the same person who showed up at 7:00.

**Discipline:** If drain symptoms appear, defer. The next morning's fresh take beats today's drained take. James/Patrick/Peter aren't going anywhere — a 24-hour delay is invisible to them.

### Mistaking constraint-driven deferral for fatigue-driven avoidance

Legitimate deferrals (location locked, tool failed, blocked by external event) are not the same as avoidance (delaying because the send feels scary). Both feel similar in the moment.

**Discipline:** Name the blocker explicitly. "Library closed → patio inaccessible at 7am" is a constraint. "I want to revise the script one more time" is avoidance. The first is a defer-and-resume; the second is a ship-it-anyway.

### Pre-emptively lowering price after a wobble

After a less-than-perfect take, the temptation surfaces: "maybe £10k is too high, what if I pitch £7k?" This is post-take anxiety, not strategic repricing.

**Discipline:** Pricing decisions happen in calm conditions, not in post-recording adrenaline. £10k stays. Video quality concerns get fixed in editing, not in pricing.

### Polluting multi-channel pitches

Email to a prospect and LinkedIn DM to a strategic contact are two clean channels. Mentioning one in the other (e.g., "I just sent X an email about Y") pollutes both.

**Discipline:** Each channel stays clean. The strategic contact finds out about the prospect outreach on their own timeline, if ever.

## Per-recording checklist

Print this. Run it the day before and the morning of each recording.

### Day before (evening)

- [ ] Patio walk-by — confirm 7am accessibility
- [ ] Teleprompter test — PromptSmart, Speakflow, or paper fallback ready
- [ ] Shirt ironed
- [ ] Laptop charged to 100%
- [ ] Script printed (paper fallback) and rehearsed once aloud
- [ ] Sleep by 11pm

### Morning of (6:30–7:00am)

- [ ] Alarm + immediate wake (no snooze)
- [ ] Hydrate, eat something light
- [ ] Arrive at patio by 7am
- [ ] Setup complete by 7:10
- [ ] Three takes done by 8:00am
- [ ] Transit home for editing

### Editing day

- [ ] First pass cut by 11:00am
- [ ] Adobe Podcast audio cleanup
- [ ] System Note captions added
- [ ] Single export at 1080p
- [ ] Vidyard upload + link verified
- [ ] Email draft locked
- [ ] Send window: Tue/Wed/Thu mornings UK time

## What to capture for next sprint

Every recording leaves behind data. Capture in the prospect's `outreach.md` file:

- Recording date and location
- Final runtime
- Tool stack used (recording, audio, assembly, hosting)
- Vidyard analytics 48 hours post-send (view percentage, geography)
- Any new failure pattern not yet documented in this playbook → file back here as a substrate update

This is how the playbook compounds. Future recordings inherit the patterns from prior ones.

## Cross-reference

- **Voice script structure templates:** `pipeline/templates/STEP_14_CALL_BRIEF_TEMPLATE.md` (the dual-voice doc that fuels post-send conversations)
- **Pre-recording sprint context:** `pipeline/pipeline_overview.md` (where in the pipeline this work lands — typically post-Step 12, pre-cold-email-send)
- **Per-prospect engagement records:** `prospects/[name]/outreach.md` (where the prospect-specific recording data accumulates)
