# experts-protocol

Source and packaged build of the **experts-protocol** skill for Claude — the
shared mechanics behind a small system of domain-specific "expert" personas
backed by an Obsidian vault. An expert is not itself a packaged skill; it's a
folder in the vault (`Experts/<name>/`), and this skill is what knows how to
read and operate on it.

The skill itself defines what it is to be an expert in that system: where
knowledge lives (profile note vs. standing notes vs. learnings vs. shared
reference), how a learning gets promoted into the profile note, how to
create a new expert, and how to deploy the whole thing to a vault — or a new
client on an already-deployed vault — that hasn't had it yet.

For what it actually says, read [`experts-protocol/SKILL.md`](experts-protocol/SKILL.md).
That file is the authoritative description — this README deliberately doesn't
restate it, because a second copy is the one that goes stale.

## Layout

```
experts-protocol/
  SKILL.md                        the protocol itself
  references/
    Frontmatter Guide.md          note-shape reference; seeded into the vault
    Frontmatter Template.md       blank starting block; seeded into the vault
experts-protocol.skill            build artifact — the uploadable zip
```

`experts-protocol.skill` is generated from the `experts-protocol/` folder. Edit
the source, then rebuild — never hand-edit the zip.

## Rebuilding the zip

Only needed for the Desktop/Cowork install path (see Installing below) — a
Code install has no zip to rebuild.

In a Cowork session with this folder connected, asking Claude to rebuild the
zip is usually the least friction: it edits the source and repackages in one
pass, so `experts-protocol/` and `experts-protocol.skill` can't drift apart.
That's a convenience, not a requirement — the manual steps below always
work, whether run by hand, by a Code session opened at the repo root, or
on a machine with no Claude session open at all.

From the repo root:

```powershell
Compress-Archive -Path experts-protocol -DestinationPath experts-protocol.zip -Force
Move-Item experts-protocol.zip experts-protocol.skill -Force
```

The archive must contain the `experts-protocol/` folder at its root, not the
folder's contents loose at top level.

## Installing

Skills are installed per machine and per Claude subscription — there is no sync
between them. Claude Code and Claude Desktop/Cowork install a skill in
different ways; both are equally valid ways to run this one.

- **Claude Code** reads a skill straight from its source folder — no zip, no
  upload. Copy (or symlink) `experts-protocol/` into
  `~/.claude/skills/experts-protocol/` (global) or a project's
  `.claude/skills/` (project-scoped). Code doesn't watch the repo for
  changes, so re-copy after editing the source, the same way the zip below
  needs rebuilding.
- **Claude Desktop/Cowork** installs from the packaged `.skill` zip. Build or
  rebuild `experts-protocol.skill` (see below), then upload it through
  Desktop's skill settings on each machine that needs it.

The two reference files under `references/` are seed copies, regardless of
which path installed the skill. On first run against a new vault, the skill
copies them into the vault's `_ExpertsProtocol/` folder if they aren't
already there. **The vault copy is authoritative**; if the two ever
disagree, the vault is right and the bundled copy is stale.
