# Machine Learning System Design

> A practical collection of end-to-end ML system design documents, templates, and checklists — a companion to the book [*Machine Learning System Design*](https://www.manning.com/books/machine-learning-system-design) (Manning).

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)
[![Book](https://img.shields.io/badge/book-Manning-blue.svg)](https://www.manning.com/books/machine-learning-system-design)

This repository provides structure and a variety of design documents you can use as references and starting points for your own work. It doesn't aim to replace the full teaching experience of the book — instead, it gives you concrete, worked examples of what good ML system design documents look like in practice.

## Repository Structure

```
├── templates/                       # Templates and guidelines
│   ├── basic_ml_design_doc.md       # Template for ML design docs
│   └── design_doc_checklist.md      # Review checklist for design docs
├── Design_Doc_Examples/             # Example design documents
│   ├── Examples/                    # Real-world–inspired examples
│   │   └── EN/                      # English examples
│   └── Mock/                        # Educational mock examples
│       └── EN/                      # English mock examples
├── BookOutline.md                   # Book chapter summaries and key concepts
├── CONTRIBUTING.md                  # Contribution guidelines
├── LICENSE                          # MIT License
└── README.md                        # This file
```

## Getting Started

1. **New to ML System Design?**
   - Start with [`BookOutline.md`](BookOutline.md) for the key concepts, chapter by chapter.
   - Read a full worked example: [Retail Demand Forecasting](Design_Doc_Examples/Examples/EN/Retail_Demand_Forecasting_Design.md).
   - Use the [design document template](templates/basic_ml_design_doc.md) for your own designs.

2. **Writing your own design doc?**
   - Copy [`templates/basic_ml_design_doc.md`](templates/basic_ml_design_doc.md) and fill it in section by section.
   - Run through the [review checklist](templates/design_doc_checklist.md) before sharing it.
   - Compare against the examples for depth and tone.

3. **Looking for examples?**
   - [`Design_Doc_Examples/`](Design_Doc_Examples/) contains worked examples across different domains and complexity levels.
   - [Retail Demand Forecasting](Design_Doc_Examples/Examples/EN/Retail_Demand_Forecasting_Design.md) — a real-world–inspired forecasting system.
   - [RAG Chat with Document Versions](Design_Doc_Examples/Mock/EN/RAG_Chat_With_Doc_Versions/RAG_Chat_With_Doc_Versions_Design.md) — an educational mock of a RAG system.

## Templates

We provide two main artifacts:

1. [`basic_ml_design_doc.md`](templates/basic_ml_design_doc.md) — a section-by-section template for ML system design documents.
2. [`design_doc_checklist.md`](templates/design_doc_checklist.md) — a review checklist to sanity-check a design doc before sign-off.

## How the Template Maps to the Book

Each section of the design document template corresponds to one or more chapters of the book:

| Template section              | Book chapter(s)                                    |
| ----------------------------- | -------------------------------------------------- |
| I. Problem Definition         | 2 (Is there a problem?), 3 (Preliminary research), 4 (Design document) |
| II. Metrics and Losses        | 5 (Loss functions and metrics)                     |
| III. Dataset                  | 6 (Gathering datasets)                             |
| IV. Validation Schema         | 7 (Validation schemas)                             |
| V. Baseline Solution          | 8 (Baseline solution)                              |
| VI. Error Analysis            | 9 (Error analysis)                                 |
| VII. Training Pipeline        | 10 (Training pipelines)                            |
| VIII. Features                | 11 (Features and feature engineering)              |
| IX. Measuring and Reporting   | 12 (Measuring and reporting results)               |
| X. Integration                | 13 (Integration)                                   |
| XI. Monitoring                | 14 (Monitoring and reliability)                    |
| XII. Serving and Inference    | 15 (Serving and inference optimization)            |
| (cross-cutting)               | 16 (Ownership and maintenance)                     |

## Contributing

We welcome contributions! Please see [`CONTRIBUTING.md`](CONTRIBUTING.md) for detailed guidelines. Key areas for contribution:

- New design document examples
- Template improvements
- Best-practices documentation
- Reviews and feedback

## License

This repository is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.

## Acknowledgments

- Based on concepts from [*Machine Learning System Design*](https://www.manning.com/books/machine-learning-system-design).
- Contributors to the examples and templates.
- The ML community, for feedback and improvements.
