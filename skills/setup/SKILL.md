---
name: setup
description: Wire the CURRENT folder to one dl-engine subject by writing its project .mcp.json for the dl corpus server. Use when the teacher asks to set up, connect or point this lesson folder at a subject, to switch the folder to a different subject, or when a new folder has no corpus tools yet.
argument-hint: <subject-slug>
disable-model-invocation: true
---

# Wire this folder to a subject

One folder per lesson or topic. This writes that folder's `.mcp.json` so every
Claude Code session started in it can read one subject's corpus.

## 1. Pick the subject

The slug is `$ARGUMENTS`. If it is empty, ask which subject and stop until the
teacher answers. Do not guess it from the folder name, and do not recite a list
from memory: subjects are added and assigned per account, so a hardcoded list
goes stale and a wrong slug looks exactly like an empty subject.

Get the real list instead. If a `dl` server is already reachable in this session,
call `list_subjects`, which answers even when the connector URL names a subject
the key cannot reach. If nothing is reachable yet, send the teacher to the
dl-engine `/plugin` page, which lists their subjects and prints the exact command
for each.

The slug must be a subject the teacher is ASSIGNED. A key belongs to a teacher
rather than to a subject, so it reaches every subject an admin has both published
and assigned to that account, and one key serves as many folders as they teach.
A slug outside that set is not a degraded mode: every tool answers 404, and the
subject never confirms it exists.

## 2. Write .mcp.json

Write it in the CURRENT working directory. Never the plugin folder, never the
home directory. If the file already exists, add the `dl` server and leave the
other servers untouched.

```json
{
  "mcpServers": {
    "dl": {
      "type": "http",
      "url": "https://mcp.dlengine.xyz/mcp/<SUBJECT>",
      "headers": { "Authorization": "Bearer ${DL_ENGINE_KEY}" }
    }
  }
}
```

`<SUBJECT>` is the slug. The key stays an env var reference. Never write the key
itself into this file, even if the teacher pastes it here: `.mcp.json` sits in a
project folder, so a literal key is a secret one `git add` away from a public
repo, and it reads that subject for anyone who ends up holding it. If they paste
a key, write the reference anyway and tell them where the key goes instead:

- PowerShell: add `$env:DL_ENGINE_KEY = '<key>'` to the file `$PROFILE` names.
- bash or zsh: add `export DL_ENGINE_KEY='<key>'` to `~/.bashrc` or `~/.zshrc`.

Once, in the profile, not per session. Claude Code reads the variable from the
environment it was launched in, so the teacher opens a new terminal afterwards.

## 3. Restart, then check

Servers are picked up at start. Tell the teacher to restart Claude Code, or run
`/mcp`, and to approve it: a project-scoped server asks for approval the first
time it is used.

Then confirm the binding with one `subject_info` call. It returns the bound
subject's name and medium, the cheapest proof that key and folder agree.

If it 404s, follow with `list_subjects`. That names every subject the key can
read and says which one this connector points at, which turns "something is
wrong" into either a typo in the URL or a subject an admin has not assigned yet.

## 4. Offer a CLAUDE.md

Offer it, and write it only if the teacher says yes. A few lines: the subject
slug, the lesson or topic this folder is for, and where output goes. A session
opened here then starts oriented instead of asking.

## When it fails

- **401 on every call**: the key is wrong, or `DL_ENGINE_KEY` is unset so the
  header went out empty, or an admin revoked plugin access. Check `/plugin`.
- **403**: the key is not scoped for that endpoint.
- **404 on every tool**: the key cannot reach this folder's subject. Run
  `list_subjects` to see the ones it can. A subject missing from that list is one
  an admin has not assigned to this account, or has not published; a subject on
  the list means the slug in the URL is simply wrong. The same 404 covers both
  cases on purpose, so a key cannot walk the slug space and map the catalogue.
- **No corpus tools at all**: `.mcp.json` was written somewhere other than the
  folder Claude Code is running in, or Claude Code has not been restarted.
