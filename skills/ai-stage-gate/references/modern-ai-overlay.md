# Modern-AI Cross-Gate Overlay

Load this whenever the product uses an LLM, RAG, a foundation model, fine-tuning, agents, tool use, memory, external connectors, or autonomous side effects. It is an **overlay**: it does not replace the six stage gates, it adds AI-harness checks that ride on top of them, so a gate cannot pass "technical feasibility" or "safety/alignment" while ignoring how the actual system can fail.

> **Scope note:** this overlay is an editorial extension beyond the base Stage-Gate source, mirroring the sibling `ml-system-design-review` skill's `modern-ai-systems.md`. It exists because modern AI products fail in ways a generic gate misses: fluent-but-wrong output, retrieval that silently misses, and tools that act with real-world consequence.

## How the overlay attaches to gates

Modern AI systems are still ML systems, but the model is often a service, the context is part of the input distribution, the system can call tools, and failures can look fluent. Fold this into the gate as **one modern-AI readiness item**, graded Met / Partial / Not met / Unknown like any other, and treated as **stage-critical** whenever the product can take autonomous or external actions, handle regulated/sensitive data, or answer users without a human in the loop. When it is stage-critical and Not met/Unknown, it blocks a 🟢 exactly like any other critical criterion (`gate-decisions.md`).

**This veto applies at every gate, including Gate 6.** Gate 6 has no *base* Stage-Gate veto set (`gate-decisions.md` Gate 6 Special Case), but the modern-AI overlay veto is not part of the base set and is not waived there: an agentic or sensitive-data product that is profitable at scale but whose safety/alignment readiness is Not met/Unknown is **not** a 🟢 Continue Growth. The empty base veto set never overrides an overlay veto.

Escalate what the overlay demands by stage:

| Gate | Modern-AI overlay expectation |
|---|---|
| 1 Ideation | The adaptation path is named and plausible (prompt / RAG / fine-tune / agent), and the worst-case failure of a fluent-but-wrong or over-permissioned system is acknowledged. |
| 2 Concept Validation | The cheapest adequate adaptation is chosen with a reason; data/grounding source identified; a small golden set and the unacceptable-failure definition are planned; tool/permission risk classes sketched. |
| 3 Prototype Development | Retrieval evaluated separately from generation (RAG); tools are narrow and permission-gated in the prototype; a first eval set runs; traces exist; injection tried at least once. |
| 4 Beta Testing | Grounding/refusal behavior measured on real traffic; tool actions bounded with approval gates for risky ones; cost/latency per request tracked; regression cases captured from beta failures. |
| 5 MLP Launch | Evals gate releases with thresholds; permission enforcement is deterministic code, not prompt text; end-to-end traces in production; low-confidence fallback live. |
| 6 Scale & Optimize | Quality/retrieval/cost/safety drift monitored at scale; adversarial and injection regression suite maintained; kill-switch and budget caps proven under load. |

## Model and Adaptation Choice

Grade higher when the design starts cheap and escalates only with evidence.

- Prompting and examples are often the first baseline.
- RAG fits when the answer depends on fresh, private, long-tail, or attributable knowledge.
- Fine-tuning fits when behavior, style, format, or task policy cannot be supplied reliably as context.
- Full fine-tuning needs a strong data, cost, deployment, and regression argument.
- Self-hosting should justify latency, privacy, cost, reliability, licensing, or control benefits.

Red flags (each is a gate finding): fine-tuning proposed before prompting/RAG baselines are tested; model chosen by leaderboard/popularity only; no cost/latency/context-window/privacy/licensing comparison; synthetic data used without validation against real target behavior.

## RAG

RAG quality is bounded by the weakest part of the retrieval path, so a gate that only saw generation quality has not validated the system. Review: parsing and cleaning; chunking and overlap; metadata and version handling; embedding model and index-update policy; **retrieval metrics before generation metrics**; reranking and context assembly; source attribution/citations; freshness, deleted-content handling, and access control; low-confidence fallback and refusal; injection resistance (retrieved content is data, not instructions).

Overlay grade anchors: F — "use a vector DB", no retrieval design. D — basic embeddings/search described, but retrieval quality is not measurable or the retrieval path is only partially visible. C — parsing/chunking/retrieval visible and retrieval quality is measurable, but freshness, attribution, or fallback is weak. B — retrieval, reranking, context limits, citations, and generation failure modes covered. A — retrieval eval, freshness, attribution, access control, injection tests, fallback, and monitoring all present.

## Agents and Tool Use

If the system can call tools or change state, the gate must review the control plane, not just the model. Required design elements: a clear boundary between model judgment and deterministic enforcement; narrow typed tools, not generic execute-anything interfaces; bounded outputs and structured errors; risk classes for read / local-write / external-comms / destructive / financial / privileged / regulated-data actions; draft→commit or preview→apply for risky actions; explicit approval gates for irreversible or external side effects; durable state for plans, approvals, and tool handles; step/time/token/cost/retry/kill-switch budgets; end-to-end traces.

Critical red flags — treat as gate blockers for any stage where the agent can act: raw shell/SQL/HTTP/cloud-admin/filesystem-write exposed to the model; permission enforced by prompt text asking the model to be careful rather than by code; external messages, deploys, destructive writes, purchases, or permission changes possible in one model-selected step; approval state living only in chat history; opaque tool errors that invite retry loops.

## Evaluation

Evaluate behavior, not vibes — a demo is not gate evidence. Build task-specific evals with real or realistic artifacts; score retrieval separately from generation; check grounding, unsupported claims, contradiction, refusal quality, and usefulness; verify final system state for agents, not transcript wording; use repeated runs when outputs are stochastic; keep regression cases from production failures; calibrate any LLM-as-judge with human spot checks. Watch eval contamination: verbatim golden-set Q&A pairs in fine-tuning data or few-shot examples make scores meaningless. For RAG, retrieving the answer-bearing source document is the *point* — contamination is the eval **answer key itself** (the graded Q&A pairs) being indexed as a document, not the source content. Treat public-benchmark results as weak evidence when pretraining overlap is plausible.

## Observability and Operations

Log enough to debug one bad answer without guessing: request ID and user/session scope; model name/version and inference config; prompt/template/config version; retrieved chunk IDs, ranks, scores, source versions; tool calls with arguments and structured results; safety/filter/permission decisions; token counts, latency, cost, retries, fallback flags; user feedback and later ground truth. Monitor quality drift, retrieval drift, cost drift, safety incidents, latency, rate limits, vendor errors, and segment-specific failures — this is what turns the Gate 6 "long-term monitoring" criterion from a promise into evidence.
