# Fast Track

For products with low technological uncertainty, the process offers an accelerated path that collapses several standard stages. It trades deep technical investigation for faster product validation — appropriate only when the technical risk is genuinely low.

## When to Use Fast Track

All of these should hold:

- The technology is well known and proven.
- Technical risks of building the AI components are low.
- The main focus is product validation, not technical validation.
- There are successful references for similar solutions in the industry.

If any of these is false — novel model, unproven feasibility, scarce or risky data, no comparable precedent — use the full six-stage path. When unsure, default to the full path; the cost of a wrongly fast-tracked technical risk surfaces late and expensive.

## The Collapsed Path — First-Class Gates

Four consolidated stages instead of six, each ending in its own gate. Use **FT-Gate 1..4** numbering in the report (not standard Gate N) so no one mistakes a Fast Track review for the full path. Gates still resolve to 🟢 / 🟡 / 🔴, and each collapses the *union* of the merged standard stages' deliverables, criteria, and allowed decision variants.

| FT stage | Standard stages merged | Market stage | Report label |
|---|---|---|---|
| 1 Ideation | Stage 1 | TAM | FT-Gate 1 (Ideation) |
| 2 Discovery | Stages 2 + 3 (Concept Validation + Prototype Development) | SAM | FT-Gate 2 (Discovery) |
| 3 Delivery | Stages 4 + 5 (Beta Testing + MLP Launch) | SOM | FT-Gate 3 (Delivery) |
| 4 Scale | Stage 6 | penetration vs. SOM | FT-Gate 4 (Scale) |

**Merged deliverables and criteria.** At a consolidated gate, defend the union of the merged standard stages' deliverables (`stage-gates.md`), and treat the union of their `[critical]` criteria as the veto set:

- **FT-Gate 2 (Discovery)** — critical: problem confirmed by research; data availability/quality/volume confirmed; technical feasibility confirmed by experts; **prototype hits minimum technical metrics; users confirm value**; full-scale build feasible and economically justified. (Concept's *and* Prototype's critical items — Fast Track shortens the path, not the bar.)
- **FT-Gate 3 (Delivery)** — critical: AI stable/reliable in real conditions; meets ethical norms and interacts safely with users; high safety and ethics indicators confirmed; consistent user+business value; reliable under load; economics hold under scaling. (Carries both Gate 4's compliance threshold *and* Gate 5's elevated evidence bar.)
- **FT-Gate 1** and **FT-Gate 4** are identical to standard Gate 1 and Gate 6 respectively (the Gate 6 special case in `gate-decisions.md` applies unchanged to FT-Gate 4).

**Allowed decision variants** at a consolidated gate are the union of the merged gates' variants — e.g. FT-Gate 2 may return Recycle, Pivot, Technical Pivot, Return to Concept, or Limited Go; FT-Gate 3 may return Extended Beta, Scope Reduction, Return to Prototype, Optimize First, Pivot, Sustain, or Sunset Plan. The deterministic procedure and dual-validation rule in `gate-decisions.md` apply exactly as on the full path: Fast Track reduces the *technical-investigation depth*, never the safety, data, or value bars.

## Characteristics

- **Shorter validation cycles** — less time on technical research, more on product validation.
- **Parallel activities** — some work from adjacent stages runs concurrently rather than in strict sequence.

## Deciding to Apply Fast Track

The following is practical guidance inferred from the process logic; treat it as editorial rather than transcribed. Fast Track is itself a decision, best made at or before Gate 1:

- Confirm low technical uncertainty with a technical expert, not just the product team.
- Confirm the industry references are real and comparable, not aspirational.
- If the collapsed Discovery gate surfaces unexpected technical risk (metrics not trivially met, data harder than assumed, alignment non-trivial), drop back to the full path rather than pushing a weak prototype into Delivery.

Document the Fast Track decision and its justification so a later gate can revisit it if the risk profile turns out higher than assumed.
