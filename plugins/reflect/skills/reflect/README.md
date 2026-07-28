# reflect

Extract reusable patterns from a session and route them to the right
lasting home: memory, CLAUDE.md, or a recommendation to build a new skill
or subagent.

## When it triggers

Use to wrap up a session or checkpoint what's worth keeping: "reflect on
this session", "what should we remember from this", "any patterns worth
saving", "should this be a skill", "capture this for next time", "update
memory or CLAUDE.md from this conversation".

## What it does

Works through a structured extraction framework:

1. **Scan** — walk the session for corrections, confirmations, facts about
   ongoing work, revealed expertise, pointers to external systems, and
   repeatable procedures or specialist roles that kept coming up.
2. **Classify** — sort each candidate into one of the four memory types
   (user, feedback, project, reference), a CLAUDE.md addition (global or
   project-specific), a skill candidate, an agent candidate, or drop it if
   it's not durable/reusable at all.
3. **Dedupe** — check the existing `MEMORY.md` index, the relevant
   CLAUDE.md file(s), and installed skills/agents so a candidate updates
   something existing instead of duplicating it.
4. **Draft** — memory and CLAUDE.md candidates get the full proposed
   content. Skill and agent candidates get a short opportunity note
   (what it is, why it's worth codifying, the recommended next step) —
   reflect never authors the full skill/agent itself.
5. **Present** — direct-write candidates (memory + CLAUDE.md) in one
   batch, and handoff recommendations (skill + agent) in a separate list.
6. **Write** — only approved memory/CLAUDE.md candidates get written.
   Skill/agent recommendations are handed off (e.g. to `skill-creator`),
   never authored by reflect.

## Write policy

Reflect always drafts first and waits for approval before writing memory
or CLAUDE.md. Both persist across every future session — CLAUDE.md is read
on *every* turn — so an unwanted write is much harder to notice and undo
than an unwanted file edit.
