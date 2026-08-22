---
name: evidence
description: The citation contract for every answer drawn from the dl-engine corpus. Use whenever answering a question about the bound subject, producing teaching material (notes, lesson plans, questions, summaries, slides) from corpus_search / corpus_read / corpus_index / corpus_links, or checking, verifying or fact-checking a claim against the corpus.
---

# Evidence rules

Where this comes from: dl-engine's Atlas indexer requires every proposed connection to carry a quote copied verbatim from the source lesson, and discards any link whose quote is not found character-for-character in that lesson. That is the project's own standard of proof, so output held to anything looser is output the corpus itself would have rejected.

## Rules

1. **Cite a lesson slug for every claim.** When the claim came from `corpus_search`, add the 1-based line number the tool returned: `(photosynthesis-light-reactions:142)`. From `corpus_read` or `corpus_index`, the slug alone is enough.

2. **A quote is the source's characters.** Copy them exactly, including punctuation, capitalisation and the original Sinhala. Never present a paraphrase, a translation or a tidied line inside quote marks. Translate outside the quote if the teacher needs it, and mark it as a translation.

3. **Label inference as inference and keep it.** Connecting two lessons, filling a gap, or extending a definition is useful work. Say `Inference:` and state it. Do not delete it, and do not let it pass as sourced.

4. **Never characterise a lesson you did not read.** A slug, a title and a concept card summary are not the lesson. If you are going to say what a lesson covers, argues or omits, call `corpus_read` on it first.

5. **On edges, prefer the quote over the claim.** An edge's `type` and `why` are model-generated claims about the corpus. Its `evidence` quote is verified source text. When the link is load-bearing for what you are producing, fetch it with `corpus_links evidence=true` and cite the quote, not the `why`.

6. **Edge confidence is two buckets, not a scale.** 95 means two lessons independently witnessed the link, 60 means one did. Report it as "one witness" or "both lessons". Never print it as a percentage and never reason about the gap between two numbers. An edge that shows no confidence at all predates the Atlas pass: say the agreement is unknown rather than assuming a bucket.

7. **Say when the corpus does not answer.** If searching and reading do not turn up the answer, state plainly that this subject's corpus does not cover it. Do not fill the gap from general knowledge. The point of working from this corpus is that the answer is this corpus's answer. If you add outside knowledge because the teacher asked for it, fence it off and name it as outside the corpus.

## Shape of a cited answer

> Enzymes lower activation energy (`enzyme-action`).
> "එන්සයිම මගින් සක්‍රීයන ශක්තිය අඩු කරයි" (`enzyme-action:88`)
> Inference: this is why the temperature curve in `metabolic-rate` peaks and then falls, though neither lesson states the link.

One search hit is one witness. Before asserting something as what the corpus says, check whether the phrasing you searched is the only phrasing the textbook uses.
