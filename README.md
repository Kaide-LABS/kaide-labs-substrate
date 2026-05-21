---
type: navigation
domain: substrate_root
last_updated: 2026-05-19
canonical: true
key_tags:
  - readme
  - substrate_overview
  - kaide_labs_internal
audience: internal_only
---

# kaide-labs-substrate

The internal knowledge substrate for Kaide Labs. Indexed via Nia, queried by Claude Code, compounds across enterprise FDE engagements.

This is **not** a documentation repository. It is operational knowledge that AI agents query to make better per-engagement decisions. Each prospect engagement deposits artifacts; future prospects' architecture, fit assessments, and outreach draw from accumulated context.

## Who uses it

Internal to Kaide Labs:

- **Hafeedh** — Founder, Lead AI Architect. Files artifacts as work happens.
- **Isaac** — GTM and positioning lead.
- **Claude Code + Nia** — Indexes the substrate and retrieves at runtime for judgment tasks (fit assessment, dossier expansion, cross-prospect pattern matching).

Private repository. Not for public distribution.

## Navigation

- Start at [`CLAUDE.md`](CLAUDE.md) for the full directory map and query patterns.
- Conventions live in [`_schema/`](_schema/) — read once to understand the system.
- Per-directory `CLAUDE.md` files give scope-local navigation.

## Substrate principles

- **Canonical-in-one-place.** Content lives once; everywhere else references. The `canonical: true` frontmatter flag marks the authoritative source.
- **YAML frontmatter is mandatory.** Every indexed `.md` file carries structured metadata. Empty frontmatter blocks compounding. See [`_schema/frontmatter_conventions.md`](_schema/frontmatter_conventions.md).
- **Topic-per-file** within prospect folders (overview, intel, architecture, build, outcome) — not stage-per-file. See [`_schema/file_splitting_conventions.md`](_schema/file_splitting_conventions.md).
- **Files starting with `_`** (e.g., `_schema/`, `_archive/`) are meta-folders, not engagement content. They sort to the top alphabetically by design.
- **Tag deliberately.** `key_tags:` and `generalizable_learnings:` arrays are how Nia surfaces cross-prospect patterns.

## How to add new content

**New prospect engagement:** copy [`_schema/prospect_folder_template/`](_schema/prospect_folder_template/) to `prospects/[name]/`, fill frontmatter, populate topic files as the pipeline advances.

**New architectural pattern (post-engagement):** follow the 9-section structure in any existing [`frameworks/architectural_patterns/*.md`](frameworks/architectural_patterns/) file. Update the patterns table in that folder's `CLAUDE.md`.

**New pipeline template:** add to [`pipeline/templates/`](pipeline/templates/) with `pipeline_template` frontmatter. Update [`pipeline/CLAUDE.md`](pipeline/CLAUDE.md).

**Killed or archived prospect:** file `kill_log.md` or `archive_log.md` only. No full topic structure.

## Operational discipline

File artifacts as work happens. Sprints that don't get filed produce no compounding value. There is no "I'll file it later" — later means it decays.

## Status

V1 populated as of 2026-05-19. Active sprints: Matta, Solvo. Step 0 in progress: Juna.
