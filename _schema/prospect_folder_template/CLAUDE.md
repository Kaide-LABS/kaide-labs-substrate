---
type: navigation
scope: prospect_template
last_updated: 2026-05-17
---

# Prospect Folder Template

This is a template. To use it: copy this entire folder into `prospects/[prospect_name]/` and fill in the frontmatter and content as work progresses.

For active prospects, all 8 topic files exist as stubs and get populated as the pipeline advances. For killed prospects, delete everything except `CLAUDE.md` and create `kill_log.md` instead. For archived prospects, delete everything except `CLAUDE.md` and create `archive_log.md`.

Topic files (in order they typically get populated):
1. `overview.md` — frontmatter-heavy summary, written/updated at every pipeline stage
2. `intel.md` — Step 0 + Step 0b
3. `fit_assessment.md` — Step 0 verdict
4. `architecture.md` — Step 0.5 + Step 1A + Step 1B
5. `build.md` — Step 2 + Step 3 + Step 3C
6. `outreach.md` — cold email work
7. `debug_learnings.md` — what broke and how it was fixed
8. `outcome.md` — final result + generalizable_learnings
