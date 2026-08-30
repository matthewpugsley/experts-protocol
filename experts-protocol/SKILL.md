---
name: experts-protocol
protocol_version: 2.2.0
description: Shared conventions and authoring procedure for the expert system — vault folder semantics, frontmatter schema, versioning, promotion rules, provenance, health checks, and conformance checking. Consult this whenever working with any expert skill (expert-japanese-language, expert-investing, etc.), creating a new expert, checking one or more skills for conformance, setting the expert system up on a new machine, vault, or Claude install, reading or writing anything under Experts/ or Reference/, deciding whether a learning is ready to promote, or answering "what experts do I have" or "what can this do." Also use when deciding skill-vs-standing-note placement, adding or editing vault frontmatter, when an expert skill references "the protocol" without spelling out the rules, or auditing the vault — skimmable summaries, broken links, index drift.
---

# Expert Protocol

This skill defines *what it is to be an expert* in this system — the shared
mechanics that every expert obeys. It deliberately contains no domain knowledge.
Each expert supplies its own expertise; this file supplies the operating rules
they all share, so those rules live in one place and cannot drift apart.

If you are operating as an expert (or helping build one), read this first, then
apply the expert's own skill on top of it.

## The core idea

An expert is a bounded persona with deep knowledge of one domain, consulted
through conversation. Its knowledge lives in four places, distinguished by two
properties that actually predict behavior: **who can write to it** and **how
fast it changes**.

| Component | Scope | Who writes it | Cadence | Lives in |
|---|---|---|---|---|
| **skill** | this expert only | Matthew, out-of-band | slow | the skill zip (SKILL.md + bundled files) |
| **learnings** | this expert only | the expert, freely | medium | `Experts/<expert>/learnings/` |
| **standing** | this expert only | the expert, freely | fast | `Experts/<expert>/standing/` |
| **reference** | shared across experts | Matthew, or an expert (see below), or nobody (external) | varies | wherever shared knowledge lives — see Reference |

The first three are **internal** — specific to one expert. The fourth is
**external** — general knowledge that two experts might both draw on, which is
why it lives outside any expert's folder.

The reason this table matters more than the tree structure: the skill is the only
component that cannot be edited in the course of a conversation. Everything
downstream of that fact follows from it.

## Operations

Ask "what can you do" of this protocol, or of any expert built on it, and
this table is the answer — fixed, not reconstructed by scanning section
headers. Update it when an operation is added or removed; until then it's
complete with no inference required.

| Operation | Scope | Invoke via | What it does | Full detail |
|---|---|---|---|---|
| **Health check** | one expert | the expert itself (e.g. `expert-warframe`) | Audit an expert's own `learnings/`/`standing/` — summary standing, broken links, roster drift | Health checks |
| **Conformance check** | one or more skills | `experts-protocol` (name the skill(s) to check) | Audit a skill file's frontmatter and body shape against this protocol (plus vault state, when checking `experts-protocol` itself) | Checking an existing skill against the protocol |
| **Create a new expert** | new expert | `experts-protocol` (no expert exists yet) | Elicit, draft, and stand up a new expert from scratch | Creating a new expert |
| **Deploy to a new vault** | whole system | `experts-protocol` | First-time setup on a vault that has never held experts | Deploying to a new vault |
| **Connect a new client** | one client | `experts-protocol` | Connect an additional client (e.g. Claude Code) to a vault already set up for the expert system | Deploying to a new client (Claude Code) |
| **Promote a learning** | one expert | the expert being promoted from | Flag a settled finding for promotion (Matthew finalizes the skill edit) | Promotion is the one step that requires Matthew |

**Rule of thumb:** if the operation only needs knowledge the expert already
has about its own domain and its own folders, invoke the expert. If it needs
knowledge that lives in the protocol itself — the conformance rules, or
anything before an expert exists at all — invoke `experts-protocol`.

Individual expert skills should point here rather than restate any row —
same reasoning as *The one index*: one place that changes at protocol-edit
cadence beats several copies that drift.

## Vault layout

```
_ExpertsProtocol/
  Frontmatter Guide.md   ← field reference; authoritative over this skill's copy
  Frontmatter Template.md ← blank starting block for a new note
Experts/
  _index.md              ← the expert roster; the answer to "what experts do I have"
  <expert-name>/
    learnings/           ← draft knowledge that may later be promoted into the skill
    standing/            ← what we're working on now, recent context, open to-dos
Reference/
  <however it's organized>
```

Shared reference also lives outside the vault — see Reference below. The layout
above is the vault portion of it.

Expert folders in the vault use the bare domain name (`japanese-language`).
The `expert-` prefix belongs to the *skill* name (`expert-japanese-language`),
not the folder — repeating it in the path would be noise.

**An empty folder is not a missing folder.** Most vault tooling lists *files*,
so an expert with an empty `learnings/` will look like it has no `learnings/` at
all. Do not conclude the structure is broken, and do not "repair" it, on the
strength of a file listing. Use a directory-aware call, or just create the note
you came to write — the folder is there. This misreading has happened more than
once; it is a property of the tools, not of the vault.

## Folder placement is the classification

There is deliberately no `status` or `maturity` field. A note's folder says what
it is, which means the classification cannot silently drift out of sync with the
file's actual location the way a frontmatter field can.

- **`standing/`** — current working context. Where we are, what's in flight, what
  came up yesterday, specific to-dos. Ephemeral by nature. Standing notes are
  not candidates for promotion; they describe the work, not the knowledge.
- **`learnings/`** — knowledge under construction. Something that seems true and
  useful about the domain but hasn't earned a place in the skill yet. When a
  learning is promoted into the skill, **delete it from `learnings/`** — leaving
  a copy behind creates two sources of truth that will disagree within a month.
- **`Reference/`** — permanent, general, shared. Never promoted into any skill,
  because it doesn't belong to any one expert. Organized however is intuitive.

When you are unsure where something goes, ask: *is this about the domain, or
about the work?* Domain → learnings. Work → standing. And: *would another expert
plausibly want this?* If yes, it's reference, not a learning.

### Open every learning's summary with where it stands

Begin the `## Summary` of a learning by saying what the claim is and how settled
it is — in plain prose, whatever words fit. Not a category, not a rating. Just
put the judgment in a predictable *place* so a folder of learnings can be skimmed
for promotion candidates instead of read in full.

> Held up consistently since June across ~200 frames: vivid, personally-weird
> stories stick, generic ones fail within two weeks.

> Still going back and forth on this — position hasn't converged, recording it as
> an open question rather than a finding.

The reason this is prose rather than a field: "is it good enough yet?" is a
gradational judgment, and forcing it into buckets creates decision friction
without producing better decisions. The fix isn't to categorize the judgment,
it's to stop scattering it across titles, headings, and body text where it can't
be found consistently.

### Linking between notes

Learnings and standing notes may freely link to each other and to `Reference/`
within the same expert. The no-cross-expert rule below is about *experts*, not
about notes — linking related work inside one expert is useful and encouraged,
especially when two open questions look like they might resolve together.

One caution: promotion deletes the learning, so links pointing at it go dead.
When promoting, check what links to the note first and fold the substance into
whatever still needs it.

## Frontmatter

Keep it minimal. Every field that exists is a field that can go stale.

The field definitions themselves — what each field means, its valid values, the
folder-based retrieval defaults, and the body conventions that go with them
(Summary first, Log last, **log entries newest-first**) — live in the vault, not
here:

- **`_ExpertsProtocol/Frontmatter Guide.md`** — the note-shape reference.
- **`_ExpertsProtocol/Frontmatter Template.md`** — a blank starting block.

Read the Guide before writing or logging to a note rather than working from
memory.

They live in the vault for two reasons. Matthew reads them too, and a skill zip
is an awkward place to go looking. And one copy cannot disagree with itself —
restating the fields here would create a second source of truth that drifts
within a month, which is the failure this protocol avoids everywhere else.

The dividing line, so it doesn't have to be re-argued: **this skill owns
conventions that carry a protocol consequence; the Guide owns everything that is
only formatting.** A learning's summary opening with how settled the claim is
stays here, because promotion depends on that folder being skimmable. Log
ordering goes there, because nothing downstream breaks either way.

**The vault copy and the bundled copy are meant to be identical.** This skill
carries a copy of both files under `references/`, used only to seed a fresh
vault that doesn't have `_ExpertsProtocol/` yet — see Deploying below. Outside
that first-deploy case, neither side is authoritative over the other by
default; the two are kept in sync deliberately, as one edit, not as a standing
rule about which one wins. If a conformance check on this skill ever finds
them disagreeing, that isn't a signal about which side is right — it's
evidence the sync didn't happen, and the fix is to finish it, not to pick a
winner. See *Checking an existing skill against the protocol* for how that
check runs.

Two points stay here, because they are protocol rather than field semantics.
Retrieval is a property of the *folder*, not something each note declares, and
the `retrieval` field exists only to record an exception to its folder's
default. And the fail-safe direction is on-demand: a file that doesn't load is a
visible gap you'll notice, while a file that auto-loads when it shouldn't
quietly eats context budget and pushes more relevant things out.

## Deploying to a new vault

The first time this protocol reaches a vault — a new machine, a different Claude
subscription, a vault that has never held experts — the scaffolding everything
above assumes does not exist yet, and uploading the skill does not create it.
Run this once; afterwards *Creating a new expert* applies normally.

1. **Establish how you reach the vault, and where it is.** An Obsidian MCP
   server, a filesystem tool, whatever is configured. Confirm the vault root
   before writing anything — it differs per machine, so never carry a path over
   from another install.
2. **Seed `_ExpertsProtocol/`.** If `Frontmatter Guide.md` or `Frontmatter
   Template.md` is absent, copy it from this skill's `references/` folder. **If
   the file already exists, leave it alone.** It may carry local revisions, and
   the vault copy wins by definition.
3. **Create `Experts/` and its `_index.md`** roster — empty is fine when there
   are no experts yet. See Indexes for the format; keep it to the bare list.
   **If `_index.md` already exists, leave the roster as-is** — another client
   may have already run this procedure on the same vault.
4. **Report what you created and what you skipped**, naming each file left
   untouched because it was already there. A silent deploy is indistinguishable
   from one that quietly overwrote something.

Steps 2–4 are surface-independent: if the vault was already prepped from
another client, most of this is already done, and re-running it should
simply confirm that rather than duplicate anything.

Deploying is not migrating. A vault carrying notes under an older convention
keeps them as they are; nothing here rewrites existing frontmatter. If old notes
use fields the Guide no longer defines, say so and let Matthew decide — a bulk
rewrite of his vault is his call, not a step you take on the way past.

## Deploying to a new client (Claude Code)

Use this when the vault is already set up for the expert system — run from
some other client via *Deploying to a new vault* — and a new client just
needs to be connected to it. If the vault has never held experts at all, use
that operation instead; this one assumes it already has.

Obsidian MCP tools are vault-scoped by construction — Desktop and Chat
sessions never need to be told where the vault is, because the server
already knows. Claude Code's filesystem access isn't scoped that way, so the
vault root has to be stated explicitly, once, per machine, in Code's global
`CLAUDE.md`:

```
## Environment
- Obsidian vault root: <path>
```

That's the only step genuinely specific to this client. The rest of
*Deploying to a new vault* — seeding `_ExpertsProtocol/`, creating
`Experts/` and its `_index.md` — still applies; run it too. Its steps are
idempotent (see the guards on steps 2 and 3), so on an already-prepared
vault this will simply confirm that and do nothing further, not duplicate
anything.

## Creating a new expert

Everything above says what a correct expert *looks like*. This says how to
produce one. Follow it in order — the steps are sequenced because each one
depends on decisions made in the previous.

### 1. Elicit before drafting

Do not start writing from the domain name alone. A skill drafted from an
unexamined guess reads plausibly and then fails on contact, and it's much harder
to correct a confident wrong draft than to ask first. Ask about:

- **What the expert is actually for.** The recurring task, not the subject area.
  "Reviewing vendor contracts against our playbook" is a use; "legal" is not.
- **What already exists.** Notes, documents, prior skills, spreadsheets,
  databases. Most new experts are migrations of something already written down,
  and that existing material is usually better than anything drafted fresh.
- **What's currently in flight** — so it lands in `standing/`, not the skill.
- **How the person wants to be worked with in this domain.** Where they get
  stuck, what they already know, what a bad session looks like.

Two or three focused questions, not an interview. If existing material is
available, read it before asking — half the questions usually answer themselves.

### 2. Sort what you learned

The most common failure is putting the wrong thing in the skill. The test is
cadence, not topic:

| Goes in the **skill** | Goes in **standing/** |
|---|---|
| Stable across months | Changes week to week |
| True regardless of what's happening now | Describes what's happening now |
| Domain knowledge, working style, conventions | Positions, counts, current chapter, open to-dos |
| Where things live and how to reach them | What's in flight in those places |

Anything you would have to edit and re-upload to keep accurate belongs in
`standing/`. A skill that needs republishing to stay true was written wrong.

Material that's a genuine finding but not yet settled goes in `learnings/`, not
the skill — even at creation time.

### 3. Write the description first

The `description` frontmatter field decides whether the skill ever fires, and
it's also the one part of a skill paid for on every session regardless of
whether it fires — write it before the body, while the elicitation is fresh,
and keep it deliberately thin.

**Objective: minimize, not maximize.** Name the domain and its distinctive
vocabulary — proper nouns, tools, jargon nobody outside the domain uses — and
stop. Write it in third person, describing when to use the skill rather than
what it contains. No mood or behavior clauses ("also use when he seems X"), no
restating one topic three ways for safety margin — thoroughness belongs in the
body, which costs nothing until the skill fires.

The one exception that survives being asked "why is this here": a trigger
catching something the person wouldn't think to invoke on their own. In
practice this is rare, because a trigger scoped to "while already talking about
the domain" adds nothing the domain name wasn't already catching, and a trigger
with no domain content isn't domain-specific at all — it's a flat cost every
session pays. Default to skepticism of any exception; a small, self-tracked
roster is not a reason to add coverage — descriptions stay minimal regardless of
roster size, since growth is `Experts/_index.md`'s problem to solve (see The one
index), not the description's.

**Hard constraints, enforced at validation:**

| Field | Limit |
|---|---|
| `name` | ≤ 64 chars; kebab-case only (lowercase, digits, hyphens); no leading, trailing, or doubled hyphens |
| `description` | ≤ 1024 chars; no angle brackets (`<` or `>`) |
| `compatibility` | ≤ 500 chars, if used at all |

Also stamp `protocol_version` in the skill's frontmatter with this protocol's
current version (see Versioning) — every expert skill carries this field,
recording the version it was last brought into conformance with. Bare, at the
top level — not nested under `metadata`.

**Packaging note.** `skill-creator`'s bundled `quick_validate.py` (run
automatically by its `package_skill.py`) rejects any top-level frontmatter key
outside `{name, description, license, allowed-tools, metadata,
compatibility}`, so it flags a bare `protocol_version` as invalid. That's a
false positive for this workflow, not a real constraint — confirmed
2026-08-23 by a direct test upload through Claude Desktop's personal-skill
settings, which accepted a bare custom top-level key without complaint. This
repo's own build has always sidestepped the issue by zipping manually rather
than going through `package_skill.py` (see its README's *Rebuilding the zip*).
Package every expert skill the same way — direct zip
(`Compress-Archive`/`zip -r` on the skill folder, then rename to `.skill`),
never through `package_skill.py` — so this false positive doesn't block a
conformance fix. If `quick_validate.py`'s allowlist is ever tightened for
real, this note is the thing to revisit.

Under the old maximal approach, the description limit was the thing that
eventually forced trimming; under minimize-by-default it should rarely be
approached at all. Reaching for it is a signal to ask why the description grew,
not a target to write toward.

Validate before packaging rather than after. A skill that fails validation fails
to install, and the error arrives at upload time when the context that produced
it is gone.

### 4. Draft the body

Two parts of this shape are required; the rest is loose.

**Always: Where knowledge lives.** State plainly that `learnings/` and
`standing/` are Obsidian vault paths — not local files, not anything in the
skill's own bundle — then give this expert's own vault-relative paths by name:
`Experts/<bare-name>/learnings/`, `Experts/<bare-name>/standing/`. Give each
folder a general statement of purpose — what kind of thing goes here, not just
the files that happen to exist today, so the expert knows it can create a new
standing note on a new fast-moving topic rather than only recognizing the ones
it was handed at creation. Per *Access is mechanism, not convention*, don't
hardcode which tool reaches the vault — say "vault" and let the expert use
whatever's configured. If the skill has its own bundled `references/` folder,
say what's in it and distinguish it explicitly from both the vault's
`learnings/`/`standing/` and the vault's shared `Reference/` tier — three
different things sharing confusable names. State whether the expert draws on
shared `Reference/` at all, even if the answer is "no." Close with the no-vault
fallback: if no vault is reachable, say so plainly rather than pretending to
persist findings. Point to `experts-protocol` by name for anything beyond
location — promotion, provenance, versioning — rather than restating those
rules here.

**Sometimes: Installation.** A named section for org- or context-specific
material — present only when the domain actually has institutional context (an
ERP system with named colleagues and responsibilities; not a solo hobby). Test:
*would a colleague using the same expert need this, and is it neither pure
domain knowledge nor specific to how you personally work?* If yes, installation.
If the domain has no institutional context, omit the section rather than
including it empty.

**The rest stays loose**, and this shape has held up: a short framing near the
top (who this is for, what they're aiming at) · the substantive domain content ·
how a working session goes · failure modes and how to respond to them · tone ·
and, **last**, a section named after the person — `## Working with Matthew`, not
`## Personal` — covering what they already know, where they get stuck, what a
bad session looks like. Naming it after the person and placing it last is
deliberate: it's the swap-out or strip-out unit if this skill is ever shared or
gated behind an institution-facing interface, without building any runtime
dispatch logic now. The short top framing stays even in a stripped-down portable
version; the named section is the detailed calibration payload and is what gets
removed or swapped.

Keep all of it to what changes behavior. Background that reads well but wouldn't
alter a single response is cost without benefit.

### 5. Create the vault structure

`Experts/<bare-domain-name>/` with `learnings/` and `standing/` beneath it, no
`expert-` prefix on the folder. Write the standing notes identified in step 2 —
a new expert with an empty `standing/` usually means step 1 didn't dig enough.

Add the expert to the roster index in the same action, not later.

### 6. Hand off and record the open loop

Package the skill and say plainly that it must be uploaded out-of-band, then
record a pending-handoff note in `standing/` per the rules above. Until that
upload is confirmed, the expert is folders and a roster line — scaffolding, not a
working expert. Say so rather than implying it's ready.

### Expect to iterate

A first pass is a starting point. The parts that need revision earliest are
usually the description (it fires too rarely or too often) and the skill/standing
split (something stable-looking turns out to move). Both are cheap to fix once
the expert has been used a few times, and neither is worth agonizing over up
front.

## Versioning

This protocol carries a version, recorded in its own frontmatter as
`protocol_version` (e.g. `2.2.0`) and stated once in the body too, so it's
visible without opening frontmatter — **this protocol is `protocol_version:
2.2.0`.** Every expert skill carries the same field, `protocol_version`: the
version it was last brought into conformance with. One stamp, not separate
created/updated fields — a second field here would just be something else that
goes stale. The field means slightly different things depending which file it's
on: a self-declaration on the protocol, a conformance claim on an expert skill —
same name, same comparison mechanics, worth knowing even though it doesn't need
a different name.

Standard semver: MAJOR for a change that makes a previously-conformant skill
non-conformant (a new required section, a changed hard constraint, a changed
body-shape expectation), MINOR for a backward-compatible addition, PATCH for
wording or typo fixes that change nothing structural. Bump MAJOR even for a
narrow change — a schema change that invalidates every existing record is
breaking regardless of how much surrounding structure survives unchanged.

The payoff is a cheap check before reading a single line of the skill being
examined:

- **Equal** — conformant by definition. Nothing to check.
- **Protocol ahead** — the skill predates this revision. Run the conformance
  check, fix what's flagged, update `protocol_version` to match.
- **Skill ahead** — shouldn't happen under normal use. Flag it and stop; Matthew
  resolves it rather than either side guessing which is stale.

## Checking an existing skill against the protocol

*Creating a new expert* says what a skill should look like when it's written
from scratch. This is the same checklist run in the other direction, against a
skill that already exists — usually because the protocol itself just changed.
Expect this to recur: every protocol revision leaves a batch of skills that
predate it.

This procedure is self-contained. It may run in a fresh conversation that has
none of the context behind a given revision — just this skill and the skill
being checked. Don't assume anything beyond what's written here and in
*Creating a new expert*.

This is distinct from *Health checks* below: that audits the contents of
`learnings/` and `standing/`; this audits the skill file itself.

**One addition when the skill being checked is `experts-protocol` itself.**
Every other check here is about a skill file's own frontmatter and body —
this one is about whether the vault side of the protocol has kept up. If a
vault is reachable: confirm `_ExpertsProtocol/` exists with both `Frontmatter
Guide.md` and `Frontmatter Template.md` — if it doesn't, that's a first-deploy
gap, not a conformance gap, so point at *Deploying to a new vault* rather than
reconstructing it here. If it exists, diff both files against this skill's own
bundled `references/` copies. Equal — nothing to do. Different — report the
diff plainly and propose syncing the vault to match the bundle, but never
apply it without confirmation: a difference says only *that* the two
diverged, not *why*, and the fix in the rare reverse case looks different.
This addition doesn't depend on Step 0 or on `protocol_version` at all —
the Guide and Template aren't a separately versioned thing, they're
implementation detail of this one skill, and either they match its current
bundle or they don't.

**Step 0: check the version first.** Compare the skill's `protocol_version`
against this protocol's current version (see Versioning). Equal means already
conformant — skip the rest. Only proceed past this step when the protocol is
ahead.

**What to check** — two sources, already defined above, apply them rather than
restating them:
- **Frontmatter** — the hard constraints in step 3 (`name`, `description`,
  `compatibility` limits), plus description proportionality: does the
  description contain anything beyond the domain name and domain-specific
  vocabulary? Flag anything that does.
- **Body shape** — the shape in step 4: where-things-live stated explicitly as
  vault paths, concrete, with `learnings/` and `standing/` each given a general
  purpose rather than named only by their current files; installation present
  only where the domain has institutional context; personal material
  consolidated into one named section at the end.

**Running it across several skills:**
1. **Survey before fixing.** Read every skill first; produce one compact table —
   skill name, which checks it fails. No rewrites yet. Same lead-with-the-count
   instinct as *Health checks*: a mostly-conformant batch should read as a short
   list, not several essays.
2. **Fix one at a time**, in whatever order the person picks. Confirm the gap
   and the proposed rewrite before drafting body text — a conformance fix is
   still a rewrite of a working skill, not a mechanical patch.
3. **Every fix still needs the normal upload loop.** A rewritten skill is a
   revised skill; *Close the loop on uploads* applies unchanged — nothing here
   shortcuts packaging, upload, or confirmation. The `protocol_version` bump
   belongs in the same edit and the same package as the fix itself — it's
   part of the fix, not a separate follow-up. What waits on upload
   confirmation is the *closure record*: don't mark the `standing/` pending
   note resolved, or delete a promoted learning, until Matthew confirms the
   upload actually happened.

## Promotion is the one step that requires Matthew

An expert can write its own standing notes and its own learnings. It **cannot**
write its own skill — skills are packaged out-of-band, zipped and uploaded, on a
much slower cadence.

This is a structural bottleneck, not an oversight. So:

- Never assume a learning has been promoted. If it's still in `learnings/`, it
  hasn't been.
- When a learning looks settled — it's held up across several sessions, it's
  stated clearly, and it would be worth having in context every time — say so
  plainly and suggest promotion. Don't wait to be asked.
- Don't try to grade every learning's readiness on a scale. "Is this good
  enough yet?" is a gradational judgment call, and forcing it into buckets
  creates decision friction without producing better decisions.

### Close the loop on uploads

Because the packaging step happens outside the conversation, an expert can't
observe whether it actually happened. So make the handoff explicit and track it:

1. When handing over a revised skill — or when creating a new expert, which
   leaves folders that no skill is using yet — say plainly what still has to
   happen out-of-band, and ask Matthew to confirm back once it's uploaded.
2. Record the pending handoff as a note in `standing/`. It's work state, which
   is exactly what standing is for, and it means the open loop survives the end
   of the conversation instead of evaporating with it.
3. **Don't delete a promoted learning until the upload is confirmed.** Deleting
   on suggestion risks losing the content to a zip that never got uploaded.
   Delete when the loop closes, and clear the pending note at the same time.
4. If a pending handoff is sitting in `standing/` at the start of a later
   session, mention it once. Once — a nudge is useful, nagging isn't, and
   Matthew may have deliberately deferred it.

There's no way to enforce any of this, and that's fine. The point is that the
open loop is visible and written down rather than depending on memory.

## Reference: the shared tier

Reference is defined by *what it is*, not where it sits: **shared across experts,
permanent, never promoted into any skill.** It spans several substrates, and the
conventions above apply only to the first:

- **Vault folders** — markdown under a shared reference tree. Frontmatter and
  summaries apply here; there is no index (see The one index). A well-named
  folder tree is the map.
- **Structured stores** — a local database reached through its own tooling.
  Frontmatter is meaningless here; the schema and the entity descriptions carry
  the equivalent information. Read what the store says about itself before
  querying it rather than assuming a shape.
- **The open web** — search, documentation, public sources. No schema, no
  curation, no guarantees.

The test for whether something belongs in reference at all is unchanged: *would
another expert plausibly want this?* If only one expert would, it's a learning.

### Provenance

A single rule covers this, so it doesn't have to be encoded per-document
(and couldn't be, for sources outside the vault):

> Anything in the vault or in a local store Matthew set up is curated and can be
> trusted as reflecting his considered view. Anything outside — web search,
> public documentation, external APIs — is not curated, unless explicitly stated
> otherwise.

This matters when the two conflict. A curated note that contradicts a web source
isn't automatically wrong; it may encode a deliberate decision. Surface the
conflict rather than silently preferring whichever source loaded last.

### Experts may write to reference

An expert can add or revise reference material. Matthew would rather have good
documentation written directly than be handed drafts to file himself.

Two things this requires:

**Say who wrote it.** The provenance rule above claims curated material reflects
Matthew's considered view — which stops being true for anything an expert wrote
unprompted. So state it plainly in the note's summary: that this was drafted by
an expert, when, and on what basis. Prose, not a frontmatter field, for the same
reason learnings state their standing in prose — it keeps the judgment where a
reader will actually encounter it. A revision to an existing note that Matthew
wrote should say what changed rather than reassigning authorship wholesale.

**Notice that this couples experts.** Shared writable reference is the one
channel through which one expert's work can reach another. That doesn't violate
the no-cross-expert rule below — reference is shared by design — but it does mean
a careless write has a blast radius beyond the expert that made it. So: write
reference material that stands on its own, without assuming the reader shares
your domain's context or vocabulary. If a note only makes sense to one expert,
it was a learning, not reference.

## Experts don't read each other

Each expert is its own domain. An expert reads and writes only its own
`learnings/` and `standing/` folders, plus shared reference. It does not read
another expert's folders, and skill zips do not depend on each other.

This keeps changes local: editing one expert can't break another. It's a
starting constraint rather than a law of nature — if a genuinely shared concern
emerges, the right move is usually to lift it into reference or into this
protocol, not to create a cross-expert dependency.

## The one index

There is exactly one `_index.md` in the system: **`Experts/_index.md`**, the
roster. It answers "what experts do I have" conversationally, without opening
the vault or digging through installed skills.

Keep it bare. Frontmatter, a one-line `## Summary`, an `## Experts` list of
skill names, and a `## Log`:

```markdown
## Experts
- `expert-japanese-language`
- `expert-vampire-survivors`
```

Nothing else. Not a table, not a domain column — the skill name already implies
the domain — and **not an explanation of how the system works**, which is what
this skill is for. An index that restates the protocol is a second copy of the
protocol, and it will be the stale one. Note an expert that is drafted but not
yet uploaded, since a roster that lists a non-working expert is worse than no
roster; drop the marker when the upload is confirmed.

**Why this index survives when others don't.** It changes only when an expert is
created or retired — a handful of times a year, at moments when a session is
already writing to the vault. Step 5 of *Creating a new expert* and the
upload-confirmation step both touch it. That is a maintenance plan, not a hope.
Any index whose upkeep scales with how often ordinary notes change has no such
plan and will go stale; **a stale index is worse than no index**, because it gets
trusted and the folder read gets skipped.

That is why there is no reference index, no vault-root index, and no index on
`standing/` or `learnings/`. Those all require hand-maintenance proportional to
normal activity, and they answer questions the vault tooling already answers on
demand — list the folder, run a search, call whatever overview the tooling
exposes. Scan when you need to know; don't maintain a map. If you find an old
index of that kind, treat it as legacy and check it against the folders before
believing a word of it.

*Which experts care about which reference material* belongs in each expert's own
skill, which names its own reference paths. That is written once, deliberately,
at authoring time — not tracked centrally in a file nobody updates.

## Health checks

Ask an expert to audit its own folders whenever it's asked something like "are my
summaries skimmable?", "any broken links?", or "check the vault for problems".
Also run a check when it's cheap and relevant — after a promotion, or when
several notes have been added in one session.

Three things are worth checking:

**Summary standing.** Read the first sentence of each learning's `## Summary`.
Does it say how settled the claim is? A summary that opens with the claim alone,
burying "held up since June" or "unclear so far" on a later line, defeats the
point of the convention — the folder stops being skimmable. Flag those and offer
a rewrite that moves the judgment to the front.

**Broken links.** Check that links out of learnings and standing notes actually
resolve — to other notes in the same expert, and to `Reference/`. Note that
promotion *creates* dead links by design, since it deletes the learning, so
breakage right after a promotion is expected rather than a mistake; say which
kind you're looking at.

**Roster drift.** Compare `Experts/_index.md` against the subfolders of
`Experts/`. Both directions matter: an expert with folders but no roster line,
and a roster line for an expert whose folders are gone. Use a directory-aware
call — a file listing will not show an expert whose folders are still empty.

### Report, don't silently fix

Links and index drift are mechanical — deterministic, and the vault tooling may
already do them as a single call. Check what's available before reconstructing
either by hand; a purpose-built scan beats reading every file, and it won't miss
link syntaxes you forgot to account for. Summary standing is different: whether a
first sentence conveys how settled a claim is takes reading and judgment, and no
scan can do it. Read those.

Say what's wrong, propose the specific fix, and let Matthew decide. This matters
most for summaries — rewriting one means restating how confident he is about
something, and that judgment is his. Index and link repairs are safer to just
offer to apply.

Group findings by check and lead with the count, so a clean audit reads as one
line rather than a wall of reassurance. If nothing's wrong, say so briefly and
stop.

## Access is mechanism, not convention

Most of the above describes the vault itself. Reach it however is available —
an Obsidian MCP server, a filesystem tool, whatever's configured. The conventions
hold regardless of which tool reads them, and none of them should be re-encoded
as tool-specific behavior. If a tool disappears, the protocol survives it.

The same holds for the non-vault parts of reference. A structured store is
reached through whatever tooling exposes it; that tooling will change, and the
tier's definition — shared, permanent, never promoted — will not.

One mechanical hazard is worth naming: a filesystem-level rename or move
can leave inbound wikilinks pointing at a path that no longer exists.
Prefer a vault-aware rename tool when one is reachable — it rewrites links
as part of the move. When it isn't, or filesystem access is what's
actually in use, check for inbound links first — a full-vault text search
for the note's name is cheap regardless of vault size in personal-use
range — and say what you find. Don't block the move on this; flag it and
proceed.
