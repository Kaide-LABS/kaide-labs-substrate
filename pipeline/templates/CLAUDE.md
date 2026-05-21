---
type: navigation
scope: pipeline_templates
last_updated: 2026-05-17
---

# pipeline/templates/

Canonical template prompts for each pipeline stage. Customize per prospect at runtime; do not edit canonical templates without versioning.

## Pending migrations from /mnt/user-data/outputs/

These template prompts already exist as outputs from prior sessions and should be migrated here:

- `Juna_assessment_claude_code_prompt.md` → `step_0_fit_assessment_claude_code.md`
- `Juna_fit_assessment_prompt.md` → `step_0_fit_assessment_gemini_web.md` (deprecated alternative)
- `Solvo_Step_1A_migration_prompt.md` → `step_1A_architecture_generation.md` (with Solvo-specific content templatized)
- `Solvo_step_0.5_prompt.md` → `step_0.5_dossier_expansion.md`
- `Step_3C_comprehension_prompt.md` → `step_3C_comprehension.md`
- `Step_3C_build_reconciliation_prompt.md` → `step_3C_build_reconciliation.md`
- `Prometheux_fit_assessment_prompt.md` → kept as a reference for kill-verdict examples

Migrate each by:
1. Stripping prospect-specific content, replacing with `[PROSPECT_NAME]` placeholders
2. Adding `pipeline_template` frontmatter with stage + model_target + status
3. Filing here

After migration, the canonical templates here become the source of truth. Per-prospect customizations happen at runtime and stay in the prospect folder.
