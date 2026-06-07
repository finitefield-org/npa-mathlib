# npa-mathlib downstream fixture

Visibility: public example fixture.

This fixture models a downstream package that consumes the local
`npa-mathlib` release artifact set without a registry server.

Consumed package artifact:

- release metadata: `../../generated/publish-plan.json`
- downstream import bundle module:
  `Mathlib.Algebra.Field.Basic`
- source-free proof artifact:
  `../../Mathlib/Algebra/Field/Basic/certificate.npcert`

The fixture vendors the source-free certificate import closure for
`Mathlib.Analysis.Calculus.ImplicitFunction`:

- `vendor/npa-std/Std/Logic/Eq/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Logic/EqReasoning/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Topology/Metric/Local/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/Ring/Basic/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/Field/Basic/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/OrderedField/Basic/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/OrderedField/Square/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/LinearAlgebra/VectorSpace/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Analysis/NormedSpace/Basic/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Analysis/NormedSpace/LinearMap/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Analysis/Calculus/Derivative/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Analysis/Calculus/ImplicitFunction/AugmentedMap/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Analysis/Calculus/ImplicitFunction/certificate.npcert`

The imports in `npa-package.toml` are pinned to package names, package
versions, export hashes, and certificate hashes from the publish plan's
`downstream_import_bundle` and the public `npa-std` release evidence. Package
checks also pin certificate file hashes in `generated/package-lock.json`.

Source files, replay files, meta files, theorem indexes, and registry state
are not proof evidence for this fixture. They are deliberately absent from the
vendored dependency tree; source-free verification reads only the hash-pinned
certificate bytes and the downstream package certificate.

The local theorems in `Downstream.ImplicitFunction` import the field and
implicit-function closures and apply the exported `field_div_eq_mul_inv`,
`implicit_augmented_map_derivative`, `implicit_function_theorem`, and
`implicit_function_derivative_theorem` surfaces.
