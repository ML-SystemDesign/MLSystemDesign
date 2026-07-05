# Review Workflow

## Classify First

Before grading, name:

- Evidence mode: doc and repo, doc-only, or repo-only (see SKILL.md Mandatory First Step).
- Project stage: concept, design doc, prototype, pilot, production, maintenance.
- Domain and task type: forecasting, ranking/search, classification, regression, CV/OCR, NLP, RAG, agentic workflow, recommender, anomaly/fraud/risk scoring.
- Users and decision owners.
- Worst-case mistake and blast radius.
- Available evidence: design doc, repo, data docs, experiment logs, notebooks, dashboards, tests, incidents, monitoring, deployment config.
- Modern-AI triggers: LLMs, RAG, fine-tuning, agents, tool use, memory, autonomous actions, external connectors.

Scale rigor to stage. A concept doc needs problem, value, risk, data feasibility, baselines, and validation direction. A production design needs release, monitoring, fallback, ownership, incident response, and maintenance.

## Evidence Pass

Read enough to answer these questions before judging:

- What problem is the system supposed to solve?
- What does the current workflow do without this system?
- What metric or decision improves if the system works?
- What data and labels exist, and where do they come from?
- What baseline is used as the comparison and possible fallback?
- How does evaluation mirror production information boundaries?
- How is the system trained, served, monitored, rolled back, and owned?
- Where does the repo contradict or extend the design doc?

Prefer evidence in this order: runnable code and configs, experiment artifacts, tests, design docs, README/memos, issue/PR discussion, remembered context.

## Formal Doc Gate

The gate applies only when a repo is under review and no formal design doc is found in it. A standalone doc with no repo is doc-only mode, not a gate trigger. Ask before continuing:

```text
I don't see a formal design doc in the repo. Is it in Google Docs, Confluence, Notion, or elsewhere? If no formal doc exists, say so and I'll do a repo-only MLSD audit with explicit assumptions.
```

Do not infer that no doc exists just because it is absent from the repo. If the user confirms no doc exists, continue in repo-only mode and clearly mark inferred assumptions. When running unattended and no user can answer, do not block: proceed repo-only and put the missing-doc caveat at the top of the report.

## Grade And Triage

Use `rubrics.md` for letter grades. Do not let the gradecard bury the review. After grading, rank findings by severity and leverage:

- What blocks safe progress?
- What is the cheapest high-impact improvement?
- What uncertainty would change the design if answered?
- What good choices should not be rewritten away?

## Review Lenses

- Problem framing: user pain, current workflow, stakeholders, value, antigoals.
- Risk: cost of mistakes, second-order effects, human override, privacy/security, compliance.
- Metrics: business metric, model metric, loss, offline-online bridge, uncertainty.
- Data: sources, labels, quality, metadata, lineage, freshness, representativeness.
- Validation: split logic, leakage, time/group boundaries, golden sets, update policy.
- Baselines: constant/rule/simple/pretrained/vendor baselines and complexity gates.
- Error analysis: learning curves, residuals, cohorts, best/worst/corner cases.
- Pipeline: reproducibility, configs, artifacts, tests, experiment tracking, packaging.
- Features: hypotheses, stability, leakage, cost, interpretability, feature tests.
- Measurement: A/B hypothesis, split unit, MDE, uncertainty, controls, reporting.
- Integration: API, versioning, release, fallback, override, monitoring, incident response.
- Serving: latency, throughput, cost, scaling, platform, profiling.
- Ownership: owners, runbooks, dashboards, escalation, bus factor, docs.

For modern AI systems, also apply `modern-ai-systems.md`.

## Output Discipline

- Findings first, ordered by severity.
- Include evidence references when reviewing files.
- Phrase recommendations as concrete changes, not abstract advice.
- Separate blockers from improvements.
- Include praise only when tied to a specific design mechanism.
- Include an author verdict only after the technical verdict is supported by evidence.
- End with a short book-backed takeaway the team can reuse.
