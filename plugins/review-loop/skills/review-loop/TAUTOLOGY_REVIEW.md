# TAUTOLOGY_REVIEW.md

A tautology is content that cannot be wrong or cannot fail, or that repeats
what sits next to it. This pass finds tautologies in tests, labels, comments
and UI components. Tautological content adds reading cost and false
confidence, so each one is a finding.

## Scope

- In scope: tests, labels, comments and UI components in the diff.
- Out of scope: missing tests and test mechanics (`GO_REVIEW.md`). Identifier
  naming in APIs (`CODE_ARCHITECTURE_REVIEW.md`). Comment length and voice (the
  `unslop` step of the loop).
- Report each finding with its target (test, label, comment or component) and
  the check that showed it is tautological.

## Tests

A tautological test passes whatever the implementation does.

Check: name one single-line change to the implementation that would make this
test fail. If none exists, flag the test.

Flag a test when:

- It asserts a value the test itself set up, with no code under test between
  the setup and the assertion. Example: a mock returns `"abc"` and the test
  asserts `"abc"`.
- The expected value is computed by the same logic as the implementation. This
  includes calling the function under test, or a copy of its code, to build the
  expectation.
- It asserts something always true: `assert.True(t, true)`, `NotNil` on a value
  the constructor never returns as nil, `len(x) >= 0`.
- It asserts only "no error" or "no panic" for a function whose purpose is to
  produce a result.
- It verifies a mock call that the test itself arranged and checks no
  observable outcome.
- Its golden or snapshot file was regenerated from the current output, with no
  independent expectation.
- Its table cases differ only in name. The inputs reach the same code path with
  the same assertion.

Do not flag a test for referencing a production constant such as a key, a name
or a path. Sharing a constant is correct. Sharing a computation is the
tautology.

## Labels

A label is text that names or titles something: test names, subtest names,
headings, section titles, table headers, UI text and field documentation.

Check: replace the label with the name of the thing it labels. If nothing is
lost, flag it.

Flag a label when:

- A test or subtest name only repeats the function name or the assertion
  (`TestParse` with subtest "parses", "returns the value"). A good name states
  the scenario and the expected outcome.
- A heading restates its parent or its first sentence (`## The lock`, followed
  by "The lock is...").
- UI text equals the field or component name it sits on (a "Name" label above a
  field already named "Name", a button labeled "Button").
- A table column header repeats the table title.
- Field documentation restates the field name (`// Name is the name`).

## Comments

A tautological comment gives the reader nothing the code does not.

Check: delete the comment and read the code. If the reader loses no
information, flag the comment.

Flag a comment when:

- It restates the next line (`// increment the counter`).
- A doc comment paraphrases the function name or signature (`// GetUser gets
  the user`).
- It describes what a test asserts when the assertion is readable.
- It is a banner or divider that restates what follows.
- It contradicts the code. This is not a tautology. This pass reads each
  comment against its code, so report it here.

Keep a comment that states the rule the code enforces, or the reason when the
reason is not obvious. Do not flag these.

## UI components

A tautological component adds a layer and no behavior.

Check: inline the component at its call sites. If the result behaves and looks
the same, flag it.

Flag a component when:

- It only forwards its props to one other component, with no added state,
  behavior or styling.
- It renders a fixed string equal to its own name (`<UserNameLabel>` showing
  "User Name").
- Its story or test only renders it with props and asserts that those props
  appear.
