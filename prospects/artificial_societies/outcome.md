---
type: prospect_outcome
prospect: artificial_societies
outcome: lost
revenue_generated: null
relationship_status: dormant_warm
post_engagement_signal: warm
generalizable_learnings:
  - primary_source_verification_before_sprint_commit
  - marketing_page_contradicts_assumption_pattern
  - relationship_win_vs_deal_win_distinction
key_failure_mode: unverified_load_bearing_assumption
key_success_factor: null
last_updated: 2026-05-17
---

# Artificial Societies — Outcome

## Result

Deal lost. Relationship retained with CPO Patrick Sharpe (warm contact, dormant). Architecture proposal was killed in the post-demo conversation because it rested on an assumption about AS's product capabilities that AS's own marketing page directly contradicted. The verification was a single primary-source check that would have taken 30 minutes and would have killed the proposal before sprint commit — but it wasn't done because the pipeline didn't have a falsifiable-claim-audit gate at the time.

## Why this outcome

The architecture proposal was built on top of Step 0.5 dossier and Step 1A research that contained the unverified claim. Step 1B red-team didn't catch it because the red-team focused on architectural soundness rather than on falsifiable load-bearing claims. The first time the assumption was tested against AS's primary marketing materials was during the sales conversation itself — at which point the proposal was dead on arrival.

Patrick Sharpe was generous about the gap, the conversation stayed productive, but the deal was structurally unrecoverable.

## Generalizable learnings

### Lesson 1: Primary-source verification before sprint commit

Every load-bearing claim in an architecture proposal must be falsifiable from public sources. The 30-minute verification cost is far cheaper than the sprint hours lost on a proposal that gets killed in the customer conversation. This generalizes to every prospect, every architecture, every pitch. The post-AS pipeline added Step 1F as a dedicated falsifiable-claim audit gate specifically to prevent this failure mode.

### Lesson 2: Marketing-page contradicts assumption is a recurring shape

The specific pattern — a vendor's own marketing page directly contradicting an architectural assumption — recurs across multiple prospect types. The reason: marketing pages emphasize capability breadth ("we do everything in category X") while architectural proposals tend to assume capability narrowness ("they need help with adjacent thing Y"). The contradiction is often visible on the homepage. Read the homepage as adversarially as you read primary intel.

### Lesson 3: Relationship win vs deal win distinction

Deal loss does not equal relationship loss. Patrick Sharpe remains a warm contact who can be re-engaged when Kaide Labs has a stronger track record (2-3 logos signed) or when AS's situation changes (new vertical, new procurement bottleneck). The post-loss conduct — owning the gap honestly, not over-explaining, keeping the relationship clean — preserved the optionality. **Track relationship status separately from deal status in `outcome.md` frontmatter.**

## What was specific to this prospect

AS's specific marketing page wording, AS's specific product category. Don't generalize the *substance* of the assumption that failed; generalize the *pattern* of "load-bearing assumption was unverified."

## Cross-prospect tags applied

- `primary_source_verification_before_sprint_commit` → must be applied to every future prospect's Step 1F gate
- `marketing_page_contradicts_assumption_pattern` → check during Step 1B red-team for every prospect
- `relationship_win_vs_deal_win_distinction` → relevant for outcome.md frontmatter discipline across all lost deals

## What I'd do differently

Run Step 1F before Step 2 commit. Audit every load-bearing claim in the architecture proposal against the customer's own public materials. Make this a non-negotiable gate.
