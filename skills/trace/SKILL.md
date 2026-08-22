---
name: trace
description: Trace a past paper question back to where the syllabus teaches it. Use when the teacher names a paper question ("2023 Part II question 4", or a handle like al-ict-2025-part-i/q7), asks where a question comes from, what a question is really testing, which lesson covers it, what a student needed to know before attempting it, or wants past paper questions found on a topic and tied to the corpus.
argument-hint: [question handle, or the paper and number]
---

# Trace a question to the syllabus

Start at a real past paper question, end at the lessons that teach it, cite both.
A question and a lesson are two kinds of writing about one idea: the lesson
explains it, the question hides it inside a scenario and asks the student to do
something with it. Getting from one to the other is the work.

## The one rule everything else serves

**A question is its page images and nothing else.** dl-engine stores no
transcript, no options table and no sub-part tree, because a question can be a
circuit diagram, a match-the-following, a code listing, or an MCQ whose five
options are themselves figures. Any text rendering of those fails on some
variant, and it fails silently, by producing plausible prose about something that
was never prose.

So open the pages and read them. `topic` and `keywords` exist to FIND a question
and for nothing else. A trace built on a label instead of on the page is an
answer about a summary of a question: fluent, checkable, and quite possibly about
a different idea than the one printed.

This skill runs in your own session rather than forked, for the same reason. A
forked run would read the images and hand back a description of them, which is
exactly the failure the page images exist to prevent.

## The four paper tools

| Tool | What it answers |
| :--- | :--- |
| `list_papers` | Which papers this key can open. Narrowed by `year`, `subject` and `origin`. |
| `paper_contents` | One paper end to end: cover instructions, section rules, and every question's handle, kind, marks and topic. The only place the printed rules appear. |
| `find_questions` | Which questions carry a term, across papers. Matches the `topic` label and the `keywords` ONLY, and caps at twenty rows. |
| `open_question` | The pages, as images. The only tool that shows you a question. |

Names may carry the connector's prefix. If the paper tools are missing entirely,
the key was minted before the paper surface existed: see the end of this file.

**Papers are not reached through a subject.** Each row prints a subject tag or
says it has none, and the tag grants nothing: a paper is assigned one teacher at
a time, so the papers you can open are not the papers of the subjects you can
read. Filtering `list_papers` by subject therefore hides every untagged paper,
which is why an empty filtered list says something different from an empty
library. Drop the filter before concluding a paper is not there.

The tag is still worth reading, because it is the hinge between the two halves of
this skill: it names the subject slug to carry into the corpus tools. A paper
with no tag means the corpus side starts from the teacher's own `CLAUDE.md`
instead.

## Step 1: settle the handle before you open anything

A question's address is `<paper-slug>/q<number>`, for example
`al-ict-2025-part-i/q7`. When this skill was invoked with an argument, that
argument is the question; otherwise it is whatever the teacher just said.

- **The teacher gave a handle.** Use it.
- **The teacher named it in words** ("2023 Part II, question 4"). `list_papers`
  with the year, read the titles and the `part` field, then `paper_contents` on
  the slug you picked. Never assemble a slug out of the words: a slug is an
  ingest identity, not a naming convention you can predict.
- **The teacher described it** ("that MCQ about normalisation").
  `find_questions`, then confirm which hit is meant before spending a call on its
  images.

Never guess a number. `open_question` will happily return the pages of whatever
question that number does name, and everything downstream will be careful, cited
and about the wrong question.

The check that costs nothing: **the page usually prints its own number.** When it
does and it disagrees with the handle you asked for, stop and say so.

`paper_contents` earns its call here for a second reason. It carries the section
rules and the cover instructions, and those are what make the marks legible:
"answer any four of these" is why question 7 is worth 15, and it tells the
teacher what a full answer was expected to look like.

## Step 2: read the pages

`open_question` with the handle.

- **Read every page, in the order returned.** A structured question's second page
  can open mid-sentence, so a page skipped is not a detail missed, it is a
  misreading.
- **Empty `pages` means object storage is not configured.** Report it as "no
  pages stored", never as an error and never as an empty question, and stop the
  trace there. Do not quietly fall back to `topic` and `keywords` and carry on as
  though you had seen it.
- **Write down, before searching anything:** what the question gives, what it
  asks the student to DO (the verb: define, calculate, draw, convert, compare,
  justify), the marks, and the kind (MCQ, STRUCTURED, ESSAY).

The verb matters more than the nouns for what comes next. A lesson that names the
term but never performs the operation is not where the question is taught. It is
where the word is printed.

## Step 3: the hard part, and it is a vocabulary problem

**A paper question and a lesson rarely share vocabulary, so one literal search
usually misses, and a miss looks exactly like "the syllabus does not cover
this".** That confusion is the failure mode of this whole skill.

Why they miss:

- `corpus_search` is literal substring matching, case-insensitive, not semantic.
  `enzyme` finds `enzymes`, but `enzymes` never finds `enzyme`, `cell wall` never
  finds `cell-wall`, and no English string ever finds the Sinhala term.
- An exam question is written to TEST, so it withholds the syllabus term rather
  than printing it. It gives you a school library, a shop's stock table, a
  household circuit, and the word the corpus uses appears nowhere on the page.
- The paper may print the English term where the lesson prints the Sinhala one,
  or the reverse. Both are live in every subject here.
- `find_questions` is no safety net. It matches the `topic` label by substring
  and `keywords` only as whole values, and plenty of questions carry neither, so
  a miss there means "nobody labelled it that" and not "no paper asks it". When
  it misses, list a likely paper with `paper_contents` and open the questions
  that look right.

**Every corpus call takes `subject`.** Take the slug from the paper's subject tag
or from the folder's `CLAUDE.md`, and when those two disagree, say so and ask
rather than picking one. A trace that reads the right question against the wrong
subject produces perfect citations to lessons nobody sitting that paper was ever
taught.

What to do, in order, and do not stop at the first empty result.

1. **Search the question's objects, not its sentences.** From the page, list the
   technical nouns actually printed, diagram labels and axis names included, the
   operation the verb demands, and the artifacts shown: a truth table, a gate
   symbol, a flowchart, a SQL keyword, a unit, a notation. Those are your search
   terms. The sentences are scenario and will match nothing.
2. **Name the concept yourself, and label it.** Going from "a library keeps one
   row per borrowed book" to "normalisation" is your reasoning, not a citation.
   Mark it `Inference:` and keep it marked until a lesson you have READ confirms
   it in its own words. This is where a trace goes wrong most often and most
   invisibly, because the guess is usually nearly right.
3. **One term per call, shortest stem first,** each variant its own call: the
   Sinhala term, the hyphenated form, the plural, the abbreviation and its
   expansion. Keep `context: 2`, so you can judge whether a hit is substantive.
4. **When the searches come back empty, stop searching text and read the map.**
   `corpus_index` at `format: 'concise'`, paged, and read the titles, tags and
   summaries. This is the move that breaks the vocabulary problem: the index is
   written in the SYLLABUS's words, and you cannot search for a word you cannot
   guess, but you can recognise it going past. Pick the two or three candidate
   lessons, then `corpus_index format='full' slug=<slug>` and take `keyConcepts`
   verbatim. Those are the exact strings the corpus uses. Search those.
5. **`examRelevance` on a full card is the bridge from the exam side,** because
   it is written about what examiners do with the concept. A card whose
   `examRelevance` describes what this question is doing is a strong candidate
   even when no search hit ever pointed at that lesson.
6. **Use sibling questions as a source of spellings.** Run `find_questions` on
   the terms you have settled on, across years. Other questions' `topic` and
   `keywords` are labels someone wrote after looking at a page: useless as
   evidence, genuinely useful as the words a labeller thought this family of
   question was about. Feed them back into step 3.
7. **Then walk the map backwards.** `corpus_links to=<slug>` on the lesson you
   landed on. A question usually tests the last lesson in a chain, and what the
   student actually needed sits upstream of it. Walk it a step at a time until a
   lesson has nothing arriving.

**Only reading licenses a claim.** `corpus_read` every candidate before saying it
teaches the question. A search hit says the term sits on that line and nothing
about whether the lesson performs the operation the question demands.

**"Not found" and "not taught" are different answers.** You may give the second
only after you have read the concept index rather than only searched it, tried
both languages, and tried the vocabulary the concept cards themselves use. Short
of that, say "not found under the terms I tried" and list them. A teacher acts on
those two sentences differently: one sends them back with a better term, the
other tells them what not to examine.

## The answer

**The question, as printed.** Handle, paper title, year, part, kind, marks, and
the section rule that explains the marks. One or two sentences on what it asks.
Where a claim of yours rests on a figure rather than on printed words, say it is
your reading of the figure.

**What it tests.** The syllabus terms behind the scenario, with `Inference:` on
every one the corpus did not confirm in those words.

**Where the syllabus teaches it.** The lessons in teaching order, `slug` for
anything read and `slug:LINE` for anything a search surfaced, with the defining
sentence quoted verbatim in the corpus's own characters. Say which lesson is the
primary treatment and which are supporting.

**What a student needed first.** Incoming `prerequisite` edges plus card
`prerequisites`. One row each: the prerequisite, the lesson that teaches it or
"not in this corpus", and the one thing the student needed out of it.

**The gap.** What this question demands that the corpus never supplies: the
operation no lesson performs, the term used and never defined, the answer format
no `examRelevance` mentions. This is what the teacher takes into the room, and it
is the section easiest to leave empty by shrugging.

**Where else it has been asked.** Sibling questions by handle and year, marked as
found by label rather than by content, because you did not open their pages.
Offer to open one.

**Coverage.** Searches that returned nothing, lessons surfaced but never read.

## Never

- **Never state the answer as though the paper confirmed it.** Marking schemes
  and answer keys are deliberately not on this surface, and this key cannot reach
  one. If you work an answer out, it is `Inference:`, however sure you are, and
  the teacher must be able to tell it apart from a scheme they could rely on.
- **Never quote a figure.** Transcribing a diagram, a table or an option set into
  prose and setting it in quote marks is the exact silent failure the image-only
  design refuses to commit. Describe it, and say you are describing it.
- **Never let a paper claim and a syllabus claim share a sentence.** They are
  different sources with different consequences for a student.
- No em dashes.

## Evidence

The `evidence` skill governs this output, and a question image is one of the four
sources it names. In short: cite the handle; quote only what is printed as text,
in its own characters; describe a figure and label the description as your
reading; never present `topic` or `keywords` as what the question says; and no
answer is ever sourced. Read that skill for the full contract, and for how the
sources are kept apart on a page a teacher scans rather than reads.

## Saving

Offer once, after presenting the trace, to save it as
`./<paper-slug>-q<number>-trace.md` in the current folder, and write it only on a
yes, with every citation intact.

## When it fails

- **"This key is not scoped for that."** The corpus tools work and the paper
  tools refuse, which means the key was minted before papers were reachable. Tell
  the teacher to mint a fresh one at
  [dlengine.xyz/plugin](https://dlengine.xyz/plugin) and update `DL_ENGINE_KEY`.
  Nothing else changes, and the corpus tools keep working in the meantime.
- **`list_papers` comes back empty and nothing was filtered.** No paper has been
  assigned to this account. Publishing a paper reaches nobody on its own and a
  subject tag grants nothing, so an admin assigns it. There is nothing to
  configure here, and there is nothing to trace until they do.
- **"No paper ... is in this key's library."** Same answer for a paper that does
  not exist and one this account was never assigned, deliberately, so a key
  cannot map the library by guessing. `list_papers` shows what is actually there.
- **"That is not a question handle."** The handle was assembled by hand. Take one
  from `paper_contents` or `find_questions` instead.
- **No page images are stored.** Object storage is not configured on that
  deployment. The question exists, the key can reach it, and there is nothing to
  read. Say so, and stop: retrying changes nothing and there is no text version
  to fall back on.
