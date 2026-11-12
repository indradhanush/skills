# GO_REVIEW.md

Review gates for Go code. Read each item, check the diff, move on. Items are
ordered by how often the problem shows up in real reviews.

## Scope

- In scope: Go language mechanics in `.go` files. Error handling and logging
  calls, test mechanics, naming and file layout.
- Out of scope: design, API shape and failure recovery (`CODE_ARCHITECTURE_REVIEW.md`).
  Tests, labels and comments that hold no information (`TAUTOLOGY_REVIEW.md`).
  Comment length and voice (the `unslop` step of the loop).
- Read this file next to the `pf9-go-programmer:go-programmer` skill. This file
  lists review gates and does not restate that skill's conventions.

Tags show how often the problem was seen: **[frequent]**, **[occasional]**.

## Error handling

- Never both log an error and return it from the same site. The caller logs
  returned errors, so doing both logs twice. **[frequent]**
  - *Why: the same failure appears twice in production logs, and the duplicate
    hides where the error started.*
- Wrap every propagated error with `fmt.Errorf("failed to <operation>: %w", err)`.
  Always `%w`, always a short description of what failed. **[frequent]**
  - *Why: bare returns lose call-site context and break `errors.Is` and
    `errors.As` for callers.*
- Include the identity of the object being processed in every error and log
  line in code that handles many objects at once, for example a
  `namespace/name` pair. **[frequent]**
  - *Why: a generic message is not actionable when many objects run
    concurrently.*
- Never silently `return nil` on unexpected input or a failed type assertion.
  Return a descriptive error. **[occasional]**
  - *Why: callers read nil as success and continue with undefined state. The
    bug surfaces far from its source.*
- Match log level to severity. Log failures at Error and expected or
  in-progress states at Info. Never report an error at Info level.
- Declare `err` in the narrowest scope that uses it. An outer `err` reassigned
  with `:=` inside an `if` or `else` block creates a shadow and swallows the
  error. **[occasional]**
- Prefer `if err := f(); err != nil {` to pre-declaring `err`, unless `err` is
  needed outside the block.
- Quote string values in error and log messages with `%q`, not single quotes.

## Testing

- Assert every error-returning call in a test with `require.NoError(t, err)`.
  Never discard it with `_`. **[frequent]**
  - *Why: a swallowed setup error lets the test run against zero-value state,
    so it proves nothing and the real bug shows up as a confusing failure
    later.*
- Add a direct unit test for every new function or helper, and a regression
  test for every guard or mitigation the change claims to add. A guard that
  must do nothing in some case gets a test asserting it does nothing.
  **[frequent]**
  - *Why: without coverage for new paths, the next refactor silently breaks
    the invariant the change was written to establish.*
- Assert errors with `errors.Is` or `errors.As` against the exact sentinel or
  typed error. Replace `Contains` and substring assertions. **[frequent]**
  - *Why: a substring match passes for any error containing the keyword, so it
    never confirms the intended path ran.*
- Assert preconditions before the action under test, and assert the result of
  the call under test. Do not ignore return values. **[occasional]**
- Reference the production constant or template for names, keys and paths
  instead of copying the string into the test. Never compute the expected value
  by calling the function under test (see `TAUTOLOGY_REVIEW.md`). **[occasional]**
  - *Why: a copied string drifts from the real artifact and stops catching
    regressions. For rendered output, unmarshal into the struct and compare the
    struct.*
- Do not write a test that asserts current buggy behavior. Fix the
  implementation and the test together so the test asserts the correct
  contract. **[occasional]**
- Avoid `if` and `else` branches inside test assertions. Use a fail-fast guard
  such as `require.NotEmpty(t, ...)` before the asserts so the path stays
  linear. **[occasional]**
- Split chained calls in assertions onto separate lines: `got, err := ...`,
  then `require.NoError(t, err)`, then `require.Equal(t, want, got)`. A failure
  then points at the exact step. **[occasional]**
- Merge near-duplicate test functions into one table-driven test with
  subtests. Remove any assertion repeated within one case. **[occasional]**
- Do not ship skipped or commented-out tests. If a test cannot pass yet, track
  it in an issue. **[occasional]**

## Naming and layout

- Group all methods of a struct together. Keep standalone helper functions
  apart, never between methods. **[frequent]**
  - *Why: interleaving breaks the reading flow of a type and forces readers to
    jump around the file.*
- Keep an identifier unexported unless another package consumes it. Check the
  whole module, not only the current file. **[frequent]**
  - *Why: an unnecessary export widens the API surface, complicates refactors
    and bypasses the compiler's unused-symbol detection.*
- Name a variable or field for its purpose, not its Go type (`cloudConfigTmpl`,
  not `tmplStr`). **[occasional]**
- Name constructors `New<Type>`, not `Parse*`, `Build*` or `Create*`, when the
  job is to construct the type. Prefer a method on the type to passing the type
  as an argument. **[occasional]**
- Keep Kubernetes client and API imports in their own import group, apart from
  other third-party imports. **[occasional]**
- Give every `//nolint:<linter>` directive an inline reason. A bare directive
  silences the linter for good, not only for this change. Require the fix or
  the justification next to the directive.
