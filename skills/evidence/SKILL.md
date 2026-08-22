---
name: evidence
description: The provenance contract for teaching material. Use whenever answering about the bound subject or producing notes, lesson plans, questions, summaries, slides, handouts or artifacts, whether the material comes from the dl-engine corpus, from a web search, or from both. Governs how syllabus, internet and inference are told apart in the output.
---

# Provenance rules

A teacher is not asking whether something is TRUE. They are asking whether it is
**on the syllabus**, and those are different questions with different
consequences. A fact from the corpus is examinable. A fact from the internet is
enrichment: useful in the room, and dangerous the moment a student writes it in a
paper believing it was taught. So the origin of a claim is not metadata about the
answer. For this reader it is part of the answer.

Where the standard comes from: dl-engine's own Atlas indexer discards any
proposed link whose supporting quote is not found character for character in the
source lesson. Output held to anything looser is output the corpus itself would
have rejected.

## The three sources, and they never blur

| Source | What it is | How it is marked |
| --- | --- | --- |
| **Syllabus** | The dl-engine corpus: lesson markdown, concept cards, connection edges. What the student is actually taught. | `` (`lesson-slug`) ``, plus `:142` when it came from a search |
| **Web** | Anything from `WebSearch` or `WebFetch`. | `(web: domain.com, YYYY-MM-DD)` |
| **Inference** | Your own reasoning: joining two lessons, filling a gap, extending a definition. | `Inference:` at the head of the sentence |

Web search is allowed and often useful: a worked example the textbook omits, a
recent figure, an explanation pitched at a different level. Use it freely when it
helps. What is never allowed is letting it arrive **unmarked**, because unmarked
it reads as syllabus.

**No sentence carries two sources.** If a claim is half corpus and half web,
split it into two sentences and mark each. A paragraph that mixes them without
markers is the failure this contract exists to prevent, and it is the easiest one
to commit, because mixed prose reads more smoothly than marked prose. Smoothness
is not the goal here.

## Rules

0. **Name the subject, every call.** Each corpus tool takes `subject`, and one
   connector reaches every subject the teacher has been assigned. Take the slug
   from the folder's `CLAUDE.md` or from what the teacher said; if neither names
   one, run `list_subjects` and ask. Never guess. Each tool echoes the subject it
   read in its first line, so check that line rather than trusting the argument
   you sent. A well-formed citation from the wrong subject is the one error this
   whole contract cannot catch.

1. **Search the corpus before the web, always.** Not as etiquette: a web answer
   found first will shape how you read the lesson afterwards, and you will start
   seeing the syllabus agree with it. Establish what this subject teaches, then
   go outside if a gap remains. Say what the gap was.

2. **Cite a lesson slug for every syllabus claim.** With the 1-based line number
   when it came from `corpus_search`: `` (`photosynthesis-light-reactions`:142) ``.
   From `corpus_read` or `corpus_index`, the slug alone is enough.

3. **Cite a domain and a date for every web claim.** `(web: who.int, 2026-08-22)`.
   The date matters because the page can change and the teacher may check it
   months later. A search result summary is not a source: open the page before
   citing it, or say you are quoting a search snippet.

4. **A quote is the source's characters.** Copy them exactly, including
   punctuation, capitalisation and the original Sinhala. Never present a
   paraphrase, a translation or a tidied line inside quote marks. Translate
   outside the quote and mark it as a translation.

5. **Label inference and keep it.** Connecting two lessons or extending a
   definition is useful work. Say `Inference:` and state it. Do not delete it,
   and do not let it pass as sourced.

6. **Never characterise a lesson you did not read.** A slug, a title and a
   concept card summary are not the lesson. Before saying what a lesson covers,
   argues or omits, `corpus_read` it.

7. **On edges, prefer the quote over the claim.** An edge's `type` and `why` are
   model-generated claims about the corpus. Its `evidence` quote is verified
   source text. When a link is load-bearing, fetch it with
   `corpus_links evidence=true` and cite the quote, not the `why`.

8. **Edge confidence is two buckets, not a scale.** 95 means two lessons
   independently witnessed the link, 60 means one did. Report it as "one witness"
   or "both lessons", never as a percentage, and never reason about the gap
   between two numbers. An edge with no confidence predates the Atlas pass: say
   the agreement is unknown rather than assuming a bucket.

9. **Say plainly when the syllabus does not cover it.** That sentence is often
   the single most useful thing in the answer, because it tells the teacher what
   not to examine. Say it even when the web answered well.

## Shape of a cited answer

> Enzymes lower activation energy (`enzyme-action`).
> "එන්සයිම මගින් සක්‍රීයන ශක්තිය අඩු කරයි" (`enzyme-action`:88)
> The syllabus does not give a numerical example of the effect.
> Induced fit is the model most textbooks now use for the same idea (web: britannica.com, 2026-08-22). Not in this subject's corpus.
> Inference: this is why the temperature curve in `metabolic-rate` peaks and then falls, though neither lesson states the link.

One search hit is one witness. Before asserting something as what the corpus
says, check whether the phrasing you searched is the only phrasing the textbook
uses.

## In a file, a handout or an artifact

Everything above still holds, and gains a visual job: a teacher scanning a page
must be able to see where a line came from **without reading the citation**.
Markers alone fail here, because at a glance a page of prose looks uniformly
authoritative.

- **Open with a one-line legend** naming the three sources, so the reader learns
  the code before meeting it.
- **Give each source a consistent visual treatment** and keep it identical
  throughout: syllabus as plain body text, web in a tinted or bordered aside,
  inference in an outlined or italic block. In Markdown, blockquote and label.
  Colour alone is never the signal, since a page gets printed in black and white
  and handed to a class.
- **Close with two separate lists**, never one merged "Sources": the lessons
  read, by slug, and the pages fetched, by URL with the date. Merging them undoes
  in the bibliography exactly what the markers achieved in the body.
- **If the artifact is student-facing, say so and cut the web material**, or keep
  it under a heading that says it is beyond the syllabus. A student cannot be
  expected to hold the distinction the teacher is holding.
