# npa-mathlib downstream fixture

Visibility: public example fixture.

This fixture models a downstream package that consumes the local
`npa-mathlib` release artifact set without a registry server.

Consumed package artifact:

- release metadata: `../../generated/publish-plan.json`
- downstream import bundle modules:
  `Mathlib.Algebra.Group.Kernel`, `Mathlib.Algebra.Group.Image`
- source-free proof artifact:
  `../../Mathlib/Algebra/Group/Kernel/certificate.npcert`,
  `../../Mathlib/Algebra/Group/Image/certificate.npcert`

The fixture vendors the source-free certificate import closure for
`Mathlib.Algebra.Group.Kernel` and `Mathlib.Algebra.Group.Image`:

- `vendor/npa-std/Std/Logic/Eq/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Logic/EqReasoning/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/Group/Basic/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/Group/Kernel/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/Group/Image/certificate.npcert`

The imports in `npa-package.toml` are pinned to package names, package
versions, export hashes, and certificate hashes from the publish plan's
`downstream_import_bundle` and the public `npa-std` release evidence. Package
checks also pin certificate file hashes in `generated/package-lock.json`.

Source files, replay files, meta files, theorem indexes, and registry
state are not proof evidence for this fixture. They are deliberately absent
from the vendored dependency tree; source-free verification reads only the
hash-pinned certificate bytes and the downstream package certificate.

The local theorem
`Downstream.GroupKernelImage::kernel_mul_closed_passthrough` imports the
Layer 3D-A kernel closure and applies the exported theorem
`kernel_mul_closed`. The local theorem
`Downstream.GroupKernelImage::image_intro_passthrough` imports the image
closure and applies the exported theorem `image_intro`.
