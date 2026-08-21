# Design: `lossless-doc-compress` skill

## Summary

A standalone Agent Skill that losslessly compresses a design document. It removes
only provable redundancy — filler, hedging, LLM-slop, empty transitions, restated
content — while never dropping a fact, number, decision, caveat, constraint, or named
entity. Anything that requires editorial judgment is **flagged for the author, not cut**.

The skill ships three artifacts: the compressed document, a categorized removal log
(the "what was removed"), and a shareable scorecard (the "final results" — before/after
word count, percent reduction, fidelity confirmation, flag count).

It is the third sibling in the `MLSystemDesign` skills family, alongside
`ml-system-design-review` (grades substance) and `ai-stage-gate` (decides Go/Kill).
Its single job is length: *review grades substance; compress trims length without
touching substance.*

## Motivation

From the framework authors' guidance to anyone preparing documentation and design docs
(paraphrased from the source note that prompted this skill):

1. An LLM-written draft is not a problem **if** the author read it, corrected it, and
   supplied context — i.e. delegated the *editing*, not the *thinking*. Delegating the
   thinking usually produces nonsense.
2. Running a doc through "compress this as much as possible without losing any
   information" typically halves it and clears out slop — better for the author,
   reviewers, the team, and the models that later read it.

This skill operationalizes principle 2 while respecting principle 1: it edits for
concision and never paraphrases in a way that could drift meaning or "think" on the
author's behalf.

## Goals

- Losslessly reduce word count of a design doc (or any prose/markdown document).
- Guarantee no information loss: facts, numbers, decisions, caveats, constraints, and
  named entities are preserved verbatim in meaning.
- Show the author exactly what was removed, categorized, so cuts are auditable and
  reversible.
- Surface judgment calls (possible duplication, tangents, weak examples) as flags with
  recommendations, never as silent removals.
- Produce a shareable scorecard consistent with the house scorecard-first convention.

## Antigoals

- **Not** a summarizer or paraphraser. No rewriting that could change meaning.
- **Not** a substance reviewer. It does not grade design quality, find leakage, or make
  Go/Kill decisions — that is what the sibling skills do.
- **Not** a repo auditor. Doc-only; it does not inspect code or compare doc-to-repo.
- **Not** domain-locked. It works on any design doc, not only ML docs (though it gives
  ML design content extra protective attention).

## Scope and positioning

- **Standalone, doc-only, domain-agnostic.** Input is a document (pasted, linked, or a
  file path to markdown/prose). No repo dependency.
- **Cross-linked, not coupled.** Docs reference the two siblings with the frame:
  "`ml-system-design-review` grades substance; `ai-stage-gate` decides Go/Kill;
  `lossless-doc-compress` trims length without touching substance." No runtime
  dependency on either.
- **Framework attribution.** Credit "Kravchenko and Babushkin" / "the authors" for the
  framework, matching the sibling skills. Reuse the small skill/book attribution line on
  the scorecard.

## Core contract: the two-bucket fidelity model

Every span of text the skill considers falls into exactly one of three fates:

1. **KEEP** — substance. Facts, numbers, metrics, decisions, caveats, constraints,
   requirements, named entities, code, tables, and their meaning. Untouched.
2. **REMOVE (safe, automatic)** — provable redundancy with no information content:
   - Filler and empty transitions ("it is important to note that", "as we discussed
     above", "in order to").
   - Hedging that adds no qualification ("it seems that", "arguably", "basically").
   - LLM-slop intros/outros ("In today's fast-paced world…", "In conclusion, …").
   - Restated content: a sentence or paragraph that repeats information already stated,
     with nothing new.
   - Verbose phrasings with an exact shorter equivalent ("at this point in time" → "now").
3. **FLAG (author decides, never auto-removed)** — anything requiring judgment:
   - A section that appears to duplicate another (recommend merge, don't merge).
   - A tangent, anecdote, or example that may be low-value (recommend cut, leave in).
   - Content whose removal *might* lose nuance.

**Hard rule (stated at the top of `SKILL.md`): when in doubt, flag — don't cut.**
Paraphrase is disallowed unless it is a strict shortening carrying identical
information. The skill never "thinks for" the author.

### Severity framing (mirrors sibling skills' Severity sections)

- A REMOVE that could plausibly carry information is a **contract violation** — it must
  be a FLAG instead.
- Over-flagging is acceptable; under-flagging (silently cutting something meaningful) is
  the failure mode the skill is designed to prevent.

## Output package (three artifacts)

1. **Compressed document.** The full tightened document. All structure preserved:
   headings, tables, code blocks, lists, and ordering. Flagged items remain present,
   marked inline with a lightweight, greppable marker (e.g. an HTML comment
   `<!-- FLAG: … -->`) so the rendered doc stays clean but flags are findable.

2. **Removal log** (`<slug>-removal-log.md`). The "what was removed":
   - A per-category breakdown (filler / hedging / slop / restatement / verbose-phrasing)
     with a count and one representative `before → after` per category.
   - The full list of flags with location and recommendation.
   - Written so a reviewer can audit or reverse any cut.

3. **Scorecard** (`<slug>-compression-scorecard.md`). The "final results": a
   screenshot-friendly, shareable markdown block matching house convention:
   - The small skill/book attribution line at the top (unmodified, small text).
   - Verdict line reframed for compression, e.g.
     **`Tightened −51% · lossless ✓ · 3 flags`**.
   - Before/after word count and percent reduction.
   - Fidelity confirmation (lossless — no facts/numbers/decisions removed).
   - Flag count and the single highest-value structural suggestion ("top flag").
   - A one-line takeaway phrased for internal sharing, not marketing.

   Saved as a standalone file so it can be pasted into Slack/PR/wiki. Not committed
   (it is per-run output). The saved paths are named at the end of the run.

## File layout

```
skills/lossless-doc-compress/
  SKILL.md                      # activation, hard fidelity rule, two-bucket model,
                                # severity, reference routing, output contract
  references/
    compression-workflow.md     # procedure: pass order, how to measure word count,
                                # the KEEP/REMOVE/FLAG decision per span, when to stop
    removal-taxonomy.md         # the removable categories + the non-negotiable
                                # "never remove" list (facts, numbers, decisions,
                                # caveats, constraints, named entities, code)
    slop-and-hedging.md         # concrete LLM-slop / filler / hedging patterns,
                                # each with a before → after example
    fidelity-rules.md           # the lossless guarantee, flag-don't-cut rule,
                                # the no-paraphrase boundary, contract-violation defn
    output-templates.md         # compressed-doc marker convention, removal-log
                                # template, scorecard template + save rules
```

Five reference files, consistent with the siblings' depth (7 references each). All
content is domain-agnostic; a short note in `removal-taxonomy.md` directs extra
protective attention to ML design content (metrics, baselines, validation caveats,
decisions) without narrowing the skill.

## SKILL.md structure (mirrors sibling conventions)

- **Frontmatter:** `name: lossless-doc-compress`, a `description` written for activation
  (compress/tighten/shorten a design doc without losing information), and `metadata`
  with `version`, `scope`, and the book `reference` URL — same shape as the siblings.
- **Activation:** when the user asks to compress, tighten, shorten, or de-slop a design
  doc, PRD, RFC, architecture note, or similar prose. Explicitly *not* for substance
  review (route to `ml-system-design-review`), gate decisions (route to `ai-stage-gate`),
  code review, or free summarization.
- **Mandatory first step:** confirm the input document and its current word count before
  compressing; note the fidelity guarantee up front.
- **The hard fidelity rule** and the two-bucket model, stated near the top.
- **Severity** framing (contract violation vs acceptable over-flagging).
- **Reference routing** table (which reference to load when).
- **Default output**: the three-artifact package, formatted per `output-templates.md`.

## Ship path

- Add `skills/lossless-doc-compress/` (SKILL.md + 5 references).
- Add a `## lossless-doc-compress` section to `skills/README.md` matching the existing
  Install / Use pattern (`npx skills add ML-SystemDesign/MLSystemDesign`).
- Commit on branch `skill/lossless-doc-compress` (already branched from `origin/main`).
- Open a PR into `ML-SystemDesign/MLSystemDesign` and merge — the same path
  `ai-stage-gate` took (PR #23) and `ml-system-design-review` before it.

## Success criteria

- Running the skill on a real design doc produces all three artifacts.
- Word count drops materially (the source note cites ~2× on slop-heavy drafts) with a
  verifiable lossless guarantee.
- The removal log accounts for the delta: `before − after` words are explained by the
  logged categories.
- No fact, number, decision, caveat, or constraint present in the source is absent from
  the compressed doc; anything ambiguous appears as a flag, not a silent cut.
- Structure and depth are consistent with the two sibling skills so it reads as a
  first-class member of the family.
