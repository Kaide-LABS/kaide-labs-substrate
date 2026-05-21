---
type: prospect_debug
prospect: [PROSPECT_NAME]
debug_sessions: [N]
hours_in_debug: [estimated total hours]
generalizable_lessons:
  - [lesson1]
last_updated: [YYYY-MM-DD]
---

# [PROSPECT_NAME] — Debug Learnings

## What broke

[Each significant issue + symptoms]

## How it was diagnosed

[Diagnostic approach that worked — bisection, log reads, MCP queries, etc.]

## How it was fixed

[The actual fix — with code references if applicable]

## What generalizes

[Patterns that will recur in future builds — these go to substrate-level learnings]

## What does NOT generalize

[Prospect-specific quirks — note them so future debugging doesn't waste cycles applying them where they don't fit]
