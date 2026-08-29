# Changelog

What changed, newest first. Run `/dl-engine:whatsnew` in Claude Code to read this
without leaving the terminal.

Versions matter more here than they look: Claude Code pins an installed plugin to
the version string, so a release that forgets to bump it never reaches anyone.

## 0.6.0

**Connect instead of pasting a key.**

Installing this plugin used to leave you with skills and no tools: the connector
arrived from a `claude mcp add` command the setup skill printed, which is a
Claude Code command, so installing from the git repo in Claude Desktop gave you
half a plugin and no way to finish. And there was no way to finish, because
Desktop's connector UI takes a URL and has no field a teacher could paste a key
into.

- **The connector ships with the plugin**, in `.mcp.json` at the root. Installing
  registers it. There is no command to run and no `--header` to get right.
- **Authorizing is a Connect button.** dl-engine now answers an unauthorized call
  with an OAuth challenge, so the client offers to connect, opens dlengine.xyz in
  your browser, and you approve on a screen naming the client and what it will
  read. If you are already signed in there, that is one click.
- **`DL_ENGINE_KEY` is no longer needed anywhere**, and no shell profile has to
  be edited. A key you already hold keeps working exactly as it did: approving
  mints the same kind of key, so a connection appears in the list at
  https://dlengine.xyz/plugin and revoking it there disconnects the client.
- **`/dl-engine:setup` rewritten** around all of that, including what each
  failure now actually means. Its second job, pointing a folder at a subject, is
  unchanged.

## 0.5.0

**A lesson, or a paper, read through the revised Bloom's taxonomy.**

Teachers keep needing the same two answers and the corpus states neither: what a
lesson is actually asking of a student, and what an examiner actually rewards.

- **New skill, `bloom`.** Map a lesson onto the taxonomy table, profile what
  levels a paper tests, or write questions and notes at a level you name. It
  reaches for the corpus and the paper tools that already exist rather than
  answering from what it remembers about a subject.
- **The 2001 revision, and both of its axes.** Six cognitive processes crossed
  with four knowledge types, so an objective lands in a CELL and not on a level.
  Almost everyone drops the knowledge axis and keeps the six renamed levels,
  which is where the value went: the blank cells are the finding, and the
  metacognitive row is empty in nearly every A/L corpus.
- **It will not classify by looking a verb up in a table**, because that is the
  documented error rather than the method. "Explain" is Understand when a
  student must reconstruct a mechanism and Remember when the lesson already
  prints that explanation in a reproducible sentence. So the skill reads the
  lesson first and asks what the student has to do that the source does not do
  for them.
- **No pyramid.** The triangle appears in neither the 1956 nor the 2001 book, and
  the reviser who led the 2001 edition says so in as many words. The processes
  are a toolbox, not a ladder, so applying in order to understand is ordinary.
- **A cognitive level is inference, and the contract now says so.** The corpus
  never states what level anything sits at, so a classification is a judgment
  about a cited thing rather than something a source asserted. The lesson or the
  question handle is cited normally; the level is marked `Inference:`. Same for
  DOK and SOLO.
- Two findings the skill goes looking for, because neither is visible from one
  library alone: what a paper tests that the corpus never teaches, and what the
  corpus teaches that no paper has ever asked.
- Honest about the framework: a shared language and a heuristic, not a validated
  theory of learning, with empirical support claimed for roughly its lower four
  levels. "Higher" does not mean harder, and lower levels are the foundation
  rather than disposable steps.

## 0.4.0

**Past papers, and a question is the page you look at.**

A teacher's real question about a past paper is rarely "what is the answer". It
is "where does this come from, and have I taught it". That is a several-step
investigation across two libraries, which is exactly the kind of work that drifts
in a single chat turn, so it now has a skill.

- **New skill, `trace`.** Name a question, by handle or as "2023 Part II question
  4", and it opens the pages, reads what is actually being asked, finds the
  lessons that teach it, walks back to what a student needed first, and reports
  what the question demands that the corpus never covers.
- **Four new tools:** `list_papers`, `paper_contents`, `find_questions` and
  `open_question`. The last one is the one that matters: it hands over the
  question's pages as images.
- **A question arrives as its images, never as text**, because dl-engine stores
  no transcript of one. A question can be a circuit diagram, a
  match-the-following, a code listing, or an MCQ whose five options are
  themselves figures, and prose renderings of those fail on some variant and fail
  quietly. `topic` and `keywords` are there to FIND a question and are never used
  to describe one.
- **A past paper is now its own source in the evidence contract**, marked
  `` (`al-ict-2023-part-ii/q4`) ``, and it is deliberately not filed under
  syllabus: a paper is what an examiner did, and a question can test something
  the corpus never covers. That mismatch is worth a lot to a teacher and only
  shows up when the two are marked apart. A claim resting on a figure now says it
  is a reading of a figure instead of posing as a quote, and no answer to a past
  paper question is ever presented as sourced, because a marking scheme is not
  reachable from a plugin key and is not going to be.
- **The contract now separates "I did not find it" from "it is not taught".** A
  paper question and a lesson rarely share vocabulary and corpus search is
  literal substring matching, so a term the corpus prints differently comes back
  empty and looks exactly like a term the corpus never covers. The second claim
  now has to be earned, and `trace` says how to earn it.
- **Your key needs re-minting for this.** What a key may reach is written on it
  when it is minted, so one minted before this release reads the corpus and has
  no paper tools behind it at all. Mint a new one at
  [dlengine.xyz/plugin](https://dlengine.xyz/plugin) and update `DL_ENGINE_KEY`.
  The old key keeps working for the corpus in the meantime.
- **Papers are assigned per paper.** Being assigned a subject grants none of its
  papers, because the subject on a paper is a tag for filtering and grants
  nothing. An admin assigns papers separately, so `list_papers` can be empty on
  an account whose subjects all work.
- `corpus-reader` will not open a question's pages. It can tell you which papers
  and which question numbers exist, and then hands the address back, because a
  forked agent describing an image to you is the one thing this release is built
  to avoid.
- Marking schemes, answer keys and the mock question Setter stay out of reach, as
  does every admin route. The plugin surface is reads, and a narrow set of them.

## 0.3.0

**Syllabus, web and inference are told apart, always.**

Web search is on and worth using. What changed is that it can no longer arrive
looking like syllabus, because a teacher is usually not asking whether something
is true, they are asking whether it is on the syllabus. A corpus fact is
examinable; a web fact is enrichment, and it turns dangerous the moment a student
writes it in a paper believing it was taught.

- Every claim now carries its origin: `` (`lesson-slug`:142) `` for the corpus,
  `(web: domain.com, 2026-08-22)` for the internet, `Inference:` for reasoning.
- **No sentence carries two sources.** A half-corpus, half-web claim splits into
  two marked sentences.
- The corpus is searched before the web. Not etiquette: a web explanation read
  first colours how the lesson is read afterwards.
- In a handout the marks become visual too, and lessons and URLs are listed
  separately at the end rather than merged into one "Sources" block.
- If an artifact is student-facing, web material is cut or fenced under a heading
  saying it is beyond the syllabus.
- `corpus-reader` lost web access outright. Its value is a clean answer to what
  THIS syllabus teaches, and a gap it reports is only trustworthy if it had no
  way to quietly fill one.

## 0.2.0

**One connector reaches every subject.**

The connector URL used to name a subject, so a teacher of two subjects installed
two connectors. That put back in configuration the limit the key no longer had.

- Register once, at user scope: `https://mcp.dlengine.xyz/mcp`, no subject in it.
- Every corpus tool takes a `subject` argument, so switching is a parameter, in
  the same session, with nothing to reinstall.
- `list_subjects` names what your key can read.
- `setup` no longer writes a per-folder `.mcp.json`. It proves the slug is
  readable and records it in the folder's `CLAUDE.md`, so switching a folder to
  another subject is a one-line edit.
- `corpus_index` takes a lesson `slug`; `corpus_links` takes `to`, which is the
  direction a prerequisite chain is actually walked.

## 0.1.0

First release. Five skills, one agent, and the citation contract they share.

- `setup`, `teach`, `sweep`, `audit`, `evidence`
- `corpus-reader`, for wide reads that would otherwise fill your session with raw
  lesson text
- The bar for output is dl-engine's own: a claim carries its lesson slug and, for
  a search, its 1-based line number; a quote is the source's own characters;
  anything uncitable is labelled inference and kept; a lesson that was not read is
  never characterised.
