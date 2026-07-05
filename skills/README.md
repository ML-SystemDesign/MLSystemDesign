# Skills

Agent skills that apply the ML System Design framework by Kravchenko and Babushkin. Compatible with Claude Code and other [Agent Skills](https://skills.sh) consumers.

## ml-system-design-review

Reviews ML/AI system designs — a design doc, a repo, or both. Produces a verdict, a 10-dimension stage-aware gradecard (plus an optional modern-AI row for LLM/RAG/agent systems), severity-ranked findings, low-hanging fruit, questions for authors, and a shareable takeaway. Compares doc claims against repo evidence in both directions.

### Install

```bash
npx skills add ML-SystemDesign/MLSystemDesign
```

Or copy the skill directory manually:

```bash
cp -r skills/ml-system-design-review ~/.claude/skills/
```

### Use

Ask your agent to review, grade, or audit an ML system design: "review my ML design doc", "audit this repo for production ML readiness", "grade this RAG architecture". The skill activates on design-review requests and routes itself through `SKILL.md`; the depth lives in `references/` (workflow, rubrics, doc/repo audit, modern-AI addendum, red flags, praise patterns, output templates).
