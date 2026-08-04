# Direct Catalog Materialization: `Mathlib.Category.Basic`

This audit records the authorized direct catalog materialization of the adopted
`Mathlib.Category.Basic` exact interface into the mutable `npa-mathlib` catalog.
It does not authorize publication, handoff, snapshot release, or any change to
`Mathlib.Category.Functor`.

## Qualification identity

| Field | Value |
| --- | --- |
| Route | direct target reconciliation |
| Artifact owner | `npa-mathlib` |
| Previous target | clean `npa-mathlib`, version `0.2.5` |
| Current target | `npa-mathlib`, version `0.2.6` |
| Intended change | add `Mathlib.Category.Basic`; no rename, replacement, split, merge, or retirement |
| Interface proposal | `Mathlib.Category.Basic`, adopted revision `5` |
| Proposal SHA-256 | `sha256:a96149b22c40c8905b342396821c22f66cb753a722f26aab7501cbacb5aa9498` |
| `proof_evidence` | `false` |

The pinned `npa-corpus` and `npa-project-iut` sources are artifact-identical
for `Proofs.Ai.Category.Basic`; the target source is the namespace-rewritten
`Mathlib.Category.Basic` implementation. The target is admitted as a direct
catalog target so the registry transaction records the exact target identity
without asserting source artifact identity across the namespace rewrite.

## Exact target surface

| Field | Value |
| --- | --- |
| Target module | `Mathlib.Category.Basic` |
| Source path | `Mathlib/Category/Basic/source.npa` |
| Certificate path | `Mathlib/Category/Basic/certificate.npcert` |
| Direct imports | `Mathlib.Logic.EqReasoning`, `Std.Logic.Eq` |
| Declarations | public `CategoryLawArgs`, `category_definition_intro`, `category_comp_assoc_law`, `category_comp_id`, `category_id_comp`, `category_comp_assoc`, `opposite_category_laws` |
| Custom axioms | none (`axioms = []`; package policy permits `Eq.rec`) |
| Exact-surface gate | parity against adopted Basic revision 5 |

## Hash-bound target inputs

| Artifact | Hash |
| --- | --- |
| `npa-package.toml` | `sha256:fe10efd8746ea0f4c99197fa39248a133870bbd58d8778fdcc40443418cba798` |
| `generated/package-lock.json` | `sha256:ecbabbd06837f9535b4c5b72297dd608eca3818d392e6783cd600c68f7f2c4dd` |
| `generated/axiom-report.json` | `sha256:89caedd6596c11e8fefb285b6bcb91673fb7694c559c13a0ebc100d46c6446ad` |
| `generated/theorem-index.json` | `sha256:794cd1bdc7767b865fb416b4902c48b8e05d6893df22d8681cd6a3dc5bc89152` |
| `generated/theorem-premise-report.json` | `sha256:7562c60236079d53821ab586df308454a9207dbed4aa700a3253ff1fbbec5d29` |
| `generated/verified-export-summary.json` | `sha256:f7df4c8dc0e6b43f94999f119d00f4a59ebaa32ed4af59aa584625276a611c92` |
| `generated/publish-plan.json` | `sha256:fca12ec468e86a5e9adad7d82620c478b495d258eed72e2a8ebba7d4f2493f20` |
| `Mathlib/Category/Basic/source.npa` | `sha256:3c753fbca599bb572b98a62fc158ff1a7340e9bec6804067510ca144cab44bf9` |
| `Mathlib/Category/Basic/certificate.npcert` | `sha256:75effd5c2e7abb96a7ca8c0ae02bc0c33eb5a94d56624dc62ad1dbee5b5fe8eb` |
| `Mathlib/Category/Basic/meta.json` | `sha256:ea3f0fc85d61c7e59715aa098ff0b39518f4178a251d8b1edde82b932195924f` |
| `Mathlib/Category/Basic/replay.json` | `sha256:63d12062273364dee076be2618b6d94d05c811df1aabdd5944d959adbd0a51f0` |
| target export identity | `sha256:dc3c33b93717abdfffe78d599dc53eb852ceb84e8f413b5845213331b7b9c736` |
| target certificate identity | `sha256:6f2dd1d4b3b97af7bac3a8b28ccae6ff621163562a430d3611b176d4b5731c28` |
| target axiom-report identity | `sha256:ca8c17e4fd5704de9fcbf752e350162e7c79a827f7c1184a68b8bc8a4aea25be` |

## Qualification gates

The prepared target passed package checks, checked hashes, full certificate
build, cache-off source-free reference verification, all generated projections,
and exact proposal-surface parity. The registry reconciliation dry-run and
temporary apply both accepted one `catalog_target_added` row for
`Mathlib.Category.Basic`, with no revised or lifecycle-change rows.

This audit is governance metadata with `proof_evidence: false`; it does not
establish `reviewed` or `recommended` maturity and does not authorize release
publication.
