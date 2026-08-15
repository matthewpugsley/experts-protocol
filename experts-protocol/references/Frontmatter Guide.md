---
title: Frontmatter Guide
tags: [workflow, reference]
priority: 
retrieval: auto
updated: 2026-08-15
---

## Summary
Reference guide for the shape of a vault note: the YAML frontmatter fields and
the body conventions that go with them. Aligned with the model defined in the
`experts-protocol` skill, which points here rather than restating any of it.

## Fields

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
| `standing/` | auto-load |
| `learnings/` | on-demand |
| `Reference/` | searched, never bulk-loaded |

Elsewhere in the vault there's no folder-based default defined yet — use
`retrieval` explicitly (`auto` or `on-demand`) until one is, the way this note
does.

## Note structure
A note opens with `## Summary` and ends with `## Log`. Whatever the note is
actually about goes between them.

**Log entries are newest-first.** New entries go at the *top* of the `## Log`
section, directly under the heading — not appended at the bottom. The reason is
that the usual question is "what changed lately?", and the answer should be the
first thing under the heading rather than the last line of the file.

Entry headings are `### YYYY-MM-DD`, optionally followed by a short label
(`### 2026-08-14 — Bone Zone hunt`). Where a note uses `## Entries` instead of
`## Log` — session logs do — the same ordering applies.

One practical note for tools: newest-first is a single heading-targeted prepend,
which works no matter where the Log sits in the file. Appending to the end of
the file is *not* equivalent, because many notes have content after the Log.

## Log
### 2026-08-15
- Added this Note structure section, setting log order to newest-first
  across the vault, and reversed the logs in this file and the Template.
- Replaced `status` (draft/active/archived) with folder-based classification
  and the `retrieval` field, and re-scoped `priority` to ordering-only —
  matching the frontmatter model in `experts-protocol`. Reordered fields to
  title, tags, priority, retrieval, updated.

### 2025-05-29
- Created
