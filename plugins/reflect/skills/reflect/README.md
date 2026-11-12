# reflect

Extract reusable patterns from a session and route them to the right
lasting home: memory, CLAUDE.md, a fix to an existing skill/agent's own
instructions, or a recommendation to build a brand-new skill or subagent.
Also audits the session for places where behavior violated an instruction
that already existed, and proposes the fix that would prevent it next
time.

## When it triggers

Use to wrap up a session or checkpoint what's worth keeping: "reflect on
this session", "what should we remember from this", "any patterns worth
saving", "should this be a skill", "why didn't an existing rule catch
that", "capture this for next time", "update memory or CLAUDE.md from this
conversation".

## What it does

Works through a structured extraction framework:

1. **Scan** — walk the session for corrections, confirmations, facts about
   ongoing work, revealed expertise, pointers to external systems,
   repeatable procedures or specialist roles that kept coming up, and any
   point where behavior diverged from a CLAUDE.md rule, a memory entry, or
   an active skill/agent's own instructions (sub-agent runs included).
2. **Classify** — sort each candidate into one of the four memory types
   (user, feedback, project, reference), a CLAUDE.md addition (global or
   project-specific), a fix to an existing skill/agent's own instructions,
   a brand-new skill/agent candidate, or drop it if it's not
   durable/reusable at all. A violation is classified by which existing
   file should have prevented it.
3. **Dedupe** — check the existing `MEMORY.md` index, the relevant
   CLAUDE.md file(s), and installed skills/agents so a candidate updates
   something existing instead of duplicating it.
4. **Draft** — memory, CLAUDE.md, and existing-skill/agent fixes get the
   full proposed content (a precise, minimal edit, not a rewrite). A
   brand-new skill/agent candidate gets a short opportunity note instead —
   reflect never authors the full artifact for those.
5. **Present** — direct-write candidates (memory, CLAUDE.md, existing
   skill/agent fixes) in one batch, and handoff recommendations
   (brand-new skill/agent) in a separate list.
6. **Write** — only approved direct-write candidates get written.
   Brand-new skill/agent recommendations are handed off (e.g. to
   `skill-creator`), never authored by reflect.

## Write policy

Reflect always drafts first and waits for approval before writing memory,
CLAUDE.md, or a skill/agent file. All of these persist across every future
session — CLAUDE.md is read on *every* turn — so an unwanted write is much
harder to notice and undo than an unwanted file edit.
