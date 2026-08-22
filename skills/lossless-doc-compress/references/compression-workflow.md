# Compression Workflow

The procedure. Follow it in order; the guarantee in `fidelity-rules.md` governs every
step.

## 1. Establish The Baseline

- Read the whole document first. Do not compress section-by-section blind — later
  sections tell you whether an earlier passage is a genuine restatement or a distinct
  point.
- Measure the starting word count. Use one consistent method (whitespace-separated tokens
  of the prose body, excluding code blocks and tables) and use the *same* method for the
  final count so the reduction figure is honest.

## 2. Classify Every Span

Walk the document. For each sentence or clause, assign one fate (see `fidelity-rules.md`):

- KEEP if it carries any information — always the default.
- REMOVE only if it matches a category in `removal-taxonomy.md` and carries no
  information.
- FLAG if removal or change is a judgment call — duplication you cannot prove, a possible
  tangent, a rewrite that would be *almost* but not exactly equivalent.

When unsure between REMOVE and FLAG, choose FLAG. When unsure between KEEP and FLAG,
choose KEEP (leave it, don't even flag) unless there is a concrete reason to raise it.

## 3. Apply

- Remove the REMOVE spans. Log each under its category with a representative
  before → after.
- Leave FLAG spans in place; mark each with a lightweight inline marker (see
  `output-templates.md`) and record it with a recommendation.
- Preserve all structure: headings, ordering, lists, tables, code blocks.

## 4. Confirm Losslessness (Self-Check)

Before producing output, verify:

- Every sacred item (numbers, decisions, caveats, named entities, code — see
  `removal-taxonomy.md`) present in the source is present in the compressed doc.
- The word-count delta is fully explained by the removal log: `before − after`
  approximately equals the words accounted for across the logged categories.
- No REMOVE was applied to a span that could plausibly carry information (zero contract
  violations). If you find one, restore it and convert it to a FLAG.

If any check fails, fix it before proceeding — a failed self-check means the output is
not yet lossless.

## 5. When To Stop

Stop when no span remains that is provably pure redundancy. Do not keep squeezing by
paraphrasing or by demoting KEEP content to hit a target percentage — the reduction is
whatever honest lossless compression yields, not a quota. A document that is already
tight yields a small percentage, and that is a correct result.

## 6. Produce The Three Artifacts

Format per `output-templates.md`: the compressed document, the removal log, and the
scorecard. Name the saved paths at the end.
