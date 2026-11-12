# skills

Personal [Claude Code](https://claude.ai/code) skills marketplace.

## Install

```
/plugin marketplace add indradhanush/skills
```

## Skills

### retro

Retrospective analysis of Claude decisions within a session.

Trigger phrases: "why did you do that", "I didn't ask for that", "what were you thinking", or any meta question about Claude's reasoning.

```
/plugin install retro@indradhanush-skills
```

### reflect

Extract reusable patterns and flag instruction violations, routing fixes to memory, CLAUDE.md, or skill/agent docs.

Trigger phrases: "reflect on this session", "what should we remember from this", "any patterns worth saving", "should this be a skill", "why didn't an existing rule catch that", "capture this for next time".

```
/plugin install reflect@indradhanush-skills
```

### unslop

Rewrite AI-cadence prose in commit messages, PR bodies and code comments into Dhanush's voice.

Trigger phrases: "unslop", "this reads like AI", "too wordy", "tighten this", "de-slop".

```
/plugin install unslop@indradhanush-skills
```

### review-loop

Run `/code-review`, add Go, architecture and tautology guide passes, review plan and doc files, clean up comments when no pull request exists, and optionally improve the guides from the session. Needs the `unslop` plugin.

Trigger phrases: "/review-loop", "run the review loop".

```
/plugin install review-loop@indradhanush-skills
```
