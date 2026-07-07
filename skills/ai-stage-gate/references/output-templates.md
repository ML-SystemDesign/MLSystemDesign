# Output Template

One template for every gate review, in two parts:

1. **Gate Scorecard** — approximately one screenshot-friendly page carrying the decision. Every review produces it.
2. **Comments** — findings, gaps, and the path to a clean Go, after the scorecard. A quick pass may stop at the scorecard.

The decision is the product. Keep findings concrete and ordered by whether they block the gate.

## The Decision

The gate decision is **read off the stage's transition criteria** (`stage-gates.md`) via the logic in `gate-decisions.md`; it is never chosen independently of the readiness table:

- All stage-critical deliverables and criteria **Met** on both value and technical axes → 🟢 **Go**.
- Core value present but one or more closable gaps (Partial / Not met / Unknown on a stage-critical item) → 🟡 **Conditional** (name the variant and conditions).
- A fatal or unclosable gap — unconfirmed problem/value, absent data, insurmountable technical/ethical risk, economics that never close → 🔴 **Kill**.

Show which blocker(s) drove a 🟡 or 🔴 next to the decision. The decision summarizes the readiness table; it does not soften it.

## Part 1: Gate Scorecard

A self-contained markdown block the team can screenshot or paste into a gate deck, Slack, or a wiki. Fill every field; keep the attribution line at the top intact and unmodified — small text, never a header.

Also save the filled scorecard as a standalone markdown file so the team can share it beyond the chat: default to `stage-gate-<product-slug>-gate<N>.md` in the working directory (repo root when reviewing a repo). Do not commit it, and name the saved path at the end of the review. Skip the file only when no writable filesystem is available or the user asks not to.

```markdown
*Reviewed with [ai-stage-gate](https://github.com/ML-SystemDesign/MLSystemDesign/tree/main/skills)*

## AI Stage-Gate Decision: <product name> — Gate <N> (<stage name>)

**Decision:** <🟢 Go | 🟡 Conditional (<variant>) | 🔴 Kill>
**Stage / track:** <stage> · <Discovery | Delivery | Fast Track> · market stage <TAM | SAM | SOM>
**Driven by:** <the blocker(s) behind a 🟡/🔴, or "all stage-critical criteria met" for a 🟢>

| Deliverable / criterion | Status | Note |
|---|---|---|
| <one row per required deliverable and stage-critical criterion for this gate> | <Met | Partial | Not met | Unknown> | <short evidence or gap> |

**Top blocker:** <the single gap that most holds back a clean Go, or "none" for a 🟢>
**Conditions to advance:** <for 🟡: the concrete conditions and owners that close the gaps; for 🟢: "none — proceed"; for 🔴: why no realistic path>
**Recommended next-stage investment:** <the next stage's scope of spend only, scaled to remaining risk>
```

## Part 2: Comments

Follows the scorecard in the report (not included in the saved scorecard file). Skip sections with nothing to say rather than padding them.

```text
Evidence reviewed: <gate deck, research, metrics, telemetry, docs; note what was unavailable>
Stage inference: <stage/gate and how it was determined; state explicitly if inferred in an unattended run>

Blocking findings (must close before a Go)
- <deliverable/criterion> - <evidence or gap>. <why it blocks the gate>. Condition to clear: <concrete result + owner>.

Gaps to strengthen (do not block, but weaken the case)
- <criterion> - <gap>. Fix: <concrete action>.

Strengths to carry forward
- <specific proven result or good decision worth reusing at the next stage>.

Questions that change the decision
- <question whose answer flips Met/Not-met on a stage-critical item>, recommended default: <answer>.

Path to a clean Go
1. <first condition and why it is first>
2. <second condition>
3. <third condition>
```

When the audience is the defending team rather than the gate committee, keep the same two parts but open the comments with strengths to carry forward, phrase blockers as conditions to close before the gate, and state what not to over-invest in yet given the incremental-investment principle.
