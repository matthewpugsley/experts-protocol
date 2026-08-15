# experts-protocol

Source and packaged build of the **experts-protocol** skill for Claude — the
shared conventions and authoring procedure behind a small system of
domain-specific "expert" skills backed by an Obsidian vault.

The skill itself defines what it is to be an expert in that system: where
knowledge lives (skill vs. standing notes vs. learnings vs. shared reference),
how a learning gets promoted into a skill, how to create a new expert, and how
to deploy the whole thing to a vault that has never had one.

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

From the repo root:

```powershell
Compress-Archive -Path experts-protocol -DestinationPath experts-protocol.zip -Force
Move-Item experts-protocol.zip experts-protocol.skill -Force
```

The archive must contain the `experts-protocol/` folder at its root, not the
folder's contents loose at top level.

## Installing

Skills are installed per machine and per Claude subscription — there is no sync
between them. Upload `experts-protocol.skill` through Claude Desktop's skill
settings on each machine that needs it.

The two reference files under `references/` are seed copies. On first run
against a new vault, the skill copies them into the vault's `_ExpertsProtocol/`
folder if they aren't already there. **The vault copy is authoritative**; if the
two ever disagree, the vault is right and the bundled copy is stale.

## Related

The expert skills themselves (`expert-japanese-language`,
`expert-vampire-survivors`, …) are separate skills with their own packages.
This repo holds only the shared protocol they all obey.
