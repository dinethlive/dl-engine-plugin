# dl-engine Corpus

[![Version](https://img.shields.io/badge/version-0.4.0-informational)](CHANGELOG.md)
[![License](https://img.shields.io/badge/license-PolyForm%20Noncommercial-blue)](LICENSE.md)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-plugin-orange)](https://code.claude.com/docs/en/discover-plugins)

A Claude Code plugin for teachers preparing A/L lessons from a dl-engine subject.

It reads your syllabus corpus and your past papers, and every claim it makes
carries where it came from: a lesson slug and line number, a paper question
handle, a web domain, or an explicit `Inference:` label.

```
"Prepare me a lesson on enzyme action."
"Where does 2023 Part II question 4 come from?"
"Check this subject for links pointing nowhere."
```

## Requirements

| | |
| :--- | :--- |
| **Claude Code or Claude Desktop** | any version with plugin support |
| **A dl-engine account** | with plugin access enabled by an admin |

No API key. Since v0.6.0 the connector signs in with **Connect**: it opens
dlengine.xyz in your browser and you approve there. A key you already hold keeps
working; nothing about it changed.

Two things about access that catch people out:

- **A key minted before v0.4.0 reads the corpus but not papers.** Scopes are
  written when a key is minted, so an older key keeps working and simply has no
  paper tools behind it. Connect again, or mint a fresh one.
- **Papers are granted per paper, not per subject.** A paper's subject is a tag
  for filtering and grants nothing, so being assigned A/L ICT does not hand you
  the ICT papers. `list_papers` can be empty on an account whose subjects work.

## Install

**1. Install the plugin.** The connector ships inside it, so this is the only
step that adds anything:

```
/plugin marketplace add dinethlive/dl-engine-plugin
/plugin install dl-engine@dinethlive
```

**2. Restart.** Plugins and the servers they carry are both read at start, so
neither appears in the session that installed them.

**3. Connect.** Ask for anything from the corpus. The first call comes back
asking you to authorize, your client offers **Connect**, and approving on
dlengine.xyz is the whole of it. Already signed in there? One click.

**4. Point a folder at a subject:**

```bash
mkdir enzymes-lesson && cd enzymes-lesson && claude
```

```
/dl-engine:setup al-bst
```

That records the subject in the folder's `CLAUDE.md`. One folder per lesson or
topic; switching a folder to another subject is a one-line edit of that file.

## Skills

| Skill | What it does |
| :--- | :--- |
| `setup <subject>` | Checks the connector, proves the slug is readable, records the folder's subject. |
| `teach` | A cited brief for one concept: what the corpus says, the vocabulary, what it rests on, how it is examined, and the gaps you fill yourself. |
| `sweep <theme>` | Reads wide across a whole subject for one theme and hands back one report. |
| `trace <question>` | Opens a past paper question as images, reads what it asks, then finds where the syllabus teaches it and what it never covers. |
| `bloom` | Reads a lesson or a paper through the revised Bloom's taxonomy: which cells it occupies, which are empty, and questions or notes written at a level you name. |
| `audit` | Checks a subject against itself: dangling links, evidence quotes that no longer hold, orphan lessons. Reports, never edits. |
| `whatsnew` | What changed, which version you are on, how to update. |
| `evidence` | The provenance contract. Loaded automatically whenever output is produced. |

Only `setup` and `whatsnew` need typing. **The rest are model-invoked** from how
you phrase a request, so "prepare me a lesson on enzyme action" reaches `teach`
without naming it. The `/dl-engine:` slash forms still work.

`sweep`, `trace` and `audit` run in a forked context and return only the report,
so your session stays clear.

## How sources are told apart

A teacher is rarely asking whether something is true. They are asking whether it
is **on the syllabus**, so the origin of a claim is part of the answer rather
than metadata about it.

| Source | Marked as |
| :--- | :--- |
| **Syllabus**, the corpus, what students are taught | `` (`lesson-slug`:142) `` |
| **Paper**, a past question, what an examiner asked | `` (`al-ict-2023-part-ii/q4`) `` |
| **Web**, anything from a search | `(web: domain.com, 2026-08-22)` |
| **Inference**, reasoning the sources do not state | `Inference:` |

**No sentence carries two sources.** A paper is deliberately not filed under
syllabus: a question can test something the corpus never covers, and that
mismatch is one of the most useful things a teacher can be told.

Web search is on and worth using. What it may not do is arrive unmarked, because
unmarked it reads as syllabus.

### Why the output is worth trusting

dl-engine's own indexer discards any proposed link whose supporting quote is not
found character for character in the source lesson. Every skill here is held to
that bar:

- a quote is the source's own characters, never a paraphrase dressed as one
- anything uncitable is labelled inference and kept, never quietly asserted and
  never quietly dropped
- a lesson that was not read is never characterised
- a gap in the syllabus is reported as a gap, not filled from the web

<details>
<summary><b>The tools underneath</b></summary>

The skills are how you use this. Ten tools sit underneath, and you can ask for
any of them by name.

**Corpus.** Each takes `subject`, and that argument alone decides what you read.

| Tool | Answers |
| :--- | :--- |
| `list_subjects` | Which subjects this key can read. Takes no arguments. |
| `subject_info` | Name, medium, description, publication state. |
| `corpus_index` | The concept index, one card per lesson in syllabus order. |
| `corpus_search` | Literal substring search of lesson markdown, with 1-based line numbers. |
| `corpus_read` | One lesson's full markdown. |
| `corpus_links` | The connection map. `from` is what a lesson leads to, `to` is the prerequisite direction. |

**Papers.** A separate library, not part of a subject, so these take no
`subject`.

| Tool | Answers |
| :--- | :--- |
| `list_papers` | Which papers this key can read. |
| `paper_contents` | Cover rubric, section rules, and every question's handle, kind, marks and topic. |
| `find_questions` | Which questions carry a term. Searches the labels a question was filed under, never the question text, because there is no question text. |
| `open_question` | A question's pages, as images. |

A question's address is a handle, `al-ict-2025-part-i/q7`, and it is the same
string everywhere: in a search result, a tool call and a citation.

**A question arrives as images, never text.** dl-engine stores no transcript of
one, because a question can be a circuit diagram, a match-the-following, a code
listing, or an MCQ whose five options are themselves figures, and prose
renderings of those fail silently.

No paper tool can carry a marking scheme, an answer key, or a question's answer.
Those sit outside what any plugin key can reach, and not by a filter someone has
to remember: the shapes a key receives have nowhere to put them.

</details>

<details>
<summary><b>Updating</b></summary>

**Auto-update is off by default** for third-party marketplaces. Claude Code
enables it for Anthropic's own, so without changing this you stay on the version
you installed and nothing says so.

Turn it on once: `/plugin`, then **Marketplaces**, then `dinethlive`, then
**Enable auto-update**.

Or check by hand:

```
/plugin marketplace update dinethlive
```

`/dl-engine:whatsnew` reads the changelog shipped with your copy, so it also
tells you which version you are actually running.

</details>

<details>
<summary><b>Troubleshooting</b></summary>

| Symptom | Cause |
| :--- | :--- |
| No corpus tools at all | The plugin is installed but the client has not restarted. `/mcp` lists what is registered. |
| Every call asks you to connect | Not authorized yet, or the credential was revoked. Connect again, unless an admin withdrew plugin access. |
| `No subject was named` | A corpus tool was called without `subject`. The folder's `CLAUDE.md` should carry the slug. |
| 404 on one subject | Not assigned to your account, or not published. `list_subjects` shows what is readable. |
| Paper tools refused | Your credential predates v0.4.0. Connect again, or mint a fresh key. |
| `list_papers` is empty | No papers assigned yet. An admin assigns them per paper. |

</details>

## Where this runs

Built for Claude Code, and that is where all of it works. The connector and the
skills also work in Claude Desktop, where you add the same server URL and key in
connector settings. Forked skills and agents are Claude Code features and do not
carry across, so `sweep`, `trace` and `audit` would run inline there.

## License

[PolyForm Noncommercial 1.0.0](LICENSE.md), **plus an explicit grant for
teaching**: you may use this to prepare and deliver your own lessons, including
lessons you are paid for. See [NOTICE.md](NOTICE.md).

That grant does not extend to selling, sublicensing or redistributing the
plugin, offering it as a service, or building a commercial product on it.

This repository is instructions, not data. It reaches nothing on its own: a
dl-engine API key decides what any of it can read, and cloning this grants no
access to any subject, lesson or paper.

Built by [Dineth Pramodya](https://dinethlive.com).
