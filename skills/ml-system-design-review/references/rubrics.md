# Rubrics

Use letter grades. A grade is a review aid, not the review itself.

Global anchors:

- A: specific, evidence-backed, operationally useful, and tied to tradeoffs.
- B: reviewable and plausible, with enough evidence to support a decision, but with remaining operational detail or tradeoff gaps.
- C: named and directionally reasonable, but incomplete, generic, or weakly supported.
- D: shallow label with little mechanism; the team would mostly be building from assumptions.
- F: missing, unsafe, contradictory, or too vague to review.

Use `+` or `-` for meaningful nuance:

- Add `+` when a section exceeds the anchor on stage-critical evidence.
- Add `-` when a material caveat could change the design decision.
- Avoid `A+` unless the artifact has measured production evidence, explicit tradeoffs, and a maintenance loop.
- Do not average grades into a fake number. Overall grade is dominated by stage-critical dimensions and red flags.

## Base MLSD Gradecard

Ten dimensions. This table is the single source for the base gradecard rows: report one row per dimension, in this order. Modern AI systems add one optional row, graded per `modern-ai-systems.md`. Within a merged dimension, grade the weakest stage-critical facet and name it in the one-line reason. Templates may also use `n/a (stage)` for stage-irrelevant dimensions and `not fairly gradeable` in repo-only mode; these are explicit non-grades, not letter grades.

| Dimension | F | D | C | B | A |
|---|---|---|---|---|---|
| Problem framing, goals & antigoals | Starts with model/tech; no success criteria or scope | Basic task named; generic goals | Problem, users, and a measurable goal or rough scope named | Current workflow, value, goals, scope, and key exclusions are clear | Quantified pain, stakeholders, why now, antigoals, tradeoffs, minimum viable target |
| Cost of mistakes & risk | Not discussed | Generic FP/FN mention | Concrete failure modes | Error costs tied to user or business harm | Asymmetric costs, second-order effects, and mitigation path |
| Prior work, build/buy & baselines | Single chosen solution, no baseline | Alternatives named without comparison; one trivial baseline | Plausible alternatives listed; baseline exists but weakly used | Alternatives compared on value/cost/risk; several baselines compare complexity | Prior attempts and build/vendor/open-source economics; baselines support smoke test, fallback, and complexity gate |
| Metrics, loss & measurement | Generic accuracy/F1/RMSE; no experiment plan | Metrics named; hypothesis only | Metrics tied to the task; experiment metrics and split described | Online/business metric and offline proxy connected; controls, duration, and reporting outlined | Online metric, offline proxy, loss, uncertainty; MDE, alpha/beta, CIs, segment effects, recommendation |
| Data, labels & features | Assumes data exists; feature list only | Sources listed; some feature candidates | Sources and labels described; feature candidates with partial rationale | Quality, freshness, privacy, and ownership covered; feature cost, stability, and leakage discussed | Lineage, metadata, QA, cold-start, representativeness; feature hypotheses with leakage/stability/cost checks and selection plan |
| Validation & leakage | Random split or absent | Standard split named | Split fits obvious constraints | Split matches major production boundaries | Mirrors production knowledge boundary, leakage reviewed, update policy |
| Error analysis | Final metric only | Manual examples | Curves, residuals, or cohorts started | Failure modes are actionable | Convergence, residuals, cohorts, worst/corner cases, artifacts |
| Training pipeline & reproducibility | Notebook/ad hoc | Scripts with weak tracking | Structured runnable path | Versioned configs and artifacts | Versioned configs, data, artifacts, reports, smoke/component tests |
| Serving, integration & release | Ends at model; serving ignored | Deployment or platform mentioned | API/release basics; latency/throughput/cost considered | Fallback or rollback considered; scale and reliability tradeoffs covered | Versioning, rollback, shadow/canary where useful, overrides; profiling-first plan, scale margins, security/privacy |
| Monitoring, ownership & maintenance | Accuracy or uptime only; no owner | Basic infra metrics; team named | Software/data/model metrics; owners/docs/on-call mentioned | Thresholds and owners named; runbooks and escalation mostly clear | Segment/drift strategy, alert owner, root-cause workflow; RACI-lite, runbooks, dashboards, bus-factor mitigation |

## Verdict Mapping

- Fail: any critical design gap, any stage-critical `F`, or any stage-critical `D` that makes the project likely to build the wrong system.
- Pass-with-concerns: no critical gaps, but `C` or `D` grades remain in areas that matter before pilot or production.
- Pass: major design risks are covered for the current stage; remaining issues are minor or explicitly deferred.

## Author Verdicts

Add one short author verdict after the technical verdict when the evidence is strong enough. Keep it concrete and proportional.

| Overall shape | Author verdict pattern |
|---|---|
| A range | Valerii and Arseny would be proud of this system: it starts from the problem, earns complexity with baselines, and has a boring path to production. |
| B range | Valerii and Arseny would call this a solid design draft: the core decisions are reviewable, but the remaining gaps still decide whether it survives contact with production. |
| C range | Valerii and Arseny would likely find this WIP: enough structure to discuss, not enough evidence to trust the important bets yet. |
| D range | Valerii and Arseny would label this mediocre ML system design: the artifact has ML vocabulary, but the metric, validation, baseline, or fallback mechanism is doing too little work. |
| F range | Valerii and Arseny could write a failure story from this: the design can spend engineering effort before proving the problem, boundary, baseline, or safety path. |

Vary the sentence to match the finding. Do not force the exact wording when it sounds cute or unfair.

## Stage Adjustment

Do not punish an early concept for missing production runbooks. Do punish a production candidate for missing runbooks, monitoring, fallback, ownership, or rollback.

Minimum acceptable by stage:

| Stage | Minimum useful evidence |
|---|---|
| Concept | Problem, value, stakeholders, cost of mistakes, why ML, data feasibility, simplest baseline |
| Design doc | Goals/antigoals, metrics/loss, data plan, validation, baseline, risks, rollout direction |
| Prototype | Runnable baseline, first validation, data QA, error examples, experiment notes |
| Pilot | Offline-online bridge, fallback, monitoring, owner, pilot cohort, A/B or shadow plan |
| Production | Release/rollback, incident response, dashboards, runbooks, drift handling, ownership |
| Maintenance | Regression tracking, retraining/update policy, docs freshness, bus-factor mitigation |

## Grade Interpretation

- F means the reviewer cannot rely on the design.
- D means the team knows the word but not the mechanism.
- C means the section can start a review but cannot yet carry a key decision alone.
- B means the section can support a decision with caveats.
- A means the section can survive production ambiguity.

When a dimension grades `F` or `D` and the project stage depends on it, make it a finding. When a dimension grades `C`, consider whether the missing evidence would change the design. When a dimension grades `A`, consider praising the specific mechanism.
