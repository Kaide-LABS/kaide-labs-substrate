---
type: schema_doc
domain: file_splitting
last_updated: 2026-05-17
canonical: true
---

# File Splitting Conventions

Within prospect folders, files split by **topic**, not by **pipeline stage**.

## Why topic-per-file beats stage-per-file

**Stage-per-file** (`step_0.md`, `step_1a.md`, `step_2.md`) has two failure modes:
1. The pipeline evolves (we already added Step 0b and Step 1F post-AS). Stage-numbering becomes obsolete and requires file renames.
2. Semantic retrieval is shape-blind. Claude Code querying "show me Solvo's architecture decisions" finds it harder when architecture content is split across `step_0.5.md`, `step_1a.md`, and `step_1b.md`.

**Topic-per-file** is stable across pipeline evolution and aligns with how the AI consumer actually queries.

## Canonical topic files per prospect

For active or completed prospects:

| File | Contents |
|------|----------|
| `overview.md` | Frontmatter-heavy. 1-paragraph summary + cross-references to other files. Always the first file Claude Code reads for a prospect. |
| `intel.md` | Step 0 raw intel + Step 0b cleanup. Founder profiles, team scrapes, company-page activity, press coverage. |
| `fit_assessment.md` | Step 0 judgment-mode verdict (Job A). GO / CONDITIONAL GO / NO-GO with reasoning. |
| `architecture.md` | Step 0.5 dossier expansion + Step 1A three-architecture output + Step 1B red-team + winning architecture pick. |
| `build.md` | Step 2 PRD + Step 3 phase specs + Step 3A/3B build artifacts + Step 3C comprehension doc. |
| `outreach.md` | Cold email drafts + sent versions + response data + follow-up logic. |
| `debug_learnings.md` | What broke during build or debug + how it was fixed + what generalized. |
| `outcome.md` | Final result. Won / lost / stalled. `generalizable_learnings:` frontmatter is load-bearing. |

For killed prospects (hard filter fired before any sprint commit):

| File | Contents |
|------|----------|
| `kill_log.md` | Why considered, filter that fired, generalizable learnings. |

For archived prospects (didn't fit current motion but revisitable):

| File | Contents |
|------|----------|
| `archive_log.md` | Why considered, why archived, conditions under which revisit would make sense. |

## CLAUDE.md per prospect

Every prospect folder gets its own `CLAUDE.md`:
- 1-2 paragraphs summarizing what's in the folder
- Frontmatter status block (active / killed / archived / won / lost)
- When to query this prospect for cross-prospect pattern matching

## How big should files be?

Smaller and topically coherent beats larger and mixed. Target:
- `overview.md`: 200-500 words
- Topic files: 500-3000 words each
- `outcome.md`: 800-2000 words (rich generalizable_learnings section)
- `kill_log.md` / `archive_log.md`: 200-500 words

Files larger than ~5000 words should be split further by sub-topic. Nia chunks semantically — large files produce noisy retrieval.

## What goes where: edge cases

- Pre-sale conversation logs from founder calls → `outreach.md`
- Post-mortem after a lost deal → `outcome.md` with `failure_mode:` frontmatter
- Pivoted architecture mid-sprint → both versions in `architecture.md`, with the pivot reasoning explained
- Verification gates that surfaced during build → `debug_learnings.md`
- Cross-prospect strategic decisions (model stack, pipeline evolution) → NOT in prospect folders. Goes in `frameworks/` or `_schema/`.
