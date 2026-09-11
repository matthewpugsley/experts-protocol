---
title: Frontmatter Guide
tags:
  - workflow
  - reference
priority:
retrieval: auto
updated: 2026-09-09
---

## Summary
Reference guide for the shape of a vault note: the YAML frontmatter fields and
the body conventions that go with them. Aligned with the model defined in the
`experts-protocol` skill, which points here rather than restating any of it.

## Frontmatter Field Definitions

### title
Human-readable name for the note. Can differ from the filename.
Leave blank in the template; fill in when creating a new note.

### tags
A YAML list of lowercase strings. Example: `[health, exercise, active]`
No strict controlled vocabulary — let it grow naturally.

### priority
Optional. Used to order notes within an auto-loaded set — it does not decide
whether a note loads at all. Skip it unless a folder has enough files that
ordering actually matters.
Valid values:
- `high` — load first
- `medium` — load second
- `low` — load last
- (blank) — sorts after all prioritized files

### retrieval
Optional. Records a deliberate *exception* to a folder's default loading
behavior. Add it only when a note should load differently than its folder
normally implies — e.g. a note too long to justify auto-loading, or one
being iterated on hard enough to want in context every time.
Valid values:
- `auto` — load automatically regardless of folder default
- `on-demand` — load only when relevant/requested, regardless of folder default

If the field is absent, the relevant folder default applies (see below).

### updated
ISO date of last meaningful edit: `YYYY-MM-DD`
You update this manually; Claude updates it automatically on writes. A pure
reformatting pass is not a meaningful edit — don't bump it for that.

## Classification is by folder, not a status field
There is deliberately no `status` field anymore (this replaces the old
`draft` / `active` / `archived` convention). A note's *location* says what it
is, so classification can't silently drift out of sync with frontmatter the
way a status value could.

Within `Experts/<expert>/`, folder defaults are:

| Folder | Default |
|---|---|
| `standing/` | on-demand |
| `learnings/` | on-demand |
| `Reference/` | searched, never bulk-loaded |

Nothing auto-loads by folder default anywhere. `retrieval: auto` is the only
way a note loads automatically — a deliberate, per-note exception, never a
folder-level assumption. (Changed 2026-09-09: `standing/` previously
defaulted to auto-load.)

Elsewhere in the vault there's no folder-based default defined yet — use
`retrieval` explicitly (`auto` or `on-demand`) until one is, the way this note
does.

## Template Structure

- ### Frontmatter - list of frontmatter properties (yaml)
- ### Summary - summary of the document goes here
- ### Content - body of the document goes here
- ### Log - newest-first; entries are `### YYYY-MM-DD`, prepended directly
  under the `## Log` heading, never appended to the end of the file — many
  notes have content after the Log, so those two aren't equivalent
