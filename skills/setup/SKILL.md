---
name: setup
description: Connect Claude to the dl-engine corpus, and tell the CURRENT folder which subject it is about. Use when the teacher asks to set up, connect or point a folder at a subject, to switch a folder to a different subject, or when a folder has no corpus tools yet.
argument-hint: <subject-slug>
disable-model-invocation: true
---

# Set this folder up for a subject

Two separate things, and only the first is installation:

1. **The connector**, authorized ONCE for the whole account. It reaches every
   subject the teacher has been assigned.
2. **This folder's subject**, a note saying what the work here is about, so a
   session opened in it starts oriented.

After the first time, only step 2 is left to do.

## 1. Is the connector already there?

Call `list_subjects`. It takes no arguments and needs no subject, so it answers
whenever the connector exists at all.

- **It answers with a list.** Connected. Go to step 2.
- **It is not a tool you have.** This plugin ships the connector in its own
  `.mcp.json`, so there is no command to run and nothing to add by hand. It
  normally means the plugin was installed in this session: servers are read at
  start, so ask them to restart and begin again.
- **It fails asking you to sign in or authorize.** Registered but not authorized
  yet. See below.

## Authorizing

There is nothing to paste and no key to keep anywhere.

The dl-engine server answers an unauthorized call with an OAuth challenge, so the
client offers to **Connect**. That opens `dlengine.xyz` in the teacher's browser,
where they are very likely already signed in, and they approve on a screen naming
the client and exactly what it will read. Claude holds the credential afterwards.

Tell them to click Connect and approve. If nothing offers to, ask them to restart
and make one corpus call again: the challenge only arrives on a real request.

**Never write a key into any file.** If a teacher pastes one here, do not use it
and do not save it. Tell them Connect replaces it, and that any key they already
hold keeps working and is revocable at `https://dlengine.xyz/plugin`.

Approving mints a key on their account, so the connection appears in the list on
that page like any other, and revoking it there disconnects the client.

## 2. Which subject is this folder about?

The slug is `$ARGUMENTS`. If it is empty, show what `list_subjects` returned and
ask which one. Do not guess it from the folder name, and do not recite a list
from memory: subjects are assigned per account, so a remembered list goes stale.

Then prove the slug before writing it down: call `subject_info` with `subject`
set to it. That returns the subject's name and medium, the cheapest proof the
connection reaches it. A 404 means the slug is wrong OR an admin has not assigned
it; say which slugs ARE available rather than guessing which of the two it was.

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

- **No corpus tools at all**: the plugin is installed but Claude has not been
  restarted since. Servers are picked up at start; `/mcp` lists what is
  registered.
- **Every call asks you to connect**: not authorized yet, or the credential was
  revoked on the `/plugin` page or by an admin withdrawing the grant. Connecting
  again is the fix, unless the grant itself is gone.
- **"Plugin access is not enabled for this account"**: the grant is per account
  and an admin turns it on. Nothing on this side changes it, and reconnecting
  will not help.
- **"No subject was named"**: a corpus tool was called without `subject`. Pass
  the slug from `CLAUDE.md`.
- **404 on one subject but not others**: that subject is unassigned or
  unpublished for this account. `list_subjects` shows what IS readable. The same
  404 covers a wrong slug and an unassigned subject on purpose, so a credential
  cannot map the catalogue by guessing.
- **`list_subjects` comes back empty**: the account is connected and has no
  subjects assigned. An admin fixes that; say so plainly rather than suggesting a
  reconnection, which cannot add a subject.
- **A key in `DL_ENGINE_KEY`, from before Connect existed**: still valid, and it
  authenticates the same account. Leave it alone unless the teacher wants it
  gone.
- **It worked for months and now every call asks you to connect**: a Connect
  credential lasts ninety days. Connecting again is the whole fix and takes one
  click when the browser is already signed in. A key minted by hand on the
  `/plugin` page does not lapse this way.
