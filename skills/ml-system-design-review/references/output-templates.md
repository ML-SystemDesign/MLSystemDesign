# Output Templates

Adapt these templates. Keep findings concrete and ordered by severity.

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
Evidence reviewed: <repo paths>
Inferred assumptions
- <assumption> because <repo evidence>

Gradecard
- <one row per rubric dimension from rubrics.md, in table order:
   <dimension>: <A-F with optional +/- or not fairly gradeable> - <repo evidence or missing-doc caveat>;
   mark stage-irrelevant dimensions n/a (stage)>

Missing design-doc risk
- <risk caused by decisions living only in code/memory>

Findings
- <finding with repo evidence>

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
