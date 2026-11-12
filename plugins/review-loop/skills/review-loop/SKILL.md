---
name: review-loop
description: >
  Run the code review loop on the current diff: /code-review, guide passes for
  Go, architecture and tautologies, a read-only review of plan and doc files,
  and comment cleanup when the branch has no pull request. Use when the user
  says "/review-loop", "run the review loop", or asks for the full review
  before handing work over. Invoke as `/review-loop <effort> [reinforce]
  [paths...]`. Never edits code. Returns one ranked findings list to the
  calling agent.
---

# Review loop

## Invocation

`/review-loop <effort> [reinforce] [paths...]`

- `<effort>`: `low`, `medium`, `high`, `xhigh` or `max`. Pass it to
  `/code-review` unchanged. Use `max` only when the user asked for it. If the
  effort is missing, run `/code-review` without one and let it apply its own
  default.
- `reinforce`: opt-in. Runs the last step, which costs extra tokens.
- `paths...`: explicit plan or doc files. They replace the default doc list in
  step 2.

## Rules

- Never edit code. Every code finding goes back to the calling agent as a
  recommendation. The caller owns the fix.
- Two edits are allowed. Step 3 edits comment text when the branch has no pull
  request. Step 5 edits the guide files after the user approves.
- Reviewer subagents read and report. They do not edit.

## Steps

### 1. Resolve the target

1. Run `gh pr view --json number,baseRefName`. If a pull request exists, record
   `has_pr` and use `baseRefName` as the base. If not, use the repository's
   default branch as the base.
2. List changed files: `git diff --name-only <base>...HEAD`, uncommitted changes
   from `git diff --name-only HEAD`, and untracked files from
   `git ls-files --others --exclude-standard`. Use bare `git` for the current
   repository and `git -C <path>` for any other.
3. Build the doc list unless the user passed `paths...`:
   - Changed files ending in `.org`, `.md` or `.html`.
   - Uncommitted files in the plans repository. If `./plans` is a symlink, run
     `git -C <resolved path> status --porcelain`.
4. Note whether the changed files include `.go` files.

### 2. Run the reviews

Run `/code-review <effort>` through the Skill tool and keep its findings.

Then dispatch all of the following in one message, so they run at the same
time. Use a Sonnet-class agent for the guide passes, for example
`sonnet-5-5-medium`.

| Pass | Dispatch when | Reads |
|---|---|---|
| Go | the diff has `.go` files | `GO_REVIEW.md` |
| Architecture | the diff has any code file | `CODE_ARCHITECTURE_REVIEW.md` |
| Tautology | the diff has tests, comments, labels or UI components | `TAUTOLOGY_REVIEW.md` |
| Docs | the doc list is not empty | agent `review-loop:doc-reviewer` |

The guide files sit next to this `SKILL.md`. Pass each agent the absolute path
from this skill's base directory.

Each guide-pass prompt contains facts only:

- The base ref, the changed file list and the path to its guide file.
- The instruction: read the guide, review only the changed files against that
  guide, and stay inside the guide's scope.
- The report format: `[SEVERITY] title`, `file:line`, evidence, problem, fix.
  Severity is BLOCKER, MAJOR, MINOR or NIT.
- A stop rule: report within about ten tool calls, and do not read vendored or
  third-party code.

Do not put a hypothesis in a prompt. Every pass starts from the same facts so
the passes stay independent.

The Docs prompt contains the doc list and the original requirement if you have
it.

### 3. Clean up comments

Skip this step when `has_pr` is set. The pull request belongs to someone else
and the loop does not touch its comments.

Otherwise:

1. List the comments on added or changed lines of the changed code files.
2. Invoke `unslop:unslop` on them and rewrite each comment in place, whatever
   its length. Change comment text only.
3. Read `git diff` afterwards and confirm that no code line changed. Revert any
   hunk that touches code.
4. A comment that is still longer than 2 lines after unslop becomes a clarity
   recommendation: the code should explain itself. Do not edit the code.

Unslop edits and clarity recommendations are independent. A file can get both.

### 4. Report

Merge everything into one list, highest severity first. Drop duplicates that
point at the same `file:line` and the same problem, and keep the one with the
best evidence.

Return these sections:

- **Findings**: from `/code-review`, the guide passes and `doc-reviewer`. Each
  names its source.
- **Clarity recommendations**: code the reviewers found unclear, plus comments
  over 2 lines after unslop.
- **Comments unslopped**: the files edited in step 3, or "skipped: pull request
  exists".
- **Not verified**: anything only a run can confirm, with the command.

### 5. Reinforce (only with `reinforce`)

The goal is to improve the guide files from what happened in this session.

1. Take the findings from step 4 and read the conversation after the report for
   the outcome of each: accepted, dismissed or ignored.
2. Collect two kinds of signal:
   - A dismissed finding. The guide item that produced it is too broad or wrong.
   - A problem the user raised that no pass reported. The guide lacks a gate.
3. Route each signal to the file that owns it:

   | Signal is about | File |
   |---|---|
   | Go mechanics, tests, naming | `GO_REVIEW.md` |
   | Design, API shape, failure handling | `CODE_ARCHITECTURE_REVIEW.md` |
   | Content that cannot be wrong or cannot fail | `TAUTOLOGY_REVIEW.md` |

4. Read the target file and drop any signal that an existing item already
   covers. If an existing item needs narrowing, draft that edit.
5. Draft each edit as the smallest change. Show all drafts together in one
   batch.
6. Wait for explicit approval. Write only the approved edits.
7. Edit the guide files in the plugin's source repository (`indradhanush/skills`),
   not in the installed plugin copy. Bump `version` in `plugin.json`.

If the session holds nothing worth recording, say so. Do not invent edits.
