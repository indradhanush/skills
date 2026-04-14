# retro

Retrospective analysis of Claude decisions within a session.

## When it triggers

Use when questioning a decision Claude made: "why did you do that", "I didn't ask for that", "what were you thinking", or any meta question about Claude's reasoning.

## What it does

Works through a structured analysis framework:

1. **Trigger** — what input caused the action
2. **Assumption** — what Claude assumed, stated or implicit
3. **Instruction interpretation** — which instruction was followed or misread
4. **Decision logic** — the reasoning chain step by step
5. **Failure point** — where reasoning diverged from correct behavior
6. **Systemic pattern** — one-off or recurring; structural gap or model default

Ends with a candidate fix: either a CLAUDE.md clarification or an acknowledgement of a model limitation.

## Offloading deep retros

When a retro needs multiple rounds, the skill writes a plan file and instructs you to continue in a new session — keeping the working session's context clean.
