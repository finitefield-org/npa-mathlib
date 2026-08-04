# Usage-Evidence-Driven Interface Adoption Design

Status: implemented for the v1 pilot; deferred decisions remain explicitly
open

Date: 2026-08-02

## Summary

This document defines the implemented v1 workflow for choosing future
`npa-mathlib` module and declaration interfaces from observed use in multiple
theorem-prover and mathematics repositories before the corresponding NPA proofs
exist. The original design rationale and deferred decisions remain preserved
below; the implementation record and acceptance audit at the end of this
document close the non-deferred pilot scope.

The workflow introduces **interface adoption** as a curation stage distinct
from **artifact promotion**:

- interface adoption decides a target `Mathlib.*` module, its public
  definitions and theorems, their exact intended signatures and
  definition/family contracts, and the evidence supporting those decisions;
- implementation creates new NPA proof source and canonical certificates,
  using external proofs only as references unless an explicit source-backed
  promotion route is selected;
- artifact promotion or direct catalog reconciliation applies the existing
  certificate, hash, source-free verification, import, axiom, provenance, and
  package gates.

An adopted interface is not a verified artifact, catalog admission, L2 review,
or released snapshot. This separation allows interface design to proceed from
real usage without weakening the `npa-mathlib` trust boundary.

Canonical interface-proposal records live outside `docs/` under:

```text
npa-mathlib/interface-proposals/
```

They are non-proof sidecars and must declare `proof_evidence = false`.

## Current Implementation Record (UIA-21)

The v1 proposal contract, read-only validators, Lean/mathlib4 inventory
adapter, implementation handoff, exact surface gate, owner artifact, and
direct catalog reconciliation route are implemented. The completed pilot is
the `function-comp-assoc` trace:

| Record | Exact value |
| --- | --- |
| Proposal | `interface-proposals/Mathlib/Logic/Function/Basic.toml` |
| Proposal revision/hash | revision `3`; `sha256:24b858109bcda41b7f7586d77edec3c2484574866668035bee83f185881f12bd` |
| Target | `Mathlib.Logic.Function.Basic` in `npa-mathlib` version `0.2.5` |
| Target surface | public `comp` definition and public `comp_assoc` theorem; direct imports `Mathlib.Logic.Eq`, `Std.Logic.Eq` |
| Owner artifact | `Mathlib/Logic/Function/Basic/source.npa`, `certificate.npcert`, `meta.json`, and `replay.json` |
| Qualification audit | `docs/promotion/function-comp-assoc-interface-adoption.md` |
| Reconciliation attestation | `docs/promotion/function-comp-assoc-reconciliation.json` |
| Registry result | generation `3`; one `catalog_target_v1` owner for `Mathlib.Logic.Function.Basic` at version `0.2.5` |
| Governance boundary | proposal, audit, attestation, and registry metadata retain `proof_evidence = false`; no L2 `reviewed` or release state was created |

The certificate-first package gates and the exact proposal-surface comparison
passed before and after reconciliation. The handoff, audit, attestation, and
registry are traceability metadata; canonical certificate bytes and the
source-free checker remain the proof authority. Existing catalog modules
require no retroactive proposal records. Downstream compatibility continues to
use immutable released snapshots and hash-pinned certificate closures rather
than mutable catalog HEAD.

## Related Documents

Read this design with:

- [`promote-to-mathlib-interface-mode-design.md`](promote-to-mathlib-interface-mode-design.md),
  which specifies a proposed skill mode for operating this workflow without
  conflating interface adoption with artifact promotion;
- [`catalog-policy.md`](catalog-policy.md), which defines verified artifact
  admission and immutable snapshots;
- [`namespace-policy.md`](namespace-policy.md), which defines `Mathlib.*`
  ownership, module paths, and identifier evolution;
- [`promotion-origin-registry.md`](promotion-origin-registry.md), which
  documents source and target artifact provenance;
- `npa-core/docs/promotion-origin-registry-reconciliation.md`, which defines
  direct catalog addition, revision, rename, split, merge, replacement, and
  retirement transactions.

This design does not replace or relax any of those contracts.

## Problem Statement

The existing promotion workflow starts from a proof artifact and asks whether
its exact closure can enter the mutable catalog. That workflow is intentionally
strict because catalog admission makes a concrete certificate artifact public.

Starting interface design from already verified source closures has two
practical limitations:

1. the source module boundary and names may reflect a local proof campaign
   rather than a reusable public library interface;
2. requiring a promotion-ready artifact before deciding the intended public
   surface delays naming and module-organization work until late in proof
   development.

The new workflow reverses that dependency for newly authored catalog content.
It surveys how mathematical definitions and theorems are actually organized
and used, adopts an NPA-facing interface, and then implements proofs for that
interface. Existing proofs can guide the new proof design, but they do not
become NPA proof evidence merely because they informed it.

## Goals

- Choose module boundaries from observed import and use patterns instead of
  inheriting one source repository's physical layout.
- Choose declaration names from mathematical meaning and demonstrated use.
- Record exact target signatures and definition/family contracts, not only
  names.
- Preserve pinned, reviewable evidence for every adopted decision.
- Support evidence from multiple proof assistants, theorem libraries, and
  mathematical codebases without making any one ecosystem authoritative.
- Allow interface decisions to be revised before implementation and allow
  catalog HEAD to evolve after implementation under the existing snapshot
  policy.
- Keep interface curation, proof verification, L2 review, catalog maturity,
  and release status semantically separate.
- Give future tooling a deterministic, machine-readable proposal format.
- Permit proof implementations to be newly authored in `npa-mathlib`,
  `npa-corpus`, or an `npa-project-*` package while making canonical ownership
  explicit.

## Non-Goals

- Interface adoption does not prove any declaration.
- It does not establish `verified`, `reviewed`, or `recommended` catalog
  maturity.
- It does not reserve a public name in a released snapshot.
- It does not bypass the promotion-origin registry or its reconciliation
  transaction.
- It does not copy external source, certificates, or proof terms into NPA.
- It does not require future API compatibility for mutable catalog HEAD.
- It does not require a fixed number of independent repositories before an
  interface can be adopted.
- It does not make popularity a substitute for mathematical accuracy,
  dependency quality, or NPA expressibility.
- It does not introduce quotient-backed interfaces. Repository policy still
  requires setoids instead of quotient constructions.

## Terminology And Authority

### Observation

An observation is pinned evidence from an external or NPA repository. It may
record a declaration, module boundary, import, direct application, rewrite,
instance-driven dependency, or repeated downstream pattern.

An observation is design evidence only. It is not proof evidence.

### Interface proposal

An interface proposal is one TOML record for one intended target module. It
contains the proposed module name, declarations, exact signatures,
dependencies, observations, alternatives, and decision rationale.

### Interface adoption

Interface adoption is the curation decision that the proposal is sufficiently
precise to guide implementation. The decision applies only to the proposal
revision recorded in Git. It does not create a package artifact.

### Proof reference

A proof reference identifies an external proof or proof strategy that may
inform a new NPA implementation. It is deliberately separate from usage
observations because a useful proof is not necessarily evidence of a good
public interface.

### Artifact origin

Artifact origin is the provenance represented by the promotion-origin
registry. A repository observed during interface research is not automatically
the artifact origin of an independently authored NPA implementation.

### Proof authority

Proof authority remains limited to canonical `.npcert` bytes and the existing
kernel, reference checker, deterministic hash, import, and axiom gates.
Interface proposals, observations, proof references, reviews, Git history, and
generated proposal indexes remain non-proof sidecars.

## Lifecycle

Interface status uses a vocabulary that cannot be confused with catalog
maturity:

```text
observed -> proposed -> adopted
    |           |          |  \
    v           v          v   v
 withdrawn   withdrawn  proposed  superseded
                         (rework)
```

- `observed`: evidence has been collected, but no complete target interface is
  proposed.
- `proposed`: a target module and declaration surface are available for
  review.
- `adopted`: the target interface is approved as an implementation contract.
- `withdrawn`: an unadopted record will not be pursued in its current form.
- `superseded`: an adopted record has been replaced by another explicit
  proposal.

In this design, `observed`, `proposed`, and `adopted` are active statuses;
`withdrawn` and `superseded` are terminal historical statuses.

Before materialization, an adopted proposal returns to `proposed` when
implementation requires a change to its target surface. After materialization,
same-module research changes the canonical file to `change_kind = "revise"`,
sets `source_modules = [module]`, and returns it to `proposed`. Both cases must
pass adoption review again before implementation continues. `superseded` is
reserved for a change that replaces the old target with one or more different
target-module proposals; the old record remains at its old module path and
names the new proposal IDs. Same-module rework uses `adopted -> proposed`, not
`superseded`.

The logical review may pass through a committed or uncommitted `proposed`
revision. A persisted comparison is also allowed to show `adopted -> adopted`
when the new record increments `proposal_revision`, binds the old file through
`previous_proposal_hash`, refreshes the adoption decision fields, and adds a
nonempty `re_adoption_rationale`. This represents completed rework and
re-adoption, not an in-place edit of the old decision.

Implementation state is intentionally not stored as another proposal status.
The package manifest, canonical certificates, promotion records, registry, and
released snapshots remain authoritative for artifact state.

The full workflow is:

```text
repository survey
    -> observed interface evidence
    -> normalized concept and usage clusters
    -> proposed NPA module and declarations
    -> adopted interface
    -> newly authored NPA implementation
    -> existing promotion or reconciliation gates
    -> materialized verified catalog artifact
    -> optional immutable snapshot release
```

Fresh exact-target L2 review may occur after the target artifact exists, either
before or after catalog admission. It is not a fixed later step in this
sequence. `recommended` remains a separate curation decision available only
for a reviewed surface and has no machine-validated record at the time of this
design.

The existing promotion stages begin only after a concrete artifact route
exists. `observed`, `proposed`, and `adopted` must never be reported as
`structural_candidate`, `verified_candidate`, `catalog_ingest_approved`,
`materialized_verified`, or `snapshot_released`.

## Canonical File Layout

The canonical root is:

```text
npa-mathlib/interface-proposals/
├── README.md
├── Mathlib/
│   ├── Data/
│   │   └── Nat/
│   │       └── Parity.toml
│   └── Topology/
│       └── Metric/
│           └── Local.toml
└── generated/
    └── index.json
```

The module-to-file mapping mirrors the public module path:

```text
Mathlib.Data.Nat.Parity
    -> interface-proposals/Mathlib/Data/Nat/Parity.toml
```

`interface-proposals/README.md` defines the accepted schema versions, status
meanings, review procedure, and local validation command. The optional
`generated/index.json`
is a disposable projection built only from canonical TOML files. It must not
be treated as proof evidence or edited by hand.

The canonical proposal scan domain is exactly
`interface-proposals/Mathlib/**/*.toml`. `interface-proposals/README.md` and
`generated/index.json`
are not proposal records and are excluded from proposal-file and proposal-set
hash rows.

Proposal records do not belong in:

- `npa-package.toml`, which enumerates actual package artifacts;
- `promotion-origins.json`, which is transaction-owned artifact provenance;
- `generated/theorem-index.json`, which describes implemented declarations;
- `docs/namespace-policy.md`, which contains global rules rather than pending
  module decisions.

## Proposal File Contract

### Illustrative record shape

The following example demonstrates the v1 shape. Its repository URLs,
revisions, abbreviated signatures, and abbreviated definition body are
illustrative and must be replaced with resolvable evidence and exact NPA
surface terms before the record can become canonical.

```toml
schema = "npa.mathlib.interface_proposal.v1"
proposal_id = "Mathlib.Data.Nat.Parity"
proposal_revision = 1
module = "Mathlib.Data.Nat.Parity"
change_kind = "add"
source_modules = []
interface_status = "adopted"
proof_evidence = false

summary = "Parity predicates and closure facts for natural numbers."
scope = "The even predicate and the basic facts required by downstream arithmetic."
imports = ["Std.Logic.Eq", "Std.Nat.Basic"]
adoption_date = "2026-08-02"
adoption_rationale = "Observed consumers unfold evenness and use its zero and addition facts."
alternatives_review = """
Parity groups the observed evenness API without implying a one-theorem module.
"""
supersedes = []

[[declarations]]
name = "NatEven"
kind = "definition"
surface = "public"
signature = "(n : Nat) -> Prop"
body = "... exact proposed NPA definition body ..."
semantic_role = "Canonical evenness predicate for natural numbers."
depends_on = []
evidence_ids = ["library-nat-even-declaration", "consumer-even-application"]

[[declarations]]
name = "nat_even_zero"
kind = "theorem"
surface = "public"
signature = "... exact proposed NPA declaration signature ..."
semantic_role = "Zero is even."
depends_on = ["NatEven"]
evidence_ids = ["consumer-even-zero"]
proof_reference_ids = ["library-even-zero-proof"]

[[declarations]]
name = "nat_even_add"
kind = "theorem"
surface = "public"
signature = "... exact proposed NPA declaration signature ..."
semantic_role = "The sum of two even natural numbers is even."
depends_on = ["NatEven"]
evidence_ids = ["consumer-even-add"]
proof_reference_ids = ["library-even-add-proof"]

[[alternatives]]
kind = "module_name"
candidate = "Mathlib.Data.Nat.Even"
disposition = "rejected"
rationale = "Parity leaves room for the observed oddness companion in the same coherent module."
evidence_ids = ["library-nat-even-declaration"]

[[observations]]
id = "library-nat-even-declaration"
repository = "https://github.com/example/theorem-library"
revision_kind = "git_commit"
revision = "0123456789abcdef0123456789abcdef01234567"
license = "Apache-2.0"
path = "Library/Data/Nat/Parity.lean"
source_module = "Library.Data.Nat.Parity"
source_declaration = "Even"
usage_kind = "declaration"
notes = "Provides the predicate represented by the proposed definition."

[[observations]]
id = "consumer-even-application"
repository = "https://github.com/example/downstream-project"
revision_kind = "git_commit"
revision = "89abcdef0123456789abcdef0123456789abcdef"
license = "MIT"
path = "Project/EvenSum.lean"
source_module = "Project.EvenSum"
source_declaration = "even_sum"
usage_kind = "direct_application"
notes = "Applies the evenness predicate through the imported public interface."

[[observations]]
id = "consumer-even-zero"
repository = "https://github.com/example/downstream-project"
revision_kind = "git_commit"
revision = "89abcdef0123456789abcdef0123456789abcdef"
license = "MIT"
path = "Project/EvenSum.lean"
source_module = "Project.EvenSum"
source_declaration = "even_sum"
usage_kind = "rewrite"
notes = "Uses the even-zero theorem as a base fact."

[[observations]]
id = "consumer-even-add"
repository = "https://github.com/example/downstream-project"
revision_kind = "git_commit"
revision = "89abcdef0123456789abcdef0123456789abcdef"
license = "MIT"
path = "Project/EvenSum.lean"
source_module = "Project.EvenSum"
source_declaration = "even_sum"
usage_kind = "rewrite"
notes = "Uses closure of evenness under addition."

[[proof_references]]
id = "library-even-zero-proof"
repository = "https://github.com/example/theorem-library"
revision_kind = "git_commit"
revision = "0123456789abcdef0123456789abcdef01234567"
license = "Apache-2.0"
path = "Library/Data/Nat/Parity.lean"
source_declaration = "even_zero"
reference_role = "proof_structure"
notes = "May inform a new NPA base proof; no source text is copied."

[[proof_references]]
id = "library-even-add-proof"
repository = "https://github.com/example/theorem-library"
revision_kind = "git_commit"
revision = "0123456789abcdef0123456789abcdef01234567"
license = "Apache-2.0"
path = "Library/Data/Nat/Parity.lean"
source_declaration = "even_add"
reference_role = "proof_structure"
notes = "May inform a new NPA closure proof; no source text is copied."
```

### Top-level fields

The v1 schema requires:

- `schema`: exactly `npa.mathlib.interface_proposal.v1`;
- `proposal_id`: a stable logical identifier, initially equal to `module`;
- `proposal_revision`: a positive integer starting at `1` and increasing by
  exactly one whenever the canonical record changes;
- `module`: the exact proposed `Mathlib.*` module;
- `change_kind`: one of `add`, `revise`, `rename`, `split`, `merge`, or
  `replace`;
- `source_modules`: sorted existing catalog module names changed or replaced by
  this proposal;
- `interface_status`: one lifecycle value defined above;
- `proof_evidence`: exactly `false`;
- `summary`: a one-sentence mathematical description;
- `scope`: what belongs in the module and, when useful, what does not;
- `imports`: the intended direct public import boundary;
- `alternatives_review`: required for `proposed`, `adopted`, and `superseded`;
  a summary of the naming, signature, and module-boundary alternatives
  considered, including an explicit statement when no material alternative
  was found;
- `supersedes`: proposal IDs replaced by this proposal.

Revision `1` omits `previous_proposal_hash`. Every later revision requires
`previous_proposal_hash`, equal to the canonical SHA-256 file hash of the
immediately preceding proposal revision. This creates an explicit revision
chain without placing the current file hash inside its own bytes.

`adoption_date` and `adoption_rationale` are required for `adopted` records and
retained by `superseded` records. A withdrawn record requires
`withdrawal_rationale`. A superseded record requires `superseded_by` as a
sorted, nonempty array of replacement proposal IDs. Every supersession link is
reciprocal: each new record names the old ID in `supersedes`, and the old
record names every new ID in `superseded_by`.

`change_kind` describes intent only. It does not authorize the corresponding
catalog mutation. Rename, split, merge, replacement, revision, and retirement
still require the canonical registry reconciliation transaction.
Retirement by itself does not define a new interface and therefore remains a
registry lifecycle request rather than an interface-proposal `change_kind`.

`source_modules` has these cardinality rules:

- `add`: empty;
- `revise`: exactly `[module]`;
- `rename`: exactly one old module and a different target `module`;
- `replace`: exactly one old module and a different target `module`;
- `split`: exactly one old module; every target proposal in the split carries
  the same nonempty `change_group`;
- `merge`: at least two old modules and one target `module`.

The change kinds have these meanings:

- `add`: before materialization, the target module has no current catalog
  route. After materialization, the adopted record remains as historical
  curation metadata and is rechecked by the exact-surface gate;
- `revise`: the target keeps its module name but changes its exported surface,
  imports, proof artifact, or other identity-bearing content;
- `rename`: one current module moves to a new meaning-equivalent module name;
- `replace`: one current module is retired in favor of a materially different
  successor that should not be described as a rename;
- `split`: one current module is retired in favor of two or more target
  modules;
- `merge`: two or more current modules are retired in favor of one target
  module.

`change_kind` is relative to current catalog HEAD, not to proposal history. If
an unmaterialized proposal is reorganized into multiple new targets, those new
records remain `add` proposals and use proposal supersession links; they do not
claim a catalog `split` event for an artifact that never existed.

The read-only proposal validator accepts one post-materialization exception:
an `adopted` `add` record may continue to name an existing target module. This
keeps the canonical curation record valid for current-only checks after catalog
admission. It does not inspect certificates or establish exact target parity;
the separate `check-interface-proposal-surface` gate remains mandatory. An
unadopted `add`, or an adopted `add` whose target does not exist in the current
catalog, remains a catalog relation error.

`change_group` is required for `split` and omitted otherwise. It groups the two
or more new target proposals produced by one split; the later canonical
registry change request still records and validates the exact old/new module
relation.

### Alternative fields

`[[alternatives]]` is optional, but `alternatives_review` is required for
`proposed`, `adopted`, and `superseded` records. Each alternative entry
requires:

- `kind`: `module_name`, `declaration_name`, `signature`, or
  `module_boundary`;
- `candidate`: the rejected or deferred alternative;
- `disposition`: `rejected` or `deferred`;
- `rationale`: why it was not selected now;
- `evidence_ids`: observations relevant to the comparison.

This makes the adoption criterion reviewable without requiring a rejected
alternative when the survey found none.

### Declaration fields

Every `[[declarations]]` entry requires:

- `name`: the intended unqualified NPA declaration name;
- `kind`: one of `inductive`, `definition`, or `theorem`;
- `surface`: `public` or `support`;
- `semantic_role`: why the declaration belongs in this module;
- `evidence_ids`: observations supporting the name, signature, or inclusion.

For `proposed` and `adopted` records, every declaration additionally requires:

- `signature`: the exact intended declaration signature without a proof body;
- `depends_on`: same-module declarations required by the signature or intended
  proof surface.

An adopted `definition` also requires `body`, containing the exact intended
NPA definition body. An adopted `inductive` requires `family_members`, listing
the exact ordered constructor, recursor, projection, and other generated
public names expected from the complete declaration family. A superseded
record retains the last-adopted body or family list. Definition bodies and
inductive families are part of the interface contract because changes to them
change the public artifact identity and may affect downstream reduction or
dependency behavior.

An `observed` record may omit these two fields while its target formulation is
unresolved. A `withdrawn` record retains the last unadopted surface that was
actually decided and may also omit `signature` and `depends_on` when it was
withdrawn before reaching a complete proposal. It is terminal history, not an
implementation contract. Names alone are not a complete proposed interface:
argument order, implicit parameters, premises, typeclass-like inputs, and
generality affect downstream use. A `superseded` record retains the last
adopted signatures and dependencies. They must remain syntactically valid, but
the validator does not require them to resolve against current catalog HEAD
after their historical imports or target routes have been retired.

A public declaration normally has at least one evidence ID tied to actual
downstream use. When direct use is not expected for a foundational primitive,
it may instead carry a nonempty `foundation_exception` explaining the
foundation role. A support declaration may have an empty `evidence_ids` array
only when it carries a nonempty `support_rationale` and is reached by a public
declaration's `depends_on` closure or a complete declaration-family rule.

Every theorem in an `adopted` or `superseded` record additionally requires a
nonempty `proof_reference_ids` array, unless it carries a nonempty
`proof_reference_exception`. The exception is for cases such as an immediate
projection whose implementation route is evident but no separate source proof
exists; it must explain the intended proof construction. A proof reference
supports implementation feasibility only and never establishes NPA proof
authority.

The adopted list is the intended exported surface. If implementation discovers
additional support declarations or complete declaration-family members that
will be exported by the target certificate, the proposal must return to
`proposed`, add those declarations, and be adopted again before catalog
materialization.

### Observation fields

Every `[[observations]]` entry requires:

- a proposal-local unique `id`;
- `repository` and an immutable `revision`;
- `revision_kind`, initially `git_commit` or `release_digest`;
- a known `license` value or an explicit `UNKNOWN` marker and follow-up note;
- `path`, `source_module`, and `source_declaration` when applicable;
- `usage_kind`;
- a concise `notes` explanation.

Allowed initial `usage_kind` values are:

- `declaration`;
- `module_import`;
- `direct_application`;
- `rewrite`;
- `instance_dependency`;
- `transitive_dependency`;
- `module_layout`.

Floating branches, tags that can be moved, unpinned default branches, search
result URLs, and prose without a resolvable source location are insufficient
for an `adopted` record. `license = "UNKNOWN"` is allowed during observation
but must be resolved before adoption when that observation or proof reference
is required by the adopted decision.

### Proof-reference fields

`[[proof_references]]` is optional until adoption. Each entry records a
proposal-local unique `id`, repository, immutable revision, license, path,
referenced declaration, reference role, and notes. Initial `reference_role`
values are `proof_structure`, `lemma_choice`, `induction_scheme`, and
`normalization_strategy`. At adoption, every theorem's
`proof_reference_ids` must resolve to these rows unless its documented
exception applies; a superseded record retains those last-adopted links.

A proof reference must never be presented as:

- a canonical NPA certificate;
- evidence that the eventual NPA statement is verified;
- source-backed promotion provenance for independently authored NPA content;
- permission to copy code whose license or attribution requirements are
  unresolved.

## Repository Survey Procedure

For each research batch:

1. Define the mathematical area and the repositories in scope.
2. Pin every repository to an immutable commit or release digest.
3. Inventory relevant modules, definitions, theorems, imports, and downstream
   uses.
4. Inspect use sites, not only declaration sites. Include tactic-, rewrite-,
   instance-, or inference-driven use when it materially affects the public
   interface.
5. Cluster declarations by mathematical meaning even when source names differ.
6. Record semantic differences such as stronger premises, different argument
   order, bundled versus unbundled structures, constructive versus classical
   assumptions, and quotient versus setoid presentation.
7. Propose the smallest coherent NPA module surface that supports the observed
   uses.
8. Record rejected or deferred alternatives and why they were not selected, or
   state explicitly that no material alternative was found.
9. Review the exact names, signatures, definition bodies, inductive families,
   imports, theorem proof references, and module boundary before changing
   `interface_status` to `adopted`.

Repository count is a signal, not a hard gate. One authoritative library plus
a concrete downstream use can justify adoption. Multiple independent
ecosystems provide stronger evidence. A foundational primitive with no direct
call site may be adopted only with an explicit `foundation_exception`
rationale explaining why direct-use evidence is not expected.

## Adoption Criteria

An interface may be marked `adopted` only when all of the following hold:

- the module name satisfies `namespace-policy.md` and its file path satisfies
  this design's module-to-proposal path rule;
- each declaration has an exact target signature and accurate mathematical
  name;
- every definition has an exact adopted body and every inductive has a complete
  adopted family-member inventory;
- at least one immutable observation supports every public declaration, or a
  documented foundation exception applies;
- actual downstream use supports the proposed surface, not merely the
  existence of similarly named declarations;
- alternative names and materially different statement shapes have been
  considered and recorded through `alternatives_review` and any applicable
  `[[alternatives]]` rows;
- imports are explicit and the intended dependency direction is acyclic;
- the module is one coherent semantic layer rather than a repository dump;
- collisions with current catalog modules, declarations, and other active
  proposals are resolved;
- the interface can be expressed under current NPA and repository policy,
  including the setoid requirement;
- external licenses and attribution requirements are recorded sufficiently to
  guide independent implementation;
- every theorem has a pinned proof reference or a documented proof-reference
  exception;
- adoption rationale explains why the selected surface is preferable to the
  observed alternatives.

Adoption does not require an NPA proof, canonical certificate, L2 vote,
downstream snapshot, or demonstrated API stability. Its external proof
references remain implementation guidance rather than NPA proof authority.

## Naming And Module-Placement Rules

### Module names

- Use `Mathlib.*`; never use `Std.*` or historical `Proofs.Ai.*` names.
- Name modules by current mathematical meaning, not by a source repository,
  proof campaign, implementation technique, or researcher.
- Use observed import clusters to inform boundaries, but do not copy a source
  repository's directory tree mechanically.
- Keep one semantic layer per module and prefer a bounded dependency closure.
- Use `Basic` only for a genuine foundational surface, not as a miscellaneous
  destination.
- Use a specialized suffix such as `Derived`, `Order`, `Power`, or `Carrier`
  only when it accurately identifies the module's role.
- Split a proposal when consumers use independent portions and the resulting
  modules have clean dependency direction. Merge when separate files merely
  expose one inseparable declaration family.

### Declaration names

- Choose the shortest unambiguous meaning-first name within the target module.
- Preserve conventional mathematical vocabulary when multiple ecosystems
  agree on it.
- Do not preserve a familiar source name if the adopted signature has
  materially different semantics.
- Names that encode conditions, directions, or result shapes must match the
  exact signature.
- Record source aliases in observations; do not automatically expose every
  alias as a public theorem.
- Avoid adding convenience theorems solely because they are easy to prove.
  Require observed use or an explicit foundational rationale.

### Signatures

- Normalize equivalent source formulations into one intended NPA signature.
- Prefer the weakest sufficient premises and most reusable conclusion only
  when that generality remains implementable and accurately evidenced.
- Record intentional differences from major observed alternatives.
- Treat changes in binders, premises, result type, imports, or axioms as
  interface changes even when the declaration name stays the same.
- Treat a definition-body or inductive-family change as an interface change;
  downstream proofs may depend on reduction and generated family members.

## Ownership, Provenance, And Licensing

The proposal must distinguish three roles:

1. **interface evidence owner**: the repository in which a declaration or use
   was observed;
2. **proof reference owner**: the repository containing a proof idea consulted
   during implementation;
3. **artifact owner**: the NPA package owning the newly implemented source and
   certificate.

When the proof is independently authored for the adopted interface directly in
`npa-mathlib`, the eventual registry route is target-owned catalog content.
The observed repositories remain sidecar evidence and are not promotion
origins.

When implementation occurs first in `npa-corpus` or an `npa-project-*`
package, that NPA package can become the canonical source owner and use the
existing complete-module or declaration-selection promotion route.

Copying or mechanically translating external proof source is outside the
default workflow. If that becomes necessary, licensing, attribution, source
identity, and whether the result is truly independently authored must be
resolved before implementation. Names and mathematical statements should
still retain source citations as research provenance even when no source code
is copied.

## Implementation Handoff

Every implementation task created from an adopted proposal must identify:

- the exact proposal path and canonical SHA-256 proposal file hash;
- the intended artifact owner;
- the target module and complete adopted declaration list;
- exact signatures, definition bodies, inductive families, and imports;
- permitted axioms;
- proof references that may be consulted;
- declarations or alternatives explicitly excluded from scope;
- the intended catalog route: source-backed promotion or direct target
  reconciliation.

Implementation may change theorem proof terms freely while preserving the
adopted interface. It may not change an adopted definition body or inductive
family. A required change to any exported declaration, signature, definition
body, inductive family, import, or module name returns the proposal to
`proposed`. Implementation must not silently expand the public surface to
satisfy proof convenience.

## Integration With Catalog Admission

### Source-backed implementation

If the new proof is implemented in `npa-corpus` or `npa-project-*`, the exact
NPA artifact enters the current promotion workflow:

1. discover a closed artifact route and establish `structural_candidate`;
2. verify the exact closure and establish `verified_candidate`;
3. resolve namespace, collision, provenance, and materialization capability,
   return `Ingest into mutable catalog`, and establish
   `catalog_ingest_approved` for that exact closure;
4. materialize through the supported transaction, run every target gate, and
   establish `materialized_verified` only after those gates pass;
5. when publication is separately authorized, complete the required commits,
   pushes, synchronization, immutable tag, and snapshot publication before
   establishing `snapshot_released`.

The adopted proposal informs the target mapping but cannot substitute for any
promotion gate.

### Direct `npa-mathlib` implementation

If the proof is newly authored directly in `npa-mathlib`, use a target-owned
catalog identity. The caller first prepares the complete unpublished target,
including a strictly newer package version, source, certificates, metadata,
replay, manifest, generated projections, and applicable downstream fixtures.
The reconciliation command validates but does not create, edit, delete, or
roll back those target artifacts. Addition or revision must then use the
versioned promotion-origin registry reconciliation transaction with an
explicit previous target and audit. Run dry-run before apply. Rename, split,
merge, replacement, and retirement additionally require the corresponding
canonical lifecycle request. If current tooling cannot represent the change,
stop as `tooling-blocked`; do not hand-edit `promotion-origins.json`.

An independently authored target does not inherit L2 evidence from any
repository used for interface observations or proof references. If the target
will be called `reviewed`, obtain fresh theorem-level, hash-bound acceptance
for the exact target artifact under the canonical L2 policy. That review may
occur before or after catalog admission once the exact target theorem hashes
exist. If the required exact-target maturity-recording transaction is not
available, report `tooling-blocked` and do not claim the target is `reviewed`.

### Traceability

Promotion or reconciliation audit sidecars must record the proposal path and
exact proposal hash. This link explains why the public surface was selected,
but remains `proof_evidence: false` and does not alter certificate identity.
The implemented registry reconciliation transaction stores the audit file hash
and its change-set relation in the transaction-owned attestation and registry
event.

## Operator Runbook (Implemented v1)

The v1 workflow is deliberately split into read-only evidence collection,
manual curation, target verification, and one of two catalog routes. The
following commands are the operator-facing boundary; all paths and revisions
must be replaced only with explicitly reviewed local inputs.

### Survey and manual adoption

Inventory a pinned local Lean/mathlib4 checkout with the implemented adapter:

```sh
cargo run --locked --offline -q --manifest-path npa-core/Cargo.toml -p npa-cli -- \
  package inventory-interface \
  --ecosystem lean4-mathlib4 \
  --root npa-core/testdata/interface-inventory/lean4-mathlib4 \
  --repository https://github.com/leanprover-community/mathlib4 \
  --revision c5ea00351c28e24afc9f0f84379aa41082b1188f \
  --license Apache-2.0 \
  --path Mathlib/Logic/Function/Defs.lean \
  --path Mathlib/Logic/Function/Iterate.lean \
  --declaration Function.comp_assoc \
  --declaration Function.iterate_invariant \
  --json
```

The adapter is read-only. A curator reviews declaration rows, use sites,
licenses, alternatives, exact NPA signatures/bodies, imports, and proof
references; then writes the module-mirrored TOML record and runs the current
proposal validator. Inventory output never writes a proposal or changes its
status. `adopted` is a manual curation decision, not an adapter result.

### Current-only and previous-root validation

Validate the current set without history, or supply the immediately preceding
validated set explicitly for transition checks:

```sh
cargo run --locked --offline -q --manifest-path npa-core/Cargo.toml -p npa-cli -- \
  package check-interface-proposals \
  --root npa-mathlib --proposal-root interface-proposals --json

cargo run --locked --offline -q --manifest-path npa-core/Cargo.toml -p npa-cli -- \
  package check-interface-proposals \
  --root npa-mathlib --proposal-root interface-proposals \
  --previous-proposal-root ../../npa-mathlib-0.2.4/interface-proposals --json
```

The second root is caller-supplied, distinct, read-only, and not inferred from
Git. Two-root validation catches detectable revision, lifecycle, terminal
record, identity, and hash-chain errors; it cannot prove that an unrelated
addition-only snapshot was not omitted.

### Handoff hashing and exact surface

Before implementation, record the exact tracked proposal bytes and require
surface parity against the prepared target:

```sh
git ls-files --error-unmatch \
  npa-mathlib/interface-proposals/Mathlib/Logic/Function/Basic.toml
shasum -a 256 \
  npa-mathlib/interface-proposals/Mathlib/Logic/Function/Basic.toml

cargo run --locked --offline -q --manifest-path npa-core/Cargo.toml -p npa-cli -- \
  package check-interface-proposal-surface \
  --root npa-mathlib --proposal-root interface-proposals \
  --proposal-path Mathlib/Logic/Function/Basic.toml \
  --proposal-sha256 sha256:24b858109bcda41b7f7586d77edec3c2484574866668035bee83f185881f12bd \
  --target-module Mathlib.Logic.Function.Basic --json
```

The surface gate is local, read-only, and must report `status = "parity"`.
Any proposal-byte, module, import, declaration, signature, body, family, or
exported-support change requires a new proposal revision and handoff.

### Catalog route selection

The proposal informs both supported routes but does not choose one implicitly:

| Route | Operator commands | Ownership and boundary |
| --- | --- | --- |
| Source-backed | `package prepare-promotion` with a complete v1/v2 request; `package materialize-promotion --phase temporary --apply`; `package validate-promotion-materialization`; then `package materialize-promotion --phase tracked --apply` | The source package owns the source-backed plan and attestation. The materializer owns source extraction, target writes, certificate-first gates, and registry-last tracked admission. |
| Direct target | Prepare and validate the complete target, run `check-interface-proposal-surface`, run `reconcile-promotion-origin-registry --dry-run`, then repeat with `--apply`; finish with `validate-promotion-origin-registry` | `npa-mathlib` owns independently authored target artifacts. Reconciliation writes its attestation and registry event last within the recoverable transaction; it does not create artifacts, grant L2 maturity, mutate released snapshots, or publish a release. |

The source-backed route uses the existing materializer contract:

```sh
npa package prepare-promotion \
  --root <source>/proofs \
  --target-baseline-root <clean-npa-mathlib> \
  --declaration-request promotion/<name>.selection.json \
  --out promotion/<name>.plan.json \
  [--check] [--json]

npa package materialize-promotion \
  --root <source>/proofs \
  --target-baseline-root <clean-npa-mathlib> \
  --target-root <temporary-npa-mathlib-copy> \
  --plan promotion/<name>.plan.json \
  --phase temporary --apply --json

npa package validate-promotion-materialization \
  --root <source>/proofs \
  --target-baseline-root <clean-npa-mathlib> \
  --target-root <temporary-npa-mathlib-copy> \
  --plan promotion/<name>.plan.json \
  --out promotion/<name>.verified-materialization.json \
  [--check] [--json]
```

The `--declaration-request` form above is the implemented v2 selection route.
For a complete-module v1 route, use the mutually exclusive existing
`--acceptance-policy`, `--source-acceptance`, `--transport-policy`, and
`--mapping` inputs documented by the promotion materializer. Both forms use
the same temporary validation and tracked, registry-last materialization
boundary.

The direct pilot used the following route-specific transaction against the
explicit `0.2.4` target; a future direct adoption must substitute its own
strictly newer target and audit paths:

```sh
npa package reconcile-promotion-origin-registry \
  --root npa-mathlib \
  --previous-target-root ../npa-mathlib-0.2.4 \
  --audit docs/promotion/function-comp-assoc-interface-adoption.md \
  --out docs/promotion/function-comp-assoc-reconciliation.json \
  --dry-run --json
npa package reconcile-promotion-origin-registry \
  --root npa-mathlib \
  --previous-target-root ../npa-mathlib-0.2.4 \
  --audit docs/promotion/function-comp-assoc-interface-adoption.md \
  --out docs/promotion/function-comp-assoc-reconciliation.json \
  --apply --json
npa package validate-promotion-origin-registry --root npa-mathlib --json
```

### Proposal hash contract

The canonical proposal file hash is SHA-256 over the exact tracked UTF-8 TOML
bytes and is rendered as `sha256:<lowercase-hex>`. Proposal paths are UTF-8,
relative to `interface-proposals/`, use `/` separators, and may contain neither
tabs nor newlines.

The deterministic proposal-set hash is SHA-256 over these UTF-8 bytes:

```text
npa.mathlib.interface_proposal_set.v1\n
<relative-path>\t<proposal-file-hash>\n
...
```

Rows are sorted by relative-path byte order and include every canonical TOML
proposal, including withdrawn and superseded records. The optional generated
index records the same ordered rows and resulting set hash. This hash is
curation traceability only and remains non-proof evidence.

## Validation Tooling Design

Manual review remains mandatory for interface curation. The implemented Rust
CLI contract is:

```sh
npa package check-interface-proposals \
  --root npa-mathlib \
  --proposal-root interface-proposals \
  [--previous-proposal-root <previous>/interface-proposals] \
  --json
```

`--previous-proposal-root` is an optional, explicit, read-only earlier proposal
snapshot. The caller must supply the immediately preceding validated
proposal-set snapshot. The command validates the supplied pair and rejects
every detectable per-record revision skip, but it does not invoke Git or a
history ledger and therefore cannot prove that an unrelated addition-only
proposal set was not omitted between the two roots. Without this argument it
validates only the current snapshot. With it, the command also validates
lifecycle transitions, preservation of terminal records, and cross-snapshot
proposal identity. The previous root must not resolve to the same canonical
directory as the current proposal root.

The implemented validator:

- reject symlinks, non-regular proposal files, path escapes, invalid UTF-8,
  and non-`.toml` canonical proposal records;
- enforce explicit bounds on proposal count, file bytes, declarations,
  observations, proof references, alternatives, imports, paths, and strings
  before allocating nested data;
- reject unknown schema versions and unknown fields;
- require `proof_evidence = false`;
- validate lifecycle-specific fields;
- enforce `Mathlib.*` ownership and module-to-file path correspondence;
- enforce globally unique proposal IDs, reject reuse of a terminal proposal ID,
  and enforce unique active target modules;
- validate positive proposal revisions and the presence or absence of
  `previous_proposal_hash` required by the revision number;
- enforce unique declaration names within a proposal;
- validate declaration kinds, surfaces, dependencies, and evidence references;
- allow unresolved signatures only at `observed` or `withdrawn` status;
- reject `proposed` or `adopted` declarations with empty, placeholder,
  syntactically invalid, or import-unresolvable NPA signatures;
- require parseable exact bodies for adopted definitions and complete,
  duplicate-free family-member inventories for adopted inductives;
- require `superseded` declarations to retain nonempty, non-placeholder,
  syntactically valid signatures plus their last-adopted definition bodies or
  inductive families, without resolving them against current HEAD;
- require evidence for each public declaration or a valid
  `foundation_exception`, and validate the dependency/family rationale for
  each unevidenced support declaration;
- require each adopted or superseded theorem to reference at least one
  resolvable, pinned proof reference or carry a valid
  `proof_reference_exception`;
- validate `alternatives_review` and every `[[alternatives]]` evidence link;
- reject floating evidence revisions;
- for `observed`, `proposed`, and pre-materialization `adopted` records, check
  `add` targets do not already exist in `npa-package.toml`; accept only the
  post-materialization `adopted` exception and require the exact-surface gate
  for its target;
- for `observed`, `proposed`, and `adopted` records, check `revise`, `rename`,
  `split`, `merge`, and `replace` targets reference the appropriate existing
  module or superseded proposal;
- enforce `source_modules` cardinality and split `change_group` rules;
- detect collisions among current catalog declarations and active proposals;
- validate `proposed` and `adopted` imports against current catalog modules,
  immutable package imports, or other adopted proposals;
- detect cycles in the proposed/adopted import graph;
- require adoption, withdrawal, and supersession rationale where applicable;
- require reciprocal `supersedes` and `superseded_by` links;
- when `--previous-proposal-root` is supplied, verify that same-module rework
  uses `adopted -> proposed`, cross-module replacement uses explicit
  supersession links, every changed record increments its revision and binds
  the previous file hash, an `adopted -> adopted` change carries fresh adoption
  fields plus `re_adoption_rationale`, a transition to `withdrawn` retains the
  previous unadopted declaration/import surface, records already terminal in
  the previous snapshot remain byte-identical, and old canonical records are
  not removed;
- produce a deterministic summary and proposal-set hash using the contract
  above.

Validation is local and network-free. It checks repository locator and revision
shape but does not fetch external repositories or claim that a remote object
still exists. Evidence collection or review must establish that fact before
adoption and preserve any required local audit record separately.

The validator is a curation tool, not part of the trusted proof checker. Its
success must not be reported as proof verification or catalog admission.

## Failure And Revision Policy

- Missing or stale evidence returns the record to `observed` or `proposed`;
  it does not invalidate existing released artifacts.
- A naming or signature collision blocks adoption until resolved.
- An unresolvable license or attribution question blocks use of the affected
  proof reference and may block the observation if its source cannot be
  inspected lawfully.
- An NPA expressibility problem blocks adoption unless the interface is
  reformulated accurately.
- Discovery of required exported support declarations after adoption requires
  proposal revision and re-adoption before materialization.
- Failure of certificate or package gates is an implementation or promotion
  failure; it does not retroactively turn interface evidence into proof.
- A released module is never rewritten. A later interface correction creates a
  new catalog revision, replacement, rename, split, merge, or retirement event
  and a strictly newer snapshot.

## Concurrency And Change Discipline

One canonical TOML file owns each active target module proposal. Researchers
may gather notes elsewhere, but only curation-approved changes enter the
canonical record. Concurrent changes to the same target module must be
reconciled before adoption rather than represented by two active files.

History is preserved through Git and explicit `supersedes` or `superseded_by`
links. Do not silently reuse an abandoned proposal ID for unrelated
mathematics. Generated indexes should be written deterministically and updated
only after all canonical files validate.

## Compatibility And Migration

Existing catalog modules do not need retroactive proposal files. The new
workflow applies to newly researched interfaces and intentional revisions.

When a current module is used as the starting point for a revision:

- create a proposal with `change_kind = "revise"` or the appropriate lifecycle
  change;
- inventory the current module as one observation source;
- add downstream use evidence from pinned consumers when available;
- state the exact old and new surfaces and compatibility impact;
- use the canonical registry reconciliation transaction after implementation.

Historical source-to-public mappings in `namespace-policy.md` remain
historical facts. Proposal records must not rewrite them.

## Implementation Plan

### Phase 1: documentation and manual records (implemented)

- Add `interface-proposals/README.md` with the v1 contract.
- Add one pilot proposal under the module-mirrored path.
- Review the pilot against at least one declaration repository and one use
  site.
- Keep all records explicitly non-proof-bearing.

### Phase 2: read-only validation (implemented)

- Implement the v1 parser and validation types in Rust.
- Add the `check-interface-proposals` command.
- Support explicit current/previous proposal-root comparison without invoking
  Git or resolving floating refs.
- Add deterministic JSON diagnostics and proposal-set hashing.
- Add positive and negative fixtures without introducing GitHub Actions CI.

### Phase 3: inventory assistance (Lean/mathlib4 pilot implemented)

- Add the read-only Lean 4 `mathlib4` adapter that inventories selected pinned
  repositories; additional ecosystems remain deferred.
- Keep prover-specific extraction separate from the canonical normalized
  proposal schema.
- Require human or explicitly authorized curation before `adopted` status.

### Phase 4: implementation and catalog handoff (direct pilot implemented)

- Include proposal path and hash in implementation plans and promotion audit
  sidecars.
- Detect surface drift between an adopted proposal and the target package.
- Require re-adoption when target preparation expands the exported closure.
- Continue using the existing registry-last materialization and release gates;
  UIA-18 through UIA-20 exercised the direct target-reconciliation route, while
  source-backed promotion remains owned by the existing materializer.

## Test Strategy

The implemented validator test suite includes:

- a valid `observed`, `proposed`, `adopted`, `withdrawn`, and `superseded`
  fixture;
- valid adopted-to-proposed rework and cross-module supersession fixtures;
- valid hash-chained adopted-to-adopted re-adoption fixtures;
- current-only validation plus explicit previous-root transition validation;
- invalid schema, unknown field, and `proof_evidence = true` fixtures;
- symlink, path-escape, invalid UTF-8, wrong-extension, and resource-limit
  fixtures;
- module/path mismatch and forbidden namespace fixtures;
- duplicate module, proposal ID, and declaration fixtures;
- observed/withdrawn missing-signature acceptance plus proposed/adopted missing,
  placeholder, syntax-invalid, and import-unresolvable signature fixtures;
- adopted definition-body and inductive-family positive, missing, malformed,
  duplicate, and drift fixtures;
- valid and invalid foundation-exception and support-rationale fixtures;
- valid, missing, unresolved, and excepted theorem proof-reference fixtures;
- missing alternatives review and unresolved alternative evidence fixtures;
- floating revision and missing-license fixtures;
- invalid status-transition metadata fixtures;
- missing/reused/skipped revision, wrong previous hash,
  edited-adopted-without-re-adoption, changed-terminal-record, and
  removed-record transition fixtures;
- valid and invalid `source_modules` cardinality and split `change_group`
  fixtures;
- catalog and cross-proposal collision fixtures;
- unresolved import and import-cycle fixtures;
- valid `add`, `revise`, `rename`, `split`, `merge`, and `replace` fixtures;
- deterministic proposal-file and proposal-set hash vectors;
- a surface-drift test comparing names, signatures, definition bodies,
  inductive families, and imports with a prepared target module.

The original documentation-only phase used Markdown review, link checking
where available, and confirmation that package artifacts were untouched. The
implemented pilot additionally verifies Rust tests, package gates, proposal
validation, exact surface parity, registry reconciliation, and the explicit
trust/release boundaries recorded below.

## Acceptance Audit (UIA-21)

The non-deferred acceptance criteria are closed by the following evidence
ledger. Paths refer to the aggregate checkout root, and test names identify
the implementing Rust or package gate. Every row is either satisfied or
explicitly deferred below; no row is satisfied by a proposal, audit sidecar,
registry event, or reference-checker result acting as proof authority.

| Acceptance criterion | Implementing milestone and evidence | Status / boundary |
| --- | --- | --- |
| The v1 schema is defined and canonical records are module-mirrored. | UIA-01; `npa-mathlib/interface-proposals/README.md`; `npa-core/crates/npa-package` interface-proposal tests; current-only validator run. | Met. Canonical files are `interface-proposals/Mathlib/**/*.toml`; README and generated indexes are outside the scan domain. |
| Every record is machine-readable and explicitly non-proof-bearing. | UIA-01/02; schema field `proof_evidence = false`; parser/diagnostic fixtures; pilot proposal, audit, attestation, and registry entries. | Met. These records are curation/traceability metadata only. |
| Revisions are monotonic and hash-bound, while lifecycle status is disjoint from catalog maturity and promotion stage. | UIA-02/05/17; current proposal revision `3`, `previous_proposal_hash`, explicit two-root transition validator, and lifecycle tests in `interface_proposal`. | Met. `observed`/`proposed`/`adopted`/`withdrawn`/`superseded` do not advertise `verified`, `reviewed`, or `snapshot_released`. |
| Adopted proposals carry exact module, declaration, signature, body/family, import, source relation, observation, alternative, and rationale data. | UIA-01/02/03/07/14; `Mathlib/Logic/Function/Basic.toml`; Lean/mathlib4 inventory command; handoff exact-term tables. | Met for the v1 pilot. The adapter is read-only and manual curation remains required. |
| Proof references and usage observations remain separate. | UIA-03/07; separate `proof_references` and `observations` tables in the proposal and handoff role table. | Met. External evidence is not target proof evidence. |
| Handoffs identify exact ownership and adopted revision. | UIA-07/14/17/21; `docs/interface-research/function-comp-assoc-handoff.md` records the path, revision 3 hash, owner, complete surface, and route. | Met. A proposal-byte change invalidates the handoff and requires re-adoption. |
| Surface drift requires proposal revision rather than silent expansion. | UIA-06/15/16; `npa-core/docs/interface-proposal-surface-drift-v1.md`; `check-interface-proposal-surface` exact module/import/declaration/body/family/export-closure parity test. | Met. The checker is read-only and emits `proof_evidence: false`; it does not rewrite proposals. |
| Materialization uses existing certificate-first promotion or direct reconciliation gates. | UIA-08..13 and UIA-18..20; package check/build/verify/hash/generated/export/axiom/index/publish gates; direct reconciliation dry-run, apply, attestation, and registry validation. | Met. The pilot used direct target reconciliation; source-backed promotion remains owned by the existing materializer. |
| Existing modules need no retroactive proposals and immutable downstream snapshots remain compatible. | UIA-21 implementation record; `npa-mathlib` package projections and prior `0.2.4` snapshot; immutable snapshot/hash-pinned closure policy references. | Met. No existing module received a retroactive proposal; no released snapshot or downstream closure was rewritten. |
| Released snapshots and registry history remain immutable. | UIA-20 generation-3 registry transition, hash-bound attestation, and post-apply `validate-promotion-origin-registry`; reconciliation documentation. | Met. The transaction appended the target-owned event and did not mutate released snapshots, source artifacts, generated projections, or Git. |

All non-deferred criteria are met for the v1 pilot. The only open items are
the explicitly scoped schema/projection, ecosystem-adapter, evidence-score,
and curator-record decisions in the next section.

## Deferred Decisions

The following can be decided after one or more pilot proposals:

- whether the canonical schema should remain TOML or gain a canonical JSON
  projection;
- whether proposal-set hashes should be recorded in promotion plans by a new
  schema version;
- which external prover ecosystems receive automated inventory adapters first;
- whether evidence quality should receive a derived, non-authoritative score;
- whether adopted interfaces require a named human curator or a separate
  versioned curation-decision record.

None of these deferred decisions blocks manual creation and review of v1
proposal records.
