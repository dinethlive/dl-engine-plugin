---
name: whatsnew
description: Show what changed in the dl-engine plugin, and whether a newer version is available. Use when the teacher asks what is new, what changed, what version they are on, whether the plugin is up to date, or how to update it.
disable-model-invocation: true
---

# What changed

Answer two questions, in this order, because the second is the one that bites:
**what is new**, and **is the teacher actually running it**.

## 1. Which version is installed

Read `CHANGELOG.md` at the plugin root (`${CLAUDE_PLUGIN_ROOT}/CHANGELOG.md`, or
the copy sitting beside this skill's own directory). Its top entry is the version
in the teacher's cache, because the changelog ships inside the plugin: if they
are reading an old copy, the newest entry it can possibly show is the version
they have.

Report that number plainly, then summarise the entries in your own words. Lead
with what changes their work, not what changed in the code. A teacher does not
care that a tool grew an argument; they care that they can now switch subjects
without reinstalling.

If they named a version or asked "since when", show only what landed after it.

## 2. Is there a newer one

The installed copy cannot know this, so say so rather than implying the list is
current. Then give them the one command that checks:

```
/plugin marketplace update dinethlive
```

That refreshes the catalogue. If a newer version exists, Claude Code updates the
plugin and tells them to run `/reload-plugins`.

## The part worth telling them once

**Auto-update is OFF by default for a marketplace like this one.** Claude Code
enables it for Anthropic's own marketplaces, not for third-party ones, so a
teacher who never turns it on will sit on the version they installed
indefinitely and nothing will warn them.

Turning it on is four steps and worth doing:

1. `/plugin`
2. **Marketplaces**
3. pick `dinethlive`
4. **Enable auto-update**

After that Claude Code checks shortly after each session starts, and when a
plugin updates it notifies them to run `/reload-plugins`. That is the
notification, and it only exists once auto-update is on.

Offer this whenever a teacher turns out to be running an old version. Do not
lecture anyone already up to date.

## Tone

This is a release note read by a teacher between classes, not a commit log. Short
paragraphs, no version-control vocabulary, and nothing about files. If a change
does not affect what they can do, leave it out.
