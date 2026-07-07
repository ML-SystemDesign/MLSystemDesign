# The Six Stages and Their Gates

The process has six stages across two tracks. Each stage has a goal, key questions, a market-sizing focus, and gate content. Stages 1–5 end with deliverables defended at the gate and transition criteria that must be met to advance to the next stage. Stage 6 is a retrospective assessment with no forward gate, so it lists items *assessed* rather than *defended* and has no transition criteria. Grade each gate against the deliverables and criteria for the stage being finished.

Market sizing progresses across stages: **TAM** (total addressable market) → **SAM** (serviceable addressable market) → **SOM** (serviceable obtainable market), each refined as evidence grows.

## Stage-Critical Criteria (the veto set)

Not every criterion carries equal weight. Each gate has a small set of **stage-critical** criteria: if any one is Not met or Unknown, the gate cannot be 🟢 (see `gate-decisions.md` for the deterministic mapping). The rest are contributing criteria — weakness there pushes toward 🟡 but does not by itself veto a Go. Each stage below marks its stage-critical set explicitly with **[critical]**; this table is the summary.

| Gate | Stage-critical criteria (any one Not met / Unknown → cannot be 🟢) |
|---|---|
| 1 Ideation | Strategic fit; AI adds substantial value; a meaningful market exists |
| 2 Concept Validation | Problem confirmed by research; data availability/quality/volume confirmed; technical feasibility confirmed by experts |
| 3 Prototype Development | Prototype hits minimum technical metrics; users confirm value; full-scale build feasible and economically justified |
| 4 Beta Testing | AI stable/reliable in real conditions; meets ethical norms and interacts safely; economics show durable potential |
| 5 MLP Launch | Consistent user+business value; reliable under load; safety/ethics confirmed; economics hold under scaling |
| 6 Scale & Optimize | (No forward gate — a portfolio judgment with no *base* veto set; long-term monitoring and any modern-AI overlay veto still apply. See the Gate 6 special case in `gate-decisions.md`) |

---

## Discovery Track

### Stage 1 — Ideation → Gate 1

**Goal:** form and give an idea a first evaluation.

**Key questions:** What business problem does it solve and how does it fit company/AI-center strategy? What user need, and why does it matter? Who is the target audience and how large? What solutions already exist for them? What are our key differentiators? How does AI create value here? What is the scaling potential?

**Market sizing:** TAM — rough total market for this class of solution.

**Deliverables defended at Gate 1:**
1. Description of the idea and its potential user value.
2. TAM estimate and a preliminary competitor scan.
3. Preliminary product growth model.
4. Hypotheses about key differentiators.
5. Research plan for the next stage.
6. Preliminary technical-feasibility assessment of the AI capability.

**Transition criteria:** idea fits company/AI-center strategic priorities **[critical]**; a meaningful potential market exists **[critical]**; the growth model shows enough potential to justify deeper work; clear differentiators from existing solutions; the AI component adds substantial value **[critical]**; a clear research plan exists to validate the concept; significant scaling potential in reach and technical capability.

**Decision character:** little has been invested, so decisions can stay flexible. What matters most is strategic fit and a first read on AI feasibility.

### Stage 2 — Concept Validation → Gate 2

**Goal:** confirm the problem exists and the solution concept is viable.

**Key questions:** Do research findings confirm the problem and users' willingness to pay for a solution? Does the concept meet the identified need and user expectations? Do we have access to the data needed to train the model, and at what quality? What are the technical risks and difficulties of building the model to spec? What technical constraints could affect UX or business metrics? How will we measure model effectiveness against the user task? What are the prototype's key success metrics?

**Market sizing:** SAM — the share of TAM we can serve given our tech and business constraints.

**Deliverables defended at Gate 2:**
1. User and market research findings.
2. Refined SAM estimate.
3. Growth model refined on research.
4. Product/solution concept.
5. Roadmap to build the prototype.
6. Initial design mockups.
7. Target technical metrics for the prototype.
8. Technical-feasibility assessment of the AI components.
9. Risk and constraint analysis.
10. Key use cases and test cases for AI safety verification.
11. First assessment of key risks, flagging critical blockers.

**Transition criteria:** problem existence confirmed by research **[critical]**; SAM shows enough investment potential; updated growth model confirms potential on the data; technical feasibility confirmed by AI experts **[critical]**; realistic prototype metrics defined and agreed; key use cases defined for later AI-safety checks; data availability, volume, quality, and representativeness confirmed sufficient to build an effective model **[critical]**.

**Decision character:** research data and first technical expertise now exist. Confirm the concept holds on both user value and AI feasibility.

### Stage 3 — Prototype Development → Gate 3

**Goal:** build a working prototype proving core value and AI technical feasibility.

**Key questions:** Does the model hit required technical metrics? Is the value proposition confirmed when users interact with the prototype? What technical/architectural decisions will the full product need? What resources are needed to move to full development? How well are alignment mechanisms implemented, and how do they behave in prototype testing?

**Market sizing:** SAM refined for technical constraints found during prototyping.

**Deliverables defended at Gate 3:**
1. Working prototype of the AI solution.
2. Model technical metrics.
3. Prototype user-testing results.
4. Results of the first alignment implementation and a plan to improve it.
5. Refined solution architecture.
6. Resource estimate for full development.
7. MVP scope, build plan, and key success metrics.
8. Growth model updated with prototype results.

**Transition criteria:** prototype meets minimum required technical metrics **[critical]**; users confirm the value **[critical]**; initial alignment mechanisms are effective on key use cases; full-scale technical implementation is feasible and economically justified **[critical]**; the team understands all technical challenges and paths to solve them; MVP success metrics defined and agreed; updated growth model shows enough potential to move to beta.

**Decision character:** technical metrics dominate. If accuracy/speed/reliability targets are missed, expect a substantial technical rework or a return to concept.

---

## Delivery Track

### Stage 4 — Beta Testing → Gate 4

**Goal:** build an MVP (minimum viable product) and test it with a limited user group.

**Key questions:** Does the product work correctly in a real environment? Are the key AI technical metrics achieved? How do users interact with it? What improvements are needed before full launch? How does the model behave in edge cases and how well does alignment work? Are there unexpected use cases needing extra model tuning?

**Market sizing:** SOM — realistic near-term obtainable share.

**Deliverables defended at Gate 4:**
1. Beta-testing results.
2. AI technical metrics in the real environment.
3. User behavioral metrics.
4. Alignment testing results in real conditions and a refinement plan.
5. Improvement plan before full launch.
6. Growth model refined with a SOM estimate from beta data.

**Transition criteria:** AI-system stability and reliability confirmed in real operating conditions **[critical]**; qualitative and quantitative real-world data and user feedback gathered toward the MLP; a clear strategy exists to fix gaps and grow into the MLP; the product meets ethical norms and interacts safely with users **[critical]**; the updated growth model, backed by economics, shows durable long-term potential **[critical]**.

**Decision character:** significant investment is now sunk, so Kill is reserved for serious problems. Focus on real usage metrics and technical performance in near-production conditions.

### Stage 5 — MLP Launch (Minimum Lovable Product) → Gate 5

**Goal:** launch a full version that is not only functional but creates a positive user experience.

**Key questions:** Does the product hit target product metrics? Do all components work together reliably? How does it integrate with existing enterprise/product systems? How reliably do AI safety and alignment systems work? What limits scaling?

**Market sizing:** detailed SOM with user segmentation and conversion potential.

**Deliverables defended at Gate 5:**
1. Product metrics and user-satisfaction indicators.
2. Technical performance of the AI systems in production.
3. Model-behavior monitoring from an alignment view, plus a continuous-improvement plan.
4. Scaling and further-development plan.
5. Growth model with a detailed scaling-potential analysis and key-metric forecast.

**Transition criteria:** product consistently shows high user and business value **[critical]**; AI systems work reliably and efficiently under increased load **[critical]**; high safety and ethics indicators confirmed **[critical]**; a detailed scaling plan exists with technical and resource requirements; product economics hold under scaling **[critical]**; enough user feedback accumulated for confident development; the updated growth model shows durable long-term potential and a clear monetization strategy.

**Decision character:** decisions lean on business and satisfaction metrics. Technical optimization of the AI becomes a lever for business results.

### Stage 6 — Scale & Optimize → Gate 6

**Goal:** scale to the full target audience and optimize operation.

**Key questions:** How does the product handle increased load? Do the models need optimization to scale? What metrics show room for further improvement? Do real results match the business case? Do AI reliability and ethics hold at scale?

**Market sizing:** actual market penetration vs. SOM forecast, with corrected projections.

**Assessed at Gate 6:**
1. Overall performance and stability at scale — technical, user, and business metrics.
2. Whether achieved results match the original goals.
3. Technical development plan, including long-term AI support strategy.
4. Long-term growth model and detailed scaling economics — ROI, CAC, LTV, and other key financials.
5. Comprehensive assessment of the AI at scale — reliability, alignment under load, and a long-term monitoring strategy for quality degradation, data drift, and ethical compliance.
6. User-satisfaction and engagement at scale.

**Decision character:** the final gate weighs long-term viability and the product's place in the portfolio. The cost of support and development versus generated value is decisive.
