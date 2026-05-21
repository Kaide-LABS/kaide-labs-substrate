---
type: navigation
scope: substrate_root
last_updated: 2026-05-17
---

# kaide-labs-substrate

The Kaide Labs knowledge substrate. Indexed via Nia. Queried by Claude Code. Compounds across engagements.

This is **not** a documentation repository. It is operational knowledge that AI agents query to make better decisions per engagement.

## Directory map

- `identity/` — Canonical Kaide Labs identity, pricing, positioning. Stable. Query when grounding prospect assessments or outreach in canonical company shape.
- `pipeline/` — The post-AS pipeline steps (Step 0 through Step 6) and their template prompts. Query when running any pipeline stage.
- `prospects/` — Per-prospect engagement records. Each subdirectory follows the same topic-per-file structure. Query for similar-prospect patterns when assessing new candidates.
- `frameworks/` — Operational frameworks (sourcing, vertical whitelist). Stage-agnostic decision tools.
- `_schema/` — Conventions for how the substrate is organized. Read once to understand the system; not for runtime query.

## Conventions

- All files use YAML frontmatter for metadata. See `_schema/frontmatter_conventions.md`.
- Files within prospect folders split by topic, not by stage. See `_schema/file_splitting_conventions.md`.
- Each directory has its own CLAUDE.md for navigation.

## Usage patterns

**When evaluating a new prospect:**
1. Read `identity/identity_doc.md` to ground in canonical positioning
2. Read `frameworks/vertical_whitelist.md` for hard-kill check
3. Search `prospects/` by frontmatter `vertical:` for similar industry shapes
4. Read 2-3 nearest-neighbor prospects' `outcome.md` files for generalizable learnings

**When running a pipeline stage:**
1. Read `pipeline/CLAUDE.md` to find the relevant template
2. Read the canonical template from `pipeline/templates/`
3. Customize for the prospect — do not edit the canonical template

**When closing or killing a prospect:**
- Killed (hard filter fired): file `kill_log.md` in `prospects/[name]/`
- Archived (didn't fit, may revisit): file `archive_log.md`
- Lost (engaged, didn't close): full topic-file structure with `outcome.md` capturing failure mode
- Won (closed): full topic-file structure with `outcome.md` capturing what worked

## Operational rules

- Filing artifacts is non-optional. Sprints that don't file artifacts to the substrate produce no compounding value.
- Frontmatter `generalizable_learnings:` tags are load-bearing. They are how Claude Code surfaces cross-prospect patterns. Tag deliberately.
- The `_schema/` folder is canonical. If conventions drift across files, the schema doc wins.
