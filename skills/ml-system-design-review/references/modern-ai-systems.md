# Modern AI Systems

Load this reference for LLMs, RAG, foundation models, fine-tuning, agents, tool use, memory, external connectors, autonomous workflows, or LLM-driven side effects.

Modern AI systems are still ML systems. The difference is that the model is often a service, the context is part of the input distribution, the system can call tools, and failures can look fluent. Review the system, not the demo.

## Core Review Questions

- What task does the AI system perform, and what failures are unacceptable?
- Why this adaptation path: prompt, examples, RAG, PEFT, full fine-tuning, vendor model, local model, or hybrid?
- What evidence shows this model choice works for this task rather than for a leaderboard?
- What context is provided, how is it selected, and what happens when context is insufficient?
- What can the model cause the system to do, and what does deterministic code enforce?
- How are outputs evaluated, traced, rolled back, and monitored over time?

## Model And Adaptation Choice

Grade the design higher when it starts cheap and escalates only with evidence.

- Prompting and examples are often the first baseline.
- RAG is appropriate when the answer depends on fresh, private, long-tail, or attributable knowledge.
- Fine-tuning is appropriate when behavior, style, format, domain conventions, or task policy cannot be supplied reliably as context.
- Full fine-tuning should have a strong data, cost, deployment, and regression argument.
- Self-hosting should justify latency, privacy, cost, reliability, licensing, or control benefits.

Red flags:

- Fine-tuning proposed before testing prompting/RAG baselines.
- Model selected by popularity or leaderboard only.
- No cost, latency, context-window, privacy, licensing, or deployment comparison.
- Synthetic data used without validation against real target behavior.

## RAG Review

RAG quality is bounded by the weakest part of the retrieval path.

Review:

- Parsing and document cleaning.
- Chunking granularity and overlap.
- Metadata and version handling.
- Embedding model and index update policy.
- Retrieval metrics before generation metrics.
- Reranking and context assembly.
- Source attribution and citation behavior.
- Freshness, deleted-content handling, and access control.
- Low-confidence fallback and refusal behavior.
- Injection resistance: retrieved content is data, not instructions.

Grade anchors:

- F: "Use vector DB" with no retrieval design.
- D: Basic embeddings/search described, but retrieval quality is not measurable.
- C: Parsing, chunking, and retrieval path are visible, but freshness, attribution, or fallback is weak.
- B: Retrieval, reranking, context limits, citations, and generation failure modes are covered.
- A: Retrieval eval, freshness, attribution, access control, injection tests, fallback, and monitoring are covered.

## Agent And Tool-Use Review

If an AI system can call tools or affect state, review the control plane.

Required design elements:

- Clear boundary between model judgment and deterministic enforcement.
- Narrow typed tools or APIs, not generic execute/call-anything interfaces.
- Bounded tool outputs and structured errors with next actions.
- Risk classes for read, local write, external communication, destructive, financial, privileged, or regulated-data actions.
- Draft/commit or preview/apply split for risky actions.
- Explicit approval gates for irreversible or external side effects.
- Durable state for plans, approvals, tool handles, memory, and workflow status.
- Step, time, token, cost, retry, and kill-switch budgets.
- End-to-end traces: model, prompt/config version, retrieved context, tool calls, permission decisions, output, cost, latency.

Critical red flags:

- Raw shell, SQL, HTTP, cloud-admin, filesystem-write, or database access exposed directly to the model.
- Prompt text asks the model to be careful instead of code enforcing permission.
- External messages, production deploys, destructive writes, purchases, or permission changes can happen in one model-selected step.
- Approval state lives only in chat history.
- Tool errors return opaque prose and invite retry loops.

## Evaluation

Evaluate behavior, not vibes.

- Build task-specific evals with real or realistic artifacts.
- For RAG, score retrieval separately from generation.
- For generated answers, check grounding, unsupported claims, contradiction, refusal quality, and usefulness.
- For agents, verify final system state or structured result, not transcript wording.
- Use repeated runs for reliability when outputs or tool choices are stochastic.
- Keep regression cases from production failures.
- Calibrate LLM-as-judge with human spot checks and disagreement analysis.

Grade anchors:

- F: Demo-only or "looks good" eval.
- D: Generic metrics or manual spot checks.
- C: Task-specific cases exist, but coverage, calibration, or launch thresholds are weak.
- B: Human or judge-assisted scoring covers task failures with grounding and usefulness checks.
- A: Regression set, robustness/adversarial cases, calibrated judging, repeated runs, cost/latency metrics, and launch thresholds.

## Observability And Operations

Log enough to debug one bad answer without guessing:

- Request ID and user/session scope.
- Model name/version and inference config.
- Prompt/template/config version.
- Retrieved chunk IDs, ranks, scores, and source versions.
- Tool calls with arguments and structured results.
- Safety/filter/permission decisions.
- Token counts, latency, cost, retries, fallback flags.
- User feedback and later ground truth where available.

Monitor quality drift, retrieval drift, cost drift, safety incidents, latency, rate limits, vendor errors, and segment-specific failures.
