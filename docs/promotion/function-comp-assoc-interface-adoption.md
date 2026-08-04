# Direct Qualification Audit: `function-comp-assoc`

This audit records the exact inputs for the UIA-19 direct catalog
reconciliation dry-run. It is governance metadata, not source, a certificate,
proof evidence, an L2 decision, a maturity label, or a release record.

## Qualification identity

| Field | Value |
| --- | --- |
| Route | direct target reconciliation |
| Artifact owner | `npa-mathlib` |
| Previous target | `../npa-mathlib-0.2.4` (`npa-mathlib`, version `0.2.4`) |
| Current target | `npa-mathlib` (`npa-mathlib`, version `0.2.5`) |
| Intended change | add `Mathlib.Logic.Function.Basic`; no rename, replacement, split, merge, or retirement |
| Qualification state | UIA-19 dry-run passed; registry apply is deferred to UIA-20 |
| `proof_evidence` | `false` |

The previous target is a read-only baseline copied from the clean
`npa-mathlib` package at version `0.2.4`. The reconciliation command receives
both target roots explicitly; it does not consult Git, tags, or remote history.

## Exact proposal pin

| Field | Value |
| --- | --- |
| Proposal-relative path | `Mathlib/Logic/Function/Basic.toml` |
| Proposal repository path | `npa-mathlib/interface-proposals/Mathlib/Logic/Function/Basic.toml` |
| Proposal revision | `3` |
| Proposal status | `adopted` |
| Proposal SHA-256 | `sha256:24b858109bcda41b7f7586d77edec3c2484574866668035bee83f185881f12bd` |
| `proof_evidence` | `false` |

The proposal hash covers the exact tracked UTF-8 TOML bytes, including
whitespace and the final-byte state. A proposal-byte change invalidates this
audit and requires a new revision, re-adoption, and handoff.

## Exact target surface

| Field | Value |
| --- | --- |
| Target module | `Mathlib.Logic.Function.Basic` |
| Source path | `Mathlib/Logic/Function/Basic/source.npa` |
| Certificate path | `Mathlib/Logic/Function/Basic/certificate.npcert` |
| Direct imports | `Mathlib.Logic.Eq`, `Std.Logic.Eq` |
| Declarations | public `comp`; public `comp_assoc` |
| Custom axioms | none (`axioms = []`) |
| Exact-surface gate | UIA-16 parity against proposal revision 3 is required immediately before reconciliation acceptance |

The owner certificate exports both top-level declarations. `comp` is therefore
an explicit public foundation, with the foundation exception recorded in the
proposal and handoff; it is not presented as private support. The target is
independently authored and does not copy upstream source or proof terms.

## Hash-bound target inputs

These hashes are SHA-256 file hashes unless a field is explicitly named an
export, certificate, or axiom-report identity.

| Artifact | Hash |
| --- | --- |
| `npa-package.toml` | `sha256:b33dae84a242f036987a77415fe30fc921686dd67dad5e59c46f945b0feaf705` |
| `generated/package-lock.json` | `sha256:566cc8215b2125ad80e310ed7afc332175a961eb7d7bc543bab935b08ea32f15` |
| `generated/axiom-report.json` | `sha256:389e52c5c9e7537e98f8e514a940a7cf06285b34373c56bc907008a8cf9a35d5` |
| `generated/theorem-index.json` | `sha256:47c726391c3aa16048874c403691e635c3f63d2827719dda81fedc297bc26e22` |
| `generated/verified-export-summary.json` | `sha256:ce0ef89dab342a8d78d616c1a4c5a428c6b901b6d15cee2ce6a16188592f13d0` |
| `generated/publish-plan.json` | `sha256:5fa927b7ea415af6428b594f6e5ba45c5d70a43cb8a1902c8307c36162d13b92` |
| `Mathlib/Logic/Function/Basic/source.npa` | `sha256:f291adabbdb16222185e539fac8cdb82809df5e3b2630a9496a4a908ad5b7dac` |
| `Mathlib/Logic/Function/Basic/certificate.npcert` | `sha256:c9ad4665f2fac3aa5220908896c3ea2bb9641d14f2149cb3e4355c22cc642573` |
| `Mathlib/Logic/Function/Basic/meta.json` | `sha256:e9c9bb2fd573871c0b6205c662fb5f879bb204b9cffe8d729f43de4087c9ba74` |
| `Mathlib/Logic/Function/Basic/replay.json` | `sha256:eb8701be4d8588c4f74ddb4de9d5a8639748a183cdbc0b6a1015ed6004c545a8` |
| target export identity | `sha256:6a982156a124a9750b2fd165cf2eea3694280ea66ee1242dd6a3b52ce6541f02` |
| target certificate identity | `sha256:6be8f43875855b0aa4693fc0979bdbf6dd3642867d6987e3cb2dc71b57cf2e22` |
| target axiom-report identity | `sha256:1b1a79456ee1ba2bc13de73e311a14ccd977b26904fabc748558b5350010ab1b` |

The generated reconciliation attestation is the transaction-owned record of
the complete previous/current package projections and their change-set hash.
This audit does not replace that attestation and does not authorize editing
`promotion-origins.json`.

## Required qualification gates

Before accepting the dry-run, run the owner package gates from the UIA-17
handoff with caches disabled for source-free reference verification, then run
the exact UIA-16 proposal-surface comparison using proposal hash
`sha256:24b858109bcda41b7f7586d77edec3c2484574866668035bee83f185881f12bd`.
Finally run the supported transaction:

```sh
cargo run --locked --offline -q --manifest-path npa-core/Cargo.toml -p npa-cli -- package reconcile-promotion-origin-registry --root npa-mathlib --previous-target-root ../npa-mathlib-0.2.4 --audit docs/promotion/function-comp-assoc-interface-adoption.md --out docs/promotion/function-comp-assoc-reconciliation.json --dry-run --json
```

The expected reconciliation is one `catalog_target_added` row for
`Mathlib.Logic.Function.Basic` and no lifecycle-change rows. UIA-19 ends after
the deterministic dry-run is reviewed and its transaction-owned attestation
is captured; UIA-20 applies the same explicit inputs and performs the registry
write last.

## Boundary and non-goals

- `proof_evidence` remains the literal JSON/metadata value `false`.
- This audit does not make the proposal, source, replay, metadata, generated
  projections, or reference-checker output proof authority.
- No registry, released snapshot, L2 acceptance, `reviewed` maturity, or
  release state is created by UIA-19.
- `promotion-origins.json` and generated evidence remain transaction-owned;
  they must not be hand-edited.
