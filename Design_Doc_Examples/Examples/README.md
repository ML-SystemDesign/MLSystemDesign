# Real-World–Inspired ML System Design Examples

This directory contains ML system design documents based on real-world scenarios. While these examples are inspired by actual systems, they may be adapted or simplified for educational purposes.

## Current Examples

### EN (English)

1. **[Retail Demand Forecasting](EN/Retail_Demand_Forecasting_Design.md)**
   - Domain: Retail / Supply Chain
   - Problem: Optimizing inventory management through ML-based demand forecasting
   - Key features:
     - Granular SKU-per-store forecasting with category-level features
     - Integration of external data sources (weather, competitor prices, traffic)
     - Scheduled batch retraining with drift-triggered model updates
     - Risk management and monitoring

## Contributing New Examples

To contribute a new example:

1. Choose a descriptive name following the convention `[Domain]_[Problem]_Design.md`
   (e.g. `Retail_Demand_Forecasting_Design.md`).
2. Use the template from [`../../templates/basic_ml_design_doc.md`](../../templates/basic_ml_design_doc.md).
3. Include:
   - A clear problem definition
   - System architecture
   - Data pipeline design
   - Model selection rationale
   - Evaluation strategy
   - Implementation plan
4. Add supporting diagrams if available.
5. Follow the contribution guidelines in the root [`CONTRIBUTING.md`](../../CONTRIBUTING.md).

## Example Structure

Each example should:

1. Follow the standard template.
2. Include an executive summary (see the template).
3. Provide clear architectural diagrams where helpful.
4. Detail evaluation metrics.
5. Discuss implementation challenges.
6. Include monitoring and maintenance plans.

## Language Organization

- `EN/` — English examples
- Additional language folders can be added following the same structure.
