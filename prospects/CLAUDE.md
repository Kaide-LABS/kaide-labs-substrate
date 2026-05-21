---
type: navigation
scope: prospects
last_updated: 2026-05-17
---

# prospects/

Per-prospect engagement records. Each subdirectory is one prospect. Structure varies by status.

## Status taxonomy

- **Active** — currently in sprint or pre-sprint stage. Full topic-file structure with files populated as pipeline advances.
- **Won** — closed customer. Full topic-file structure + `outcome.md` with success factors.
- **Lost** — engaged but didn't close. Full topic-file structure + `outcome.md` with failure mode.
- **Killed** — hard filter fired before any sprint commit. Only `CLAUDE.md` + `kill_log.md`.
- **Archived** — didn't fit current motion but revisitable. Only `CLAUDE.md` + `archive_log.md`.

## Current pipeline (as of 2026-05-19)

| Prospect | Status | Sprint stage | Notes |
|----------|--------|--------------|-------|
| matta | Active | Sprint 1 — demo recording phase | Phase 1.5 integration debug closed; recording per `frameworks/demo_recording_playbook.md` |
| solvo | Active | Sprint 2 active | Step 1A complete in Gemini DR; Step 1B red-team queued in Claude Web |
| juna | Active | Step 0 intel captured, fit assessment pending | Claude Code + Nia assessment queued |
| artificial_societies | Lost | Closed (relationship win, deal loss) | Triggered post-AS pipeline re-engineering |
| tracelight | Lost | Closed (no response) | Held no-follow-up discipline correctly |
| wexler | Archived | Architecture generated, not pursued | Pre-Submission Bundle Attestation Sidecar |
| tortus | Archived | Did not pursue post-sourcing | Too deep in clinical vertical |
| strangeworks_quantagonia | Archived | Architecture explored, not pursued | OpenClaw strategic hook |
| prometheux | Killed | Step 0 fit assessment | Killed on riba filter (financial services primary vertical) |
| avallon_ai | Killed | Pre-Step-0 sourcing review | Killed on insurance filter |

## Query patterns

**For a new prospect assessment:**
1. Filter prospects by frontmatter `vertical:` — find similar industry shapes
2. Filter by `outcome:` — find won vs lost vs killed patterns
3. Read `outcome.md` files of 2-3 most similar prospects for generalizable learnings
4. Read `architecture.md` files for similar-shape architecture patterns

**For pipeline evolution:**
- Read multiple `debug_learnings.md` files to surface recurring failure patterns
- Read multiple `outcome.md` `generalizable_learnings:` frontmatter tags to surface cross-prospect patterns

**For kill / archive discipline:**
- Read `kill_log.md` files before sourcing to avoid re-surfacing previously-killed candidates
- Read `archive_log.md` files when revisiting archived prospects to remember why they were archived
