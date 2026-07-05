# Output Template

One template for every review, in two parts:

1. **Scorecard** — approximately one screenshot-friendly page carrying the verdict. Every review produces it.
2. **Comments** — one or a few pages of findings and recommendations after the scorecard. A quick pass may stop at the scorecard; a full review fills every applicable comments section.

Keep findings concrete and ordered by severity.

## Verdict

The verdict is computed from the gradecard, never chosen independently:

1. Map each graded row to points: A = 4.0, B = 3.0, C = 2.0, D = 1.0, F = 0.0; `+` adds 0.3 and `-` subtracts 0.3. Skip `n/a (stage)` and `not fairly gradeable` rows; include the Modern AI row when graded.
2. Average the points across the remaining rows.
3. Read the verdict off the average:
   - 3.0 and higher (B and above): **approve**
   - 2.3 up to 3.0 (B- to C+): **approve with concerns**
   - below 2.3 (C and below): **needs improvement**

Show the average next to the verdict. The verdict summarizes the gradecard; it does not soften findings — critical findings are counted on the scorecard and lead the comments.

## Part 1: Scorecard

A self-contained markdown block the team can screenshot or paste into Slack, a PR, or a wiki. Fill every field; keep the attribution line at the top intact and unmodified — it stays small text, never a header.

Also save the filled scorecard as a standalone markdown file so the team can share it beyond the chat: default to `mlsd-scorecard-<project-slug>.md` in the working directory (repo root when reviewing a repo). Do not commit it, and name the saved path at the end of the review. Skip the file only when no writable filesystem is available or the user asks not to.

```markdown
*Reviewed with [ml-system-design-review](https://github.com/ML-SystemDesign/MLSystemDesign/tree/main/skills) · [ML System Design](https://arseny.info/ml_design_book) by Kravchenko and Babushkin*

## ML System Design Scorecard: <project name>

**Verdict:** <approve | approve with concerns | needs improvement> (avg <points>) · <stage>, <domain>, <risk class>
**Critical findings:** <count, or none>
**Author verdict:** <one Valerii and Arseny sentence tied to evidence>

| Dimension | Grade | Why |
|---|---|---|
| <one row per rubrics.md dimension, in table order> | <A-F with optional +/-, n/a (stage), or not fairly gradeable> | <short reason> |
| <Modern AI systems, only if applicable> | <A-F with optional +/-> | <short reason> |

**Top fix:** <single highest-leverage action>
**Takeaway:** <the review's book-backed takeaway, one sentence>
```

## Part 2: Comments

Follows the scorecard in the report (not included in the saved scorecard file). Skip sections with nothing to say rather than padding them; drop the repo-only lines outside repo-only mode.

```text
Evidence reviewed: <docs, repo paths, unavailable artifacts>
Doc status: <found in repo | external doc provided with repo | external doc provided, no repo | no formal doc confirmed by user | unattended run, doc location unconfirmed>

Inferred assumptions (repo-only reviews)
- <assumption> because <repo evidence>

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
```

In repo-only mode, missing-design-doc risk is placed among the findings by its severity like any other finding.

When the audience is the doc's authors rather than a reviewing team, keep the same two parts but open the comments with good decisions to preserve, phrase findings as what to add next, and note what not to overbuild yet with the trigger for revisiting it.
