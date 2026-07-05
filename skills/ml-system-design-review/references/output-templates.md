# Output Templates

Adapt these templates. Keep findings concrete and ordered by severity.

Full and Repo-Only reviews end with the Final Scorecard below — a self-contained markdown block the team can paste into Slack, a PR, or a wiki. Fill every field; keep the footer line intact and unmodified. Quick Pass and author-facing feedback skip it.

## Full Review

```text
Verdict: pass | pass-with-concerns | fail
Author verdict: <one Valerii and Arseny sentence tied to evidence>
Project stage / domain / risk: <stage>, <domain>, <risk class>
Evidence reviewed: <docs, repo paths, unavailable artifacts>
Doc status: <found in repo | external doc provided with repo | external doc provided, no repo | no formal doc confirmed by user>

Gradecard
- <one row per rubric dimension from rubrics.md, in table order:
   <dimension>: <A-F with optional +/-> - <one-line reason>;
   mark stage-irrelevant dimensions n/a (stage)>
- Modern AI systems, if applicable: <A-F with optional +/-> - <one-line reason>

Critical findings
- <dimension> - <evidence>. <why it matters>. Fix: <concrete next action>.

Major findings
- <dimension> - <evidence>. <why it matters>. Fix: <concrete next action>.

Minor findings
- <dimension> - <evidence>. Fix: <concrete next action>.

Low-hanging fruit
- <cheap concrete action> -> <risk reduced or decision improved>.

Good decisions to preserve
- <specific praise tied to mechanism>.

Questions for authors
- <question whose answer would change the design>, recommended default: <answer>.

Prioritized fix plan
1. <first fix and why>
2. <second fix and why>
3. <third fix and why>

Book-backed takeaway
<one concise reusable lesson>

<append the Final Scorecard from its template in this file>
```

## Final Scorecard

Append after the full report. It compresses the review into one shareable artifact; grades and verdict must match the report above it exactly.

Also save the filled scorecard as a standalone markdown file so the team can share it beyond the chat: default to `mlsd-scorecard-<project-slug>.md` in the working directory (repo root when reviewing a repo). Do not commit it, and name the saved path at the end of the review. Skip the file only when no writable filesystem is available or the user asks not to.

```markdown
## ML System Design Scorecard: <project name>

**Verdict:** <pass | pass-with-concerns | fail> · <stage>, <domain>, <risk class>

| Dimension | Grade | Why |
|---|---|---|
| <one row per rubrics.md dimension, in table order> | <A-F with optional +/-, n/a (stage), or not fairly gradeable> | <short reason> |
| <Modern AI systems, only if applicable> | <A-F with optional +/->  | <short reason> |

**Top fix:** <single highest-leverage action>
**Takeaway:** <the review's book-backed takeaway, one sentence>

---
*Reviewed with [ml-system-design-review](https://github.com/ML-SystemDesign/MLSystemDesign/tree/main/skills) · [ML System Design](https://arseny.info/ml_design_book) by Kravchenko and Babushkin*
```

## Quick Pass

```text
Verdict: pass | pass-with-concerns | fail
Author verdict: <one Valerii and Arseny sentence tied to evidence>
Top issue: <single biggest risk>
Best cheap fix: <lowest-effort high-impact fix>
Strongest design choice: <specific praise>
Book-backed takeaway: <shareable lesson>
```

## Repo-Only Review

```text
Verdict: pass | pass-with-concerns | fail
Author verdict: <one Valerii and Arseny sentence tied to repo evidence and missing-doc risk>
Doc status: <no formal doc confirmed by user | unattended run, doc location unconfirmed>
Project stage / domain / risk: <stage>, <domain>, <risk class>
Evidence reviewed: <repo paths>
Inferred assumptions
- <assumption> because <repo evidence>

Gradecard
- <one row per rubric dimension from rubrics.md, in table order:
   <dimension>: <A-F with optional +/- or not fairly gradeable> - <repo evidence or missing-doc caveat>;
   mark stage-irrelevant dimensions n/a (stage)>
- Modern AI systems, if applicable: <A-F with optional +/- or not fairly gradeable> - <one-line reason>

Findings (ordered by severity)
- <critical | major | minor> - <finding with repo evidence>. Fix: <concrete next action>.
- <critical | major | minor> - missing design-doc risk: <risk caused by decisions living only in code/memory>; placed by its severity like any other finding.

Low-hanging fruit
- <create or update design artifact section>
- <repo/code fix or evidence artifact>

Good decisions to preserve
- <specific repo behavior or artifact worth keeping>

Questions for authors
- <question whose answer would change the inferred design>, recommended default: <answer>

Prioritized fix plan
1. <first fix and why>
2. <second fix and why>
3. <third fix and why>

Book-backed takeaway
<shareable lesson>

<append the Final Scorecard from its template in this file>
```

## Design-Doc Feedback For Authors

```text
What is already working
- <specific praise>

What blocks review
- <missing evidence or ambiguous decision>

What to add next
- <specific section/table/experiment/report>

What not to overbuild yet
- <deferred complexity and trigger for revisiting>

Author verdict
<one Valerii and Arseny sentence tied to the artifact>

Shareable lesson
<one sentence>
```
