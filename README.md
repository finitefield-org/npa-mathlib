# npa-mathlib

`npa-mathlib` is the public theorem-library package for NPA.

`npa-mathlib` is developed by
[Finite Field K.K.](https://finitefield.org/en/) as part of the NPA project
family.

Related repositories:

- [npa](https://github.com/finitefield-org/npa): kernel, certificate format,
  checker, frontend, tactic, and package CLI
- [npa-std](https://github.com/finitefield-org/npa-std): small standard-library
  package

This repository contains the Layer 3C public package:

```text
Mathlib.Algebra.Group.Subgroup.Order
Mathlib.Algebra.Group.Subgroup
Mathlib.Logic.EqReasoning
Mathlib.Algebra.Group.Basic
Mathlib.Geometry.RightTriangle
Mathlib.Geometry.Metric
Mathlib.Vector.Basic
Mathlib.Vector.Dot
Mathlib.Algebra.Ring
Mathlib.Algebra.Square
Mathlib.Algebra.OrderedField
Mathlib.Logic.Basic
Mathlib.Logic.Prop
Mathlib.Logic.Eq
Mathlib.Data.Nat.Basic
Mathlib.Core.Reduction
```

Package metadata:

```text
package = "npa-mathlib"
version = "0.1.6"
schema = "npa.package.v0.1"
```

The public module namespace policy is fixed in
[`docs/namespace-policy.md`](docs/namespace-policy.md). Released module names
are stable package identifiers and must not be renamed casually.

## Toolchain Pin

Package CI builds `npa` from the current public toolchain ref:

```text
NPA_GIT_TAG = v0.1.1
RUST_TOOLCHAIN_VERSION = 1.95.0
NPA_ENABLE_PUBLISH_PLAN = true
```

The workflows require exactly one pinned `npa` source: `NPA_BINARY_PATH`,
`NPA_GIT_TAG`, or `NPA_GIT_COMMIT`. `NPA_VERSION` is reserved for a later
release-download mode and is rejected by the current setup helper.

## Verification

With `npa` on `PATH`, run:

```sh
npa package check --root . --json
npa package build-certs --root . --check --json
npa package verify-certs --root . --checker reference --json
npa package check-hashes --root . --json
npa package axiom-report --root . --check --json
npa package index --root . --check --json
npa package publish-plan --root . --check --json
```

The PR workflow runs the reference-checker package gate. The release workflow
also uploads package artifacts and records fast-kernel diagnostics.

## External Imports

The only external imports are hash-pinned `npa-std v0.1.0` certificate
artifacts:

- `Std.Logic.Eq`
- `Std.Nat.Basic`

These vendored certificates are pinned to the `npa-std v0.1.0` release bundle:

- Release:
  <https://github.com/finitefield-org/npa-std/releases/tag/v0.1.0>
- Bundle:
  `npa-std-v0.1.0-release-artifacts.tar.gz`
- Bundle SHA-256:
  `3ed967d1870f97f7042e87a75efebd3cf553e8c86d8959c720080115a78fe85c`
- `Std.Logic.Eq` certificate file SHA-256:
  `7aa25a1adf44de35cdaaa514484c1220fec0e543d3f65803805b5e6efc5b36a1`
- `Std.Nat.Basic` certificate file SHA-256:
  `d057dbc0e3c1e21649968eeaf882616602cfeb1f1cbb8393031c2010ea9596fb`

## Downstream Smoke Fixture

`fixtures/downstream-smoke/` models a downstream package that consumes the
vendored `Mathlib.Algebra.Group.Subgroup.Order` Layer 3C import closure. It
does not vendor `npa-mathlib` source, replay, meta, theorem index, registry
state, or package source tree.

To verify it:

```sh
npa package check --root fixtures/downstream-smoke --json
npa package build-certs --root fixtures/downstream-smoke --check --json
npa package verify-certs --root fixtures/downstream-smoke --checker reference --json
npa package check-hashes --root fixtures/downstream-smoke --json
```

## Trust Boundary

Trusted proof evidence remains:

- canonical `.npcert` bytes
- Rust kernel / verifier verdict
- source-free reference checker verdict
- deterministic `export_hash`, `certificate_hash`, and `axiom_report_hash`

Untrusted helper data remains:

- `.npa` source files
- replay files
- meta files
- package manifest
- theorem index
- publish plan
- CI status
- Git tags and release pages

This package does not emit `verified_high_trust`. High-trust evidence requires
separate pinned external checker binaries, runner policies, checker registry
data, and release audit evidence.

## Axiom Policy

Custom axioms are not allowed. The package permits the built-in `Eq.rec` axiom
surface because `Mathlib.Logic.EqReasoning` and
`Mathlib.Algebra.Group.Basic` / `Mathlib.Algebra.Group.Subgroup` expose
equality reasoning certificates that use it explicitly. The Layer 3C
`Mathlib.Algebra.Group.Subgroup.Order` module adds no direct or transitive
axioms.

## License

`npa-mathlib` is licensed under the [Apache License 2.0](LICENSE).

Copyright 2026 [Finite Field K.K.](https://finitefield.org/en/). See
[NOTICE](NOTICE).
