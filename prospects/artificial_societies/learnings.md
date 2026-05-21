---
type: prospect_debug
prospect: artificial_societies
debug_sessions: 0
hours_in_debug: 0
generalizable_lessons:
  - primary_source_verification_before_sprint_commit
  - marketing_page_contradicts_assumption_pattern
last_updated: 2026-05-17
---

# Artificial Societies — Learnings

Note: AS did not go through a build sprint, so there are no debug-session learnings. The learnings here are about the pipeline failure, not technical debugging. They informed the post-AS pipeline re-engineering.

## Pipeline learnings

### Step 0b was added because of AS
Intel cleanup as a discrete stage. Pre-AS, raw intel was used directly. AS surfaced that aggregator noise + LinkedIn UI chrome was infiltrating Step 0.5 and Step 1A reasoning. Step 0b cleans this up before downstream stages.

### Step 1F was added because of AS
Falsifiable claim audit. The specific failure mode was an unverified load-bearing assumption. Step 1F now requires every load-bearing claim in the architecture proposal to be audited against public primary sources before sprint commit.

### Step 1B red-team scope was expanded
Previously focused on architectural soundness. Now also scopes falsifiable claims — the red-team asks "what would kill this if surfaced in the sales conversation?" not just "is the architecture sound?"

## Why this matters for future prospects

Every new prospect's fit assessment + architecture proposal must pass through Steps 1B and 1F. The cost is ~1 hour total. The benefit is preventing the AS failure mode from recurring.
