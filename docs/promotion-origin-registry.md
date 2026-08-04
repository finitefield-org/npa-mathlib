# Promotion-Origin Registry

`promotion-origins.json` is the canonical, tracked provenance registry for
catalog ingestion and historical promotions. It records exact source and target
artifact identities, namespace routes, declaration closures, lifecycle,
replacement/retirement history, and hash-bound governance evidence. It is
policy metadata with `proof_evidence: false`; it never replaces certificate
checking.

Every active catalog module is owned by exactly one sourced entry,
`catalog_target_v1` entry, or unresolved legacy reservation. A retired route
remains reserved so removal from mutable HEAD cannot erase provenance or
silently repurpose an identifier. The initial generation reserves all modules
present in version 0.2.1 without inventing historical source provenance; see
`docs/promotion/legacy-origin-registry-bootstrap.md`.

Before qualification or release, run:

```sh
npa package validate-promotion-origin-registry --root npa-mathlib --json
```

Supply repeatable `--source-root` values when the corresponding source package
is available, and `--previous-registry` when reviewing a registry transition.
Complete-module namespace-only reviewed ingestions use promotion plan v1.
Selected declaration ingestions use declaration request v1, promotion plan v2,
and a verified materialization attestation. Both are applied with `package
materialize-promotion --phase tracked`; the transaction writes the registry
entry last. `package register-equivalent-promotion-origin` may append a fully
artifact-identical source package to either supported route.

The registry preserves append-only history. Existing canonical origins, module
routes, target revisions, evidence, declaration closures, legacy reservations,
and catalog-change events cannot be edited or deleted. New target revisions may
be appended through catalog-change events without altering v1/v2 entry or
reservation arrays. Rename, replacement, split, merge, and retirement relations
may be appended as explicit lifecycle events; old identifiers and released
identities remain reserved.

Routine direct catalog maintenance uses the versioned
`package reconcile-promotion-origin-registry` transaction. It may compare any
valid older registry/catalog snapshot with any strictly newer validated target;
versions need not be consecutive. Additions and in-place revisions require no
separate request. Rename, replacement, split, merge, and retirement require a
canonical hash-bound request. The command owns the attestation, registry
generation, locking, recovery journal, and registry-last write.

New direct-maintenance modules receive meaning-first `catalog_target_v1`
ownership. Do not create new unresolved legacy reservations; those remain only
for historical target identities with unknown original source provenance.

In registry v3, a target revision version means the first registry-observed
snapshot binding that exact artifact identity. It does not reconstruct an
unknown introduction release when synchronization skips versions. An unchanged
module may remain active in any later package version without a duplicate
revision; catalog-change events bind its continued presence. Validation still
compares all artifact and theorem hashes exactly.

The implemented contract is specified in
`npa-core/docs/promotion-origin-registry-reconciliation.md`. Use dry-run first,
then apply the same explicit older target, audit, optional lifecycle request,
and output path. Do not emulate the transaction by hand-editing the registry.
Exact-target L2 promotion remains tooling-blocked until a separate evidence
variant and apply contract exists.

## Registry V2

`npa.mathlib.promotion_origin_registry.v2` has the same registry ID, target
package, monotonic generation, legacy-reservation array, domain-separated
self-hash, and `proof_evidence: false` boundary as v1. It distinguishes:

- `whole_module_v1`, which losslessly wraps one historical or newly produced
  plan-v1 entry; and
- `declaration_closure_v1`, which records one selected source declaration
  closure and its new target module.

A declaration entry binds its roots, root/support closure rows, generated
families, externalized dependency mappings, exact source and target artifact
identities, plan and attestation files, normalized closure hash, and catalog
and namespace policy hashes. The current schema accepts exactly one immutable
target revision, whose version is the introduced version; a future revision
requires a separately versioned transaction. Admission evidence establishes
only `verified` maturity. The current schema validator requires its
`maturity_events` array to remain empty. A future versioned exact-target L2
workflow must define and append its own target-bound event before any theorem
is called `reviewed`.

The first post-feature tracked write against registry v1 migrates and appends
atomically. Every v1 entry is preserved byte-for-byte inside a
`whole_module_v1` wrapper, every unresolved legacy reservation remains at the
top level unchanged, the generation increases once, and the result is v2.
Parsers continue to accept historical v1 baselines, but no post-feature
transaction writes or downgrades to v1. The proposed reconciliation transaction
migrates v1 or v2 to v3 and permits repeated v3-to-v3 catalog-change events
without downgrading prior history.

V2 preserves one active owner per target module and rejects declaration source
collisions, including a selected support declaration already owned by another
active route. Retired identifiers remain reserved. Equivalent-origin append
requires exact source, certificate, export, selected-closure, and dependency-
edge identities; matching names alone are insufficient.

Verified-only declaration ingestion is therefore supported. Direct target
revision and lifecycle events use registry v3 reconciliation.
Target-L2 mutations remain `tooling-blocked`; do not hand-edit or bypass the
registry.
