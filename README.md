# dl-engine Corpus

Claude Code plugin for teachers who prepare lessons from a dl-engine subject.
One connector reaches every subject you have been assigned, and each folder says
which one it is about. It prepares concepts, sweeps themes across the syllabus,
audits the connection map, and traces a past paper question back to the lessons
that teach it, with every claim carrying the lesson slug and line number it came
from.

## What you need first

1. **Plugin access on your dl-engine account.** A key is granted, not assumed.
   If the plugin page says so, ask an admin to turn plugin access on.
2. **A key, minted at [dlengine.xyz/plugin](https://dlengine.xyz/plugin).** A key
   belongs to YOU, not to a subject. It is read only, and it reaches every
   subject an admin has assigned you, so one key covers all of them and one
   connector reads all of them. The plugin page lists your subjects and the
   single command that registers it.

   **A key minted before 0.4.0 reads corpus but not papers.** What a key may
   reach is written on it when it is minted, so an older one keeps working
   exactly as it did and simply has no paper tools behind it. Mint a fresh one on
   the same page and update `DL_ENGINE_KEY`. Nothing else changes.

   **Papers are granted per paper, not per subject.** A paper carries a subject
   tag for filtering, and the tag grants nothing, so being assigned A/L ICT does
   not hand you the ICT papers. An admin assigns those separately, which is why
   `list_papers` can come back empty on an account whose subjects all work.
3. **`DL_ENGINE_KEY` exported in your shell profile**, once, not per session:

   PowerShell, in the file `$PROFILE` names:

   ```powershell
   $env:DL_ENGINE_KEY = 'paste-your-key'
   ```

   bash or zsh, in `~/.bashrc` or `~/.zshrc`:

   ```bash
   export DL_ENGINE_KEY='paste-your-key'
   ```

   Open a new terminal afterwards. Claude Code reads the variable from the
   environment it was launched in.

## Install

While developing, point Claude Code straight at this folder:

```bash
claude --plugin-dir /path/to/dl-engine-plugin
```

Once this is a repository, install it the normal way:

```
/plugin marketplace add dinethlive/dl-engine-plugin
/plugin install dl-engine@dinethlive
```

Skills are namespaced by the plugin, so they are `/dl-engine:setup`,
`/dl-engine:teach` and so on. After editing plugin files, `/reload-plugins`
picks up the change without restarting.

## One connector, then a folder per lesson

Register the connector once for your whole account:

```bash
claude mcp add --scope user --transport http dl-engine \
  https://mcp.dlengine.xyz/mcp \
  --header 'Authorization: Bearer ${DL_ENGINE_KEY}'
```

That is the only installation step, and it is not per subject. Your key reaches
every subject an admin has assigned you, and the corpus tools take a `subject`
argument, so one connector reads all of them and you can switch mid-session.

After that a folder is just a folder. Give each lesson or topic its own, and let
`setup` write down which subject it is about:

```bash
mkdir enzymes-lesson
cd enzymes-lesson
claude
```

then, inside that session:

```
/dl-engine:setup al-bst
```

That checks the connector is there, proves `al-bst` is a subject your key can
actually read, and writes the slug into the folder's `CLAUDE.md` so every session
opened here knows what it is working on. Nothing is installed per folder, and
switching the folder to another subject is a one-line edit of that file.

Run `list_subjects` any time to see which slugs are yours.

Everything after that is ordinary conversation in that folder. Output saves as
files beside the lesson, or renders as an artifact.

## What you get

| Skill | What it does |
| :--- | :--- |
| `/dl-engine:setup <subject>` | Checks the connector, proves the slug is readable, and records the folder's subject in its CLAUDE.md. |
| `/dl-engine:teach` | Builds a cited teaching brief for one concept: what the corpus says, bilingual vocabulary, prerequisites, how it is examined, and the gaps you must fill yourself. |
| `/dl-engine:sweep <theme>` | Reads wide across the whole subject for one theme and reports where it lives, how the syllabus builds it, and how the lessons connect. |
| `/dl-engine:trace <question>` | Opens a past paper question as the images it actually is, reads what it asks, then finds where the syllabus teaches it, what a student needed first, and what the question demands that the corpus never covers. |
| `/dl-engine:audit` | Checks the subject against itself: dangling edges, evidence quotes that no longer hold, orphan lessons, prerequisites nothing covers. Reports, never edits. |
| `/dl-engine:whatsnew` | What changed in the plugin, which version you are on, and how to get a newer one. |
| `evidence` | The provenance contract: how syllabus, paper, web and inference are told apart. Claude loads it whenever it produces teaching material, so you rarely invoke it yourself. |

Only `setup` and `whatsnew` need typing. The rest are model-invoked: Claude reads
what you want from how you phrase it, so "prepare me a lesson on enzyme action"
reaches `teach` without you naming it, and "where does 2023 Part II question 4
come from" reaches `trace`. The slash forms still work if you prefer them.

## The tools underneath

The skills are how you use this. Underneath them the one connector exposes ten
tools, and you can ask for any of them by name when you already know what you
want.

**The corpus.** Every one of these takes `subject`, and that argument is the only
thing deciding which subject you read.

| Tool | What it answers |
| :--- | :--- |
| `list_subjects` | Which subjects this key can read. Takes no arguments, so it answers whenever the connector works at all. |
| `subject_info` | Name, medium, description and publication state of one subject. |
| `corpus_index` | The concept index, one card per lesson in syllabus order. A `slug` gives one card; `format: 'full'` adds key concepts, prerequisites and exam relevance. |
| `corpus_search` | Literal substring search of the lesson markdown, answering with lesson slugs and 1-based line numbers. |
| `corpus_read` | One lesson's full markdown, the same text the tutor itself is grounded in. |
| `corpus_links` | The connection map. `from` is what a lesson leads to, `to` is what leads into it, which is the prerequisite direction. |

**The papers**, which are a separate library and not a part of a subject. These
take no `subject`, because a paper is assigned to you directly.

| Tool | What it answers |
| :--- | :--- |
| `list_papers` | Which papers this key can read. Filterable by year, subject tag and origin. |
| `paper_contents` | One paper's contents: the cover rubric, the section rules, and every question's handle, kind, marks and topic. |
| `find_questions` | Which questions carry a term. It searches the labels a question was filed under, never the question text, because there is no question text. |
| `open_question` | A question's pages, as images. This is the one that shows you the question. |

A question's address is a handle, `al-ict-2025-part-i/q7`, and it is the same
string everywhere: in a search result, in a tool call, and in a citation.

None of the paper tools can carry a marking scheme, an answer key or a question's
answer. Those sit outside what any plugin key can reach, and not by a filter that
someone has to remember: the shapes a key receives have nowhere to put them.

## Staying current

**Auto-update is off by default for a marketplace like this one.** Claude Code
turns it on for Anthropic's own marketplaces, not for third-party ones, so
without changing it you will sit on the version you installed and nothing will
say so.

Turn it on once: `/plugin`, then **Marketplaces**, pick `dinethlive`, then
**Enable auto-update**. Claude Code then checks shortly after each session starts
and tells you to run `/reload-plugins` when something landed.

To check by hand at any time:

```
/plugin marketplace update dinethlive
```

`/dl-engine:whatsnew` reads the changelog that shipped with your copy, so it also
tells you which version you are actually running.

There is also an agent, `dl-engine:corpus-reader`, for wide reads that would
otherwise fill your session with raw lesson text. It reads, and returns cited
findings. It has no web access on purpose: what it exists to give you is a clean
answer to "what does this syllabus teach", and that stops being clean the moment
an outside explanation is in the room with it.

`sweep` and `audit` run in their own forked context. They read a great deal and
hand back only the finished report, so your session stays clear.

## Syllabus, paper, web, inference

Web search is on and you should use it. What the plugin will not do is let it
arrive unmarked, because a teacher is rarely asking whether something is true.
They are asking whether it is **on the syllabus**, and those are different
questions: a corpus fact is examinable, a web fact is enrichment, and it turns
dangerous the moment a student writes it in a paper believing it was taught.

So every claim carries where it came from, and the four never blur into one
paragraph:

| | |
| :--- | :--- |
| **Syllabus** | `` (`lesson-slug`:142) ``, the corpus, what your students are actually taught |
| **Paper** | `` (`al-ict-2023-part-ii/q4`) ``, a past paper question, what they were actually asked |
| **Web** | `(web: domain.com, 2026-08-22)`, the domain and the day it was read |
| **Inference** | `Inference:`, the model joining two things the corpus never joins itself |

A paper gets its own mark rather than counting as syllabus, because it is not the
syllabus. It is what an examiner did with it, and a question can test something
the corpus never covers. That mismatch is worth a lot to a teacher, and it only
shows up if the two are marked apart.

In a handout or an artifact this becomes visual as well as written, so a page can
be scanned rather than read, with the lessons and the URLs listed separately at
the end. Merging them into one "Sources" list would undo in the bibliography
exactly what the markers achieved in the body.

## Why output from this is worth trusting

dl-engine's own indexer throws away any proposed link whose supporting quote is
not found character for character in the source lesson. Every skill here is held
to the same bar:

* every claim cites a lesson slug, plus the line number when it came from a search
* a quote is the source's own characters, never a paraphrase dressed as one
* anything that cannot be cited is labelled as inference and kept, never quietly
  asserted and never quietly dropped
* a lesson that was not read is never characterised
* a gap in the syllabus is reported as a gap, not quietly filled from the web
* a past paper question is read as the images it actually is, and a claim resting
  on a diagram says it is a reading of a diagram rather than posing as a quote
* an answer to a past paper question is never presented as sourced, because no
  marking scheme is reachable from here at all

So a brief you take into a classroom can be checked line by line, and a gap it
reports is a real gap rather than a search that was never run.

## Three design choices worth knowing

**The subject is an argument, not the connector's address.** An earlier version
put the slug in the URL, one connector per subject. That was safer in one narrow
way, a session could not read the wrong corpus, and worse in every other: a key
already reaches all your subjects, so pinning the connection re-imposed in
configuration the limit the key had just lost, and teaching two subjects meant
installing two of everything. The subject rides on each call instead. The risk
that bought is real but it is handled by visibility rather than by a wall: every
tool prints the subject it just read at the top of its own output, so a wrong one
announces itself immediately instead of surfacing after the work is done.

**A question is its images, so nothing here ever hands you a transcript of one.**
dl-engine stores a past paper question as page images and stores no text for it,
because a question can be a circuit diagram, a match-the-following, a code
listing or an MCQ whose five options are themselves figures. A transcript of any
of those fails on some variant, and it fails quietly, by reading like ordinary
prose about something that was never prose. So `open_question` hands Claude the
page and Claude reads it. That is also why `trace` runs in your own session
rather than forked like `sweep` and `audit` do: a forked run would look at the
images and hand back a description of them, which is the exact thing the design
refuses to produce.

**The `api_key` setting is optional on purpose.** The connector reads
`DL_ENGINE_KEY` from your environment, so the plugin never needs the key itself. Marking the field required would block installing the plugin for anyone
who has not minted a key yet. Fill it in if you want the key on record, but the
environment variable is what the server actually reads.

## When it does not work

* **401 on every call**: the key is wrong, `DL_ENGINE_KEY` is unset, or plugin
  access was revoked. Check the plugin page.
* **404 on one subject**: it is not assigned to your account, or not published.
  `list_subjects` shows what is readable; an admin changes what is not.
* **"No subject was named"**: a tool was called without `subject`. The folder's
  `CLAUDE.md` should carry the slug; `setup` puts it there.
* **"This key is not scoped for that", but only from the paper tools**: the key
  was minted before 0.4.0. Mint a new one on the plugin page and update
  `DL_ENGINE_KEY`. The corpus tools keep working while you get round to it.
* **`list_papers` is empty but your subjects work**: no paper has been assigned
  to your account. Publishing a paper reaches nobody on its own, and a paper's
  subject tag grants nothing, so an admin assigns papers separately from
  subjects.
* **A question comes back with no pages**: object storage is not configured on
  the server. The question exists and its images do not, and there is nothing to
  read instead, since a question is only ever its images.
* **No corpus tools at all**: the connector was never registered, or Claude Code
  has not been restarted since. `/mcp` lists what is registered.

## Where this runs

Built for Claude Code, and that is where all of it works. The MCP connector and
the skills themselves also work in Claude Desktop, where you add the same server
URL and key in connector settings. Forked skills, agents and hooks are Claude
Code features and do not carry across, so `sweep` and `audit` would run inline
there rather than in their own context.

MIT licensed. Built by [Dineth Pramodya](https://dinethlive.com).
