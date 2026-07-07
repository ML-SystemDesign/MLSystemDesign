# AI-Specific Validation Across Gates

What makes this a Stage-Gate process *for AI* is that value validation is never sufficient on its own. The source names four AI-specific characteristics: early-stage technical-validation emphasis, dual validation, ethics/alignment, and particular attention to data. This file expands each into gate-level checks, then adds drift and long-term monitoring, which the source raises specifically as a Scale-stage (Gate 6) concern. Load it whenever a gate hinges on the AI itself rather than the product story.

**If the product uses an LLM, RAG, a foundation model, fine-tuning, agents, tool use, memory, or autonomous side effects, also load `modern-ai-overlay.md`.** The checks here (feasibility, data, alignment, metrics) are necessary but not sufficient for those systems: they fail in ways a generic gate misses — fluent-but-wrong output, retrieval that silently misses, tools that act with real consequence. The overlay adds RAG grounding, injection resistance, tool-permission gates, evals, traces, and bounded side effects as cross-gate items.

## Dual Validation

Every gate must clear **two** axes, and the weaker one governs the decision:

- **User value** — does it solve a real problem people will adopt or pay for?
- **Technical AI feasibility** — can the model be built and operated at the required parameters?

Failure patterns:

- A concept users love, built on a model that cannot hit the metrics → gate fails on the technical axis.
- A technically impressive model that solves no confirmed problem → gate fails on the value axis.
- A deck that argues one axis loudly to distract from the other → the quiet axis is the finding.

Both axes are assessed from Gate 1 onward. Crucially, technical feasibility carries particular weight *early* — the source makes early-stage technical validation a critical characteristic precisely because an unbuildable model terminates the initiative no matter how strong the user value. What shifts by stage is the *evidence*, not the importance: Ideation/Concept confirm feasibility through expert assessment and data availability; Prototype demands metrics actually hit; MLP/Scale swing toward business and satisfaction metrics, with technical optimization as a lever on business results.

## Data

Data is assessed at every gate, escalating in rigor:

- **Availability** — do we have (or can we legally obtain) the data to train and operate the model?
- **Quality** — accuracy, completeness, noise, labeling.
- **Volume and representativeness** — enough data, covering the real population and edge cases, to build an effective model.
- **Legality and privacy** — rights to use the data, consent, retention, residency, regulatory constraints.

Concept Validation (Gate 2) is where data availability/quality/volume/representativeness must be *confirmed*, not assumed. A gate that assumes data exists is not validated. From beta onward, watch for real-world data diverging from training data.

## Alignment and Ethics

Assessed at every gate — not deferred:

- **Alignment** — does the model behave as intended, consistent with company values and safety requirements? Is there evidence (not just intent) on the key use cases?
- **Ethical risk** — fairness, transparency, human oversight, prohibited uses; heightened for regulated, financial, or high-stakes decisions.
- **Safety verification** — key use cases and adversarial/edge test cases defined (Gate 2), implemented and measured (Gate 3), validated in real conditions (Gate 4), monitored in production (Gates 5–6).

Escalation of alignment evidence by stage: *defined test cases* → *first implementation + improvement plan* → *tested in real conditions* → *production monitoring + continuous improvement* → *reliable under load with long-term monitoring for degradation, drift, and ethical compliance*.

## Technical Metrics and Targets

- Metrics (accuracy, latency/speed, reliability, and task-specific measures) must be **defined and agreed as realistic targets** at Concept (Gate 2), **hit at minimum levels** by the prototype (Gate 3), **sustained in the real environment** during beta (Gate 4), and **reliable under load** at MLP/Scale (Gates 5–6).
- A metric named without a target is not gate evidence. A target met on a demo but not on representative data is not met.
- If prototype technical metrics are missed, the honest decision is Technical Pivot or Return to Concept, not a hopeful Conditional Go.

## Drift and Long-Term Monitoring

From launch and especially at scale (Gate 6), the source requires a long-term monitoring strategy covering:

- **Data drift** — inputs shift away from the training distribution.
- **Quality degradation** — metrics decay silently over time.
- **Ethical/alignment compliance** — behavior must keep meeting norms under load and over time.

In practice also watch for **concept drift** — the underlying reality the model predicts changes — which standard ML monitoring treats alongside data drift.

A Scale gate without a long-term monitoring strategy for degradation, drift, and ethical compliance is missing a required item; per the Gate 6 special case in `gate-decisions.md`, its absence blocks a 🟢 Continue Growth even though Gate 6 has no formal `[critical]` veto set — the monitoring strategy is the one item that behaves like a veto at that gate.

## How This Maps to Gate Findings

When any AI concern above is Not met or Unknown at a stage where it is required, it is a gate blocker, not a footnote:

- Missing/insufficient data at Gate 2 → blocks 🟢; likely Recycle or Kill.
- Alignment untested where the stage requires it → blocks 🟢.
- Technical metrics unmet at Gate 3 → Technical Pivot / Return to Concept.
- No drift/monitoring plan at Gate 6 → cannot confirm long-term viability.
