# Output Template

One template for every gate review, in two parts:

1. **Gate Scorecard** — approximately one screenshot-friendly page. For a **decision review** it carries the traffic-light decision; for a **prep-only** review it carries the readiness state and path to a clean Go but **no** traffic-light call (see the prep-only scorecard below). Every review produces one or the other.
2. **Comments** — findings, gaps, and the path to a clean Go, after the scorecard. A quick pass may stop at the scorecard.

For a decision review the decision is the product. For a prep-only review the readiness map and the path to Go are the product — do **not** issue a Go/Conditional/Kill call the user did not ask for. Keep findings concrete and ordered by whether they block the gate.

## The Decision

The gate decision is **computed** from the readiness table via the deterministic procedure in `gate-decisions.md` (veto logic, not an average); it is never chosen independently of the table. In short:

- Every required **deliverable** present, and all **stage-critical criteria** Met on both value and technical axes → 🟢 **Go** (non-critical Partials become noted gaps, not blockers).
- Any required deliverable **Not met / Unknown**, or any stage-critical criterion **Partial / Not met / Unknown**, but closable → 🟡 **Conditional** (name the variant and conditions). Unknown never counts as Met; a missing deliverable blocks a 🟢 even if the criteria it supports look Met.
- A fatal or unclosable gap — unconfirmed problem/value, absent data, insurmountable technical/ethical risk, economics that never close → 🔴 **Kill**.
- Gate 6 has no base veto set; use its special case in `gate-decisions.md`.

The readiness table lists **every required deliverable and transition criterion** for the gate — not only the critical ones — because non-critical Not-met/Unknown items can still downgrade the light (they turn a 🟢 into a 🟢-with-noted-gaps, or several together into a 🟡), and the saved scorecard must show all the state a reader needs to audit the decision. Flag the stage-critical rows with `★` in the Critical? column so the reader can see which items hold veto power. Show which blocker(s) drove a 🟡 or 🔴 next to the decision. The decision summarizes the readiness table; it does not soften it.

## Part 1: Gate Scorecard

A self-contained markdown block the team can screenshot or paste into a gate deck, Slack, or a wiki. Fill every field; keep the attribution line at the top intact and unmodified — small text, never a header.

Also save the filled scorecard as a standalone markdown file so the team can share it beyond the chat: default to `stage-gate-<product-slug>-gate<N>.md` in the working directory (repo root when reviewing a repo). Do not commit it, and name the saved path at the end of the review. Skip the file only when no writable filesystem is available or the user asks not to.

```markdown
*Reviewed with [ai-stage-gate](https://github.com/ML-SystemDesign/MLSystemDesign/tree/main/skills)*

## AI Stage-Gate Decision: <product name> — Gate <N> (<stage name>)

**Decision:** <🟢 Go / 🟡 Conditional (variant) / 🔴 Kill>
**Stage / track:** <stage> · <Discovery / Delivery / Fast Track> · market stage <TAM / SAM / SOM>
**Evidence mode:** <gate-deck + repo / gate-deck only / repo-telemetry only> <+ modifiers: unattended / portfolio if any> — <what the decision rested on: deck-reported metrics, repo, telemetry, or inference; note any unverified claims>
**Driven by:** <the blocker(s) behind a 🟡/🔴, or "all stage-critical criteria met" for a 🟢>

| Deliverable / criterion | Critical? | Status | Note |
|---|---|---|---|
| <one row per required deliverable AND every transition criterion for this gate> | <★ if stage-critical, else blank> | <Met / Partial / Not met / Unknown> | <short evidence or gap> |

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

## Modern-AI overlay row

When the product uses an LLM, RAG, a foundation model, fine-tuning, agents, tool use, memory, or autonomous side effects, add one overlay row to the readiness table and grade it per `modern-ai-overlay.md`. Mark it stage-critical (★) when the system can act autonomously/externally, touches regulated data, or answers users without a human in the loop.

```markdown
| Modern-AI readiness (RAG grounding / tool permissions / evals / traces) | <★ when it can act autonomously/externally, touches regulated data, or answers users unsupervised> | <Met / Partial / Not met / Unknown> | <what is proven vs. missing for this stage> |
```

## Fast Track gate template

For a Fast Track review, use the same scorecard but with FT-Gate numbering and the merged deliverable/criteria set from `fast-track.md`:

```markdown
## AI Stage-Gate Decision: <product name> — FT-Gate <N> (<Ideation | Discovery | Delivery | Scale>)

**Decision:** <🟢 Go | 🟡 Conditional (<variant>) | 🔴 Kill>
**Path / market stage:** Fast Track (collapsed 4-stage) · market stage <TAM | SAM | SOM | penetration>
**Merged from:** <standard stages this FT gate consolidates>
```

The readiness table uses the same four columns as the standard template and lists the union of the merged stages' deliverables and transition criteria, flagging the union of their stage-critical items with `★`. State in the report that this is a Fast Track review so a reader never confuses FT-Gate 2 with standard Gate 2.

## Portfolio matrix template

For a portfolio triage (several products/ideas judged together), do not write a deep single-gate report per product. Produce one matrix — a row per product — plus a short ranked recommendation. Save as `stage-gate-portfolio-<date-or-slug>.md`.

```markdown
*Reviewed with [ai-stage-gate](https://github.com/ML-SystemDesign/MLSystemDesign/tree/main/skills)*

## AI Stage-Gate Portfolio Triage: <portfolio / batch name>

| Product | Stage / gate | Decision | Top blocker | Next-stage ask |
|---|---|---|---|---|
| <name> | <Gate N or FT-Gate N> | <🟢 / 🟡 (variant) / 🔴> | <one blocker> | <scoped next-stage spend> |

**Prioritization:** <which to fund now, which to recycle/hold, which to kill — applying incremental investment: concentrate resources on the highest-potential, lowest-remaining-risk products>.
**Cross-cutting notes:** <shared risks, reusable components, or knowledge-transfer opportunities across the portfolio>.
```

A portfolio row is a summary, not a full gate defense; when a single product needs a real decision, run the standard template for it.

## Prep-only scorecard (no decision)

When the user is *preparing* a gate defense rather than defending it (review modifier: prep only), produce this readiness scorecard instead of the decision scorecard. It holds **no** traffic-light call — issuing a Go/Conditional/Kill the user did not ask for is the defect this template exists to prevent. Frame it as "here is what your gate committee will test and where you are not yet ready." Offer a dry-run call only if the user asks.

```markdown
*Reviewed with [ai-stage-gate](https://github.com/ML-SystemDesign/MLSystemDesign/tree/main/skills)*

## AI Stage-Gate Readiness (prep): <product name> — Gate <N> (<stage name>)

**Status:** preparing to defend — no gate decision issued
**Stage / track:** <stage> · <Discovery / Delivery / Fast Track> · market stage <TAM / SAM / SOM>
**Evidence mode:** <gate-deck + repo / gate-deck only / repo-telemetry only — what is on hand so far>

| Deliverable / criterion | Critical? | Status | Note |
|---|---|---|---|
| <one row per required deliverable and transition criterion> | <★ if stage-critical> | <Met / Partial / Not met / Unknown> | <what exists vs. what the committee will want> |

**Not yet ready:** <the stage-critical items that are Partial / Not met / Unknown — these would block a Go if defended today>
**Path to a clean Go:** <ordered steps + owners to turn each blocker Met before the defense>
```

If (and only if) the user asks for a dry-run verdict, add the decision scorecard's Decision line and label it clearly as a rehearsal call, not a committee decision.
