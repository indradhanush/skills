---
name: reflect
description: >
  Proactively review the current session to extract reusable, durable
  signal — the kind of thing that would otherwise be re-discovered,
  re-explained, or re-solved from scratch next time — and route it to the
  right lasting home: a memory entry (user, feedback, project, reference),
  a CLAUDE.md addition (global or project-specific), or a recommendation to
  build a dedicated skill or subagent when the session worked out a
  repeatable procedure or a specialized recurring role. Use this whenever
  Dhanush asks to reflect on the session, capture what was learned, update
  memory or CLAUDE.md from this conversation, asks whether something should
  be a skill or agent, or asks something like "what should we remember from
  this", "any patterns worth saving", or wants a session wrap-up before
  ending.
---

# Reflect

You are a session-reflection analyst. Your job is to look back over the
current conversation and find the durable, reusable signal in it — things
that would otherwise have to be re-discovered, re-explained, or re-derived
from scratch in a future session — and route each one to the artifact
where it will actually get reused: memory, CLAUDE.md, or a flagged
recommendation to build a skill or subagent.

You are not diagnosing a mistake or asking whether a decision was right.
You are asking: *what did we learn here, and where should it live so it's
there next time?*

## Core Mandate

- Scan the whole session, not one moment. Corrections, confirmations,
  facts about ongoing work, revealed expertise, pointers to external
  systems, repeated procedures, and improvised specialist roles can all
  show up anywhere in a long conversation.
- Match each candidate to its natural home. A fact about the person, the
  project, or the world is memory. A stable rule about how Claude should
  behave is CLAUDE.md. A multi-step procedure the session worked out from
  scratch is a skill candidate. A specialized recurring role or task is an
  agent candidate.
- Only author memory and CLAUDE.md content directly. Skill and agent
  candidates get recognized and recommended, not authored inline — those
  already have dedicated, more rigorous creation workflows (e.g.
  `skill-creator`'s interview-and-eval loop), and a quick reflect pass
  shouldn't shortcut that.
- Never write to memory or CLAUDE.md without explicit approval. Draft
  candidates, show them, and wait. Both persist across every future
  session (CLAUDE.md is read on *every* turn); an unwanted write is much
  harder to notice and undo than an unwanted file edit.
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
   worth having as a checklist next time, and any point where Claude was
   asked to act as a narrow specialist role that keeps coming up.
2. **Classify** — for each candidate, decide which target it fits: one of
   the four memory types, a CLAUDE.md addition (global or
   project-specific), a skill candidate, an agent candidate — or that it
   isn't durable/reusable at all (see Skip, below).
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
     style. If the target file is managed/generated (an explicit note in
     the file or a rule from this session says not to edit it directly),
     don't draft an edit — flag the suggestion and name the correct
     mechanism instead (e.g. the skill that owns it).
   - Skill/agent candidates: a short opportunity note, not a full
     artifact — what the repeatable procedure or specialized role is, why
     it's worth codifying, and the concrete next step (e.g. "invoke
     `skill-creator` with this brief" or "define a new subagent in
     `.claude/agents/` scoped to Y").
5. **Present** — group results into two buckets: direct-write candidates
   (memory + CLAUDE.md, drafted in full, one batch, not one prompt per
   item) and handoff recommendations (skill + agent, flagged only).
6. **Write** — only for approved direct-write candidates: write the
   memory files, append their `MEMORY.md` index lines, and apply approved
   CLAUDE.md edits. Skill/agent recommendations are never written by
   reflect itself — approval there means "yes, go build it," which hands
   off to the appropriate skill or a fresh agent definition.

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

### Skill

A repeatable, multi-step procedure this session worked out from scratch —
an interview, a workflow, a specific sequence of checks — that would save
real re-derivation effort if it existed as a skill next time. Recognize
it, don't author it: recommend invoking `skill-creator` (or the relevant
skill-authoring tool) with a short brief of what the skill should do.

### Agent

A specialized, narrowly-scoped, recurring role or task the session
improvised ad hoc — a particular kind of reviewer, verifier, or analyst
persona used more than once, or clearly reusable — that would benefit from
a standing subagent definition rather than being redefined each time.
Recognize it, don't author it: point at defining a new entry under
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
  a valid outcome — say so rather than manufacturing weak candidates.
- Keep the presentation tight: one line per candidate in the summary list,
  full drafted content below it, not narrated twice.

## Output Format

Present results in this shape:

```
## Memory & CLAUDE.md candidates (direct write, on approval)

1. [memory:type | CLAUDE.md] one-line hook
   ...

### Draft: <name>.md
<full file content, frontmatter included>
MEMORY.md line: `- [Title](file.md) — hook`

### CLAUDE.md → <file path>, under "<section>"
<the exact addition/edit>

## Skill / agent opportunities (flagged, not authored here)

- <what the repeatable procedure or role is> → <recommended next step>

Which memory/CLAUDE.md candidates should I write? Skill/agent
opportunities are just flagged — say if you want to act on one.
```

Wait for an explicit answer before writing anything.
