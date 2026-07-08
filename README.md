# npa-mathlib

`npa-mathlib` is the public theorem-library package for NPA.

`npa-mathlib` is developed by
[Finite Field K.K.](https://finitefield.org/en/) as part of the NPA project
family.

Related repositories:

- [npa-core](https://github.com/finitefield-org/npa-core): kernel, certificate format,
  checker, frontend, tactic, and package CLI
- [npa-std](https://github.com/finitefield-org/npa-std): small standard-library
  package

This repository contains the public package through the v0.2.1
Riemann-hypothesis candidate-proof audit boundary:

```text
Mathlib.Logic.Basic
Mathlib.Logic.Implication
Mathlib.Logic.Eq
Mathlib.Logic.EqReasoning
Mathlib.Topology.Metric.Local
Mathlib.Logic.PropConnectives
Mathlib.Data.Nat.Basic
Mathlib.Core.Reduction
Mathlib.Algebra.Ring.UnitModel
Mathlib.Algebra.Ring.Basic
Mathlib.Algebra.OrderedField.Basic
Mathlib.Algebra.OrderedField.Square
Mathlib.Algebra.OrderedField.ScalarIdentities
Mathlib.LinearAlgebra.VectorSpace
Mathlib.Analysis.NormedSpace.Basic
Mathlib.Analysis.NormedSpace.LinearMap
Mathlib.Analysis.Calculus.Derivative
Mathlib.Analysis.FixedPoint.Banach
Mathlib.Analysis.Calculus.InverseFunction
Mathlib.Analysis.Calculus.ImplicitFunction.AugmentedMap
Mathlib.Analysis.Calculus.ImplicitFunction
Mathlib.LinearAlgebra.InnerProduct
Mathlib.LinearAlgebra.InnerProduct.Derived
Mathlib.Geometry.Affine
Mathlib.Geometry.Affine.Derived
Mathlib.Geometry.RightTriangle.Carrier
Mathlib.Geometry.RightTriangle.Derived
Mathlib.Geometry.Metric.Carrier
Mathlib.Geometry.Pythagorean
Mathlib.Algebra.Ring.UnitModel.Square
Mathlib.Algebra.Group.Basic
Mathlib.Algebra.Group.Subgroup
Mathlib.Algebra.Group.Subgroup.Order
Mathlib.Algebra.Group.Kernel
Mathlib.Algebra.Group.Image
Mathlib.Algebra.OrderedField.UnitModel
Mathlib.LinearAlgebra.Vector.UnitModel
Mathlib.LinearAlgebra.InnerProduct.UnitModel
Mathlib.Geometry.RightTriangle.UnitModel
Mathlib.Geometry.Metric.UnitModel
Mathlib.Algebra.Field.Basic
Mathlib.LinearAlgebra.Matrix.Basic
Mathlib.LinearAlgebra.Matrix.Determinant
Mathlib.NumberTheory.RiemannHypothesis.CandidateAudit
```

Package metadata:

```text
package = "npa-mathlib"
version = "0.2.1"
schema = "npa.package.v0.1"
```

The public module namespace policy is fixed in
[`docs/namespace-policy.md`](docs/namespace-policy.md). Released module names
are stable package identifiers and must not be renamed casually.

## Toolchain Reference

Use an `npa` binary built from the current public toolchain ref:

```text
RUST_TOOLCHAIN_VERSION = 1.95.0
NPA_GIT_TAG = v0.2.0
NPA_ENABLE_PUBLISH_PLAN = true
```

`NPA_VERSION` is reserved for a later release-download mode and is not a valid
current package-command pin.

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

For release review, archive the generated package artifacts and any optional
fast-kernel diagnostics explicitly.

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
vendored `Mathlib.Analysis.Calculus.ImplicitFunction` implicit-function import
closure, the `Mathlib.LinearAlgebra.Matrix.Basic` matrix foundation,
`Mathlib.LinearAlgebra.Matrix.Determinant`, and the source-free
`Mathlib.NumberTheory.RiemannHypothesis.CandidateAudit` certificate. It applies
`matrix_intro`, `determinant_product`, `implicit_augmented_map_derivative`,
`implicit_function_theorem`, and `implicit_function_derivative_theorem`. It
vendors the full source-free certificate import closure, but not
`npa-mathlib` source, replay, meta, theorem index, registry state, or package
source tree.

The `Mathlib.NumberTheory.RiemannHypothesis.CandidateAudit` module is an audit
workflow boundary. It records checked gates for classifying and blocking
candidate RH proofs, and it includes a `no_rh_conclusion` projection. It does
not prove RH or any RH-equivalent criterion.

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
- command status
- Git tags and release pages

This package does not emit `verified_high_trust`. High-trust evidence requires
separate pinned external checker binaries, runner policies, checker registry
data, and release audit evidence.

## Axiom Policy

Custom axioms are not allowed. The package permits the built-in `Eq.rec` axiom
surface because `Mathlib.Logic.EqReasoning` and
`Mathlib.Algebra.Group.Basic` / `Mathlib.Algebra.Group.Subgroup` expose
equality reasoning certificates that use it explicitly. The v0.2.0 breaking
cleanup removes public modules whose certificates rely on the core `Quotient`
surface. The package direct axiom surface remains policy-approved for the
remaining modules, and they only carry the expected `Eq.rec` equality-reasoning
dependency where their proofs require transport. The v0.1.17 release adds the
abstract ordered field, square-normalization, and scalar-identity route. The
first two modules carry no custom axioms;
`Mathlib.Algebra.OrderedField.ScalarIdentities` carries the expected `Eq.rec`
dependency through equality transport. The v0.1.18
release adds the abstract vector-space foundation route, and
`Mathlib.LinearAlgebra.VectorSpace` carries no custom axioms. The v0.1.19
release adds the abstract inner-product route. `Mathlib.LinearAlgebra.InnerProduct`
carries no custom axioms, and `Mathlib.LinearAlgebra.InnerProduct.Derived`
carries the expected `Eq.rec` dependency through equality-reasoning and scalar
identity imports. The v0.1.20 release adds the abstract geometry Pythagorean
route. `Mathlib.Geometry.Affine` and
`Mathlib.Geometry.RightTriangle.Carrier` carry no custom axioms, while
`Mathlib.Geometry.Affine.Derived`,
`Mathlib.Geometry.RightTriangle.Derived`,
`Mathlib.Geometry.Metric.Carrier`, and `Mathlib.Geometry.Pythagorean` carry
the expected `Eq.rec` dependency through equality-reasoning, scalar identity,
and law-package imports. The v0.1.21 release adds the analysis metric
topology route. `Mathlib.Topology.Metric.Local` carries no direct custom
axioms and has the expected transitive `Eq.rec` dependency through
`Mathlib.Logic.EqReasoning`. The v0.1.22 release adds the analysis
normed-space route. `Mathlib.Analysis.NormedSpace.Basic` carries no direct
custom axioms and has the expected transitive `Eq.rec` dependency through
equality-reasoning and vector-space imports. The v0.1.23 release adds the
analysis linear-map route. `Mathlib.Analysis.NormedSpace.LinearMap` carries no direct
custom axioms and has the expected transitive `Eq.rec` dependency through
equality-reasoning, vector-space, and normed-space imports. The v0.1.24
release adds the analysis derivative route.
`Mathlib.Analysis.Calculus.Derivative` carries no direct custom axioms and no
transitive axioms according to its package axiom report. The v0.1.25 release
adds the analysis fixed-point route. `Mathlib.Analysis.FixedPoint.Banach`
carries no direct custom axioms and no transitive axioms according to its
package axiom report. The v0.1.26 release adds the analysis inverse-function
route. `Mathlib.Analysis.Calculus.InverseFunction` carries no direct custom
axioms and no transitive axioms according to its package axiom report. The
v0.1.27 release adds the analysis implicit-function route.
`Mathlib.Analysis.Calculus.ImplicitFunction.AugmentedMap` and
`Mathlib.Analysis.Calculus.ImplicitFunction` carry no direct custom axioms and
have the expected transitive `Eq.rec` dependency through equality-reasoning and
analysis foundation imports.
The v0.2.1 release adds
`Mathlib.NumberTheory.RiemannHypothesis.CandidateAudit`, which carries no
direct or transitive custom axioms and explicitly preserves the no-RH-conclusion
boundary.

## License

`npa-mathlib` is licensed under the [Apache License 2.0](LICENSE).

Copyright 2026 [Finite Field K.K.](https://finitefield.org/en/). See
[NOTICE](NOTICE).
