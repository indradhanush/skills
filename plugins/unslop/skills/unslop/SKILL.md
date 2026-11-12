---
name: unslop
description: >-
  Rewrite AI-cadence prose into Dhanush's voice. Use when writing or revising a
  commit message, PR body, or code comment, and whenever Dhanush says "unslop",
  "this reads like AI", "too wordy", "tighten this", "cut this down", "rewrite
  in my voice", "sounds robotic", "de-slop", or edits your draft and asks what
  changed. Also apply before handing over any prose artifact Dhanush will put
  his name on. Covers the tells of machine cadence — uniform sentence length,
  tricolons, em-dash connectors, hedging preambles, restated headings, closing
  summaries, symmetry for its own sake — and the rewrite rules that replace
  them. Complements technical-writing, which governs clarity; this governs
  voice.
---

# Unslop

## The problem

AI prose is not usually wrong. It is uniform.

Every sentence lands on the same beat. Every paragraph has the same three-part
shape. Every list has three items. The result reads competent and sounds like
nobody, and Dhanush's name goes on it.

Clarity is `technical-writing`'s job. This skill is about cadence and voice.

## The test

Read the draft aloud. If the rhythm never changes, it is slop, however accurate
it is.

Dhanush's writing varies on purpose. A long sentence carried by prepositions,
then a fragment. A bare list where the facts are structured. One joke, then
straight back to work. That variance is the signal.

## The tells

Scan for these first. They are mechanical and each has a fix.

| Tell | Looks like | Fix |
|---|---|---|
| Uniform length | Every sentence 15-20 words | Break one in half. Let another run long |
| Tricolon reflex | "X, Y, and Z" three times a page | Use two items, or four |
| Em-dash connector | "The lock protects the work — the CAS protects the record" | Full stop, or a colon |
| Restated heading | `## The lock` followed by "The lock is..." | Delete the restatement, start at the fact |
| Hedging preamble | "It's worth noting that", "Importantly", "Notably" | Delete. Say the thing |
| Closing summary | A final paragraph repeating what was said | Delete. The reader just read it |
| Symmetry fetish | Parallel structure where the ideas are not parallel | Let the shapes differ |
| "Not just X, but Y" | Any instance | Rewrite as one claim |
| Signposting | "First… Second… Finally…" in prose | Use a list, or nothing |
| Equal weight | The obvious explained at the same length as the subtle | Cut the obvious to a clause |
| Announced structure | "Let's break this down", "Here's what changed" | Delete and show it |
| Hollow intensifier | "significantly", "robust", "seamless", "leverage" | Delete, or name the number |
| Buzzword | "single source of truth", "skew", "unify", "streamline", "holistic" | Say the concrete thing: "the only pin is in the Makefile" |

## The rewrite rules

**Cut what the reader can already see.** A PR body should not restate the diff.
A commit body should not narrate code mechanics. A comment should not describe
the line below it. Write only the part that is true and not visible.

**State requirements, do not request favours.** "Someone with account access
needs to add X, because Y" becomes "Add X". The work item exists regardless of
who does it.

**Delete your own hedging.** Sections that pre-emptively defend a choice, offers
to change it, "happy to narrow this if you'd rather" — all of it is thinking out
loud in a document meant to be acted on. If there is a real question, it is a
review comment, not a section.

**Do not inventory incidental changes.** They are in the diff.

**Keep tables and lists, cut the prose around them.** Structured facts earn
their space. The paragraph introducing them usually does not.

**Vary the cadence deliberately.** After three sentences of similar length,
write a short one. Or a long one that carries a couple of clauses on
prepositions the way speech does when someone is thinking through a problem.

**Leave the personality in.** Dhanush's README opens with a Time Stone joke and
then gets on with it. One light line is voice. Three is a bit.

## Mechanics Dhanush has already fixed in writing

These are in `~/CLAUDE.md`. Apply them without being asked.

- Headings read "What is the tunnel?" or "The tunnel", never "What the tunnel is"
- "Thing: description" rather than "Thing — description"
- Em-dashes are rare. If one is unavoidable, use `–`
- Never the phrase "why it matters"
- No invented specifics. No "10+ years", no "hundreds of", unless the number came from Dhanush or from observable evidence
- Comments explain why, not what, and never address the reader
- Commit bodies state root cause and impact, not what the diff shows, and do not name exact function or test names

## Per-artifact

**Code comments.** The why. One or two lines. If the comment is longer than the
code it explains, the code needs renaming, not documenting.

**Commit messages.** Subject under 80 chars, imperative. Body uses the
Problem and Solution bullet format from `## Comitting Code` in AGENTS.md.
Never narrate the diff.

The session that wrote the change runs this on its own commit message. It knows
what the change does, so it can tell a cut from a change in meaning. Don't hand
the job to a commit agent that only sees the message.

**PR bodies.** The delta between what the reviewer will read (diff, commit) and
what they need to know. Often that is one fact. Say it and stop.

Do not narrate the commit list or repeat what the process already guarantees:
commit order, that each commit compiles, that lint and tests pass for the whole
PR. The reviewer sees the commits and the CI. The note holds only what they
cannot see.

## How to run this

1. Read the draft aloud, listening for rhythm rather than meaning.
2. Mark every tell from the table.
3. Delete before rewriting. Most fixes are cuts.
4. Check the cut version still carries every load-bearing fact, and that each claim still
   means the same thing. List the facts in the draft and in the rewrite, and compare them.
   A shorter sentence that says something different is a failure, not a cut.
5. Vary what remains.
6. Compare lengths. Halving is normal.

Deleting is the main move. A draft that survives with a third of its words was
carrying two thirds noise.

## Worked examples

Real before-and-after pairs, including a PR body Dhanush cut by half:
[EXAMPLES.md](EXAMPLES.md).

## Two different moves

Deciding which one applies comes before anything else.

**Cut duplication.** For a PR body, a commit body, or a comment, most of the
draft repeats something the reader already has. Delete hard. Halving is normal.

**Vary rhythm.** For a teaching doc, a reference, or a plan, the reasoning *is*
the content, so there is nothing to delete. The fix is cadence alone: break the
tricolons, split the parallel paragraphs, let sentence lengths differ. The
result can be longer than the draft, and that is a pass, not a failure.

Applying the first move to the second kind of document destroys it. The tells
table applies to both; the deletion rules apply only to the first.

## Limits

Do not apply to:

- Anything where the audience genuinely lacks context
- Direct quotes and cited text
- Chat, which has its own brevity rules

Voice is not the same as terseness. Cutting a load-bearing fact to save a line
is a worse failure than leaving a flabby sentence in.

## Note on the tells table

Roughly half of it is greppable — em-dashes, "not just X but Y", hedging
preambles. In this repo those are usually already absent, because `~/CLAUDE.md`
suppresses them at the source.

The half that matters is structural: tricolons, parallel paragraph openers,
lists whose items all share one shape. No grep finds those. Read the draft.
