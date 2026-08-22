# Removal Taxonomy

What is safe to remove, what is never removed, and where the line to a FLAG sits. The
five REMOVE categories below are also the category labels used in the removal log — use
these exact names.

## The Five REMOVE Categories

Removable only when the span carries no information (see `fidelity-rules.md`).

- **filler** — words and empty transitions that add no meaning: "it is important to note
  that", "as mentioned above", "needless to say", "in order to", "the fact that".
- **hedging** — qualifiers that qualify nothing: "basically", "essentially", "arguably",
  "it seems that", "sort of", "quite". (A hedge that carries a real uncertainty — "we are
  not yet confident this generalizes" — is a KEEP, not a hedge to cut.)
- **slop** — generic LLM scaffolding with no document-specific content: "In today's
  fast-paced world…", "It is worth mentioning that…", "In conclusion, …", ceremonial
  intros and outros that restate the obvious.
- **restatement** — a sentence, clause, or paragraph that repeats information already
  stated, adding nothing new. Keep the first, clearest statement; remove the echo.
- **verbose-phrasing** — a long phrasing with an exact shorter equivalent: "at this point
  in time" → "now", "a large number of" → "many", "in the event that" → "if". The
  shorter form must carry identical information.

## Never Remove (Sacred)

Restated from `fidelity-rules.md` because it is the list you check every time:

- Facts and claims.
- Numbers, metrics, thresholds, dates, versions.
- Decisions and their stated reasons.
- Caveats, risks, limitations, open questions.
- Constraints and requirements.
- Named entities (people, teams, systems, products, datasets, endpoints).
- Code, configuration, commands, and their values.
- Table data.

## REMOVE vs FLAG

- REMOVE (redundancy): "It is important to note that the p95 latency is 200ms." →
  "p95 latency is 200ms." The number survives untouched; only filler left.
- FLAG (judgment): a three-paragraph vendor anecdote in a section on latency. It *might*
  be low value, but cutting it is the author's call. Leave it; flag it with a recommend.
- FLAG (possible duplication): §4.2's metric list looks like §2.1's. Do not merge — flag
  it and let the author confirm they are the same before either is touched.

## Extra Care: ML Design Content

This skill is domain-agnostic, but ML design docs concentrate meaning in places that look
skippable. Give extra protective attention — bias toward KEEP or FLAG — to:

- Metric definitions, offline/online mappings, and expected directions.
- Baselines and the reasons a model beats or loses to them.
- Validation and leakage caveats (split logic, time/group boundaries, golden sets).
- Decisions with stated trade-offs.

Numbers and named trade-offs here are exactly the content a careless compressor destroys.
