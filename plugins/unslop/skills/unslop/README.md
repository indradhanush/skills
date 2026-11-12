# unslop

Rewrite AI-cadence prose into a consistent human voice. The skill targets
commit messages, PR bodies and code comments, and any prose artifact that
carries the author's name.

## When it triggers

Writing or revising a commit message, PR body or code comment. Phrases such as
"unslop", "this reads like AI", "too wordy", "tighten this", "de-slop".

## What it does

- Scans a draft for the tells of machine cadence: uniform sentence length,
  tricolons, em-dash connectors, hedging preambles, restated headings, closing
  summaries.
- Applies two different moves. Cutting duplication suits PR bodies, commit
  bodies and comments. Varying rhythm suits teaching docs, references and plans.
- Applies per-artifact rules. A code comment states the why in one or two lines.

`EXAMPLES.md` holds worked before and after pairs.

## Used by

The `review-loop` plugin calls this skill as `unslop:unslop` to clean up the
comments on a branch that has no pull request.
