# review-loop

A review loop that runs `/code-review`, adds guide-based passes, reviews plan
and doc files, cleans up comments and can improve its own guides. It returns
one ranked findings list to the agent that asked for the review.

## Invocation

```
/review-loop <effort> [reinforce] [paths...]
```

- `<effort>`: passed to `/code-review`.
- `reinforce`: opt-in step that drafts guide edits from the session.
- `paths...`: plan or doc files that replace the default doc list.

## Steps

1. Resolve the base branch, the changed files and whether a pull request exists.
2. Run `/code-review`. Then run the guide passes and the doc reviewer at the
   same time.
3. With no pull request, unslop the comments on the branch.
4. Merge everything into one ranked report.
5. With `reinforce`, draft guide edits and wait for approval.

## Guide files

Each file has its own scope, so findings do not overlap.

| File | Owns |
|---|---|
| `GO_REVIEW.md` | Go error handling, test mechanics, naming and layout |
| `CODE_ARCHITECTURE_REVIEW.md` | State modeling, boundaries, failure and recovery, API design, Kubernetes controllers and charts |
| `TAUTOLOGY_REVIEW.md` | Tests, labels, comments and UI components that hold no information |

The `doc-reviewer` agent in `agents/` reviews plan and doc files. It is
read-only.

## Edit rules

The loop never edits code. It edits comment text when no pull request exists,
and the guide files after approval during `reinforce`. Everything else is a
recommendation to the caller.

## Dependencies

The `unslop` plugin from the same marketplace, called as `unslop:unslop`.
