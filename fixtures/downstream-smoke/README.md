# npa-mathlib downstream fixture

Visibility: public example fixture.

This fixture models a downstream package that consumes the local
`npa-mathlib` release artifact set without a registry server.

Consumed package artifact:

- release metadata: `../../generated/publish-plan.json`
- downstream import bundle module: `Mathlib.Algebra.Ring.ChineseRemainder`
- source-free proof artifact:
  `../../Mathlib/Algebra/Ring/ChineseRemainder/certificate.npcert`

The fixture vendors the source-free certificate import closure for
`Mathlib.Algebra.Ring.ChineseRemainder`:

- `vendor/npa-std/Std/Logic/Eq/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Logic/EqReasoning/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/Group/Basic/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/Group/Image/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/Group/Kernel/Quotient/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/Group/Kernel/Quotient/Mul/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/Group/Kernel/Quotient/Group/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/Group/Kernel/Quotient/Hom/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/Group/FirstIsomorphism/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/Ring/Basic/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/Ring/FirstIsomorphism/Basic/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/Ring/FirstIsomorphism/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/Ring/ChineseRemainder/certificate.npcert`

The imports in `npa-package.toml` are pinned to package names, package
versions, export hashes, and certificate hashes from the publish plan's
`downstream_import_bundle` and the public `npa-std` release evidence. Package
checks also pin certificate file hashes in `generated/package-lock.json`.

Source files, replay files, meta files, theorem indexes, and registry state
are not proof evidence for this fixture. They are deliberately absent from the
vendored dependency tree; source-free verification reads only the hash-pinned
certificate bytes and the downstream package certificate.

The local theorems in `Downstream.RingIsoCrt` import the ring
first-isomorphism and CRT closure and apply the exported
`ring_first_isomorphism_to_image` and `ring_chinese_remainder_theorem`
surface.
