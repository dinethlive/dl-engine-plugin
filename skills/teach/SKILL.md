---
name: teach
description: Prepare one concept or lesson to teach from the bound dl-engine subject corpus. Use when the user wants to prep, plan, outline or build material for a class, lesson, topic or concept, wants a teaching brief or lesson notes, asks what the corpus says about a concept before teaching it, or asks what students need to know first.
---

# Teach a concept

Produce a cited teaching brief for one concept. The corpus is Sinhala with English
technical terms inline, so every step searches both languages.

## Method

Run all four steps before writing anything. Do not draft from step 1 hits.

**1. corpus_search the concept, then each variant as a separate call.**
Matching is literal substring, case-insensitive, not semantic, so the stem wins:
`enzyme` finds `enzymes`, but `enzymes` never finds `enzyme`, `cell wall` never
finds `cell-wall`, and no English string finds the Sinhala term. Search the
shortest string a textbook prints, then each variant separately: Sinhala term,
hyphenated form, adjective form, abbreviation and expansion. Case needs no
variant. Keep `context: 2` to judge whether a hit is substantive; raise `limit`
when hits pile into one lesson.

**2. corpus_read every lesson whose hits look substantive** (a heading, a
definition, a worked treatment) and skip the ones with a single passing mention.
Reading is the only thing that licenses describing a lesson. Name the skipped ones
as passing mentions rather than dropping them.

**3. corpus_index format='full' slug=<slug> for those lessons' cards.** One call
per lesson you settled on, and nothing else comes back. Never pull the whole
subject full: ~894 tokens a card, and you want two or three of them. The reply is
still the ordinary paged envelope, so a slug that matches nothing returns zero
items rather than an error, which is your signal that step 1 handed you a slug
that does not exist. What each field is for:
- `keyConcepts` is vocabulary already written down. Use these terms, do not coin
  your own.
- `prerequisites` is free-text concept names, NOT slugs, so a prerequisite is only
  teachable once step 1 or step 4 finds the lesson that defines it.
- `examRelevance` is what examiners do with the concept. This drives the lesson.

**4. corpus_links in both directions, to get the teaching order and to find where a
half-explained idea is explained properly.**
- Outgoing: `corpus_links from=<slug>`. What this lesson feeds.
- Incoming: `corpus_links to=<slug>`. What comes BEFORE it, which is the
  prerequisite direction and the one that sets teaching order. Walk it backwards a
  step at a time to build the chain: `to=` the answer you just got, and stop when
  a lesson has nothing arriving.
- Both at once asks only whether one specific pair is linked, which is cheaper
  than reading either list when that is the actual question.
- Direction, easy to invert: `prerequisite` A -> B means A must be understood
  before B. `builds-on` is never stored, it is flipped into `prerequisite` at write
  time. `applied-in` A -> B means A's ideas get used in B, which is usually where a
  thin explanation becomes a full one. `contrasts-with` and `shares-concept` are
  lateral.
- `why` and `confidence` come free. Leave `evidence: false` until you are about to
  quote an edge, then turn it on for that one call: the quote is verbatim from the
  FROM lesson's markdown.

## The brief

Open with the concept, the lessons it actually lives in (slug + title + order), and
one line on which lesson is the primary treatment. Then:

### What the corpus says
The substance, in the corpus's own words: the defining sentence quoted verbatim,
plus the worked treatment, examples and figures each lesson gives. Arrange in
teaching order, not search order. Where two lessons treat it differently, show both
and say which is fuller. Cite `slug` for anything read, `slug:LINE` for anything a
search surfaced.

### Vocabulary
A bilingual table: Sinhala term | English term | definition, sourced from
`keyConcepts` with the card's own definitions. Add a row only for a term the lesson
body defines and the card missed, cited `slug:LINE`. If the corpus gives a term in
only one language, leave the other cell empty and say so. Do not supply a
translation the corpus never printed.

### What a student must already know
From card `prerequisites` plus incoming `prerequisite` edges. One row each: the
prerequisite, the lesson that teaches it (`slug`) or "not in this corpus", and the
one thing the student needs out of it. End with the order to teach them in.

### How it is examined
Each `examRelevance` item, then what it demands of the lesson: what to drill, what
the student must be able to produce unaided. If `examRelevance` is empty for every
card, say that plainly instead of inventing exam formats.

### Gaps
What this concept clearly rests on that the corpus never defines: terms used but
never defined, prerequisites with no owning lesson, edges pointing at a lesson that
does not really treat the idea, examRelevance demanding a skill no lesson teaches.
Each gap: what is missing, where the hole shows (`slug:LINE`), what the teacher must
supply themselves. This is the most valuable section and the easiest to leave empty
by shrugging. If you found no gaps, list the searches you ran that came back empty.

## Evidence contract

- Every claim carries its source: `lesson-slug` when read, `lesson-slug:LINE` when
  it came from corpus_search (line numbers are 1-based, as returned).
- A quote is the source's own characters. Never tidy Sinhala, never translate inside
  the quote marks, never present a paraphrase as a quote. Translations go outside the
  quote, labelled as translation.
- Anything not citable is kept and labelled `Inference:`. Do not silently assert it
  and do not silently drop it.
- Never characterise a lesson that was not read with corpus_read.
- No figure, year, count or exam detail that did not come from a tool call.
- No em dashes.

## Saving

The user works one folder per lesson. After presenting the brief, offer once to save
it as `./<concept>-brief.md` in the current folder, and write it only on a yes, with
every citation intact.
