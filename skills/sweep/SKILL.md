---
name: sweep
description: Sweep the whole bound subject for one theme and report where it lives, how the syllabus builds it, and how the lessons connect. Use when the teacher asks a wide question across the subject rather than about one lesson, such as "where does this subject deal with X", "which lessons cover X", "how does X develop across the syllabus", or when planning a topic that spans several lessons.
context: fork
background: false
argument-hint: <theme to sweep for>
---

# Corpus sweep

Wide read across the bound subject for one theme. Answers: where does this
subject deal with X, and how does it hang together.

This runs forked because a sweep reads a lot and returns a little. Dozens of
search hits, several full lessons and a page of edges stay in this context, and
only the finished findings go back. It runs in the foreground, not background,
because the teacher asked in order to use the answer now.

## The theme

The theme is `$ARGUMENTS`.

A forked run cannot see the conversation, so `$ARGUMENTS` is the only statement
of what to sweep for. If it is empty, or is too thin to search (no noun, one
stopword, a bare pronoun), do not guess and do not sweep. Return one line asking
for the theme and stop.

## Method

Do these in order. Each step decides what the next step is allowed to spend.

1. **Shape first.** `corpus_index` with `format: 'concise'`, paging until you
   have every card. Concise, because full is roughly thirteen times the tokens
   and this step only narrows. Read titles, tags and summaries, note each card's
   `order`, and mark candidate lessons.

2. **Search the theme and its variants.** `corpus_search` is substring matching,
   not semantic, so one query is never enough. Run separate queries for: the
   English technical term as a textbook prints it, the Sinhala term, singular and
   plural, abbreviation and expansion, and any synonym the concept cards used.
   Use `limit: 50` for a broad term, `context: 2`. Collect lesson slugs with
   their 1-based line numbers and keep the matching lines, they are your quotes.

3. **Read only what earned it.** `corpus_read` the lessons that either carry many
   hits or whose concept card says the lesson is about the theme. Typically five
   to ten lessons. If more qualify than you can read, rank by hit density, read
   the top ones, and list the rest as unread rather than describing them.

4. **Then the connections.** `corpus_links` with `from` set to each lesson you
   read, `evidence: false`. Keep edges whose other end is also in your surfaced
   set, plus edges that point at a lesson you did not expect, those are findings.
   Turn `evidence: true` on only for an edge you are about to quote.

Optional, only for the two or three spine lessons: `corpus_index` with
`format: 'full'` and `slug` set to that lesson gives `examRelevance`, the field
worth the most to a teacher. One call per spine lesson, no paging. Zero items
back means the slug is wrong, so drop `examRelevance` for that lesson rather than
inventing it.

## The report

Return the report itself as your answer, not a path. Do not write files unless
`$ARGUMENTS` asked for one.

- **Answer in one line.** Where this theme lives in this subject.
- **Map.** A table: lesson slug, title, syllabus `order`, role (spine,
  supporting, or mention only), first hit line number.
- **How the subject builds it.** Walk the surfaced lessons in `order`, showing
  what each one adds to the theme. Cite every step.
- **The spine.** The lessons that carry the theme, one verbatim quote each with
  its slug and line.
- **Mentions only.** Lessons where the term appears in passing. Slug and line,
  and nothing else. You did not read them, so you do not get to say what they do.
- **How it hangs together.** Edges among the surfaced lessons as `from -> to`,
  with the edge type, its `why`, and its confidence. Note any expected link that
  has no edge.
- **What is missing.** What a teacher would expect on this theme that the corpus
  does not carry. Label it as inference.
- **Coverage.** Which lessons you read in full, which you only searched, and any
  query variant that returned nothing.

## Evidence rule

The Atlas indexer throws away any link whose quote is not in the source lesson
character for character. Hold this report to the same bar.

- Every claim carries a lesson slug, plus the 1-based line number when it came
  from `corpus_search`.
- A quote is the source's own characters. A paraphrase is never presented as one.
- Anything you cannot cite is kept and labelled as inference, not dropped and not
  asserted.
- Never characterise a lesson you did not read. A search hit tells you the term
  is on that line, nothing more about what the lesson teaches.
