# npa-mathlib

`npa-mathlib` is the mutable public catalog of verified NPA certificate
artifacts. Catalog HEAD may add, remove, rename, reorganize, or replace modules
and declarations. Downstream stability comes from immutable released snapshots
and hash-pinned vendored certificate closures, not from compatibility with the
latest branch.

`npa-mathlib` is developed by
[Finite Field K.K.](https://finitefield.org/en/) as part of the NPA project
family.

Related repositories:

- [npa-core](https://github.com/finitefield-org/npa-core): kernel, certificate format,
  checker, frontend, tactic, and package CLI
- [npa-std](https://github.com/finitefield-org/npa-std): small standard-library
  package

This repository contains the mutable public package target at version `0.2.6`.
The target preserves the v0.2.4 two-element group theorem boundary and includes
the independently authored `Mathlib.Logic.Function.Basic` interface-adoption
pilot together with the adopted `Mathlib.Category.Basic` category-law
foundation:

```text
Mathlib.Logic.Basic
Mathlib.Logic.Implication
Mathlib.Logic.Eq
Mathlib.Logic.EqReasoning
Mathlib.Category.Basic
Mathlib.Logic.Function.Basic
Mathlib.Topology.Metric.Local
Mathlib.Logic.PropConnectives
Mathlib.Data.Nat.Basic
Mathlib.Core.Reduction
Mathlib.Algebra.Ring.UnitModel
Mathlib.Algebra.Ring.Basic
Mathlib.Algebra.OrderedField.Basic
Mathlib.Algebra.OrderedField.Square
Mathlib.Algebra.OrderedField.ScalarIdentities
Mathlib.Algebra.OrderedField.Strict
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
Mathlib.Algebra.Monoid.Power
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
version = "0.2.6"
schema = "npa.package.v0.1"
```

The catalog and downstream-consumption contract is defined in
[`docs/catalog-policy.md`](docs/catalog-policy.md). Namespace ownership, path
layout, revision, retirement, and naming rules are defined in
[`docs/namespace-policy.md`](docs/namespace-policy.md). Current module names are
proof-relevant identifiers but are not a stable API across snapshots.

The implemented workflow for choosing future module and declaration
interfaces from pinned observations of real repository usage, then
independently authoring and verifying their NPA implementations, is defined in
[the interface-adoption design](docs/usage-evidence-driven-interface-adoption-design.md).
Canonical interface proposals belong under `interface-proposals/`; they are
non-proof sidecars and are not catalog artifacts.

The completed `function-comp-assoc` pilot is traceable through the proposal
`npa-mathlib/interface-proposals/Mathlib/Logic/Function/Basic.toml` at exact
file hash
`sha256:24b858109bcda41b7f7586d77edec3c2484574866668035bee83f185881f12bd`.
Its owner artifact is `Mathlib.Logic.Function.Basic` with public `comp` and
`comp_assoc`, source-free certificate verification, and direct imports
`Mathlib.Logic.Eq` and `Std.Logic.Eq`. The handoff, qualification audit,
transaction attestation, and target-owned registry entry are respectively
`docs/interface-research/function-comp-assoc-handoff.md`,
`docs/promotion/function-comp-assoc-interface-adoption.md`,
`docs/promotion/function-comp-assoc-reconciliation.json`, and
`promotion-origins.json`. All proposal and governance sidecars retain
`proof_evidence = false`; no L2 `reviewed` or `recommended` decision was
created. The pilot is included in the v0.2.6 snapshot described below.

The canonical theorem-level, hash-bound L2 authority policy is
[`policy/l2-acceptance-policy.json`](policy/l2-acceptance-policy.json). The
authority, acceptance schema, invalidation rules, and validator command are
documented in
[`docs/l2-acceptance-policy.md`](docs/l2-acceptance-policy.md).
It establishes the optional `reviewed` maturity label; it is not required for
`verified` catalog admission. Rename-only reuse of accepted source decisions
is governed separately by
[`policy/l2-namespace-transport-policy.json`](policy/l2-namespace-transport-policy.json)
and [`docs/l2-namespace-transport-policy.md`](docs/l2-namespace-transport-policy.md).

## Catalog Maturity And Snapshots

- `verified`: exact certificate, hash, source-free verification, import, and
  axiom gates pass;
- `reviewed`: the exact theorem hashes additionally have canonical L2
  acceptance;
- `recommended`: reviewed content has also received explicit API and reuse
  curation; no machine-validated recommendation record exists yet.

Released versions and tags are immutable snapshots. Package versions identify
snapshots; they do not promise source or module compatibility with later
versions. Consumers should copy the smallest exact certificate import closure
into `vendor/npa-mathlib/` and pin the package version, export hashes, and
certificate hashes. They must never resolve a floating latest version.

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
npa package build-certs --root . --check --build-check-cache off --json
npa package verify-certs --root . --package-lock checked --checker reference \
  --audit-cache off --verifier-memo off --json
npa package check-hashes --root . --json
npa package axiom-report --root . --check --json
npa package index --root . --check --json
npa package theorem-premise-report --root . --check --json
npa package export-summary --root . --check --json
npa package publish-plan --root . --check --json
npa package check-generated --root . --timings summary --json
npa package audit-artifact-ledger --root . --module Mathlib.Logic.Function.Basic --json
npa package validate-promotion-origin-registry --root . --json
npa package check-interface-proposals --root . --proposal-root interface-proposals --json
npa package check-interface-proposal-surface \
  --root . --proposal-root interface-proposals \
  --proposal-path Mathlib/Logic/Function/Basic.toml \
  --proposal-sha256 sha256:24b858109bcda41b7f7586d77edec3c2484574866668035bee83f185881f12bd \
  --target-module Mathlib.Logic.Function.Basic --json
```

When direct catalog additions, revisions, or requested lifecycle changes have
accumulated since any older registered release, reconcile them without
requiring consecutive versions:

```sh
npa package reconcile-promotion-origin-registry \
  --root . \
  --previous-target-root ../npa-mathlib-previous \
  --audit docs/promotion/catalog-sync.md \
  --out docs/promotion/catalog-sync.json \
  --dry-run \
  --json
```

Repeat with `--apply` after reviewing the deterministic dry-run. Add
`--request docs/promotion/catalog-change-request.json` only for rename,
replacement, split, merge, or retirement relations.

For a proposal revision transition, supply the immediately preceding
validated proposal root explicitly; the validator does not discover history:

```sh
npa package check-interface-proposals \
  --root . --proposal-root interface-proposals \
  --previous-proposal-root ../../npa-mathlib-0.2.4/interface-proposals \
  --json
```

The previous root is read-only and must be a distinct snapshot. The command
checks only locally detectable per-record continuity and remains curation
metadata, not proof verification or catalog admission.

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

`fixtures/two-element-downstream/` checks the public `Z2` self-inverse,
self-product, and commutativity theorems through certificate-only imports.

`fixtures/monoid-power-downstream/` is the narrow source-free smoke for
`Mathlib.Algebra.Monoid.Power`. It applies `monoid_pow_zero` and
`monoid_pow_succ` through hash-pinned certificate imports and vendors no
`npa-mathlib` source or authoring sidecars.

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

`fixtures/ordered-field-strict-downstream/` is the narrow `v0.2.2` smoke
fixture for `Mathlib.Algebra.OrderedField.Strict`. It vendors only the strict
theorem layer's source-free import closure and applies
`ordered_field_one_sub_pos_of_lt_one`,
`ordered_field_square_lt_one_of_pos_lt_one`,
`ordered_field_one_sub_square_pos`, `ordered_field_two_ne_zero`, and
`ordered_field_four_ne_zero` from a downstream module.

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
- L2 authority policy, acceptance records, and validator status
- promotion plans, registry reconciliation and namespace-transport
  attestations, and the promotion-origin registry
- interface proposals, repository observations, proof references, and generated
  proposal indexes
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
The v0.2.2 release adds `Mathlib.Algebra.OrderedField.Strict`, whose derived
positivity, strict square-bound, one-minus-square positivity, and two-nonzero
and four-nonzero theorems carry only the expected `Eq.rec` dependency used to
transport strict-inequality endpoints. The four-nonzero theorem additionally
consumes the public field no-zero-product eliminator.
The v0.2.6 release adds `Mathlib.Category.Basic`, whose category-law
certificate carries the expected `Eq.rec` equality-transport dependency and
no custom axioms.

## License

`npa-mathlib` is licensed under the [Apache License 2.0](LICENSE).

Copyright 2026 [Finite Field K.K.](https://finitefield.org/en/). See
[NOTICE](NOTICE).
