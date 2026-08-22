---
name: audit
description: Audit the bound subject's corpus for internal defects - edges pointing at slugs no lesson has, Atlas evidence quotes that no longer appear in their source lesson, lessons in no edge at all, and prerequisite topics nothing covers. Use when asked to check corpus quality, health, integrity or consistency, to verify the connection map, or to sanity-check a subject before building lessons on it. Reports findings only, never edits.
context: fork
background: false
argument-hint: [check number or lesson slug]
---

# Corpus audit

Check the subject against itself and return a short report. Read-only.

## Scope

`$ARGUMENTS`. When it names one check or one lesson, run only that and say so at
the top of the report. Empty means audit the whole subject. A forked run cannot
see the conversation, so this is the only statement of scope you get: do not
infer a narrower one.

**This skill reports and never edits.** There is no write tool here, and a fix
does not belong on the client side anyway: dangling edges, drifted quotes and
missing cards are all produced at ingest time and are repaired by re-running the
pipeline in the dl-engine repo (`ingest`, `index`, `ingest-outline`). Hand the
teacher a finding they can act on there. Never propose patching the data.

## The subject

Every corpus tool takes `subject`, and one connector reaches all of them, so an
audit that drifts between subjects would report every lesson of one as an orphan
of the other. Fix the slug before the first call, from the folder's `CLAUDE.md`
or from `$ARGUMENTS`, and use that one slug for every call in the run. A forked
run cannot ask, so if no slug is available, say that and stop.

## Before anything: page to completion

Checks 1 and 3 are set differences. A half-fetched index makes every slug you
did not fetch look dangling, and a half-fetched edge list makes every lesson you
did not cover look orphaned. Both would be pure fabrication.

Page `corpus_index` and `corpus_links` until you have every row (compare rows
seen against the reported total). If a fetch fails or you stop early, say so in
Coverage and **drop** the affected check rather than reporting partial results.

Order of work: one full `corpus_index` (concise) plus one full `corpus_links`
feed checks 1 and 3 immediately. Then check 2 (costs a lesson read per source
lesson). Then check 4 (needs `format='full'` and a search per prerequisite).

## 1. Dangling edges

`toSlug` has no foreign key on purpose: the outline pipeline builds the
prerequisite graph before every lesson body exists, so the database accepts a
target that does not exist yet.

Build the slug set from `corpus_index`, then report every edge whose `toSlug` is
not in it. Check `fromSlug` against the same set while you are there.

Report: the edge as `fromSlug -> toSlug (type)`, and its `why` so the teacher can
judge whether the target lesson is missing or the slug is simply wrong.

Caveat to carry into Coverage: an edge may legally target another subject, and
`corpus_links` does not return the target subject id, so an unresolved `toSlug`
cannot be distinguished from a cross-subject link by tooling alone.

## 2. Evidence that does not hold

The highest-value check. The whole Atlas standard rests on these quotes.

**Only audit edges that came back carrying an evidence quote.** `corpus_links`
reports an edge's slugs, type, confidence, `why`, and its evidence when you asked
for it, and nothing else. It does not need to: an edge is quote-grounded exactly
when it has a quote, and an edge with no quote was proposed without one and is
working as designed. Reporting the quoteless ones as defects would bury the real
findings under most of the map.

At write time the stored quote is a literal slice of the `fromSlug` lesson's
markdown (the verifier stores the source's own characters, never the model's).
So the quote must still be a substring today. Procedure:

1. `corpus_links` with `evidence=true`, full paging.
2. Group the quoted edges by `fromSlug` and `corpus_read` each source
   lesson **once**, checking all of its quotes against that one body.
3. Raw test: is `evidence` a substring of the markdown?
4. If not, normalize both sides and retest: collapse every run of whitespace to
   a single space and delete the characters `*`, `_` and `` ` ``. This mirrors
   the writer's own normalization, so skipping it invents findings out of
   reformatting.

Grade each failure:

- **Drifted quote** (fails both tests): the lesson body changed after indexing
  and the edge's justification is no longer in it. Report the full stored quote
  verbatim and name the lesson.
- **Formatting drift** (fails raw, passes normalized): the text survives but the
  markup around it moved. Minor. List these together in one line, not one
  finding each.

Quotes are 40 to 300 normalized characters by construction. Anything far outside
that is worth a line of its own.

## 3. Orphan lessons

Lessons present in `corpus_index` that appear in no edge in either direction,
`fromSlug` or `toSlug`.

Report the slug, title and `order`. Do not guess the cause: a genuinely
standalone lesson and one the indexer missed look identical from the outside.
Only characterise a lesson if you actually `corpus_read` it, and say which ones
you read. A run of consecutive `order` values all orphaned points at an indexing
gap rather than at the content, and that pattern is worth naming.

## 4. Prerequisites naming nothing

From `corpus_index` with `format='full'`, the `prerequisites` array.

These are **free-text topic names** ("Proteins", "Chemical reactions"), not
slugs, and lesson titles are Sinhala with English technical terms inline. A
prerequisite that fails to string-match any title is therefore not a finding.

Before reporting one, `corpus_search` the term, then search an obvious variant
(the other language's term, singular against plural, the spelled-out form against
the abbreviation). Report only when every search comes back empty, and say which
strings you searched. If a search hits, the prerequisite is covered and there is
nothing to report.

Report: the card's `lessonSlug`, the prerequisite string quoted exactly, and the
search terms that found nothing.

## Report format

Markdown to the terminal. One section per check, in the order above. Under each,
either the findings or a single line saying the check passed. End with Coverage.

Every finding names a lesson slug, quotes the offending text as its own
characters, and adds a `corpus_search` line number when the claim came from a
search. Never paraphrase inside quote marks. Anything you concluded rather than
read is labelled as inference and kept, not dropped and not asserted.

Lead with a one-line verdict, then counts per class. If nothing is wrong, say so
plainly and still print Coverage.

**Coverage is mandatory.** An audit that hides what it did not check is worse
than none. State: rows fetched against reported totals for the index and the
edge list; how many lessons were read in full and how many source lessons the
evidence check covered; which checks were skipped, sampled or truncated and why;
and the cross-subject caveat from check 1 whenever any edge came back unresolved.
