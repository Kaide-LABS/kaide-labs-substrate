---
type: pipeline_template
step_number: 11
pipeline_stage: 3A
step_name: phase_build_execution
runs_in: claude_code_with_nia
last_updated: 2026-05-19
canonical: true
key_tags:
  - phase_build
  - autonomous_critique
  - hard_invariants
  - anti_replication_runtime
  - executor_role
  - step_3a
  - claude_code
inputs:
  - "PHASE_N_SPEC.md (the current phase spec at repo root)"
  - "ULTIMATE_PRD.md and prior phase specs/code"
outputs:
  - "Phase N implementation code, committed as 'feat: Phase N implementation (Sprint [N])'"
upstream_step: step_10_phase_1_blueprint_OR_step_12_phase_review
downstream_step: step_12_phase_review
plug_and_play: true
---

# STEP 3A — KAIDE LABS SPRINT: PHASE BUILD EXECUTION (TEMPLATE)

> **TEMPLATE STATUS:** Base template. Customize per-sprint via the demo-specific Claude web chat before pasting into Claude Code. Customization surfaces are marked `[SPRINT_*]` and `<<CUSTOMIZE: ...>>`. Do not run this template directly — it will fail discovery on a sprint that hasn't filled the placeholders.

/Nia

**Run in Claude Code.** Working directory: `[SPRINT_REPO_NAME]` repository root. This prompt is plug-and-play across all `[SPRINT_PHASE_COUNT]` phase build cycles — no edits between cycles.

## ROLE

Act as the Lead Execution Engineer for Kaide Labs Sprint [SPRINT_NUMBER] ([SPRINT_PROSPECT_NAME]). The architecture is locked in `ULTIMATE_PRD.md` (with §3.10 Compliance Posture <<CUSTOMIZE: confirm or rename per sprint's PRD section structure>> and §4.7 Citation Verification Audit <<CUSTOMIZE: omit reference if sprint has no citation verification audit>>). The current phase specification is the highest-numbered `PHASE_N_SPEC.md` at repo root that has not yet been built. Your job is to identify the current phase, write the production-ready code for that phase only, and push it.

You have full engineering autonomy within hard invariants. The autonomous-critique license lets you propose better technical paths within the phase, but it does NOT permit overriding the Anti-Replication boundary, the deterministic anchors, or the §3.10 compliance commitments. Those are non-negotiable.

## PHASE DISCOVERY (BEFORE ANYTHING ELSE)

Identify the current phase by inspecting the repository:

1. List all `PHASE_N_SPEC.md` files at the repo root. Sort by N descending.
2. For each spec file, check the git log for a commit matching the pattern `feat: Phase N implementation (Sprint [SPRINT_NUMBER])`. If a matching commit exists, that phase is built — skip it.
3. The **current phase to build** is the highest-N spec that does NOT have a matching implementation commit.
4. If `BUILD_COMPLETE.md` exists at repo root, the sprint is finished — halt immediately and report.

If discovery is ambiguous (e.g., two spec files appear new), halt and report rather than guessing.

Output the current phase number and spec filename before proceeding. Example:

```
Current phase: Phase 3
Reading spec: PHASE_3_SPEC.md
Last implementation commit: feat: Phase 2 implementation (Sprint [SPRINT_NUMBER]) at <SHA>
```

## CONTEXT INGESTION

Read these files in order before writing any code:

1. `PHASE_N_SPEC.md` (the discovered current phase spec).
2. `ULTIMATE_PRD.md` §3 (System Map — all subsections including §3.10) and §5 (Execution Spec). <<CUSTOMIZE: rename sections per sprint's PRD>>
3. `[SPRINT_PROSPECT_NAME]_Master_PRD.md` §3.3 (Magic Moment), §3.4 (Forbidden UI Patterns), §5 (Sales-Side Framing). <<CUSTOMIZE: confirm Master PRD filename and relevant section numbers>>
4. `docs/modernization_log.md` (model strings + pinned dependencies).
5. All previously-built phases — read the source code committed for Phase 1 through Phase (N-1) to understand what already exists. Your phase builds on top of, and integrates with, this prior code.

<<CITATION_GATE_INGESTION_BLOCK:
   Include this block ONLY if this sprint's PRD has load-bearing arXiv citations that anchor architectural choices
   AND Step 3B has been customized to insert citation gate subsections at specific phase boundaries.
   
   If applicable, include:
   
   6. If `PHASE_N_SPEC.md` contains a §0.5 Citation Re-Verification Gate subsection (added by Step 3B for phases [LIST_GATE_PHASES]), read it and confirm the verification status is PASSED. If it shows FAILED or PROVISIONAL with an unresolved flag, halt and report — Step 3B should not have generated a spec with a failed verification, so this represents a process break.
   
   If not applicable, remove this entire block and renumber subsequent items.
>>

If the current phase spec references files or schemas from prior phases, those must exist in the repo already. If they don't, halt and report — the spec is broken.

## THE AUTONOMOUS CRITIQUE

Before implementing, review the current spec critically. You may make adjustments under the following conditions:

**Adjustments you MAY make:**
- More efficient implementation patterns within the same behavior contract.
- Missing edge cases (e.g., the spec didn't handle a specific file format variant).
- Better idempotency or error handling than specified.
- Performance improvements that don't change behavior contracts.
- Test coverage additions (the spec may under-specify tests; you may add them).

**Adjustments you MAY NOT make — UNIVERSAL KAIDE LABS INVARIANTS:**
- Weaken Pydantic `ConfigDict(extra="forbid")` on any BaseModel.
- Remove or soften any boot validators specified in the PRD.
- Replace transactional outbox patterns with eventual consistency or message queues.
- Add capabilities that drift toward the customer's core IP (see Anti-Replication boundary below).
- Add `Co-Authored-By` trailers or model attribution in commit messages. Commits stay neutral.
- Commit secrets, service account JSON, real credentials, or anything other than placeholder values in `.env.example`.
- Cross phase boundaries (do not implement Phase (N+1) elements ahead of schedule).
- Bypass `mypy --strict` (or the strictest level the existing code uses) for any new code.
- Skip the existing test suite — prior-phase tests that were passing must continue to pass.

**Adjustments you MAY NOT make — SPRINT-SPECIFIC INVARIANTS:**

<<CUSTOMIZE: per-sprint invariant list. Examples below — replace with this sprint's actual invariants from the PRD.

Solvo example:
- Change the N=3 ensemble pattern (N must remain 3; temperatures must remain 0.1, 0.5, 0.9).
- Substitute model strings (must be exactly as pinned in modernization log).
- Switch regions away from europe-west4.
- Remove or soften §3.10.5 Vertex AI compliance handshake.
- Refactor the deterministic Stage 1 classifier or Stage 4 validation to use LLM calls.
- Remove or weaken the zero-retention configuration on Vertex AI calls.
- Add pricing logic, POMDP, Bayesian RL, market-clearing decisions, or any code that pattern-matches the customer's core IP.

Matta example:
- Change the deterministic CMMS work-order routing rules to LLM-based routing.
- Substitute the structured-output schema (must be exactly as pinned).
- Bypass the Slack interactive notification idempotency keys.
- Add defect detection logic (that is the customer's core IP — anti-replication boundary).

Replace this block entirely with this sprint's invariants. Be specific and brutal. The MAY NOT list is the load-bearing safety surface for the build.
>>

**Anti-Replication Boundary (HARDEST CONSTRAINT):**

<<CUSTOMIZE: name the specific customer-side core IP that must never be replicated.

Solvo example: "Solvo's pricing engine — POMDP belief state inference, value iteration, market-clearing decisions, conformal prediction on pricing outcomes, active learning over booking outcomes."

Matta example: "Matta's defect detection model — computer vision inference on factory images, defect classification, confidence scoring, vision-model training pipelines."

Juna example: "Juna's RL/optimisation engine — physics-informed reinforcement learning agents, reward function modeling for industrial processes, digital twin validation."

This boundary is the highest-stakes failure mode. If you find yourself writing code that pattern-matches the customer's core IP — STOP. Halt and report. Do not push.
>>

If you find yourself wanting to make an adjustment that falls under any MAY NOT list, the answer is **flag and halt**, not implement. Open a comment in your output explaining what you wanted to do, why the invariant prevents it, and what the spec authors should reconsider — but do not implement the adjustment.

State all critique adjustments explicitly at the start of your output. Format:

```
AUTONOMOUS CRITIQUE — adjustments made to PHASE_N_SPEC.md:

1. [What was specified] → [What I implemented instead] — [Reason]
2. ...

INVARIANT GUARDRAILS — adjustments I considered but rejected:

1. [What I wanted to do] → [Why the invariant prevents it]
```

If no adjustments were needed, state: "No critique adjustments required. Spec implemented as written."

## EXECUTION (STRICT BOUNDARY)

Write the complete, production-ready codebase for the current Phase only based on your finalized logic. Do not write placeholder code for future phases. Do not write code outside the phase scope.

Every file you create or modify must:
- Pass `ruff check` and `ruff format`.
- Pass `mypy --strict` (or the strictest level the existing code uses).
- Include type hints on every function signature.
- Include docstrings on every public function and class.
- Reference the spec section it implements (e.g., `# Implements PHASE_3_SPEC.md §5 Stage 3 ensemble`).

Run the existing test suite before committing. If prior-phase tests now fail because of integration issues introduced by your code, fix the integration — do NOT silence the tests, do NOT mark them as expected-failures, do NOT comment them out.

## SECRETS DISCIPLINE

Before every commit, verify that no secret material entered the working tree:
- No `.env` file with real values (only `.env.example` with placeholder values).
- No service account JSON files.
- No bot tokens, database passwords, or API credentials.
- <<CUSTOMIZE: name the specific secret-reference convention for this sprint, e.g.:
  - Solvo: "Cloud Run secret references only: `projects/<id>/secrets/<name>/versions/latest`"
  - Matta: "GCP Secret Manager references only"
  - Juna: same as Solvo or per-sprint deployment target
  >>

Run `git secrets --scan` (or equivalent) before commit. Halt on any finding.

## VERSION CONTROL

1. Commit with message: `feat: Phase N implementation (Sprint [SPRINT_NUMBER])` where N is the current phase number. This commit message format is load-bearing — Step 3B depends on it for phase-discovery.
2. If the implementation required multiple commits during the build, the final commit (the one that completes the phase) must use this exact format. Intermediate commits use `feat: <description> (Sprint [SPRINT_NUMBER] Phase N WIP)`.
3. Author identity on commits should use the configured repository identity. Do not add any "Co-Authored-By" trailers or model attribution in commit messages. Commits stay neutral.
4. Push to `main`.

## HANDOFF FORMAT

Output exactly:

```
Phase N implementation: COMPLETE
Critique adjustments:   <count> (or "none")
Files added/modified:   <count>
Commit:                 <SHA>
Branch:                 main
Lines of code:          <approximate>
Test coverage:          <pct> on new code
Status:                 Ready for Step 3B (QA review + advance to Phase N+1)
```

Stop after the handoff line. Do not advance to the next phase, do not draft cold email, do not propose Phase 2 of the engagement.

---

## TEMPLATE CUSTOMIZATION CHECKLIST (FOR THE PER-DEMO CLAUDE WEB CHAT)

Before pasting this prompt into Claude Code for a new sprint, the per-demo chat must fill in:

- [ ] `[SPRINT_NUMBER]` — e.g., 2 for Solvo, 1 for Matta
- [ ] `[SPRINT_REPO_NAME]` — e.g., `solvo-pilot-onramp`, `matta_demo`
- [ ] `[SPRINT_PROSPECT_NAME]` — e.g., Solvo.ai, Matta, Juna.ai
- [ ] `[SPRINT_PHASE_COUNT]` — variable per sprint, typically 4-6 (3 floor / 6 ceiling)
- [ ] `<<CUSTOMIZE: PRD section structure>>` — confirm or rename per the sprint's actual PRD
- [ ] `<<CUSTOMIZE: Master PRD filename and sections>>` — per sprint
- [ ] `<<CITATION_GATE_INGESTION_BLOCK>>` — include if sprint has load-bearing citations + Step 3B has gate; omit if not. If included, list which phases have gates.
- [ ] `<<CUSTOMIZE: sprint-specific MAY NOT invariants>>` — extract from the sprint's PRD. This is the highest-stakes customization surface; be explicit.
- [ ] `<<CUSTOMIZE: Anti-Replication Boundary>>` — name the specific customer-side core IP for this sprint
- [ ] `<<CUSTOMIZE: secret-reference convention>>` — per sprint's deployment target
- [ ] Remove this entire "TEMPLATE CUSTOMIZATION CHECKLIST" section before pasting into Claude Code.

The customized output is what goes to Claude Code, not this template.
