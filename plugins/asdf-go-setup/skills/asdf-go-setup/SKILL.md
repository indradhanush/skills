---
name: asdf-go-setup
description: >
  Install and configure gopls for a Go project that uses asdf to manage the
  Go version. Use this whenever gopls fails to start for a project, an editor
  reports "No version is set for command gopls", or a new Go project needs LSP
  set up with asdf. Accepts a project path as args; detects the correct Go
  version from .tool-versions or go.mod automatically.
---

# asdf Go Setup

Install gopls into the right location for a project's asdf-managed Go version.

## Background

asdf's golang plugin only scans `bin/` and `go/bin/` inside an install
(via `list-bin-paths`). gopls must live at:

```
$ASDF_DATA/installs/golang/<version>/bin/gopls
```

`ASDF_DATA` is the asdf data directory, `${ASDF_DATA_DIR:-$HOME/.asdf}`. It
is not `ASDF_DIR`. When Nix installs asdf, `ASDF_DIR` points to a read-only
`/nix/store` path and the installs live under `~/.asdf`.

`go install` without an explicit `GOBIN` puts the binary in `$GOPATH/bin`
(`packages/bin/`), which asdf never finds. The fix is to set both `GOBIN`
and `GOPATH` explicitly.

## Steps

### 1. Resolve the project path

Use `args` if provided; default to the current working directory.

### 2. Detect the Go version

Check in order, stopping at the first match:

1. `<path>/.tool-versions` — look for a line starting with `golang `
2. `<path>/go.mod` — read the `go` directive (e.g. `go 1.22.12`)
3. `~/.tool-versions` — global asdf fallback

If no version is found anywhere, report the gap and stop. Do not guess.

```bash
# From .tool-versions
grep '^golang ' <path>/.tool-versions | awk '{print $2}'

# From go.mod
grep '^go ' <path>/go.mod | awk '{print $2}'
```

### 3. Locate the asdf data directory

```bash
echo "${ASDF_DATA_DIR:-$HOME/.asdf}"
```

Use this value as `$ASDF_DATA` in every `installs/` path below. Do not use
`ASDF_DIR` for these paths.

### 4. Verify the version is installed

Run this from inside the project path, so asdf reads the project's
`.tool-versions`:

```bash
cd <path> && asdf current golang
```

The installed column shows `true` or `false`.

If it shows `false`, tell the user the exact command,
`asdf install golang <version>`, and ask once whether to run it. On yes, run
it and continue to step 5. On no, stop.

If the install directory has no `bin/` or `go/bin/`, the install is broken.
Offer to reinstall it the same way.

### 5. Check whether gopls is already installed

```bash
ls "$ASDF_DATA/installs/golang/<version>/bin/gopls" 2>/dev/null
```

If the binary exists, skip to step 7.

### 6. Install gopls

Use the Go binary for that exact version so the module cache stays isolated:

```bash
GOBIN="$ASDF_DATA/installs/golang/<version>/bin" \
GOPATH="$ASDF_DATA/installs/golang/<version>/packages" \
  "$ASDF_DATA/installs/golang/<version>/go/bin/go" install \
  golang.org/x/tools/gopls@latest
```

Note: gopls >= v0.22 requires Go >= 1.26. If the project version is older,
Go will auto-switch via `GOTOOLCHAIN` to a newer installed version. This is
expected and harmless — the binary still lands in the project version's
`bin/` directory.

Then reshim:

```bash
asdf reshim golang <version>
```

### 7. Verify

Run this from inside the project path:

```bash
cd <path> && asdf which gopls
```

Expected output: `$ASDF_DATA/installs/golang/<version>/bin/gopls`

If it still fails, check that `<path>/.tool-versions` contains
`golang <version>` — the shim uses that file to resolve which version to
serve.

### 8. Report

Tell the user:
- which Go version was detected and from which file
- whether gopls was freshly installed or already present
- the verified path returned by `asdf which gopls`
