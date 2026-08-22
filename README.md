# dl-engine Corpus

Claude Code plugin for teachers who prepare lessons from a dl-engine subject.
It points a folder at one subject's corpus over MCP, then prepares concepts,
sweeps themes across the syllabus and audits the connection map, with every
claim carrying the lesson slug and line number it came from.

## What you need first

1. **Plugin access on your dl-engine account.** A key is granted, not assumed.
   If the plugin page says so, ask an admin to turn plugin access on.
2. **A key, minted at [dlengine.xyz/plugin](https://dlengine.xyz/plugin).** A key
   belongs to YOU, not to a subject. It is read only, and it reaches every
   subject an admin has assigned you, so one key covers all of them. The folder's
   connector URL is what narrows a session to one. The plugin page lists your
   subjects and the exact command for each.
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

## One folder per lesson

The corpus server is bound per folder, because one folder is one subject.
So each lesson or topic gets its own folder, wired once:

```bash
mkdir enzymes-lesson
cd enzymes-lesson
claude
```

then, inside that session:

```
/dl-engine:setup al-bst
```

That writes a `.mcp.json` in the folder pointing at
`https://mcp.dlengine.xyz/mcp/al-bst`, with the key left as a
`${DL_ENGINE_KEY}` reference rather than written into the file. Restart Claude
Code, approve the server when asked, and the corpus tools are there. Live
subject slugs today: `al-bst`, `al-ict`, `al-sft`, `ol-ict`, `gemology-101`.

Everything after that is ordinary conversation in that folder. Output saves as
files beside the lesson, or renders as an artifact.

## What you get

| Skill | What it does |
| :--- | :--- |
| `/dl-engine:setup <subject>` | Wires the current folder to one subject and checks the binding. |
| `/dl-engine:teach` | Builds a cited teaching brief for one concept: what the corpus says, bilingual vocabulary, prerequisites, how it is examined, and the gaps you must fill yourself. |
| `/dl-engine:sweep <theme>` | Reads wide across the whole subject for one theme and reports where it lives, how the syllabus builds it, and how the lessons connect. |
| `/dl-engine:audit` | Checks the subject against itself: dangling edges, evidence quotes that no longer hold, orphan lessons, prerequisites nothing covers. Reports, never edits. |
| `evidence` | The citation contract. Claude loads it whenever it answers from the corpus, so you rarely invoke it yourself. |

There is also an agent, `dl-engine:corpus-reader`, for wide reads that would
otherwise fill your session with raw lesson text. It reads, and returns cited
findings.

`sweep` and `audit` run in their own forked context. They read a great deal and
hand back only the finished report, so your session stays clear.

## Why output from this is worth trusting

dl-engine's own indexer throws away any proposed link whose supporting quote is
not found character for character in the source lesson. Every skill here is held
to the same bar:

* every claim cites a lesson slug, plus the line number when it came from a search
* a quote is the source's own characters, never a paraphrase dressed as one
* anything that cannot be cited is labelled as inference and kept, never quietly
  asserted and never quietly dropped
* a lesson that was not read is never characterised

So a brief you take into a classroom can be checked line by line, and a gap it
reports is a real gap rather than a search that was never run.

## Two design choices worth knowing

**No plugin-level `.mcp.json`.** The subject lives in the connector URL, so a
server configured once for the whole plugin would bind every folder to the same
subject, which defeats the point of a folder per lesson. The `setup` skill writes a small
`.mcp.json` per folder instead. That is also why a folder that has no corpus
tools usually just needs `setup` run in it, or Claude Code restarted.

**The `api_key` setting is optional on purpose.** The per-folder `.mcp.json`
reads `DL_ENGINE_KEY` from your environment, so the plugin never needs the key
itself. Marking the field required would block installing the plugin for anyone
who has not minted a key yet. Fill it in if you want the key on record, but the
environment variable is what the server actually reads.

## When it does not work

* **401 on every call**: the key is wrong, `DL_ENGINE_KEY` is unset, or plugin
  access was revoked. Check the plugin page.
* **404 on every tool**: this folder's subject is not the key's subject. Fix the
  slug, or use the key minted for that subject.
* **No corpus tools at all**: `.mcp.json` is not in the folder Claude Code is
  running in, or Claude Code has not been restarted since it was written.

## Where this runs

Built for Claude Code, and that is where all of it works. The MCP connector and
the skills themselves also work in Claude Desktop, where you add the same server
URL and key in connector settings. Forked skills, agents and hooks are Claude
Code features and do not carry across, so `sweep` and `audit` would run inline
there rather than in their own context.

MIT licensed. Dineth Pramodya, dinethlive@users.noreply.github.com.
