# npa-mathlib downstream fixture

Visibility: public example fixture.

This fixture models a downstream package that consumes the local
`npa-mathlib` release artifact set without a registry server.

Consumed package artifact:

- release metadata: `../../generated/publish-plan.json`
- downstream import bundle modules:
  `Mathlib.Algebra.Group.Subgroup`,
  `Mathlib.Algebra.Group.Subgroup.Order`,
  `Mathlib.Algebra.Group.Quotient`,
  `Mathlib.Algebra.Group.Quotient.Mul`,
  `Mathlib.Algebra.Group.Quotient.Group`,
  `Mathlib.Algebra.Group.Correspondence.Basic`,
  `Mathlib.Algebra.Group.Correspondence.Order`,
  `Mathlib.Algebra.Group.Correspondence`,
  `Mathlib.Algebra.Group.Correspondence.Ordered`
- source-free proof artifact:
  `../../Mathlib/Algebra/Group/Subgroup/certificate.npcert`,
  `../../Mathlib/Algebra/Group/Subgroup/Order/certificate.npcert`,
  `../../Mathlib/Algebra/Group/Quotient/certificate.npcert`,
  `../../Mathlib/Algebra/Group/Quotient/Mul/certificate.npcert`,
  `../../Mathlib/Algebra/Group/Quotient/Group/certificate.npcert`,
  `../../Mathlib/Algebra/Group/Correspondence/Basic/certificate.npcert`,
  `../../Mathlib/Algebra/Group/Correspondence/Order/certificate.npcert`,
  `../../Mathlib/Algebra/Group/Correspondence/certificate.npcert`,
  `../../Mathlib/Algebra/Group/Correspondence/Ordered/certificate.npcert`

The fixture vendors the source-free certificate import closure for
`Mathlib.Algebra.Group.Correspondence.Ordered`:

- `vendor/npa-std/Std/Logic/Eq/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Logic/EqReasoning/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/Group/Basic/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/Group/Subgroup/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/Group/Subgroup/Order/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/Group/Quotient/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/Group/Quotient/Mul/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/Group/Quotient/Group/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/Group/Correspondence/Basic/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/Group/Correspondence/Order/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/Group/Correspondence/certificate.npcert`
- `vendor/npa-mathlib/Mathlib/Algebra/Group/Correspondence/Ordered/certificate.npcert`

The imports in `npa-package.toml` are pinned to package names, package
versions, export hashes, and certificate hashes from the publish plan's
`downstream_import_bundle` and the public `npa-std` release evidence. Package
checks also pin certificate file hashes in `generated/package-lock.json`.

Source files, replay files, meta files, theorem indexes, and registry
state are not proof evidence for this fixture. They are deliberately absent
from the vendored dependency tree; source-free verification reads only the
hash-pinned certificate bytes and the downstream package certificate.

The local theorem
`Downstream.GroupCorrespondence::correspondence_theorem_evidence_passthrough`
imports the Layer 3D-G correspondence root module and applies the exported
theorem `correspondence_theorem_evidence`. The local theorem
`Downstream.GroupCorrespondence::correspondence_order_evidence_passthrough`
also imports the ordered closure and applies `correspondence_order_evidence`.
