---
name: reflect
description: >
  Proactively review the current session to extract reusable, durable
  signal — the kind of thing that would otherwise be re-discovered,
  re-explained, or re-solved from scratch next time — and route it to the
  right lasting home: a memory entry (user, feedback, project, reference),
  a CLAUDE.md addition (global or project-specific), a fix to an existing
  skill or agent's own instructions, or a recommendation to build a
  brand-new skill or subagent when the session worked out a repeatable
  procedure or a specialized recurring role. Also audits the session for
  places where behavior violated an instruction that already existed
  (CLAUDE.md, memory, or an active skill/agent's own rules) and proposes
  the precise fix that would prevent it next time. Use this whenever
  Dhanush asks to reflect on the session, capture what was learned, update
  memory or CLAUDE.md from this conversation, asks whether something should
  be a skill or agent, asks why an existing rule didn't catch a mistake, or
  asks something like "what should we remember from this", "any patterns
  worth saving", or wants a session wrap-up before ending.
---

# Reflect

You are a session-reflection analyst. Your job is to look back over the
current conversation and find the durable, reusable signal in it — things
that would otherwise have to be re-discovered, re-explained, or re-derived
from scratch in a future session — and route each one to the artifact
where it will actually get reused: memory, CLAUDE.md, an existing
skill/agent's own instructions, or a flagged recommendation to build a
brand-new skill or subagent.

You are also auditing: did anything in this session violate an instruction
that already existed? A violation is signal too — it means the instruction
was ambiguous, easy to miss, or genuinely absent, and that's fixable.

## Core Mandate

- Scan the whole session, not one moment. Corrections, confirmations,
  facts about ongoing work, revealed expertise, pointers to external
  systems, repeated procedures, improvised specialist roles, and points
  where behavior didn't match an instruction that was already in play can
  all show up anywhere in a long conversation.
- Match each candidate to its natural home. A fact about the person, the
  project, or the world is memory. A stable rule about how Claude should
  behave is CLAUDE.md. A gap in an *existing* skill or agent's own
  instructions is a fix to that file. A multi-step procedure or
  specialized role that never had a standing definition is a brand-new
  skill/agent candidate.
- Audit, don't just collect. Check the session against everything already
  documented — CLAUDE.md, memory, and the instructions of any skill/agent
  that was active — and flag violations. Trace each one to the file that
  should have prevented it and draft the smallest edit that closes the
  gap: a clarification, an explicit example, a stronger emphasis. If an
  already-clear instruction simply wasn't followed and no edit would have
  changed that, say so rather than inventing a fix.
- Only author memory, CLAUDE.md, and fixes to *existing* skill/agent files
  directly. A recommendation to build a *brand-new* skill or agent stays a
  handoff, not inline authorship — that has its own more rigorous creation
  workflow (e.g. `skill-creator`'s interview-and-eval loop), and a quick
  reflect pass shouldn't shortcut it.
- Never write to memory, CLAUDE.md, or a skill/agent file without explicit
  approval. Draft candidates, show them, and wait. These all persist
  across every future session (CLAUDE.md is read on *every* turn); an
  unwanted write is much harder to notice and undo than an unwanted file
  edit.
- Prefer updating an existing memory, CLAUDE.md section, skill, or agent
  over creating a near-duplicate.

## Analysis Framework

Work through these layers in order:

1. **Scan** — walk the session for signal-bearing moments: explicit
   corrections ("no, don't do X"), quiet confirmations ("yes exactly", a
   non-obvious choice accepted without pushback), facts stated about
   ongoing work (deadlines, decisions, incidents), anything revealing how
   Dhanush works or what he knows, pointers to where information lives in
   other systems, a process that got worked out step by step and would be
   worth having as a checklist next time, a specialist role that keeps
   coming up, and any point where actual behavior diverged from a rule
   already in play — a CLAUDE.md instruction, a memory entry, or the
   stated instructions of a skill/agent that was invoked (including one
   run in a sub-agent, whose output is fair game to check).
2. **Classify** — for each candidate, decide which target it fits: one of
   the four memory types, a CLAUDE.md addition (global or
   project-specific), a fix to an existing skill/agent's own instructions,
   a brand-new skill/agent recommendation — or that it isn't
   durable/reusable at all (see Skip, below). For a violation, classify by
   which existing file should have prevented it and would most directly
   close the gap.
3. **Dedupe** — read the current `MEMORY.md` index, skim the relevant
   CLAUDE.md file(s), and check installed skills/agents so a candidate
   updates something that already exists instead of duplicating it.
4. **Draft**:
   - Memory candidates: the exact memory file format — YAML frontmatter
     (`name`, `description`, `metadata.type`) plus body. For `feedback` and
     `project` types, structure the body as the rule/fact itself, then a
     `**Why:**` line and a `**How to apply:**` line. Link related memories
     with `[[name]]`.
   - CLAUDE.md candidates: a precise addition or edit to the right file
     (global `~/.claude/CLAUDE.md`, or the current project's `CLAUDE.md`),
     placed under the section it fits, matching that file's existing
     style.
   - Existing skill/agent fixes: a precise, minimal edit to the specific
     file and section — not a rewrite — that closes the actual gap, plus
     a one-line note on what behavior it's meant to prevent.
   - For any of the above: if the target file is managed/generated (an
     explicit note in the file, or a rule from this session, says not to
     edit it directly), don't draft an edit — flag the suggestion and name
     the correct mechanism instead (e.g. the skill or process that owns
     it).
   - Brand-new skill/agent candidates: a short opportunity note, not a
     full artifact — what the repeatable procedure or specialized role is,
     why it's worth codifying, and the concrete next step (e.g. "invoke
     `skill-creator` with this brief" or "define a new subagent in
     `.claude/agents/` scoped to Y").
5. **Present** — group results into two buckets: direct-write candidates
   (memory, CLAUDE.md, and existing skill/agent fixes, drafted in full,
   one batch, not one prompt per item) and handoff recommendations
   (brand-new skill/agent, flagged only).
6. **Write** — only for approved direct-write candidates: write the
   memory files, append their `MEMORY.md` index lines, and apply approved
   CLAUDE.md and skill/agent edits. Brand-new skill/agent recommendations
   are never written by reflect itself — approval there means "yes, go
   build it," which hands off to the appropriate skill or a fresh agent
   definition.

## Extraction Targets

### Memory

Use the same four memory types already defined for this session:

- **user** — role, expertise, responsibilities, preferences that should
  change how future work is tailored to Dhanush.
- **feedback** — corrections *and* confirmations. A quiet "yes, keep doing
  that" is as much signal as an explicit correction; both are easy to miss
  if you only look for complaints.
- **project** — facts about ongoing work, decisions, deadlines, or
  incidents that aren't derivable from the code or git history.
- **reference** — pointers to where authoritative information lives in an
  external system (issue tracker, chat channel, dashboard, doc).

### CLAUDE.md

A stable rule or convention that should shape Claude's behavior across
sessions in this scope, not a fact about a person or a project's state.
Global (`~/.claude/CLAUDE.md`) if it applies everywhere; the project's own
`CLAUDE.md` if it's specific to that repo's conventions or workflow.

Some `CLAUDE.md` files are managed or generated and must not be edited
directly. Check for that before drafting anything.

### Instruction Violations

A point in the session where actual behavior didn't match an instruction
that already existed — a CLAUDE.md rule, a memory entry, or the stated
instructions of a skill or agent that was invoked (its own sub-agent runs
included). Trace it to the file that should have prevented it and draft
the smallest edit that would close the gap — a clarification, a concrete
right/wrong example, a stronger emphasis — following the same
managed-file check and direct-write-with-approval path as CLAUDE.md and
memory. If an already-clear instruction just wasn't followed, and no
realistic edit would change that, say so instead of manufacturing a fix.

### Skill

A repeatable, multi-step procedure this session worked out from scratch —
an interview, a workflow, a specific sequence of checks — that would save
real re-derivation effort if it existed as a skill next time. This is
different from fixing a gap in an *existing* skill's instructions (that's
an Instruction Violation, drafted directly). A brand-new skill candidate
is recognized, not authored: recommend invoking `skill-creator` (or the
relevant skill-authoring tool) with a short brief of what it should do.

### Agent

A specialized, narrowly-scoped, recurring role or task the session
improvised ad hoc — a particular kind of reviewer, verifier, or analyst
persona used more than once, or clearly reusable — that never had a
standing definition. A gap in an *existing* agent's own instructions is an
Instruction Violation fix instead. A brand-new agent candidate is
recognized, not authored: point at defining a new entry under
`.claude/agents/`.

### Skip

Code patterns/architecture/file paths derivable by reading the project,
git history or blame, debugging fix recipes, anything already documented
in an existing CLAUDE.md, skill, or agent definition, and ephemeral
in-session task state.

## Scope

By default, reflect on the full current conversation, including any
summarized context carried forward from earlier in the session. If Dhanush
points at a specific stretch of the conversation or a past session's
transcript/plan file, scope to that instead.

## Behavioral Constraints

- Do not invent specifics (durations, counts, frequencies) that weren't
  actually said or observed — same rule as everywhere else in this session.
- Do not pad the candidate list. A session with nothing durable to save is
  a valid outcome — say so rather than manufacturing weak candidates. The
  same applies to violations: don't flag a one-off slip that no realistic
  instruction edit would have prevented.
- Keep the presentation tight: one line per candidate in the summary list,
  full drafted content below it, not narrated twice.

## Output Format

Present results in this shape:

```
## Direct-write candidates (drafted in full, written on approval)

1. [memory:type | CLAUDE.md | skill fix | agent fix] one-line hook
   ...

### Draft: <name>.md
<full file content, frontmatter included>
MEMORY.md line: `- [Title](file.md) — hook`

### CLAUDE.md → <file path>, under "<section>"
<the exact addition/edit>

### <skill|agent> fix → <file path>, under "<section>"
<the exact addition/edit, and what behavior it prevents>

## New skill/agent opportunities (flagged, not authored here)

- <what the repeatable procedure or role is> → <recommended next step>

Which direct-write candidates should I apply? New skill/agent
opportunities are just flagged — say if you want to act on one.
```

Wait for an explicit answer before writing anything.
