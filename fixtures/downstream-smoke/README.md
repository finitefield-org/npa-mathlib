# npa-mathlib downstream fixture

Visibility: public example fixture.

This fixture models a downstream package that consumes the local
`npa-mathlib` release artifact set without a registry server.

Consumed package artifact:

- release metadata: `../../generated/publish-plan.json`
- downstream import bundle module: `Mathlib.Vector.Dot`
- source-free proof artifact:
  `../../Mathlib/Vector/Dot/certificate.npcert`

The fixture vendors the source-free certificate import closure for
`Mathlib.Vector.Dot`:

- `vendor/npa-std/Std/Logic/Eq/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/Ring/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/Square/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/OrderedField/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Vector/Basic/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Vector/Dot/certificate.npcert`

The imports in `npa-package.toml` are pinned to package names, package
versions, export hashes, and certificate hashes from the publish plan's
`downstream_import_bundle` and the public `npa-std` release evidence. Package
checks also pin certificate file hashes in `generated/package-lock.json`.

Source files, replay files, meta files, theorem indexes, and registry
state are not proof evidence for this fixture. They are deliberately absent
from the vendored dependency tree; source-free verification reads only the
hash-pinned certificate bytes and the downstream package certificate.

The local theorem
`Downstream.VectorDot::vector_norm_sq_nonneg_passthrough` imports the Layer 2A
vector closure and applies the exported theorem `norm_sq_nonneg`.
