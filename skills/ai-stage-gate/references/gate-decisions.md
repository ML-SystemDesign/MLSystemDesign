# Gate Decisions

Every gate resolves to one of three lights. The decision is **read off the stage's transition criteria** in `stage-gates.md`, not chosen by feel. Each light has stage-specific variants that make the decision precise.

## The Traffic Light

- 🟢 **Go** — required deliverables present and transition criteria met on both the value and technical axes. Invest in the next stage.
- 🟡 **Conditional** — core value exists, but specific gaps must be closed first. Always name the conditions and their owners.
- 🔴 **Kill** — the problem/value is unconfirmed, economics or market do not hold, data is absent, or technical/ethical risk is insurmountable with no realistic path.

## Deterministic Decision Procedure

The light is **computed** from the readiness table (`output-templates.md`), not chosen by feel. This is **veto logic, not an average** — a gate is a filter, so one fatal gap sinks it no matter how many items are green. Averaging letter grades (as a gradecard would) is explicitly wrong here: it would let a strong deck average away a missing value or feasibility criterion.

Mark every required deliverable **and** every transition criterion **Met / Partial / Not met / Unknown**, noting which are **stage-critical** (`stage-gates.md` marks criteria with `[critical]`; a **required deliverable is treated as stage-critical by default** — the gate cannot certify a criterion whose supporting deliverable is absent). Then apply, in order:

1. **Any required deliverable Not met or Unknown** → the gate cannot be 🟢. A gate defends deliverables; a missing one is a blocker even if the criteria it supports look Met (those "Met" marks are then unverifiable). Fatal/unobtainable → 🔴; closable → 🟡.
2. **Any stage-critical criterion Not met, and the gap is fatal** (problem/value disproven, data unobtainable, technical/ethical risk with no realistic path, economics that never close) → 🔴 **Kill**.
3. **Any stage-critical criterion Not met or Unknown, but closable** → 🟡 **Conditional**. Unknown never counts as Met: missing evidence on a critical item blocks a 🟢. Pick the variant (below) that matches what must change.
4. **Any stage-critical criterion Partial** → at most 🟡. A critical item that is only partially demonstrated is not a Go.
5. **All required deliverables present and all stage-critical criteria Met**, and non-critical items are Met or only Partial → 🟢 **Go**. Note the Partial non-critical items as gaps to strengthen, but they do not block.
6. **All deliverables and stage-critical criteria Met, but a non-critical criterion is Not met/Unknown** → 🟢 **Go with a noted gap**, unless several such gaps together undermine the stage's intent, in which case drop to 🟡.

Both axes gate independently: a stage-critical **technical** item Not met blocks a 🟢 even if every value item is Met, and vice versa (dual validation). Enthusiasm, effort, and a polished deck do not lift a blocked gate.

## Variants by Gate

| Gate | 🟢 Go | 🟡 Conditional (forms) | 🔴 Kill |
|---|---|---|---|
| 1 Ideation | Strategy, market, and basic AI feasibility confirmed; invest in deep validation | **Pivot / Merge** — value exists but reframe the problem, segment, or approach, or merge with a nearby initiative | No strategic fit, weak market, AI adds no value, or tech/data look unsolvable at the outset |
| 2 Concept Validation | Problem and concept confirmed by research; data and technical contour look buildable; build the prototype | **Conditional Go / Recycle / Pivot** — base value exists but research, concept, data, or technical approach need work before prototyping | Problem unconfirmed, economics/market do not add up, data absent, or technical risks insurmountable |
| 3 Prototype Development | Prototype shows value and clears minimum technical metrics; go to MVP/beta | **Conditional Go / Limited Go / Technical Pivot / Return to Concept** — signal exists but improve metrics, narrow scope, change technical approach, or send part of the work back to concept | Prototype fails on value or technical metrics, or risk/cost is inadequate |
| 4 Beta Testing | MVP stable in the real environment; metrics and feedback are good; prepare full launch | **Conditional Go / Extended Beta / Scope Reduction / Return to Prototype** — value exists but gather more data, stabilize, simplify scope, or bring the prototype/AI to commercial grade | Market does not accept it, production metrics do not hold, or risk/harm outweighs benefit |
| 5 MLP Launch | MLP stable, economics and satisfaction good; scale it | **Optimize First / Pivot / Sustain / Sunset Plan** — product is alive but needs optimization, a strategy change, limited sustaining, or a planned wind-down instead of scaling | Key metrics unmet with no realistic path to improve |
| 6 Scale & Optimize | **Continue Growth / Extend Platform** — long-term value proven; keep growing or reuse as a platform | **Maintain & Optimize / Transfer / Sunset** — useful, but focus on sustaining, transferring to operations, or a planned shutdown | Long-term goals unmet; support does not pay for itself |

## Gate 6 Special Case

Gate 6 is different from Gates 1–5 and the deterministic procedure adapts:

- **There is no next stage to fund**, so the decision is not "advance vs. not." It is a *portfolio judgment*: does this product keep earning its place — keep investing in growth (🟢 Continue Growth / Extend Platform), shift to lower-cost sustaining or hand-off (🟡 Maintain & Optimize / Transfer), or wind it down (🔴 / 🟡 Sunset)?
- **The stage lists items *assessed*, not deliverables *defended*, and has no transition criteria** (`stage-gates.md`), so there is no *base* Stage-Gate `[critical]` veto set. Instead of base veto logic, weigh two things: whether achieved results match the original goals, and whether the value generated exceeds the cost of continued support and development.
- **Two things still veto a 🟢 at Gate 6**, even though the base set is empty:
  - The long-term monitoring strategy for quality degradation, data drift, and ethical compliance (`ai-validation.md`): if it is Not met/Unknown, long-term viability cannot be confirmed, so 🟢 Continue Growth is not available until it exists — default to 🟡 Maintain & Optimize meanwhile.
  - **Modern-AI overlay vetoes still apply** (`modern-ai-overlay.md`): for a product that can act autonomously/externally, touches regulated data, or answers users unsupervised, the modern-AI readiness item is stage-critical at *every* gate including Gate 6. If it is Not met/Unknown at scale, that blocks 🟢 regardless of economics — an agentic product that is profitable but unsafe at scale is not a Continue Growth. Gate 6's empty base veto set never overrides an overlay veto.
- **Map the outcome to a light** by economics and trajectory: value clearly exceeds cost and still growing → 🟢; value roughly covers cost or is flat → 🟡 (Maintain/Transfer); cost exceeds value with no path back → 🔴 (or 🟡 Sunset for an orderly wind-down).

## Investment Logic

Decisions apply the **incremental-investment** principle: release the next stage's resources only, scaled to remaining risk. Never approve scale-stage spend at a concept gate.

Sunk investment changes **strictness, not criteria**:

- Early gates (1–3) — little is spent, so decisions stay flexible; Pivot/Merge/Recycle are cheap and expected. A borderline idea can proceed on a narrowed scope.
- Late gates (4–6) — large investment is sunk, so a 🔴 Kill needs serious cause (metrics that will not hold, unacceptable risk, economics that never close). The bar for stopping is higher, but the criteria themselves do not relax — a product that fails a stage-critical criterion still cannot get a clean 🟢.

## Writing a Conditional (🟡)

A 🟡 is only useful if it is actionable. Always state:

- The specific gaps blocking a 🟢 (tie each to a transition criterion).
- The concrete condition that closes each gap (what evidence or result is needed).
- The owner and, where possible, the checkpoint or re-review trigger.
- Which 🟡 form applies (Pivot, Merge, Recycle, Limited Go, Technical Pivot, Return to Concept, Extended Beta, Scope Reduction, Return to Prototype, Optimize First, Sustain, Sunset Plan, Maintain & Optimize, Transfer, Sunset).

A 🟡 without named conditions and owners is a deferred decision, not a gate decision.

## Common Failure Modes in Gate Decisions

- Grading outputs, not outcomes: a built prototype is not a passed gate; proven value and cleared technical risk are.
- Letting a strong value story mask a missing data or feasibility criterion (violates dual validation).
- Averaging away a stage-critical blocker because most items look green.
- A gate that never kills or recycles anything — screening has failed.
- Approving next-stage-plus investment because the team is excited.
- Treating alignment/ethics as a late-stage checkbox instead of a per-gate criterion.
