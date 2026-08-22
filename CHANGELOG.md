# Changelog

What changed, newest first. Run `/dl-engine:whatsnew` in Claude Code to read this
without leaving the terminal.

Versions matter more here than they look: Claude Code pins an installed plugin to
the version string, so a release that forgets to bump it never reaches anyone.

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
