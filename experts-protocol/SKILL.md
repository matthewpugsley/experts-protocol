---
name: experts-protocol
protocol_version: 4.0.0
description: Matthew's personal expert system — converses as whichever domain expert he explicitly names (warframe, japanese-language, vampire-survivors, or any other domain under Experts/ in his Obsidian vault) when invoked directly ("talk to my warframe expert") or via a fixed per-Project/per-client instruction naming a domain. Also use for maintaining the system itself: creating a new expert, checking a profile note's or this skill's own conformance, deploying to a new vault or client, deciding note placement (profile vs. standing vs. learnings), adding or editing vault frontmatter, promoting a learning, or auditing the vault — skimmable summaries, broken links.
---

# Expert Protocol

This skill defines *what it is to be an expert* in this system — the shared
mechanics that every expert obeys. It deliberately contains no domain knowledge.
Each expert supplies its own expertise; this file supplies the operating rules
they all share, so those rules live in one place and cannot drift apart.

If you are operating as an expert (or helping build one), read this first, then
load that expert's own profile note from the vault on top of it — see
*Consulting an expert*.

## The core idea

An expert is a bounded persona with deep knowledge of one domain, consulted
through conversation. Unlike earlier versions of this protocol, an expert is
not itself a packaged skill — only this protocol is. An expert is a folder in
the vault; `experts-protocol` is what knows how to read and operate on it.

Its knowledge lives in four places, distinguished by two properties that
actually predict behavior: **who can write to it** and **how fast it
changes**.

| Component | Scope | Who writes it | Cadence | Lives in |
|---|---|---|---|---|
| **profile** | this expert only | Matthew, on the expert's proposal | slow | `Experts/<expert>/profile.md` |
| **learnings** | this expert only | the expert, freely | medium | `Experts/<expert>/learnings/` |
| **standing** | this expert only | the expert, freely | fast | `Experts/<expert>/standing/` |
| **reference** | shared across experts | Matthew, or an expert (see below), or nobody (external) | varies | wherever shared knowledge lives — see Reference |

The first three are **internal** — specific to one expert. The fourth is
**external** — general knowledge that two experts might both draw on, which is
why it lives outside any expert's folder.

The reason this table matters more than the tree structure: the profile note is
the one component whose edits go through Matthew before landing. That's an
editorial gate now, not a technical one — the note lives in the vault like
everything else, and nothing stops it being edited mechanically in the course
of a conversation. It just shouldn't be *rewritten* without confirmation.
Everything downstream of that distinction follows from it.

## Operations

Ask "what can you do" of this protocol, or of any expert built on it, and
this table is the answer — fixed, not reconstructed by scanning section
headers. Update it when an operation is added or removed; until then it's
complete with no inference required.

| Operation | Scope | Invoke via | What it does | Full detail |
|---|---|---|---|---|
| **Consult an expert** | one expert | `experts-protocol`, naming the domain (e.g. "talk to my warframe expert") | Load that expert's profile note (plus relevant `standing/`/`learnings/`) from the vault and operate as that expert on top of this protocol | Consulting an expert |
| **Health check** | one expert | `experts-protocol`, while consulting that expert | Audit an expert's own `learnings/`/`standing/` — summary standing, broken links | Health checks |
| **Conformance check** | one expert's profile note, or this protocol's own vault-side files | `experts-protocol` (name what to check) | Audit a profile note's shape against this protocol, or check this skill's bundled Guide/Template against the vault copies | Checking an expert's profile note · Checking experts-protocol itself |
| **Create a new expert** | new expert | `experts-protocol` (no expert exists yet) | Elicit, draft, and stand up a new expert from scratch | Creating a new expert |
| **Deploy to a new vault** | whole system | `experts-protocol` | First-time setup on a vault that has never held experts | Deploying to a new vault |
| **Connect a new client** | one client | `experts-protocol` | Connect an additional client (e.g. Claude Code) to a vault already set up for the expert system | Deploying to a new client (Claude Code) |
| **Promote a learning** | one expert | `experts-protocol`, while consulting the expert being promoted from | Flag a settled finding for promotion into the profile note (Matthew confirms before it's written) | Promotion is the one step that requires Matthew |

**Rule of thumb:** every operation invokes `experts-protocol` — there is no
other skill to invoke, since experts are vault folders, not skills. Naming a
domain when you invoke it (*Consulting an expert*) is what puts operations
like *Health check* or *Promote a learning* in the context of one expert
instead of the protocol in the abstract.

## Consulting an expert

There's no per-domain skill to trigger automatically, and that's deliberate —
this protocol doesn't try to guess from conversation content when an expert
is relevant. Consulting one is explicit: name the domain when you invoke
`experts-protocol`, the same way you'd seek out a particular professor rather
than hoping a general announcement reaches the right one.

In practice this looks like:

- **In conversation, directly** — "I'd like to talk to my warframe expert," or
  similar. `experts-protocol` reads that as naming the domain and loads
  `Experts/warframe/`.
- **Standing instruction, per Project or per client** — a Chat Project (or
  Code's project-level `CLAUDE.md`) can carry a fixed instruction like "use
  experts-protocol for the warframe expert," so a Project dedicated to one
  domain doesn't need to be told every session.

Either way, once a domain is named: read that expert's profile note
(`Experts/<domain>/profile.md`) in full — as an explicit step in this
procedure, not via auto-load; nothing in this system auto-loads by default,
profile notes included — then check `standing/` for anything currently in
flight, and `learnings/` if the conversation touches something still under
construction. Operate as that expert on top of this protocol's mechanics for
the rest of the conversation, until told otherwise or the conversation ends.

This is deliberately not keyword-triggered. A trigger scoped tightly enough to
avoid false positives ends up only firing when the domain was already obvious
from context, which adds nothing; scoped loosely enough to catch ambiguous
cases, it fires on the wrong expert as often as the right one. Naming the
domain costs one clause and removes the guesswork entirely.

## Vault layout

```
_ExpertsProtocol/
  Frontmatter Guide.md   ← field reference; authoritative over this skill's copy
  Frontmatter Template.md ← blank starting block for a new note
Experts/
  <expert-name>/
    profile.md            ← the profile note: what this expert is, how it
                             works, where its own knowledge lives. Read as an
                             explicit step whenever the expert is consulted —
                             no retrieval flag needed, same as everything
                             else in this system.
    learnings/            ← draft knowledge that may later be promoted into
                             the profile note
    standing/             ← what we're working on now, recent context, open to-dos
Reference/
  <however it's organized>
```

Shared reference also lives outside the vault — see Reference below. The layout
above is the vault portion of it.

Expert folders in the vault use the bare domain name (`japanese-language`),
matched by the name you give when consulting the expert — see *Consulting an
expert*. The profile note inside it is always named `profile.md`, not the
domain name — a fixed filename means finding it never depends on remembering
or guessing how a given expert named its own note. Earlier versions of this
protocol packaged each expert as its own skill and prefixed that skill's name
with `expert-` (`expert-japanese-language`); that prefix was specific to the
retired per-expert skill and never applied to the vault path.

**An empty folder is not a missing folder.** Most vault tooling lists *files*,
so an expert with an empty `learnings/` will look like it has no `learnings/` at
all. Do not conclude the structure is broken, and do not "repair" it, on the
strength of a file listing. Use a directory-aware call, or just create the note
you came to write — the folder is there. This misreading has happened more than
once; it is a property of the tools, not of the vault.

**A remembered or stated filename is not a confirmed one.** A profile note's
"where things live" section may name current files as illustrative examples,
and prior turns or memory may carry forward names from an earlier session.
Neither is ground truth once a folder has been restructured, split, or
renamed into. Before reading or writing to a specific file — and especially
before treating a profile note's list of filenames as exhaustive — list the
directory. This is the same failure as the empty-folder case above, just in
the opposite direction: trusting an assumption about contents instead of
checking them. The fix costs one cheap call; skipping it risks writing to a
file that no longer plays the role you think it does, or missing one that
now does.

## Folder placement is the classification

There is deliberately no `status` or `maturity` field. A note's folder (or, for
the profile note, its position at the expert's root) says what it is, which
means the classification cannot silently drift out of sync with the file's
actual location the way a frontmatter field can.

- **`profile.md`** — settled, stable knowledge about the expert itself: what
  it is, how it works, its own working style, where its own knowledge lives.
  The one file in an expert's folder that requires Matthew's confirmation to
  change — see *Promotion is the one step that requires Matthew*.
- **`standing/`** — current working context. Where we are, what's in flight, what
  came up yesterday, specific to-dos. Ephemeral by nature. Standing notes are
  not candidates for promotion; they describe the work, not the knowledge.
- **`learnings/`** — knowledge under construction. Something that seems true and
  useful about the domain but hasn't earned a place in the profile note yet.
  When a learning is promoted, **delete it from `learnings/`** — leaving a
  copy behind creates two sources of truth that will disagree within a month.
- **`Reference/`** — permanent, general, shared. Never promoted into any
  profile note, because it doesn't belong to any one expert. Organized
  however is intuitive.

When you are unsure where something goes, ask: *is this about the domain, or
about the work?* Domain → learnings (or, once settled, the profile note). Work
→ standing. And: *would another expert plausibly want this?* If yes, it's
reference, not a learning.

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
winner. See *Checking experts-protocol itself* for how that check runs.

Two points stay here, because they are protocol rather than field semantics.
Retrieval is a property of the *folder*, not something each note declares, and
the `retrieval` field exists only to record an exception to its folder's
default. And the fail-safe direction is on-demand: a file that doesn't load is a
visible gap you'll notice, while a file that auto-loads when it shouldn't
quietly eats context budget and pushes more relevant things out.

The profile note needs no `retrieval` field at all: it isn't loaded by a
retrieval mechanism, it's read as an explicit step of *Consulting an
expert*, the same way `standing/` and `learnings/` are read by explicit
procedure rather than auto-load.

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
3. **Create `Experts/`.** Empty is fine when there are no experts yet — no
   roster file to seed; the folder itself is the index (see *No indexes*).
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
`Experts/` — still applies; run it too. Its steps are
idempotent (see the guards on steps 2 and 3), so on an already-prepared
vault this will simply confirm that and do nothing further, not duplicate
anything.

## Creating a new expert

Everything above says what a correct expert *looks like*. This says how to
produce one. Follow it in order — the steps are sequenced because each one
depends on decisions made in the previous.

### 1. Elicit before drafting

Do not start writing from the domain name alone. A profile note drafted from an
unexamined guess reads plausibly and then fails on contact, and it's much harder
to correct a confident wrong draft than to ask first. Ask about:

- **What the expert is actually for.** The recurring task, not the subject area.
  "Reviewing vendor contracts against our playbook" is a use; "legal" is not.
- **What already exists.** Notes, documents, prior skills, spreadsheets,
  databases. Most new experts are migrations of something already written down,
  and that existing material is usually better than anything drafted fresh.
- **What's currently in flight** — so it lands in `standing/`, not the profile
  note.
- **How the person wants to be worked with in this domain.** Where they get
  stuck, what they already know, what a bad session looks like.

Two or three focused questions, not an interview. If existing material is
available, read it before asking — half the questions usually answer themselves.

### 2. Sort what you learned

The most common failure is putting the wrong thing in the profile note. The
test is cadence, not topic:

| Goes in the **profile note** | Goes in **standing/** |
|---|---|
| Stable across months | Changes week to week |
| True regardless of what's happening now | Describes what's happening now |
| Domain knowledge, working style, conventions | Positions, counts, current chapter, open to-dos |
| Where things live and how to reach them | What's in flight in those places |

Anything that changes week to week belongs in `standing/`, not the profile
note. A profile note that needs frequent rewriting to stay true was written
wrong — it's tracking work, not domain knowledge.

Material that's a genuine finding but not yet settled goes in `learnings/`, not
the profile note — even at creation time.

### 3. Draft the profile note

One part of this shape is required; the rest is loose.

**Always: where knowledge lives.** State plainly, in the profile note itself,
that `learnings/` and `standing/` are this expert's own vault paths, siblings
of the profile note: `Experts/<bare-name>/learnings/`,
`Experts/<bare-name>/standing/`. Give each folder a general statement of
purpose — what kind of thing goes here, not just the files that happen to
exist today, so the expert knows it can create a new standing note on a new
fast-moving topic rather than only recognizing the ones it was handed at
creation. Per *Access is mechanism, not convention*, don't hardcode which tool
reaches the vault — say "vault" and let the expert use whatever's configured.
State whether the expert draws on shared `Reference/` at all, even if the
answer is "no." Close with the no-vault fallback: if no vault is reachable,
say so plainly rather than pretending to persist findings — that applies to
every write this expert makes, not just `standing/`/`learnings/`. Point to
`experts-protocol` by name for anything beyond location — promotion,
provenance — rather than restating those rules here.

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
deliberate: it's the swap-out or strip-out unit if this profile note is ever
shared or gated behind an institution-facing interface, without building any
runtime dispatch logic now. The short top framing stays even in a
stripped-down portable version; the named section is the detailed calibration
payload and is what gets removed or swapped.

Keep all of it to what changes behavior. Background that reads well but wouldn't
alter a single response is cost without benefit.

No `retrieval` field is needed on the profile note — see *Frontmatter*. It's
read by an explicit step of *Consulting an expert*, not by auto-load.

### 4. Create the vault structure

`Experts/<bare-domain-name>/` with `learnings/` and `standing/` beneath it, no
`expert-` prefix on the folder, and the profile note drafted in step 3 written
to `Experts/<bare-domain-name>/profile.md`. Write the standing notes
identified in step 2 — a new expert with an empty `standing/` usually means
step 1 didn't dig enough.

### 5. It's live

Once the profile note and folder structure exist in the vault, the expert is
usable — there's no packaging or upload step standing between drafting it and
consulting it the way there was under the old skill-per-expert model. Say so
plainly rather than treating it as unfinished.

### Expect to iterate

A first pass is a starting point. The parts that need revision earliest are
usually the elicitation depth (something stable-looking turns out to move) and
the profile/standing split. Both are cheap to fix once the expert has been
consulted a few times — editing the profile note directly is exactly as cheap
as editing any other vault note, it just still goes through Matthew — and
neither is worth agonizing over up front.

## Versioning

This protocol carries a version, recorded in its own frontmatter as
`protocol_version` (e.g. `4.0.0`) and stated once in the body too, so it's
visible without opening frontmatter — **this protocol is `protocol_version:
4.0.0`.** Nothing else carries this field. Earlier versions of this protocol
had every expert skill stamp its own `protocol_version` as a conformance
claim; that machinery is gone along with per-expert skills themselves — there
is no longer a per-expert file to compare a version number against, and
tracking one on a profile note would just be something else to go stale.

Standard semver still governs changes to this protocol itself: MAJOR for a
change that makes a previously-conformant profile note or vault layout
non-conformant (a new required section, a changed body-shape expectation),
MINOR for a backward-compatible addition, PATCH for wording or typo fixes that
change nothing structural.

Without per-expert version stamps, there's no cheap equal/ahead/behind check
to run before looking at an expert. *Checking an expert's profile note against
this protocol*, next, runs whenever asked, or whenever this protocol changes
enough that older profile notes are worth revisiting — not gated on a version
comparison.

## Checking an expert's profile note against this protocol

*Creating a new expert* says what a profile note should look like when it's
written from scratch. This is the same checklist run in the other direction,
against a profile note that already exists — usually because this protocol
itself just changed shape. Expect this to recur: a protocol revision can leave
older profile notes behind even though nothing forces them to update.

This is distinct from *Health checks* below: that audits the contents of
`learnings/` and `standing/`; this audits the profile note itself. It's also
distinct from *Checking experts-protocol itself*, next: that's about this
skill's own bundled files, not about any individual expert.

There's no version number to check first — see *Versioning*. Run this
whenever asked, or when you notice a profile note that looks like it predates
a shape change to this protocol.

**What to check**, against the shape in *Creating a new expert*, step 3:
- Named `profile.md`, no `retrieval` field set on it (it's read by explicit
  procedure, not auto-load — see *Consulting an expert*).
- Where-things-live stated explicitly as vault paths, concrete, with
  `learnings/` and `standing/` each given a general purpose rather than named
  only by their current files.
- Installation present only where the domain has institutional context.
- Personal material consolidated into one named section (`## Working with
  Matthew`) at the end.

**Checking several at once:** survey before fixing — read every profile note
first, produce one compact table (expert name, which checks it fails), no
rewrites yet. Same lead-with-the-count instinct as *Health checks*. Then fix
one at a time, in whatever order Matthew picks, confirming the gap and the
proposed rewrite before drafting — a conformance fix is still a rewrite of a
working profile note, per *Promotion is the one step that requires Matthew*,
not a mechanical patch.

## Checking experts-protocol itself

This is about whether the vault side of the protocol has kept up with this
skill's own bundled copy — not about any individual expert's profile note.

If a vault is reachable: confirm `_ExpertsProtocol/` exists with both
`Frontmatter Guide.md` and `Frontmatter Template.md` — if it doesn't, that's a
first-deploy gap, not a conformance gap, so point at *Deploying to a new
vault* rather than reconstructing it here. If it exists, diff both files
against this skill's own bundled `references/` copies. Equal — nothing to do.
Different — report the diff plainly and propose syncing the vault to match
the bundle, but never apply it without confirmation: a difference says only
*that* the two diverged, not *why*, and the fix in the rare reverse case looks
different.

## Promotion is the one step that requires Matthew

An expert can write its own standing notes and its own learnings freely. It
**cannot** write its own profile note — that edit needs Matthew's confirmation
first.

This is an editorial bottleneck now, not a technical one. Under the old
per-expert-skill model it was also a technical bottleneck — packaged, zipped,
uploaded, on a much slower cadence — but that mechanism is gone. The profile
note is an ordinary vault note, and editing it is mechanically no different
from editing a learning; the gate exists because that note is what colors
every future conversation with this expert, and that judgment call stays
Matthew's.

So:

- Never assume a learning has been promoted. If it's still in `learnings/`, it
  hasn't been.
- When a learning looks settled — it's held up across several sessions, it's
  stated clearly, and it would be worth having in context every time — say so
  plainly and suggest promotion. Don't wait to be asked.
- Don't try to grade every learning's readiness on a scale. "Is this good
  enough yet?" is a gradational judgment call, and forcing it into buckets
  creates decision friction without producing better decisions.
- Once Matthew confirms, write the change to the profile note and delete the
  promoted learning in the same pass. There's no packaging or upload step to
  wait on, so there's nothing to leave half-closed — do it in one motion once
  he's said yes.

## Reference: the shared tier

Reference is defined by *what it is*, not where it sits: **shared across experts,
permanent, never promoted into any profile note.** It spans several substrates, and the
conventions above apply only to the first:

- **Vault folders** — markdown under a shared reference tree. Frontmatter and
  summaries apply here; there is no index (see *No indexes*). A well-named
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
folder — profile note, `learnings/`, and `standing/` — plus shared reference.
It does not read another expert's folder, and profile notes do not depend on
each other.

This keeps changes local: editing one expert can't break another. It's a
starting constraint rather than a law of nature — if a genuinely shared concern
emerges, the right move is usually to lift it into reference or into this
protocol, not to create a cross-expert dependency.

## No indexes

There is no hand-maintained index anywhere in this system — not for the
expert roster, not for `Reference/`, not for `standing/` or `learnings/`.
`Experts/_index.md` existed once and was removed 2026-09-09: the folders
under `Experts/` already answer "what experts do I have" as well as a
roster line did, so the roster was hand-maintenance duplicating a live
directory listing.

**A stale index is worse than no index**, because it gets trusted and the
folder read gets skipped — this is why the roster didn't survive, and it's
the same reasoning that already ruled out a reference index, a vault-root
index, or an index on `standing/`/`learnings/`. Every one of those requires
upkeep proportional to normal activity, and each answers a question the
vault tooling already answers on demand.

Scan when you need to know; don't maintain a map. List the `Experts/`
directory for the roster, list an expert's `learnings/`/`standing/` for
what's currently there, search `Reference/` for shared material. If you
find an old index of any kind — including a leftover `_index.md` from
before this change — treat it as legacy and check it against the folders
before believing a word of it.

*Which experts care about which reference material* belongs in each expert's own
profile note, which names its own reference paths. That is written once,
deliberately, at authoring time — not tracked centrally in a file nobody
updates.

## Health checks

Ask an expert to audit its own folders whenever it's asked something like "are my
summaries skimmable?", "any broken links?", or "check the vault for problems".
Also run a check when it's cheap and relevant — after a promotion, or when
several notes have been added in one session.

Two things are worth checking:

**Summary standing.** Read the first sentence of each learning's `## Summary`.
Does it say how settled the claim is? A summary that opens with the claim alone,
burying "held up since June" or "unclear so far" on a later line, defeats the
point of the convention — the folder stops being skimmable. Flag those and offer
a rewrite that moves the judgment to the front.

**Broken links.** Check that links out of the profile note, learnings, and
standing notes actually resolve — to other notes in the same expert, and to
`Reference/`. Note that
promotion *creates* dead links by design, since it deletes the learning, so
breakage right after a promotion is expected rather than a mistake; say which
kind you're looking at.

### Report, don't silently fix

Links are mechanical — deterministic, and the vault tooling may already do
this as a single call. Check what's available before reconstructing it by
hand; a purpose-built scan beats reading every file, and it won't miss link
syntaxes you forgot to account for. Summary standing is different: whether a
first sentence conveys how settled a claim is takes reading and judgment, and no
scan can do it. Read those.

Say what's wrong, propose the specific fix, and let Matthew decide. This matters
most for summaries — rewriting one means restating how confident he is about
something, and that judgment is his. Link repairs are safer to just offer to
apply.

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
