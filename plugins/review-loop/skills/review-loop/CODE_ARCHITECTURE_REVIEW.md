# CODE_ARCHITECTURE_REVIEW.md

Review gates for design and API shape. The gates apply to any language. The
last section applies only when the diff touches Kubernetes controllers, CRDs
or Helm charts.

## Scope

- In scope: how state is modeled, where boundaries sit, how the code fails and
  recovers, what the public surface promises, and the blast radius of the
  change.
- Out of scope: Go error wrapping, test mechanics and file-level naming
  (`GO_REVIEW.md`). Tests, labels and comments that hold no information
  (`TAUTOLOGY_REVIEW.md`).
- Report a gate as a recommendation. The caller decides whether to restructure.

Tags show how often the problem was seen: **[frequent]**, **[occasional]**.

## State and data modeling

- Name every state. A state is a value in an explicit field such as a phase,
  status or enum, never something inferred from what is empty.
- Give no value a second meaning. An empty string, zero or nil must not also
  mean "done" or "not started".
  - *Why: the same empty value meaning "never created" and "already done"
    makes the record unreadable without the writer's code.*
- Keep a record's fields after a transition. Change the state field. Do not
  blank other fields to signal the change.
- Let a reader know what a value means without reading the writer's code.
- Define each type once. If the type exists in production code, export it and
  reuse it in tests and other packages. Do not copy it.
- Before overwriting stored data from a struct, check that the struct is
  populated. An empty source overwrites live data, for example credentials.
  **[occasional]**

## Boundaries and abstraction

- Prefer the smallest change that works. Flag refactors presented as fixes.
- Reuse an existing pattern before adding an abstraction or a dependency.
- Inline a helper that has one caller and a few lines, unless it names a
  well-defined larger scope. A thin wrapper that only passes different
  arguments adds a navigation hop and no reuse or testability. **[frequent]**
- Keep related code in one file unless a real domain boundary separates it.
  **[occasional]**
- Read the blast radius. A shared resource affects every consumer. Confirm the
  change targets the narrowest surface and stays backward compatible for live
  data. **[occasional]**
- Source environment-specific values, such as namespaces and hostnames, from
  runtime configuration. Do not hardcode them. **[occasional]**
  - *Why: a hardcoded value silently targets the wrong place the moment the
    install differs, which breaks isolation.*

## Failure and recovery

- Compare desired state with current state before writing. Never update or
  delete unconditionally on every pass. **[occasional]**
  - *Why: unconditional writes bump versions, fire spurious change events,
    create loops that never settle, and unconditional deletes destroy live
    data.*
- Treat a flow that needs two consecutive passes to converge as a smell. It is
  usually an early return that should not be there. One pass advances as far as
  it can.
- Emit a metric or a status condition for persistent, high-impact failures, not
  only a log line. Logs are reactive. Metrics and conditions let an operator
  alert before a user notices.
- Ship a risky change behind a safe default, a configuration switch or a
  feature flag, and state the rollback.
- Skip work when the target is already being deleted and continuing is
  meaningless.

## API design

- Drop name segments that repeat the parent type (`UpgradeInfo.UpgradeAvailable`
  becomes `UpgradeInfo.Available`). **[occasional]**
- Give names that encode exact purpose and scope (`NIGHTLY_UPGRADE_TARGET_CLUSTER_KUBECONFIG`,
  not `DEV_KUBECONFIG`). Scope a new package name to its real domain, not a
  broad catch-all. **[occasional]**
- Model a field that is not always meaningful as optional, and document what
  the zero value means. A required value type forces callers to supply
  meaningless values. **[occasional]**
- Reject duplicate or conflicting entries at the boundary, such as admission,
  input validation or the API layer. Do not let the core logic silently
  overwrite. **[occasional]**
  - *Why: validating at the boundary gives immediate feedback and prevents
    corruption found only later.*
- Question every new public knob. Is it needed, and will the public docs
  change? Each one is a permanent maintenance commitment. **[occasional]**
- Treat a rename or removal of a public field as a breaking change. Require a
  migration path or a version bump. **[occasional]**
- Give a template type its own spec type. Do not embed the parent's spec, and
  exclude instance-specific fields. **[occasional]**
- Spell out condition and status names in full, and prefix their constants
  consistently. These strings appear in tool output and logs as public API.
  **[occasional]**

## Kubernetes controllers, CRDs and charts

Apply this section only when the diff touches controllers, CRD types, webhooks
or Helm charts.

- Use `apierrors.IsNotFound(err)` to detect a 404. Never match substrings of
  `err.Error()`. Treat not-found as a no-op for objects that may disappear
  mid-reconcile and return on any other error. **[occasional]**
- Return a short `RequeueAfter` for an expected transient state, such as a
  dependency that does not exist yet. Do not return an error. **[occasional]**
  - *Why: an error triggers exponential backoff, which slows recovery from an
    expected in-progress condition.*
- Choose watch predicates that match every real trigger. A generation-only
  predicate misses label, annotation, finalizer and Secret-data changes.
  **[occasional]**
- Add a kubebuilder RBAC marker for every new resource type a controller
  watches or changes, then regenerate the manifests. **[occasional]**
- Set owner references at creation time. Register `Owns(&T{})` only when the
  owned object has that reference set. **[occasional]**
  - *Why: an `Owns()` watch without a real owner reference means owned objects
    are never garbage collected and events go to the wrong place.*
- Add complete kubebuilder markers to new CRD types (validation, printcolumn,
  shortname, status subresource) and regenerate the generated files.
  **[occasional]**
- Keep conditional defaulting and patching in a Go mutating webhook, not in
  YAML patch conditionals. **[occasional]**
  - *Why: YAML-layer conditionals cannot be unit tested and hide behavior from
    anyone reading the controllers.*
- Add Helm template guards (`fail` or `required`) for coupled flag
  combinations, so an invalid combination fails at render time. **[occasional]**
- Place new Helm values under a top-level key the chart owns. Never place them
  in a subchart's key namespace. **[occasional]**
  - *Why: a future upstream chart could add the same key and silently consume
    the value.*
