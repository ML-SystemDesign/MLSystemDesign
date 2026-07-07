# Gate Review Workflow

## Classify First

Before judging readiness, name:

- Evidence mode: gate deck provided, product doc provided, or repo/telemetry only (see SKILL.md Mandatory First Step).
- Stage and gate being defended: 1 Ideation, 2 Concept Validation, 3 Prototype Development, 4 Beta Testing, 5 MLP Launch, 6 Scale & Optimize.
- Track: Discovery (1–3), Delivery (4–6), or Fast Track (collapsed 4-stage path).
- Product and decision owners: who defends the gate, who decides, who is the accountable product owner.
- AI risk profile: what the model does, worst-case failure, blast radius, regulated or sensitive data, autonomy.
- Investment spent so far: early gates are cheap to reverse; late gates carry sunk cost that raises the bar for a Kill (not the criteria).

Scale rigor to stage and to technical uncertainty. An Ideation gate needs a strategic fit, a market signal, differentiators, and a plausible AI value story. A Scale gate needs proven economics, reliability under load, and a long-term monitoring and alignment plan.

## Evidence Pass

Read enough to answer these before deciding:

- What business problem does the product solve, and how does it fit company/AI-center strategy?
- What user need does it serve, for whom, and how large is that market at this stage (TAM → SAM → SOM)?
- What does the AI component actually add, and is that value confirmed or still hypothetical?
- Is the model technically feasible at the required metrics — and is that claim backed by experts, a prototype, or production data appropriate to the stage?
- Do the data exist, at sufficient quality, volume, representativeness, and with legal clearance?
- How is alignment/ethics handled, and is there evidence it works for the key use cases?
- What is the growth model and, at later stages, the unit economics (ROI, CAC, LTV)?
- Which required gate deliverables are present, and which transition criteria are met, partial, unmet, or unknown?

Prefer evidence in this order: production/beta telemetry, prototype and model metrics, user and market research, expert technical assessment, growth/economic model, gate narrative and opinion.

## Map Deliverables And Criteria

Use `stage-gates.md` for the exact deliverables to defend and transition criteria at the current gate. For each required deliverable and each criterion, mark one of:

- **Met** — present with evidence appropriate to the stage.
- **Partial** — present but weak, incomplete, or unverified.
- **Not met** — required and absent.
- **Unknown** — cannot tell from the evidence provided (name what is missing).

A stage-critical criterion that is Not met or Unknown blocks a 🟢 Go. Do not average away a blocker.

## Decide And Justify

Use `gate-decisions.md` to translate the readiness map into a traffic-light decision and its stage-specific variant. The decision follows the criteria:

- What is proven well enough to advance?
- What single gap most blocks the gate?
- What must become true (and who owns it) to reach a clean Go — the conditions for a 🟡?
- How much investment does the next stage warrant, given remaining risk?
- What good work should be carried forward rather than redone?

## Review Lenses

- Strategic fit: alignment with company and AI-center strategy and portfolio.
- Value: user need, willingness to pay/adopt, differentiators, market stage (TAM/SAM/SOM).
- Technical feasibility: model metrics vs. targets, architecture, scalability, expert confidence.
- Data: availability, quality, volume, representativeness, legal/privacy clearance.
- Alignment and ethics: intended-behavior alignment, safety, fairness, prohibited uses, human oversight.
- Growth and economics: growth model, and from Delivery on, unit economics and monetization.
- Operations at scale (stages 5–6): reliability under load, monitoring, drift, long-term support.

For the AI-specific depth on any lens, load `ai-validation.md`.

## Output Discipline

- Lead with the gate decision; the readiness table and comments support it and never contradict it.
- Order findings by whether they block the gate, not by section order.
- Phrase gaps as conditions to advance with a named owner, not abstract advice.
- Recommend only the next stage's investment, not the whole roadmap's.
- Name reusable lessons and prior-product references (knowledge-transfer principle).
- When running unattended, state the inferred stage at the top and proceed.
