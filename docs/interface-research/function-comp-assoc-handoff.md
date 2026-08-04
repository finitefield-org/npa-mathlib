# UIA-14 Implementation Handoff: `function-comp-assoc`

Status: completed through UIA-20 after the UIA-14 re-adoption revisions and
the repeated UIA-17 artifact-owner and catalog-route decision. The `ready`
handoff state below is retained as the historical implementation entry point.
This document is a curated,
non-proof traceability sidecar. It is not an NPA source module, certificate,
proof, verification result, catalog admission, L2 review, maturity label, or
release record.

## 1. Exact proposal pin

| Field | Value |
| --- | --- |
| Handoff slug | `function-comp-assoc` |
| Handoff status | Historical `ready` handoff; UIA-18 through UIA-20 completed and the target-owned registry entry recorded |
| Proposal-relative path | `Mathlib/Logic/Function/Basic.toml` |
| Proposal repository path | `npa-mathlib/interface-proposals/Mathlib/Logic/Function/Basic.toml` |
| Proposal ID | `Mathlib.Logic.Function.Basic` |
| Proposal revision | `3` |
| Proposal SHA-256 | `sha256:24b858109bcda41b7f7586d77edec3c2484574866668035bee83f185881f12bd` |
| Proposal Git tracking | The canonical proposal is tracked in Git; verify with `git ls-files --error-unmatch npa-mathlib/interface-proposals/Mathlib/Logic/Function/Basic.toml`. |
| `proof_evidence` | `false` |

The hash is SHA-256 over the exact tracked UTF-8 TOML bytes, including their
current whitespace and final-byte state. Recompute from the repository root:

```sh
shasum -a 256 npa-mathlib/interface-proposals/Mathlib/Logic/Function/Basic.toml
```

The expected output is:

```text
24b858109bcda41b7f7586d77edec3c2484574866668035bee83f185881f12bd  npa-mathlib/interface-proposals/Mathlib/Logic/Function/Basic.toml
```

Any change to the proposal bytes invalidates this handoff: this includes a
whitespace-only edit, TOML reformatting, a changed declaration/signature/body,
an import change, a status change, or a path move. The implementation owner
must stop and obtain a new proposal revision and handoff hash; updating this
document alone cannot authorize drift.

The revision-3 UIA-14 re-adoption and the repeated UIA-17 route-selection
recheck (2026-08-02) reran the required tracking and byte-hash checks after
the import-boundary and exported-surface revisions, owner, and route were
selected:

```sh
git ls-files --error-unmatch npa-mathlib/interface-proposals/Mathlib/Logic/Function/Basic.toml
shasum -a 256 npa-mathlib/interface-proposals/Mathlib/Logic/Function/Basic.toml
```

The tracked path remained unchanged and the second command returned
`24b858109bcda41b7f7586d77edec3c2484574866668035bee83f185881f12bd`. Revision
3 records `previous_proposal_hash =
sha256:aad7f6c6dd9802ee7c832b5f2274d8e4041be31cfa469fbb1dd9c172ed30f9d1`, so
the revised proposal remains hash-chained to revision 2.

## 2. Ownership and immutable evidence

The same upstream repository appears in two roles, but the roles have
different meanings and neither is the eventual artifact owner.

| Role | Exact identity | Boundary |
| --- | --- | --- |
| Interface evidence owner | `https://github.com/leanprover-community/mathlib4` at `c5ea00351c28e24afc9f0f84379aa41082b1188f`, license `Apache-2.0`; declaration path `Mathlib/Logic/Function/Defs.lean` and use-site path `Mathlib/Logic/Function/Iterate.lean` | Authority for the observed declaration, module layout, import, and downstream rewrite rows only. |
| Proof-reference owner | `https://github.com/leanprover-community/mathlib4` at `c5ea00351c28e24afc9f0f84379aa41082b1188f`, license `Apache-2.0`; reference path `Mathlib/Logic/Function/Defs.lean`, declaration `Function.comp_assoc` | Authority only for the pinned `proof_structure` reference. It may guide independent proof design; it grants no permission to copy proof source or code. |
| Artifact owner | `npa-mathlib` mutable public catalog, package root `npa-mathlib/` in the aggregate checkout | Owns the independently authored target source, canonical certificate, metadata, replay, manifest entry, and generated package projections. The upstream evidence/proof-reference repository and `npa-corpus` are not artifact origins for this route. |

The upstream repository is not an NPA artifact origin, and the external
revision does not establish target verification, maturity, or release status.

## 3. Target and complete adopted surface

| Field | Value |
| --- | --- |
| Target module | `Mathlib.Logic.Function.Basic` |
| Target source path | `Mathlib/Logic/Function/Basic/source.npa` under the selected `npa-mathlib` package |
| Change kind | `add` |
| Direct imports | `Mathlib.Logic.Eq`, `Std.Logic.Eq` |
| Source modules | `[]` |
| Permitted custom axioms | `none` |
| Inductive families | `none` |

The adopted surface contains exactly the following two declarations, each
listed once. The owner certificate exports both top-level declarations, so
`comp` is an explicit public foundation and `comp_assoc` is the public theorem
that motivated the pilot.

| # | Name | Kind | Surface | Same-module dependencies | Evidence/proof-reference IDs |
| ---: | --- | --- | --- | --- | --- |
| 1 | `comp` | `definition` | `public` | `[]` | `[]` plus foundation exception |
| 2 | `comp_assoc` | `theorem` | `public` | `["comp"]` | `mathlib-defs-declaration`, `mathlib-iterate-rewrite`, `mathlib-comp-assoc-proof-structure` |

### Exact adopted terms

#### 1. `comp`

```text
kind = definition
surface = public
signature = forall (alpha : Sort u1), forall (beta : Sort u2), forall (gamma : Sort u3), forall (f : forall (x : beta), gamma), forall (g : forall (x : alpha), beta), forall (x : alpha), gamma
body = fun alpha => fun beta => fun gamma => fun f => fun g => fun x => f (g x)
family_members = none
```

This exact body is an adopted interface term because downstream reduction may
depend on it. It may not be changed for proof convenience.
The declaration is public under revision 3 and uses a foundation exception:
the owner package exports this top-level definition, so the handoff does not
claim a private support marker that the certificate cannot represent.

#### 2. `comp_assoc`

```text
kind = theorem
surface = public
signature = forall (alpha : Sort u1), forall (beta : Sort u2), forall (phi : Sort u3), forall (delta : Sort u4), forall (f : forall (x : phi), delta), forall (g : forall (x : beta), phi), forall (h : forall (x : alpha), beta), @Eq.{imax u1 u4} (forall (x : alpha), delta) (@comp.{u1,u2,u4} alpha beta delta (@comp.{u2,u3,u4} beta phi delta f g) h) (@comp.{u1,u3,u4} alpha phi delta f (@comp.{u1,u2,u3} alpha beta phi g h))
body = theorem proof term independently authored; proof terms may change while this exported signature remains exact
family_members = none
```

The theorem proof term is deliberately not fixed by this handoff. It must be
authored independently and may be rewritten without changing the adopted
interface. The signature, theorem name, argument order, result type, support
reference, and module/import boundary are fixed. Revision 2 makes the
`Std.Logic.Eq` equality provider explicit because `Mathlib.Logic.Eq` does not
re-export that declaration for source-free package indexing. Revision 3 makes
`comp` public because the owner certificate exports every top-level declaration;
the proposal records that foundation exception explicitly rather than claiming
that the certificate contains a private support marker.

## 4. Proof references and exclusions

### Pinned proof reference

| ID | Repository | Revision | License | Path | Source module/declaration | Reference role | Permitted use |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `mathlib-comp-assoc-proof-structure` | `https://github.com/leanprover-community/mathlib4` | `c5ea00351c28e24afc9f0f84379aa41082b1188f` | `Apache-2.0` | `Mathlib/Logic/Function/Defs.lean` | `Mathlib.Logic.Function.Defs` / `Function.comp_assoc` | `proof_structure` | Consult the independently reproducible reflexivity shape only; copy no external source, proof term, or code. |

Use-site observations remain separate from this reference. The relevant
observation IDs are `mathlib-defs-layout`, `mathlib-defs-declaration`,
`mathlib-defs-import-init`, `mathlib-iterate-import`, and
`mathlib-iterate-rewrite`; none is proof authority.

### Explicit exclusions

- All upstream `Mathlib.Logic.Function.Defs` declarations other than the
  observed `Function.comp_assoc` mapping and the locally authored `comp`
  foundation closure, including `dcomp`, `prod`, `onFun`, `Bijective`, and
  other unselected constructions or theorem families.
- The upstream `Function.comp` implementation and all external source or proof
  blocks. The NPA `comp` body must be independently authored.
- The upstream `Mathlib.Tactic.Attr.Register` import and any undeclared tactic,
  automation, instance, inference, or remote dependency.
- Any bundled-function, pointwise-equality, quotient, or quotient-backed
  alternative; no quotient construction is part of this target.
- Package manifests, certificates, replay/meta projections, generated indexes,
  promotion-origin registry entries, catalog versions, L2 decisions, and
  release publication. This handoff records the route for those later stages;
  UIA-17 does not claim that any of them has happened.
- Any public convenience declaration added merely to make the proof easier.

## 5. UIA-17 artifact owner and catalog route decision

UIA-17 selected the direct catalog-owner route after comparing the exact
adopted target with the current package capabilities. The selected owner is
`npa-mathlib`, and the selected route is direct target reconciliation. This is
an implementation route decision, not a certificate, verification, catalog
admission, maturity, L2, or release decision.

| Field | Selected value | Boundary |
| --- | --- | --- |
| Artifact owner | `npa-mathlib`, mutable public catalog, package root `npa-mathlib/` | Owns the independently authored `Mathlib.*` source and canonical certificate closure for this pilot. |
| Catalog route | Direct target reconciliation for materialized package version `0.2.5`, using the clean read-only `npa-mathlib` `0.2.4` package at `../npa-mathlib-0.2.4` as `--previous-target-root` | UIA-19 dry-run and UIA-20 apply completed; reconciliation was dry-run first and registry-last on apply. |

### Owner selection evidence

- `npa-mathlib` already owns the public `Mathlib.*` namespace and stores each
  existing catalog module as a complete `source.npa`, `certificate.npcert`,
  `meta.json`, and `replay.json` closure. The selected target therefore has a
  sustainable semantic home in the public catalog rather than in a staging
  namespace.
- The adopted direct imports `Mathlib.Logic.Eq` and `Std.Logic.Eq` are present
  in `npa-mathlib/npa-package.toml`, and both certificate closures are part of
  the catalog package. No new external package import is needed for this
  pilot.
- The owner has the required proof tooling through the sibling
  `npa-core` package CLI and the same source-free reference, hash, import,
  axiom, generated-artifact, and registry gates used by the current catalog.
- `npa-corpus` was considered but not selected. Its checked-in authoring
  package is explicitly the `Proofs.Ai.*` staging corpus and its normal build
  path would require a source-module owner plus a declaration-selection
  mapping into the public `Mathlib.*` target. That route is supported for a
  separately owned source artifact, but it adds no semantic value for this
  small public foundation when the target can be authored directly in its
  namespace. It remains neither the artifact owner nor an artifact origin.
- The pinned `mathlib4` declaration and use-site repositories remain
  interface-evidence and proof-reference owners only. Their observations do
  not supply source, certificate, or promotion provenance for the
  independently authored NPA target.

### Exact owner paths and target mapping

The following paths are relative to the aggregate checkout root unless stated
otherwise:

| Purpose | Exact path or mapping |
| --- | --- |
| Owner package manifest | `npa-mathlib/npa-package.toml` |
| Independently authored source | `npa-mathlib/Mathlib/Logic/Function/Basic/source.npa` |
| Canonical certificate | `npa-mathlib/Mathlib/Logic/Function/Basic/certificate.npcert` |
| Non-trusted build sidecars | `npa-mathlib/Mathlib/Logic/Function/Basic/meta.json`; `npa-mathlib/Mathlib/Logic/Function/Basic/replay.json` |
| Generated package projections | `npa-mathlib/generated/package-lock.json`; `npa-mathlib/generated/axiom-report.json`; `npa-mathlib/generated/theorem-index.json`; `npa-mathlib/generated/theorem-premise-report.json`; `npa-mathlib/generated/verified-export-summary.json`; `npa-mathlib/generated/publish-plan.json` |
| Owner module | `Mathlib.Logic.Function.Basic` with the exact direct imports `Mathlib.Logic.Eq` and `Std.Logic.Eq`; no source-module or declaration-name mapping |
| Admission unit | Complete target module closure: public `comp`, public `comp_assoc`, and the transitive certificate import closures of `Mathlib.Logic.Eq` and `Std.Logic.Eq` |
| Previous target | Clean, read-only package root `../npa-mathlib-0.2.4`, whose manifest must say `package = "npa-mathlib"` and `version = "0.2.4"` |
| Reconciliation audit | `npa-mathlib/docs/promotion/function-comp-assoc-interface-adoption.md`, target-relative path `docs/promotion/function-comp-assoc-interface-adoption.md` |
| Reconciliation attestation | `npa-mathlib/docs/promotion/function-comp-assoc-reconciliation.json`, target-relative path `docs/promotion/function-comp-assoc-reconciliation.json` |

The target package materialized by UIA-18 through UIA-20 sets
`version = "0.2.5"`. If the owner, target module, package versions, or
mapping changes in a later adoption, stop and repeat the UIA-17 decision
instead of silently switching routes.

### Owner build and source-free verification commands

Run these from the aggregate checkout root. The first command is the supported
source-to-certificate build; the remaining commands are package and
source-free gates. They must use the exact target paths above and must not
introduce custom axioms or an unchecked cache result.

```sh
cargo run -q --manifest-path npa-core/Cargo.toml -p npa-cli -- package build-certs --root npa-mathlib --json
cargo run -q --manifest-path npa-core/Cargo.toml -p npa-cli -- package check --root npa-mathlib --json
cargo run -q --manifest-path npa-core/Cargo.toml -p npa-cli -- package verify-certs --root npa-mathlib --checker reference --package-lock checked --audit-cache off --verifier-memo off --json
cargo run -q --manifest-path npa-core/Cargo.toml -p npa-cli -- package audit-artifact-ledger --root npa-mathlib --module Mathlib.Logic.Function.Basic --json
cargo run -q --manifest-path npa-core/Cargo.toml -p npa-cli -- package check-hashes --root npa-mathlib --json
cargo run -q --manifest-path npa-core/Cargo.toml -p npa-cli -- package axiom-report --root npa-mathlib --check --json
cargo run -q --manifest-path npa-core/Cargo.toml -p npa-cli -- package index --root npa-mathlib --check --json
cargo run -q --manifest-path npa-core/Cargo.toml -p npa-cli -- package theorem-premise-report --root npa-mathlib --check --json
cargo run -q --manifest-path npa-core/Cargo.toml -p npa-cli -- package export-summary --root npa-mathlib --check --json
cargo run -q --manifest-path npa-core/Cargo.toml -p npa-cli -- package publish-plan --root npa-mathlib --check --json
cargo run -q --manifest-path npa-core/Cargo.toml -p npa-cli -- package check-generated --root npa-mathlib --timings summary --json
cargo run -q --manifest-path npa-core/Cargo.toml -p npa-cli -- package validate-promotion-origin-registry --root npa-mathlib --json
```

The package policy in `npa-mathlib/npa-package.toml` has
`allow_custom_axioms = false` and permits only the existing built-in
`Eq.rec` axiom interface. This pilot must declare `axioms = []`; it may not
add a custom axiom or treat source, replay, metadata, generated JSON, or a
reference-checker result as proof authority.

### Target-surface and reconciliation commands

UIA-18 built the owner artifact; before the UIA-20 registry apply, UIA-19 ran
the read-only exact-surface gate from UIA-16:

```sh
cargo run -q --manifest-path npa-core/Cargo.toml -p npa-cli -- package check-interface-proposal-surface --root npa-mathlib --proposal-root interface-proposals --proposal-path Mathlib/Logic/Function/Basic.toml --proposal-sha256 sha256:24b858109bcda41b7f7586d77edec3c2484574866668035bee83f185881f12bd --target-module Mathlib.Logic.Function.Basic --json
```

UIA-19 created the audit sidecar at the exact target-relative path above,
including `proof_evidence = false`, the proposal-relative path, and the same
proposal hash. With a clean read-only `../npa-mathlib-0.2.4` baseline and the
materialized `npa-mathlib` `0.2.5` target, UIA-19 ran reconciliation in this
order:

```sh
cargo run -q --manifest-path npa-core/Cargo.toml -p npa-cli -- package reconcile-promotion-origin-registry --root npa-mathlib --previous-target-root ../npa-mathlib-0.2.4 --audit docs/promotion/function-comp-assoc-interface-adoption.md --out docs/promotion/function-comp-assoc-reconciliation.json --dry-run --json
cargo run -q --manifest-path npa-core/Cargo.toml -p npa-cli -- package reconcile-promotion-origin-registry --root npa-mathlib --previous-target-root ../npa-mathlib-0.2.4 --audit docs/promotion/function-comp-assoc-interface-adoption.md --out docs/promotion/function-comp-assoc-reconciliation.json --apply --json
```

Review the deterministic dry-run before the second command. No hand edit of
`npa-mathlib/promotion-origins.json`, generated projections, or a released
snapshot is permitted. The ordinary `add` change does not need a lifecycle
request; a later rename, split, merge, replacement, or retirement must use
the corresponding canonical reconciliation request.

Both fields were filled by UIA-17, and UIA-18 through UIA-20 implemented
exactly this owner and route. This sidecar remains no substitute for
certificate-first verification, target gates, L2 review, or release policy.

## 6. Post-implementation result

The owner artifact and catalog transaction completed with the following
hash-bound records:

| Field | Recorded result |
| --- | --- |
| Reconciliation audit | `docs/promotion/function-comp-assoc-interface-adoption.md`, exact hash `sha256:b23bb76a689de33f9e25df9021195f3256c282843c372c520bce315381802984` |
| Reconciliation attestation | `docs/promotion/function-comp-assoc-reconciliation.json`, file hash `sha256:511206ce753861b724e110da8355e5f46161f791d9f318ae7c0dd454521a96f9`, attestation identity `sha256:8a21dc0c00c953e488b12e1bd6a84a68733c78f1d19c0fc44135b8998c8724f4` |
| Change set | Previous `0.2.4` to target `0.2.5`; `unchanged_count = 47`, `revised_count = 0`, `added_count = 1`, `lifecycle_change_count = 0` |
| Registry | `promotion-origins.json` generation `3`; one target-owned `catalog_target_v1` entry for `Mathlib.Logic.Function.Basic` |
| Post-apply gates | Package, certificate, source-free, hash, generated-artifact, exact-surface, artifact-ledger, and registry validation gates passed |
| Explicit non-claims | No `reviewed`/L2 maturity, released snapshot, publication, or proof authority was created by the proposal, audit, attestation, or registry metadata |

The UIA-19 audit remains the qualification snapshot bound into the UIA-20
transaction. Its pre-apply wording is historical and is intentionally not
rewritten after apply; the post-apply result is recorded by the attestation
and registry event above.

## 7. Review and acceptance record

- [x] The canonical proposal path exists under
      `npa-mathlib/interface-proposals/`.
- [x] The proposal is tracked in Git; the recorded SHA-256 was computed from
      the exact file bytes.
- [x] The recorded hash has the required `sha256:` prefix and 64 lowercase
      hexadecimal characters.
- [x] The proposal is revision 3, `adopted`, and `proof_evidence = false`.
- [x] Revision 3 records the previous proposal hash and the re-adoption
      rationale for the explicit equality import and exported public `comp`
      foundation boundary.
- [x] `comp` and `comp_assoc` each appear exactly once in the adopted
      declaration inventory.
- [x] The target module, complete signatures, foundation body, import list,
      empty family inventory, allowed-axiom boundary, proof reference, and
      exclusions are explicit.
- [x] Evidence owner, proof-reference owner, and eventual artifact owner are
      distinguishable.
- [x] The handoff says exactly when any proposal-byte change invalidates it.
- [x] The handoff states that theorem proof terms may change while exported
      interface terms may not.
- [x] UIA-17 selected exactly one artifact owner: `npa-mathlib`.
- [x] UIA-17 selected exactly one supported route: direct target
      reconciliation for `0.2.5` against the explicit `0.2.4` previous target.
- [x] The owner has the adopted import closure, proof tooling, package gates,
      and sustainable `Mathlib.*` semantic ownership recorded above.
- [x] Exact owner paths, target mapping, build commands, source-free commands,
      permitted axioms, and dry-run/apply commands are recorded.
- [x] The proposal tracking and exact SHA-256 were recomputed after
      re-adoption and the repeated route selection.
- [x] UIA-18 produced the independently authored source, certificate,
      metadata, replay, manifest, and generated package projections.
- [x] UIA-19 recorded the qualification audit, ran the deterministic dry-run,
      and confirmed exact proposal-surface parity.
- [x] UIA-20 wrote the reconciliation attestation and registry event last and
      passed the post-apply registry and artifact-ledger gates.

The checked items and post-implementation result establish the documentation,
artifact, and target-owned registry transition recorded above. They do not
make proposal or registry metadata proof authority, do not establish L2
`reviewed` or `recommended`, and do not create a released snapshot or release
publication.
