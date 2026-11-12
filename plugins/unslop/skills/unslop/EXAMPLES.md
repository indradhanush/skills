# Unslop — worked examples

- [PR body: cut by half](#pr-body-cut-by-half) — real, 2026-08-15
- [Commit body](#commit-body) — constructed
- [Code comment](#code-comment) — constructed
- [Code comment: a rule, not an absence](#code-comment-a-rule-not-an-absence), constructed
- [Cadence](#cadence) — constructed

The first pair is real: an AI draft and Dhanush's edit of it, both verbatim. The
rest are constructed to isolate one tell each, and are labelled as such.

---

## PR body: cut by half

Onboarding an actor to a Cedar policy. Three files changed, one action needed
from the reviewer.

### Before, 40 lines

```markdown
## What

Adds `act-dhanush` (Indradhanush Gupta, infrastructure contractor) to
`maintainers` in both policy files, so the roster and the management surface
stay in step.

| File | Scope | Grants |
|---|---|---|
| `cluster/policy.yaml` | graph — `dev`, `crm`, `dev2` | read/export/change, … |
| `cluster/policy-cluster.yaml` | cluster | `graph_list` |

Five cases added to `cluster/policy.tests.yaml`, following the per-actor block
style used for `act-azim`.

## Needed from a reviewer

**Merging this grants nothing on its own.** The policy applies, but without a
token in the map the actor never authenticates. Someone with account access
needs to add `act-dhanush` to `mr-cluster/server/bearer-tokens`.

Terraform does not manage that secret — `infra/secrets.tf:44` reads it through a
data source. The apply's server restart is what picks the new token up.

## Group choice, worth a look

I put myself in `maintainers`, which carries write and merge authority on `main`
across all three graphs. My immediate need is read access for onboarding.
`maintainers` is where I expect to land as infra owner, but there is no
read-only human group today and I did not want to invent one in this PR. Happy
to narrow it if you would rather stage that.

## Verification

`omnigraph cluster validate --config cluster/` passes on this branch with output
byte-identical to `main`, exit 0 on both.

The five test cases are **unexecuted**. `omnigraph policy test` cannot run
against this cluster: it refuses a cluster carrying two policy bundles, and it
reads the applied bundle from S3 rather than local files. Both are recorded in
`docs/deploy.md`, tracked as `iss-cluster-policy-validation-graph-list`. …

So validate confirms the config is well-formed. It does not confirm the grant
evaluates as the cases assert.

## Incidental

`cluster/policy.yaml`'s header said the bearer-tokens secret is "managed in
omnigraph-platform". That repo no longer carries infrastructure … Corrected in
place with a pointer to `docs/operations.md`.

🤖 Generated with [Claude Code](https://claude.com/claude-code)
```

### After, 20 lines

```markdown
## What

Adds `act-dhanush` to `maintainers` in both policy files, so the roster and the
management surface stay in step.

| File | Scope | Grants |
|---|---|---|
| `cluster/policy.yaml` | graph — `dev`, `crm`, `dev2` | read/export/change, … |
| `cluster/policy-cluster.yaml` | cluster | `graph_list` |

Five cases added to `cluster/policy.tests.yaml`, following the per-actor block
style used for `act-azim`.

## Post merge requirements

 Add `act-dhanush` to `mr-cluster/server/bearer-tokens`.

## Verification

- `omnigraph cluster validate --config cluster/` passes
- Test cases are **unexecuted** since `omnigraph policy test` cannot run against
  this cluster yet
```

### What that teaches

| Cut | Rule |
|---|---|
| His own name and role | Already in the commit. The team knows him |
| "Group choice, worth a look", entire | Hedging and self-narration. A disagreement is a review comment, not a pre-emptive section |
| Fail-closed reasoning, Terraform data source, restart note | All true, all already in the commit and the repo. The PR body carries the delta, not a summary |
| "Incidental" section | The header fix is in the diff |
| Three verification paragraphs → two bullets | The distinction being drawn is one this repo's reviewers already hold |
| The attribution footer | Not wanted on his PRs |

**Kept unchanged: the table.** Structured facts earn their space. The prose
around them did not.

**Retitled, and the reframe matters.** "Needed from a reviewer" asks a person
for a favour and then justifies the asking. "Post merge requirements" states a
step that exists regardless of who does it, and pins the sequencing that the
prose version buried.

The whole body reduces to one fact that is true and not visible anywhere else:
**merging is not sufficient, a token has to be added.**

---

## Commit body

*Constructed, to isolate diff-narration.*

### Before

```
fix(auth): add nil check before accessing session token

This commit adds a nil check in `resolveActor` before accessing
`session.Token`, wrapping the access in an `if session != nil` guard.
Previously, the function would panic when `session` was nil. The fix
ensures that the function returns an error instead of panicking,
improving the robustness of the authentication flow.
```

### After

```
fix(auth): return an error rather than panicking on an absent session

A session is nil during the window between sign-in and the first token
refresh, which we only hit under load. Callers already handle the error
path.
```

Before narrates the diff and names the function. After gives what the diff
cannot: when nil actually happens, and why returning an error is safe.

---

## Code comment

*Constructed, to isolate what-not-why.*

### Before

```rust
// Loop over each table and call compact_files on it, then publish the
// results in a single manifest CAS at the end.
```

### After

```rust
// One CAS at the end, not per table: two compacted tables have to become
// visible together or a reader sees a half-compacted graph.
```

Before restates the code. After survives a refactor, because it records the
decision rather than the mechanism.

## Code comment: a rule, not an absence

*Constructed, to isolate a comment that explains what is missing and names
mechanisms the check does not use.*

### Before

```go
case v1.Create:
	// No stored identity exists yet on create, so the name is compared, and
	// the object must carry the identity the stamping webhook wrote.
case v1.Update:
	// The stored identity names the certificate that created the host. The
	// requester controls the new object, so only the old one counts.
```

### After

```go
case v1.Create:
	// A new host is named after its identity, so the name must match the
	// identity of the requester.
case v1.Update:
	// The requester controls the new object, so authorize against the
	// identity stored on the existing host instead.
```

Before explains what is absent and points at the certificate and the webhook,
which are not part of the check. After states the rule and names the thing it
checks.

Tells fixed by the rewrite:

- The comment explains an absence ("No stored identity exists yet") instead of the rule
- It uses a vague referent ("the old one") instead of naming the thing
- It mentions mechanisms the check does not use (the certificate, the stamping webhook), which leaks design into the comment
- It is longer than the rule needs

---

## Cadence

*Constructed, to isolate rhythm.*

### Before

```
The lock protects the work. The CAS protects the record. Together they
give one winner per apply. Neither is sufficient on its own.
```

Four sentences, near-identical length, perfectly parallel, closing summary. It
scans as competent and sounds like nobody.

### After

```
The lock protects the work, which matters because apply creates graphs and
applies schemas long before it ever touches the ledger. The CAS protects the
record. Neither is a fence.
```

Long, short, shorter. The first sentence carries a clause on a preposition the
way speech does. The parallelism that survives is doing real work, and the
closing summary is gone.

## Bullet points

*Constructed, to isolate message clarity*

### Before

```
The reproduction-steps plan pointed at an old dataplane (pcd-dataplane5.yaml) and
the sm-dev / test-jan tenant namespaces, which no longer apply. Retarget it to the
dg-d dataplane vcluster. OWN_TENANT_NS is now dg-d-default-service (holds ByoHosts
dg-byoh-cluster-blue-1..5); OTHER_TENANT_NS is dg-d-default-dev for cross-tenant
verification. Three values differ on this cluster. `auth can-i --as-group` now
requires a paired `--as` user. F7's byoh SA reads secrets in its own namespace
via byoh-role (cross-tenant read still denied). F10/F12 read the single per-DU
secret dex-dg-d in ns dg-d instead of looping across customers.
```

### After

```

The reproduction-steps plan pointed at an old dataplane. Retargeted it to the
new one. The changes are:

- pcd-dataplane5.yaml -> dg-d dataplane vcluster
- OWN_TENANT_NS -> dg-d-defulat-service (with ByoHosts dg-byoh-cluster-blue-1..5)
- OTHER_TENANT_NS -> dg-d-default-dev for cross-tenant verification

Other vlause that differ on this cluster:

- `auth can-i --as-group` now requires a paired `--as` user
- F7's byoh SA reads secrets in its own namespace via byoh-role (cross-tenant
  read still denied)
- F10/F12 read the single per-DU secret dex-dg-d in ns dg-d instead of looping
  across customers

## PR body: note to reviewers

*Real, Dhanush's edit of an AI draft.*

### Before

```
E2E is expected to fail in this PR. The stack merges together, which ensures the
agent, byohctl and controller changes are used and E2E tests pass. The six
commits build on each other: a pure rename of the registrar Ginkgo tests, then
state file, identity, CSR, registration and hostname. Each commit compiles. Lint
and tests are expected to pass for the PR as a whole.
```

### After

```
E2E is expected to fail in this PR. The stack is to be merged together, which
ensures the agent, byohctl and controller changes are used and E2E tests pass.
```

Cut the commit list, "each commit compiles" and "lint and tests pass". The
reviewer sees the commits and the CI. The note keeps the one fact they cannot
see: E2E fails until the stack merges.
