# Repo And Doc Audit

The skill reviews the design artifact and the implementation together. Either one can hide problems the other exposes.

## Finding Design Docs

Search likely locations and names:

- `README*`, `docs/**`, `design/**`, `architecture/**`, `rfcs/**`, `adr/**`, `memos/**`, `notebooks/**`.
- Files containing `design`, `architecture`, `proposal`, `prd`, `rfc`, `adr`, `experiment`, `evaluation`, `metrics`, `monitoring`, `runbook`.
- Issue/PR templates or contribution docs when the repo has no dedicated design folder.

If no formal doc is found in the repo and the user has not provided one, apply the Formal Doc Gate in `review-workflow.md`: ask the user where it lives; unattended, proceed repo-only with the missing-doc caveat at the top of the report. Do not assume absence.

## What To Inspect In Code

Map the implementation enough to compare it with the design:

- Data loading, ETL, feature generation, label construction.
- Train/validation/test split code.
- Baseline and model training code.
- Metric/loss computation.
- Experiment outputs, configs, notebooks, memos.
- Tests and smoke checks.
- Serving/inference API, batch jobs, model packaging.
- Monitoring, logging, dashboards, alert config.
- Deployment, CI/CD, rollback, runbooks.
- Ownership hints: CODEOWNERS, on-call docs, escalation docs.

For modern AI systems also inspect prompts/templates, retrieval code, index updates, tool schemas, permission checks, traces, and eval fixtures.

## Compare Doc Claims With Repo Evidence

Useful finding patterns:

- Doc promises temporal split; repo uses random split.
- Doc names business metric; repo only logs model metric.
- Doc claims baseline exists; repo trains only the complex model.
- Doc discusses leakage; repo builds future-looking features.
- Doc says monitoring exists; repo only logs request counts.
- Doc says fallback exists; code has no fallback path or untested fallback.
- Repo has useful safeguards absent from the doc, making the design hard to review or maintain.

## Repo-Only Mode

Entered only after explicit user confirmation that no formal doc exists — or, when running unattended with no user to ask, entered directly with the missing-doc caveat at the top of the report.

Report structure changes:

- Add `Doc status: no formal doc confirmed by user` (or `unattended run, doc location unconfirmed`).
- Add `Inferred assumptions` with each assumption labeled.
- Add a severity-labeled `missing design-doc risk` finding to the findings list if the repo is mature enough that lack of a doc creates coordination or maintenance risk.
- Grade from repo evidence, but mark dimensions that cannot be fairly graded without a design artifact.

Do not penalize early prototypes for missing a polished doc. Do penalize production or multi-person projects when core decisions exist only in code and memory.

## Evidence References

When possible, cite files and lines. If the relevant artifact is external or unavailable, state that explicitly and grade the uncertainty rather than inventing evidence.

Good evidence phrasing:

- `docs/design.md:42` defines a 30-day prediction horizon, but `src/split.py:18` validates on same-day labels.
- `configs/train.yaml:12` sets the model objective, but no document maps this loss to the business cost.
- No design doc was found in-repo; user confirmed none exists, so the repo-only audit assumes the CLI entry points define the intended workflow.
