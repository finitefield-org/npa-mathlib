# AGENTS.md

Guidance for agents working in this repository.

## Repository-wide Guidelines

- Use `/usr/bin/git` for git commands.
- Do not use Git LFS in this repository.
- Do not add `.gitattributes` rules that set `filter=lfs`, `diff=lfs`, or
  `merge=lfs`.
- When importing or updating subtree content, ensure LFS pointer files are not
  introduced into this repository history.
- Record any suggestions in `suggestions.md`.
- When adding proofs, make maximum effort to choose module and theorem names
  that accurately communicate their mathematical meaning and corpus role.
- When module or theorem refactoring is needed to preserve meaningful naming,
  semantic placement, or maintainable proof organization, perform it without
  hesitation rather than leaving proofs in ill-fitting modules or names.

Before adding, ingesting, removing, renaming, replacing, or reorganizing public
modules or declarations, read `docs/catalog-policy.md` and
`docs/namespace-policy.md`. Choose names that accurately communicate the
current mathematics, but do not treat catalog HEAD as a stable dependency API.
Preserve immutable released snapshots and historical artifact provenance.

Catalog admission requires canonical certificates, deterministic current
hashes, cache-off source-free reference verification, explicit imports, the
axiom policy, accurate names, origin-registry provenance, and package gates.
Definitions, interfaces, and proved theorems may be admitted at `verified`
maturity. Do not add unresolved conjectures, custom axioms,
`sorry`-equivalent placeholders, or content whose public name intentionally
misstates what its certificate proves.

`verified` admission may use either a complete module closure or a selected
declaration closure materialized as a new module. A declaration selection must
include all required same-module support declarations and complete validated
source families, create a new `Mathlib.*` artifact and package version, pass
the normalized source/target closure and independent attestation gates, and be
published only by the registry-last tracked transaction. Consumers still pin
the complete target certificate import closure, never individual declarations.

`L2 Derived certificate` acceptance is the optional `reviewed` maturity label,
not a catalog-admission gate. Its canonical authority remains
`policy/l2-acceptance-policy.json`, documented in
`docs/l2-acceptance-policy.md`. When claiming `reviewed`, validate the source
package's canonical v2 review inputs/reports and `proofs/l2-acceptance.json`
with `prepare-l2-review-input`, `aggregate-l2-acceptance`, and
`validate-l2-acceptance`, repeating `--module` for the exact reviewed closure.
Agents must not cast or edit L2 votes; only the unchanged policy-required
quorum may establish the label.

Preserving `reviewed` maturity across complete-module namespace-only ingestion
additionally
requires the canonical
`policy/l2-namespace-transport-policy.json` and a passing
`validate-l2-namespace-transport` attestation from a clean target baseline.
Declaration deletion or splitting is not L2 transport: source L2 evidence is
historical provenance only, and each promoted target theorem needs fresh
target-bound L2 acceptance before it is called `reviewed`.

Use declaration request v1, promotion plan v2, verified materialization
attestation v1, and registry v2 for supported declaration-level `verified`
admission. Direct mutable-catalog additions, revisions, renames, replacements,
splits, merges, and retirements belong to the versioned registry v3
reconciliation transaction. It may migrate any valid older snapshot to any
strictly newer validated target version. Do not bypass either transaction or
hand-edit registry evidence. Use
`npa package reconcile-promotion-origin-registry` for reconciliation;
target-L2 events require a separate versioned transaction.
