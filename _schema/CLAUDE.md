---
type: navigation
scope: schema
last_updated: 2026-05-17
---

# _schema/

Substrate conventions. Read once to understand the system. Not for runtime query — content here describes the structure, doesn't contribute to per-prospect decisions.

## Files

- `frontmatter_conventions.md` — YAML frontmatter fields by file type (prospect, framework, identity, template)
- `file_splitting_conventions.md` — Why topic-per-file beats stage-per-file for indexed retrieval
- `prospect_folder_template/` — Empty topic-file scaffolding ready to copy for any new prospect

## When to update

The schema is stable by design. Update when:
- A new file type emerges that needs a frontmatter convention
- Cross-prospect retrieval patterns are failing because the topic structure is wrong
- A new pipeline stage gets added that doesn't fit existing topic files

Do not update casually. Schema drift is the failure mode that breaks substrate compounding.
