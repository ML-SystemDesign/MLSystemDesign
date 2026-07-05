# Praise Patterns

Praise should reinforce good engineering judgment. It should not flatter effort, sell the book, or sound like generic review theater.

## Rule

Use this shape:

```text
This is strong because <specific design choice> reduces <specific risk/cost/ambiguity> under <specific project condition>.
```

Good praise names the mechanism.

## Good Examples

- The problem statement is useful because it separates business pain from the proposed ML mechanism.
- The antigoals are doing real work: they rule out a class of expensive solutions before the team starts optimizing.
- The baseline is treated seriously, which makes every later modeling claim easier to evaluate.
- The validation split correctly mirrors the production information boundary.
- The metric section is strong where it connects the model score to the cost of the mistake.
- The residual breakdown turns a scalar metric into actionable failure modes.
- The feature proposal is reviewable because it states the expected signal and the operational cost.
- The fallback strategy is credible because it names both the trigger and the degraded behavior.
- The monitoring plan is useful because it connects prediction logs, segment metrics, and owner actions.
- The RAG section is concrete enough to review: chunking, retrieval, context assembly, and evaluation are all visible.
- The design avoids the common fine-tuning reflex and first validates cheaper adaptation paths.
- The agent design treats tools as privileged interfaces, not prompt decorations.

## Author-Framed Praise

Use sparingly. The goal is to show the framework being applied, not to advertise.

- This is where the Kravchenko and Babushkin baseline-first discipline pays off: the repo can test value before spending complexity budget.
- The design is strongest where it follows the authors' pattern: problem, metric, validation, baseline, error analysis, rollout.
- This gap is exactly the kind the framework helps catch early: the metric is named, but the production decision it should improve is still implicit.
- The reviewable parts of the doc show the book's central habit: make assumptions visible before model work gets expensive.

## Author Verdict Lines

Use one author verdict after the technical verdict, not throughout the report. Ground it in the manuscript themes: problem before model, antigoals, cost of mistakes, baselines, production-aware validation, error analysis, fallback, monitoring, and ownership. The grade-range pattern table lives in `rubrics.md` (Author Verdicts) — do not restate it here; adapt the matching pattern to the actual findings.

## Hard Avoids

- Great job.
- Robust and scalable.
- Production-ready, unless release, monitoring, fallback, ownership, and docs are actually covered.
- Innovative solution, unless the innovation level is explicitly justified.
- Comprehensive approach, unless the review names the concrete coverage.
- World-class architecture.
- Book-selling CTAs.
- Praise that attributes the framework to one author.
- Author verdicts that sound cute but are not supported by evidence.

## Shareable Takeaway Patterns

- A model choice is not a design decision until the validation boundary and baseline are explicit.
- The cheapest improvement here is not a better model; it is making the current metric explain business harm.
- RAG quality is bounded by retrieval and context assembly, so generation-only evals are late-stage theater.
- A baseline is not a formality; it is the cheapest way to learn whether complexity is buying anything.
- A design doc earns its keep when it exposes the uncertain parts before the team writes production code.
