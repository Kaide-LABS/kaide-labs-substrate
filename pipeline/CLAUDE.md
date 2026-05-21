---
type: navigation
scope: pipeline
last_updated: 2026-05-17
---

# pipeline/

The post-AS pipeline. Each step has a canonical template prompt that gets customized per prospect at runtime.

## Files

- `pipeline_overview.md` — Step-by-step definition with rationale for each
- `templates/` — Canonical template prompts. **Customize per prospect; do not edit the canonical templates.**

## Pipeline steps (current)

| Step | Name | Model target | Template |
|------|------|--------------|----------|
| 0 | Manual intel gathering | Human operator | (no template — operator process) |
| 0b | Intel cleanup | Gemini Web | `templates/STEP_01_INTEL_CLEANER_TEMPLATE.md` |
| 0 fit | Fit assessment (judgment) | Claude Code + Nia | (no template — judgment process, see `_archive/` for legacy prompt reference) |
| 0.5 | Founder dossier expansion | Gemini DR | `templates/STEP_02_DOSSIER_TEMPLATE.md` |
| 1A | Three-architecture generation | Gemini DR | `templates/STEP_03_TARGET_BRIEF_TEMPLATE.md` |
| 1B | Architecture red-team & pick | Claude Web | `templates/STEP_04_PRD_REDTEAM_TEMPLATE.md` |
| 1F | Falsifiable claim audit | Claude Web | `templates/STEP_08_CLAIMS_AUDIT_TEMPLATE.md` |
| 2 | Foundational build | Claude Code | `templates/STEP_10_PHASE_1_BLUEPRINT_TEMPLATE.md` |
| 3 | Lateral feature exploration | Codex CLI | `templates/STEP_06_LATERAL_EXPLORATION_TEMPLATE.md` |
| 3A | Autonomous build executor | Gemini CLI or Sonnet 4.6 | `templates/STEP_11_PHASE_BUILD_TEMPLATE.md` |
| 3B | QA + phase advance | Claude Code | `templates/STEP_12_PHASE_REVIEW_TEMPLATE.md` |
| 3C | Sprint comprehension | Gemini CLI | `templates/STEP_13_COMPREHENSION_TEMPLATE.md` |
| 4 | Ultimate PRD synthesis | Claude Web | `templates/STEP_05_PRD_MODERNIZATION_TEMPLATE.md`, `templates/STEP_07_ULTIMATE_PRD_TEMPLATE.md` |
| 5 | Falsifiable validation gate | Gemini DR | `templates/STEP_08_CLAIMS_AUDIT_TEMPLATE.md` (reused) |
| 5b | Validation red-team | Claude Web | `templates/STEP_09_VALIDATION_REDTEAM_TEMPLATE.md` |
| 5C / 6 | Call-prep + asset production | Mixed | `templates/STEP_14_CALL_BRIEF_TEMPLATE.md` |

## When to query

- Running a stage: read the template, customize, execute
- Adding a new stage: update both `pipeline_overview.md` and this CLAUDE.md
- Deprecating a template: mark `status: deprecated` in template frontmatter, do not delete (preserves audit trail)

## Template versioning

Templates evolve. When a template is materially revised, the old version is preserved with `status: deprecated` and `supersedes:` updated on the new one.
