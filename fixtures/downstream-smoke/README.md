# npa-mathlib downstream fixture

Visibility: public example fixture.

This fixture models a downstream package that consumes the local
`npa-mathlib` release artifact set without a registry server.

Consumed package artifact:

- release metadata: `../../generated/publish-plan.json`
- downstream import bundle module: `Mathlib.Geometry.Pythagorean`
- source-free proof artifact:
  `../../Mathlib/Geometry/Pythagorean/certificate.npcert`

The fixture vendors the source-free certificate import closure for
`Mathlib.Geometry.Pythagorean`:

- `vendor/npa-std/Std/Logic/Eq/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Logic/EqReasoning/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/Ring/Basic/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/OrderedField/Basic/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/OrderedField/Square/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/OrderedField/ScalarIdentities/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/LinearAlgebra/VectorSpace/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/LinearAlgebra/InnerProduct/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/LinearAlgebra/InnerProduct/Derived/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Geometry/Affine/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Geometry/Affine/Derived/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Geometry/RightTriangle/Abstract/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Geometry/RightTriangle/Derived/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Geometry/Metric/Abstract/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Geometry/Pythagorean/certificate.npcert`

The imports in `npa-package.toml` are pinned to package names, package
versions, export hashes, and certificate hashes from the publish plan's
`downstream_import_bundle` and the public `npa-std` release evidence. Package
checks also pin certificate file hashes in `generated/package-lock.json`.

Source files, replay files, meta files, theorem indexes, and registry state
are not proof evidence for this fixture. They are deliberately absent from the
vendored dependency tree; source-free verification reads only the hash-pinned
certificate bytes and the downstream package certificate.

The local theorems in `Downstream.GeometryPythagorean` import the geometry
closure and apply the exported `pythagorean_distance_general`,
`pythagorean_theorem_dist_sq`, and `pythagorean_theorem_api_alias` surface.
