# Mutable Catalog And Immutable Snapshot Policy

Status: active

Date: 2026-07-28

## Purpose

`npa-mathlib` is a mutable public catalog of verified NPA certificate
artifacts. Its current branch is an authoring and distribution head, not a
stable dependency API. Modules and declarations may be added, removed,
renamed, reorganized, or replaced when doing so improves the catalog.

That flexibility does not extend to published snapshots. Every released
version, tag, certificate bundle, checksum, and recorded artifact identity is
immutable. A downstream proof package obtains stability by vendoring the exact
certificate import closure it uses and pinning its package version, export
hashes, and certificate hashes. It must not depend on the latest
`npa-mathlib` branch state.

In short:

```text
mutable catalog HEAD
        |
        v
immutable released snapshot
        |
        v
hash-pinned vendored certificate closure in each consumer
```

## Trust And Curation Are Separate

Catalog admission and mathematical recommendation are different decisions.
Proof authority remains:

- canonical `.npcert` bytes;
- the Rust kernel and verifier verdict;
- cache-off source-free reference-checker verification;
- deterministic statement, export, certificate, and axiom-report hashes.

Source, replay, metadata, generated indexes, reviews, maturity labels,
promotion records, Git history, and release pages remain sidecars with
`proof_evidence: false` where the format provides that field.

The catalog uses these maturity meanings:

- `verified`: the exact artifact closure passes certificate, hash,
  source-free verification, import, and axiom gates. This says only that the
  exact declarations are certified; it is not a claim of broad usefulness,
  semantic importance, or API stability.
- `reviewed`: every theorem for which this label is claimed has current
  theorem-level, hash-bound `L2 Derived certificate` acceptance under the
  canonical L2 policy.
- `recommended`: a reviewed surface has additionally received explicit
  curation for mathematical meaning, naming, dependency cost, and likely
  reuse. Until a versioned recommendation record exists, do not claim this
  label as machine-validated metadata.

`reviewed` and `recommended` are quality labels, not prerequisites for
`verified` catalog admission. L2 review may happen before or after admission.
The canonical L2 ledger remains the only authority for the `reviewed` label.

## Catalog Admission

The admission unit is either a complete module closure or a selected
declaration closure materialized as a new public module. A selected declaration
closure contains every requested root, every same-module support definition or
theorem needed by a root, and every certificate-validated member of a reached
inductive or source declaration family. Support and generated family members
are part of the admission even when the request did not name them directly.

A declaration subset always creates a new `Mathlib.*` module artifact and a
strictly newer package version. It does not delete declarations in place from
an existing catalog certificate and does not reuse the source module identity.
The rebuilt target certificate, cache-off source-free verification, normalized
source/target closure comparison, package gates, and hash-bound verified
materialization attestation establish `verified` maturity for that exact new
artifact.

A new or changed catalog closure may be admitted at `verified` maturity when:

- every selected canonical certificate exists and source-free reference
  verification succeeds with caches disabled;
- statement, certificate-file, certificate, export, and axiom-report hashes
  are deterministic and current;
- direct and transitive imports are explicit and available from the same
  snapshot or immutable hash-pinned external artifacts;
- the closure satisfies the current axiom policy and introduces no custom
  axiom, unresolved conjecture, or `sorry`-equivalent placeholder;
- names and module placement accurately describe the current mathematical
  content, even though they are not promised to remain stable;
- the canonical origin registry records the source identity for source-backed
  content, a target-owner identity for direct catalog content, the exact target
  identity, route, lifecycle, and replacement or retirement relationship;
- the package and applicable downstream smoke gates pass.

Definitions, interfaces, and proved theorems may enter the catalog under this
contract. A verifier success must not be presented as L2 review, mathematical
endorsement, or evidence that a boundary/status theorem proves the external
claim it discusses.

Source L2 acceptance retained by a declaration promotion is historical
provenance only. Deleting declarations, splitting a source module, or otherwise
changing its selected surface prevents namespace-only L2 transport from
establishing target `reviewed` maturity. Each promoted target theorem requires
fresh theorem-level, hash-bound target L2 acceptance before it is labeled
`reviewed`.

API stability, independent downstream reuse, and a unanimous L2 review are not
admission gates. They are curation signals used for the higher maturity labels.

## Changes, Removal, And Provenance

The current catalog may break compatibility at any version. A change that
alters a public module name, declaration name, type, body, import closure, or
certificate creates a new artifact identity and requires a package-version
update plus refreshed generated projections.

Removal means removal from current HEAD and later snapshots. It must not erase
or rewrite an older release. Retire the corresponding origin route, retain its
historical hashes and replacement relation, and do not silently reuse a
retired identifier for unrelated mathematics. A replacement may use a new
name or an explicitly recorded revision route.

Repository history and published tags must not be rewritten to make an old
snapshot look like the new catalog. Existing release tags and assets must
never be overwritten.

## Downstream Consumption

Consumers should vendor the smallest exact certificate import closure they
need. A consumer pin must record:

- the source `npa-mathlib` snapshot version and, when available, tag or commit;
- the imported module name;
- vendored canonical certificate bytes;
- expected export and certificate hashes;
- the complete transitive package-import closure required for source-free
  verification.

The pinning unit is still a complete module certificate and its transitive
certificate imports. A consumer cannot pin or verify an individual declaration
inside a certificate, even when that certificate was produced from a selected
declaration closure.

Authoring source, replay, meta files, the catalog checkout, and Git branch
state are not proof dependencies. Updating a pin is an explicit migration:
compare the old and new declarations and hashes, update vendored bytes and
manifest pins, rebuild the affected local closure, and rerun package gates.

If a module no longer exists on catalog HEAD, check out the immutable release
that contains it and pin from that snapshot. Do not resolve a floating
"latest" version.

## Promotion Tooling Boundary

Complete-module reviewed namespace transport remains plan v1. Selected
declaration promotion uses canonical declaration request v1, promotion plan v2,
verified materialization attestation v1, and a sourced declaration entry. The
tracked materializer appends that source-provenance entry to either registry v2
or registry v3 without changing v3 catalog events. It owns the new module,
package version, generated projections, and registry-last transaction; do not
reproduce or hand-edit those changes.

The implemented declaration route admits `verified` artifacts only. It does
not transport source L2 acceptance and cannot claim target `reviewed` maturity.
Direct maintenance of mutable catalog targets may add or revise modules
frequently and may rename, replace, split, merge, or retire them. These changes
must preserve immutable released snapshots and registry history. Plain
additions and in-place target revisions may be inferred from exact package
identity comparison; lifecycle changes require an explicit hash-bound request.
All such changes must use the versioned
`package reconcile-promotion-origin-registry` transaction specified in
`npa-core/docs/promotion-origin-registry-reconciliation.md`. The transaction
may advance any valid older registry/catalog snapshot directly to any strictly
newer validated target version, including across skipped releases.

These event forms are available through that transaction; do not hand-edit the
registry or weaken validation. Exact-target L2 events remain outside
reconciliation and require their own versioned workflow.
