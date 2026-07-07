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

## ai-stage-gate

Runs a stage-gate review for an AI product and reaches an evidence-based gate decision. Locates the product on a six-stage process (Discovery → Delivery, plus an optional Fast Track), scores the current gate's required deliverables and transition criteria (Met / Partial / Not met / Unknown), and returns a traffic-light decision — 🟢 Go, 🟡 Conditional, or 🔴 Kill — with the top blocker, the conditions to advance, and the recommended next-stage investment. Applies dual value-and-technical validation with alignment, ethics, and data checks at every gate.

### Install

```bash
npx skills add ML-SystemDesign/MLSystemDesign
```

Or copy the skill directory manually:

```bash
cp -r skills/ai-stage-gate ~/.claude/skills/
```

### Use

Ask your agent to run or prepare a gate review, or to decide whether an AI initiative can advance: "run a stage-gate review", "are we ready for Gate 3?", "Go/Kill decision for this AI prototype", "triage this portfolio of AI ideas", "should this take the fast track?". The skill activates on gate-decision requests and routes itself through `SKILL.md`; the depth lives in `references/` (gate-review workflow, the six stages and their stage-critical criteria, deterministic gate-decision logic, AI-specific validation, a modern-AI/RAG/agent overlay, fast track, and output templates for standard gates, Fast Track gates, and portfolio triage).
