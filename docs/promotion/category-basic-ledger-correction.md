# `Mathlib.Category.Basic` Ledger Correction

After the initial catalog reconciliation, the target-scoped artifact-ledger
audit identified that the manifest row omitted the permitted transitive
`Eq.rec` axiom recorded by the generated metadata. The manifest row now uses
`axioms = ["Eq.rec"]`, matching the pinned source package and the generated
`meta.json` ledger.

The correction changes only the package-wide manifest and generated projection
hashes. It does not change `source.npa`, `certificate.npcert`, `meta.json`,
`replay.json`, the exact interface, theorem statements, proof content, or the
catalog target revision identity. The original materialization audit remains
byte-stable because the registry event is hash-bound to that audit file.

| Artifact | Before correction | Current |
| --- | --- | --- |
| `npa-package.toml` | `sha256:fe10efd8746ea0f4c99197fa39248a133870bbd58d8778fdcc40443418cba798` | `sha256:7e6a962d6b9978856c3ea42e7acefafc3fcb7a85225ea1a8c7af6b05630e26b6` |
| `generated/package-lock.json` | `sha256:ecbabbd06837f9535b4c5b72297dd608eca3818d392e6783cd600c68f7f2c4dd` | `sha256:1cfece33785210e2959fcb5880439c1e57225ca7578eb0a4f521f8bdd02a27c2` |
| `generated/axiom-report.json` | `sha256:89caedd6596c11e8fefb285b6bcb91673fb7694c559c13a0ebc100d46c6446ad` | `sha256:77c41f2690fef82fd823cdecbceac03d8de9b5353e22846e3b9037c5af02895c` |
| `generated/theorem-index.json` | `sha256:794cd1bdc7767b865fb416b4902c48b8e05d6893df22d8681cd6a3dc5bc89152` | `sha256:2868a63fda69eef91bfd65c0924e26800f26901e904f4080eb4cb5105f159515` |
| `generated/theorem-premise-report.json` | `sha256:7562c60236079d53821ab586df308454a9207dbed4aa700a3253ff1fbbec5d29` | `sha256:9bbfbfe81750ecd2f2b0c19bdb0412c88c9c8bae737f5bf3e0871e68f7426468` |
| `generated/verified-export-summary.json` | `sha256:f7df4c8dc0e6b43f94999f119d00f4a59ebaa32ed4af59aa584625276a611c92` | `sha256:11b74dd479e8acf92a907eb749a7def52a5993924df4a91c4249809f3ebbd030` |
| `generated/publish-plan.json` | `sha256:fca12ec468e86a5e9adad7d82620c478b495d258eed72e2a8ebba7d4f2493f20` | `sha256:f8086dd94a8e166c1ff4463e81b0c791aea9d573be09e4e5e3dda106b0c5d0a8` |

The target-scoped ledger audit, package checks, cache-off reference
verification, generated-artifact checks, and registry validator pass after
the correction. No publication, handoff, or snapshot release is implied.
