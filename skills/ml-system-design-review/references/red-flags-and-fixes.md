# Red Flags And Fixes

Use this file to prioritize. A finding is useful when it explains why the gap matters and what concrete next action reduces risk.

## Critical Red Flags

- No clear problem, user, or business decision; the project starts with a model.
- No cost-of-mistake analysis for high-stakes, financial, safety, legal, medical, moderation, fraud, credit, or pricing decisions.
- Validation leaks future information, users, sessions, duplicates, geography, or target-derived features.
- Offline metric has no credible relationship to online or business impact.
- No baseline before complex modeling.
- Model changes can affect production with no fallback, rollback, owner, or monitoring.
- Sensitive or regulated data is used without privacy, retention, access-control, or audit design.
- RAG system answers without source grounding, access control, or injection resistance.
- Agentic system can write, send, delete, deploy, spend, or change permissions without deterministic approval gates.

## Major Red Flags

- Goals exist, but antigoals and scope boundaries are absent.
- Metrics are copied from a template without business mapping.
- Loss function is not justified by error costs.
- Dataset sources are listed, but freshness, quality, lineage, labels, and ownership are not.
- Random split used where time, user, session, group, or entity boundaries matter.
- Validation set is reused for tuning without an outer estimate or golden set.
- No confidence intervals, variance, or uncertainty where small metric deltas drive decisions.
- Error analysis stops at aggregate metrics.
- Training is notebook/manual and cannot reproduce artifacts.
- Feature store, distributed training, or complex serving is proposed without immediate need.
- Monitoring is only uptime, request count, or final accuracy.
- Ownership is a team name, not a responsible owner and escalation path.
- LLM-as-judge is used without calibration or human spot checks.

## Low-Hanging Fixes

- Add a one-paragraph plain-language problem statement.
- Add goals and antigoals side by side.
- Add a table for false positives/false negatives or over/under-prediction costs.
- Add a metric table: business goal, online metric, offline proxy, loss, expected direction, owner, failure mode.
- Add a data-source inventory: owner, freshness, join key, quality risk, privacy/security, failure impact.
- Replace generic random splits with production-aware split logic.
- Add a constant/rule baseline and one simple model baseline.
- Add a baseline comparison matrix: quality, effort, latency, maintainability, interpretability, fallback usefulness.
- Save configs, dataset versions, model artifacts, metric reports, and top-N high-loss examples for each run.
- Add residual/cohort analysis for the most important users, products, regions, devices, or document types.
- Add a minimal experiment template: hypothesis, metric, split unit, MDE, alpha/beta, duration, stopping rule.
- Add fallback table: failure mode, detector, fallback action, owner, rollback path.
- Add RACI-lite ownership table: component, responsible owner, consulted stakeholders, escalation.

## Modern AI Low-Hanging Fixes

- Add a small golden set of task-specific prompts/questions/workflows.
- Evaluate retrieval before generation.
- Log model version, prompt version, retrieved chunks, token count, latency, cost, and fallback flag.
- Define what unsupported claims look like and how they are detected.
- Add structured output validation for extraction/classification.
- Add low-confidence fallback behavior.
- Restrict tools by default and require schemas, timeouts, output caps, and structured errors.
- Add injection tests where retrieved content tries to override system behavior.
- Add a cost envelope per request, user, or workflow.

## Fix Plan Ordering

Prefer this sequence:

1. Stop unsafe or misleading evaluation.
2. Add baseline and leakage checks.
3. Clarify problem, goals, and mistake costs.
4. Add error analysis and data-quality evidence.
5. Add fallback, monitoring, and ownership for pilot/production.
6. Only then add model or architecture complexity.
