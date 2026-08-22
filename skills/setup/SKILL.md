---
name: setup
description: Connect Claude Code to the dl-engine corpus, and tell the CURRENT folder which subject it is about. Use when the teacher asks to set up, connect or point a folder at a subject, to switch a folder to a different subject, or when a folder has no corpus tools yet.
argument-hint: <subject-slug>
disable-model-invocation: true
---

# Set this folder up for a subject

Two separate things, and only the first is installation:

1. **The connector**, registered ONCE for the whole account. It reaches every
   subject the teacher has been assigned.
2. **This folder's subject**, a note saying what the work here is about, so a
   session opened in it starts oriented.

After the first time, only step 2 is left to do.

## 1. Is the connector already there?

Call `list_subjects`. It takes no arguments and needs no subject, so it answers
whenever the connector exists at all.

- **It answers with a list.** Installed. Go to step 2.
- **It is not a tool you have.** Not registered. Give the teacher the command
  below, and stop until they have run it and restarted.
- **It answers "This key is not valid".** Registered, but `DL_ENGINE_KEY` is
  unset or wrong. See "When it fails".

```bash
claude mcp add --scope user --transport http dl-engine \
  https://mcp.dlengine.xyz/mcp \
  --header 'Authorization: Bearer ${DL_ENGINE_KEY}'
```

`--scope user` is the point: it registers against the account rather than this
directory, so every lesson folder they ever make already has the corpus in it.
There is no per-folder config to write, and no second connector for a second
subject, because the subject is an argument on each call rather than part of the
URL.

The key stays an env var reference. Never write a literal key into any file, even
if the teacher pastes one here. If they do, tell them where it goes instead:

- PowerShell: add `$env:DL_ENGINE_KEY = '<key>'` to the file `$PROFILE` names.
- bash or zsh: add `export DL_ENGINE_KEY='<key>'` to `~/.bashrc` or `~/.zshrc`.

Once, in the profile, not per session. Claude Code reads the variable from the
environment it was launched in, so the teacher opens a new terminal afterwards.

## 2. Which subject is this folder about?

The slug is `$ARGUMENTS`. If it is empty, show what `list_subjects` returned and
ask which one. Do not guess it from the folder name, and do not recite a list
from memory: subjects are assigned per account, so a remembered list goes stale.

Then prove the slug before writing it down: call `subject_info` with `subject`
set to it. That returns the subject's name and medium, the cheapest proof the key
reaches it. A 404 means the slug is wrong OR an admin has not assigned it; say
which slugs ARE available rather than guessing which of the two it was.

## 3. Write it into CLAUDE.md

Create or update `CLAUDE.md` in the CURRENT working directory. Never the plugin
folder, never the home directory. Keep it short:

```markdown
# <lesson or topic>

dl-engine subject: `<slug>` (<subject name>)
Pass `subject: "<slug>"` to every corpus tool in this folder.
```

This note is what makes the folder mean something. The connector reaches every
subject, so without it a new session cannot know which one this folder is for,
and asking every time is exactly what the note prevents.

If the teacher wants the folder to span two subjects, say so in the same file and
name both slugs. Nothing stops it: one connector reads them all.

## Switching subject

Change the slug in `CLAUDE.md`. That is the whole procedure. No reinstall, no
restart, no second connector, and a single session can read two subjects in
consecutive calls when the work genuinely spans both.

## When it fails

- **No corpus tools at all**: the connector was never added, or Claude Code has
  not been restarted since. Servers are picked up at start; `/mcp` lists what is
  registered.
- **"This key is not valid" on every call**: `DL_ENGINE_KEY` is unset so the
  header went out empty, the key is wrong, or an admin revoked plugin access.
  Check the dl-engine `/plugin` page.
- **"No subject was named"**: a corpus tool was called without `subject`. Pass
  the slug from `CLAUDE.md`.
- **404 on one subject but not others**: that subject is unassigned or
  unpublished for this account. `list_subjects` shows what IS readable. The same
  404 covers a wrong slug and an unassigned subject on purpose, so a key cannot
  map the catalogue by guessing.
- **`list_subjects` comes back empty**: the account has plugin access but no
  subjects assigned. An admin fixes that; there is nothing to configure here.
