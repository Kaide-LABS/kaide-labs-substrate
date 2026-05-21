---
type: schema_doc
domain: frontmatter
last_updated: 2026-05-17
canonical: true
---

# Frontmatter Conventions

All substrate files use YAML frontmatter for metadata. Frontmatter is load-bearing — it enables filtered semantic queries by Claude Code and Nia.

## Universal fields (every file)

```yaml
---
type: [file_type]  # see type taxonomy below
last_updated: YYYY-MM-DD
---
```

## Type taxonomy

- `prospect_overview` — prospect-level summary file (`overview.md`)
- `prospect_intel` — raw intel from Step 0
- `prospect_fit_assessment` — Step 0 judgment-mode verdict
- `prospect_architecture` — Step 0.5 dossier + Step 1A/1B architecture
- `prospect_build` — Step 2/3 build artifacts
- `prospect_outreach` — cold email drafts + responses
- `prospect_debug` — debug learnings from sprint
- `prospect_outcome` — final result + generalizable learnings
- `prospect_kill_log` — kill record for hard-filter-failed prospects
- `prospect_archive_log` — archive record for unpursued prospects
- `identity` — canonical Kaide Labs identity content
- `framework` — operational framework (sourcing, whitelist, etc.)
- `pipeline_overview` — pipeline-level documentation
- `pipeline_template` — canonical template prompt for a pipeline stage
- `schema_doc` — substrate conventions (this file)
- `navigation` — CLAUDE.md navigation files

## Prospect file frontmatter (full schema)

For active or completed prospects, the `overview.md` carries the full frontmatter:

```yaml
---
type: prospect_overview
prospect: solvo
vertical: logistics_ai
geography: london_uk
stage_at_engagement: late_seed_aging_into_series_a
funding_state: "$4M Seed Nov 2022, no Series A"
employee_count_at_engagement: 8
outcome: active  # active | won | lost | killed | archived
sprint_status: step_1a_running  # step_0 | step_0.5 | step_1a | step_1b | step_2_build | step_3_qa | demo_recorded | outreach_sent | response_received | closed | abandoned
killed_reason: null  # only if outcome == killed
archived_reason: null  # only if outcome == archived
key_tags:
  - commercial_function_collapse
  - bajaj_posting_silence
  - founding_gtm_not_yet_hired
generalizable_learnings:
  - window_fit_acuity_via_commercial_departures
  - manual_intel_verification_beats_aggregator_data
related_prospects:
  - juna  # adjacent vertical/shape
  - matta  # similar low-frontier-lab-exposure structure
created: 2026-04-XX
last_updated: 2026-05-17
---
```

## Kill log frontmatter

```yaml
---
type: prospect_kill_log
prospect: prometheux
vertical: financial_services_ai
geography: italy
killed_at_stage: step_0  # at what pipeline stage was the kill
filter_fired: riba  # which hard filter triggered the kill
secondary_filter: anti_replication  # if multiple filters fired
killed_at: 2026-05-XX
generalizable_learnings:
  - financial_services_kill_at_sourcing_not_dossier
  - vadalog_engine_absorbs_dmz_pattern
---
```

## Archive log frontmatter

```yaml
---
type: prospect_archive_log
prospect: tortus
vertical: clinical_ai
geography: london_uk
archived_at_stage: post_sourcing  # where in pipeline was it archived
archive_reason: vertical_depth_mismatch  # short tag for why
revisitable: true  # could be revisited if conditions change
archived_at: 2026-05-XX
generalizable_learnings:
  - deep_vertical_specialists_require_matching_vertical_depth
---
```

## Framework / identity frontmatter

```yaml
---
type: framework
domain: prospect_sourcing  # what operational area
last_updated: 2026-05-XX
canonical: true
supersedes: null  # filename of prior version, if this replaces one
---
```

## Pipeline template frontmatter

```yaml
---
type: pipeline_template
stage: step_0_fit_assessment  # canonical stage name
model_target: claude_code_nia  # gemini_web | gemini_dr | claude_code_nia | claude_web | sonnet_debug
status: active  # active | deprecated | experimental
last_used_on: matta
last_updated: 2026-05-XX
---
```

## Why this matters

Claude Code can query the substrate filtered by frontmatter:
- "List all prospects with `vertical: logistics_ai`"
- "List all `outcome: killed` prospects with `filter_fired: riba`"
- "Find all `generalizable_learnings:` tagged `window_fit_acuity_via_commercial_departures`"

Without disciplined frontmatter, these queries fail and the substrate becomes a flat document pile.

## Discipline

Fill frontmatter when filing artifacts. Do not file content without frontmatter. Empty frontmatter blocks compounding.

When `generalizable_learnings:` are added to an outcome.md, the same tag should appear on the `related_prospects:` they generalize to. This bidirectional tagging is how cross-prospect retrieval surfaces patterns.
