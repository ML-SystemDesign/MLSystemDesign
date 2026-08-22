# Slop And Hedging Patterns

Concrete patterns with before → after rewrites. Each rewrite preserves all information;
if a rewrite would lose any, it belongs as a FLAG instead (see `fidelity-rules.md`).

## Filler And Empty Transitions

- "It is important to note that the API is rate-limited." → "The API is rate-limited."
- "In order to reduce latency, we cache results." → "To reduce latency, we cache results."
- "As mentioned above, the model retrains nightly." → "The model retrains nightly."
- "The fact that the dataset is small means we cross-validate." → "The small dataset
  means we cross-validate."
- "It goes without saying that we log errors." → "We log errors."

## Hedging That Qualifies Nothing

- "This is basically a ranking problem." → "This is a ranking problem."
- "The results are essentially identical." → "The results are identical."
- "We arguably need a fallback." → "We need a fallback." (If the doubt is real, keep it as
  a stated uncertainty instead — see counter-examples.)
- "It seems that throughput drops under load." → "Throughput drops under load." (Only if
  the doc asserts it as fact elsewhere; if it is genuinely tentative, KEEP the hedge.)

## LLM Slop

- "In today's fast-paced, data-driven world, forecasting is more important than ever."
  → (remove entirely; carries no document-specific information)
- "In conclusion, this design addresses the requirements outlined above." → (remove;
  the design either addresses them or the body says how)
- "It is worth mentioning that this section covers monitoring." → "This section covers
  monitoring." (or remove if the heading already says so)
- "Let's dive into the architecture." → (remove; the heading is the transition)

## Restatement

- Two paragraphs both defining the same metric: keep the clearer one, remove the echo,
  and log it as `restatement`. Never merge their numbers — if the two give *different*
  numbers, that is a FLAG (a contradiction the author must resolve), not a removal.

## Verbose Phrasing With Exact Equivalents

- "at this point in time" → "now"
- "in the event that" → "if"
- "a large number of" → "many"
- "due to the fact that" → "because"
- "has the ability to" → "can"
- "in the near future" → "soon" (only if the doc gives no specific date; a date is sacred)

## Looks Like Slop But KEEP

Not everything soft is removable. These carry information — leave them:

- "We are not yet confident this generalizes beyond the pilot region." — a real, scoped
  uncertainty. KEEP.
- "Roughly 200ms, pending load testing." — "roughly" and "pending" are caveats on a
  number. KEEP both the number and the caveat.
- "Historically, this approach failed for sparse users." — "historically" anchors a
  factual claim. KEEP.
