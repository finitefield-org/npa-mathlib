# npa-mathlib downstream fixture

Visibility: public example fixture.

This fixture models a downstream package that consumes the local
`npa-mathlib` release artifact set without a registry server.

Consumed package artifact:

- release metadata: `../../generated/publish-plan.json`
- downstream import bundle modules:
  `Mathlib.Algebra.Group.FirstIsomorphism`,
  `Mathlib.Algebra.Group.FirstIsomorphism.Image`,
  `Mathlib.Algebra.Group.Image`,
  `Mathlib.Algebra.Group.Kernel.Quotient`,
  `Mathlib.Algebra.Group.Kernel.Quotient.Mul`,
  `Mathlib.Algebra.Group.Kernel.Quotient.Group`,
  `Mathlib.Algebra.Group.Kernel.Quotient.Hom`
- source-free proof artifact:
  `../../Mathlib/Algebra/Group/FirstIsomorphism/certificate.npcert`,
  `../../Mathlib/Algebra/Group/FirstIsomorphism/Image/certificate.npcert`,
  `../../Mathlib/Algebra/Group/Image/certificate.npcert`,
  `../../Mathlib/Algebra/Group/Kernel/Quotient/certificate.npcert`,
  `../../Mathlib/Algebra/Group/Kernel/Quotient/Mul/certificate.npcert`,
  `../../Mathlib/Algebra/Group/Kernel/Quotient/Group/certificate.npcert`,
  `../../Mathlib/Algebra/Group/Kernel/Quotient/Hom/certificate.npcert`

The fixture vendors the source-free certificate import closure for
`Mathlib.Algebra.Group.FirstIsomorphism.*`:

- `vendor/npa-std/Std/Logic/Eq/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Logic/EqReasoning/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/Group/Basic/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/Group/Image/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/Group/Kernel/Quotient/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/Group/Kernel/Quotient/Mul/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/Group/Kernel/Quotient/Group/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/Group/Kernel/Quotient/Hom/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/Group/FirstIsomorphism/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/Group/FirstIsomorphism/Image/certificate.npcert`

The imports in `npa-package.toml` are pinned to package names, package
versions, export hashes, and certificate hashes from the publish plan's
`downstream_import_bundle` and the public `npa-std` release evidence. Package
checks also pin certificate file hashes in `generated/package-lock.json`.

Source files, replay files, meta files, theorem indexes, and registry
state are not proof evidence for this fixture. They are deliberately absent
from the vendored dependency tree; source-free verification reads only the
hash-pinned certificate bytes and the downstream package certificate.

The local theorem
`Downstream.GroupFirstIsomorphism::first_isomorphism_to_image_passthrough`
imports the Layer 3D-C first isomorphism closure and applies the exported
theorem `first_isomorphism_to_image`.
