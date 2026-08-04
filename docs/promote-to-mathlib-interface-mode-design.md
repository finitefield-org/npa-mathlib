# Promote-To-Mathlib Interface Mode Design

Status: interface mode implemented for v1; proposal and artifact-route extensions remain deferred

Date: 2026-08-02

The skill integration is implemented in
`.agents/skills/promote-to-mathlib/SKILL.md`, with the operational contract in
`.agents/skills/promote-to-mathlib/references/interface.md`. The existing Rust
CLI commands provide proposal validation, hashing, and exact-surface parity;
this task adds orchestration and trust-boundary behavior to the skill.

## Current Skill Integration Record

The v1 integration now provides:

- explicit `interface` mode selection beside `discover`, `judge`, `promote`,
  and `loop`;
- `survey`, `draft`, `adopt`, `lifecycle`, and `handoff` actions with explicit
  mutation and curation-authority boundaries;
- conditional interface preflight that does not require an artifact source
  package unless a later artifact phase is requested;
- separate interface recommendations and failure/completion fields; and
- the operational reference at
  `.agents/skills/promote-to-mathlib/references/interface.md`.

The existing proposal validator and exact-surface checker remain the
implementation of proposal mechanics. No new proof, catalog, registry, or
release state is introduced by this skill integration.

## Summary

Add an `interface` mode to the `promote-to-mathlib` skill so one entry point
can research, curate, validate, adopt, and hand off a future `npa-mathlib`
interface before a corresponding verified NPA artifact exists.

The mode does not perform an “interface-only artifact promotion.” It operates
the existing interface-proposal lifecycle and stops at an exact, hash-pinned
implementation handoff. It never advances an artifact through
`structural_candidate`, `verified_candidate`, `catalog_ingest_approved`,
`materialized_verified`, or `snapshot_released`.

When a proof artifact later exists, the operator leaves `interface` mode and
enters one of the existing artifact routes:

- source-backed `promote` from `npa-corpus` or `npa-project-*`; or
- direct target preparation followed by catalog reconciliation.

This preserves the existing trust boundary: interface evidence explains why a
surface was selected, while canonical certificate bytes and package gates
remain the only authority for catalog verification.

## Related Contracts

This mode is an orchestration layer over the existing
[interface-adoption design](usage-evidence-driven-interface-adoption-design.md)
and the canonical
[interface-proposal contract](../interface-proposals/README.md). It must also
use the [implementation-handoff template](interface-implementation-handoff-template.md)
and preserve the [catalog policy](catalog-policy.md),
[namespace policy](namespace-policy.md), and
[promotion-origin registry contract](promotion-origin-registry.md).

## Design Decision

The skill will expose five top-level modes:

| Mode | Purpose | Default write authority |
| --- | --- | --- |
| `discover` | Find source-backed artifact candidates. | Read-only. |
| `judge` | Qualify a named artifact closure. | Read-only. |
| `promote` | Materialize and verify a named artifact closure. | Scoped artifact writes. |
| `loop` | Exhaust eligible artifact closures through the authorized lifecycle. | Scoped artifact writes; publication only when authorized. |
| `interface` | Research and curate a future public interface, then prepare an exact implementation handoff. | Read-only unless the prompt explicitly authorizes proposal or handoff writes. |

`interface` is a sibling of the artifact modes, not an additional artifact
maturity or promotion stage. The skill must use the word **adopt** for an
interface decision and reserve **promote**, **ingest**, **materialize**, and
**release** for artifact operations.

## Goals

- Make the implemented usage-evidence-driven interface workflow discoverable
  through the existing catalog skill entry point.
- Support interface research before an NPA proof artifact exists.
- Reuse the canonical proposal schema, validators, hash contract, and exact
  surface checker without creating a second proposal format.
- Require explicit curation authority before writing `interface_status =
  "adopted"`.
- Produce a complete, hash-pinned handoff that either artifact route can
  consume.
- Prevent interface observations, proof references, proposal validation, or
  surface parity from being reported as proof evidence or catalog admission.
- Preserve immutable released snapshots and the existing registry-last
  artifact transactions.

## Non-Goals

The first `interface` mode will not:

- fetch floating external repositories or resolve remote branches and tags;
- automatically infer that frequently used APIs should be adopted;
- write a proposal directly from inventory output without manual curation;
- copy external proof source into NPA;
- create NPA source, certificates, metadata, replay, or generated package
  projections;
- establish `verified`, `reviewed`, or `recommended` catalog maturity;
- add, revise, rename, replace, split, merge, or retire catalog artifacts;
- update `promotion-origins.json` or invoke a registry apply transaction;
- commit, push, tag, publish, or rewrite an immutable snapshot;
- run an exhaustive auto-adoption loop; or
- require retroactive proposals for existing catalog modules.

## Two Independent State Machines

The skill must keep interface state and artifact state separate in its working
notes and final report.

### Interface proposal lifecycle

The canonical lifecycle remains:

```text
observed -> proposed -> adopted
    |           |          |  \
    v           v          v   v
 withdrawn   withdrawn  proposed  superseded
                         (rework)
```

`adopted` means that exact proposal bytes are an implementation contract. It
does not mean that a target artifact exists.

The mode may additionally report derived validation facts without storing a
new proposal status:

- `current_validated`: the current proposal set passed the v1 validator;
- `transition_validated`: the current and explicitly supplied previous sets
  passed transition validation;
- `handoff_pinned`: an adopted tracked proposal path and exact file hash are
  recorded in a complete handoff; and
- `surface_parity`: a prepared target exists and the exact-surface checker
  returned `status = "parity"` for the pinned proposal hash.

These are run results, not lifecycle values and not proof evidence.

### Artifact promotion lifecycle

The existing stages remain unchanged:

```text
structural_candidate
  -> verified_candidate
  -> catalog_ingest_approved
  -> materialized_verified
  -> snapshot_released
```

An `interface` run reports `Artifact promotion stage: not entered`. Even an
adopted proposal with `surface_parity` cannot establish
`structural_candidate`, because a closed source artifact route may still be
absent.

## Mode Selection And Authorization

Choose `interface` when the user asks to research, propose, review, adopt,
revise, withdraw, supersede, or hand off a future `Mathlib.*` interface, or
explicitly asks for “interface mode.”

Infer the narrowest internal action:

| Action | Trigger | Allowed effects |
| --- | --- | --- |
| `survey` | Find usage evidence, compare APIs, or inspect what should exist. | Read-only inventory and recommendation. |
| `draft` | Create or revise an `observed` or `proposed` record. | Canonical proposal and associated research sidecars only when writes are explicit. |
| `adopt` | Approve an exact interface for implementation. | May write `adopted` only when adoption authority is explicit and every adoption gate passes. |
| `lifecycle` | Return an adopted proposal to rework, withdraw an unadopted proposal, or supersede an adopted proposal. | May write only the explicitly requested transition after its revision, hash, status, and reciprocal-link rules pass. |
| `handoff` | Prepare implementation from an adopted proposal. | May write/update the handoff and audit sidecars explicitly requested; target comparison remains read-only. |

If the request says only `$promote-to-mathlib interface`, choose `survey` and
remain read-only. A request to “review” or “judge” a proposal also remains
read-only. A request to create a draft does not authorize adoption. A request
to adopt does not authorize proof implementation, catalog reconciliation, or
publication.

The skill must not infer curation approval from validator success. It may set
`interface_status = "adopted"` only when the user explicitly authorizes
adoption of the exact reviewed surface, or repository policy identifies an
equivalent authorized curation decision already present in the workspace.

If one request explicitly asks both to adopt an interface and implement or
promote its artifact, execute and report two bounded phases. Finish the
interface handoff first, then re-enter the existing artifact qualification
boundary. Do not carry an interface recommendation across a changed proposal
hash or changed target surface.

## Inputs

Resolve the following inputs before making any canonical proposal write:

- target `Mathlib.*` module and intended declaration scope;
- action: `survey`, `draft`, `adopt`, `lifecycle`, or `handoff`;
- canonical proposal root and module-mirrored proposal path;
- current proposal status, revision, and exact file hash when one exists;
- immediately previous validated proposal root when transition validation is
  required; and
- the status-specific fields and evidence required for the intended write.

Before writing `proposed` or `adopted`, additionally resolve:

- pinned evidence repositories, immutable revisions, paths, declarations,
  licenses, and locally available checkouts;
- observed declaration and use-site evidence;
- exact NPA signatures, adopted definition bodies, inductive family members,
  direct imports, and public/support designation;
- proof references, kept separate from usage observations;
- alternatives considered and adoption, withdrawal, supersession, or rework
  rationale.

Before declaring an implementation handoff ready, additionally resolve:

- intended artifact owner and route: `source-backed` or `direct-target`;
- the current package axiom policy and known compatibility impact; and
- for handoff, explicitly excluded declarations and alternatives.

Artifact owner and catalog route may remain unresolved when an exact interface
is adopted. They become blocking only for `handoff_pinned`, matching the
canonical implementation-handoff contract. Proposal v1 has no
`allowed_axioms` field; the handoff records the package-policy boundary without
inventing proposal metadata.

External repository coordinates identify curation evidence, not an artifact
origin. Only a later source-backed artifact route may establish the canonical
promotion origin used by the catalog registry.

## Preflight

Reuse the shared `promote-to-mathlib` repository-policy, Git-topology, and
unrelated-change safety rules. Replace its artifact-campaign package discovery
with this mode-specific preflight:

1. read the interface-adoption design, `interface-proposals/README.md`, catalog
   policy, namespace policy, and applicable `AGENTS.md` files; for `handoff`,
   also read the implementation-handoff template;
2. locate `npa-core`, `npa-mathlib`, the canonical proposal root, and any
   explicitly supplied previous proposal root;
3. identify every pinned local evidence checkout and reject floating revision
   descriptions for adopted evidence;
4. inspect status once per distinct Git root before a tracked write;
5. preserve unrelated changes and detect concurrent edits to the same proposal
   path;
6. confirm that every proposed public import resolves to current catalog
   content, another adopted proposal, or a hash-pinned immutable package; and
7. determine whether a prepared target already exists. Its presence enables a
   read-only surface check but does not expand write authority.

The mode is local and network-free by default. If required evidence is not
available locally, return `Defer` with the missing pinned input; do not replace
it with a floating web result.

## Workflow

### 1. Resolve the canonical record

Map the target module to exactly one
`interface-proposals/Mathlib/**/*.toml` path. Check active proposal IDs,
modules, declarations, imports, lifecycle links, change groups, current catalog
names, and retired identifiers for collisions. Never create two active records
for one target module or silently reuse a terminal proposal ID.

For an existing record, preserve its stable `proposal_id`. Any changed record
increments `proposal_revision` by exactly one and binds
`previous_proposal_hash` to the exact prior tracked bytes. Terminal records are
immutable after their first terminal revision.

### 2. Inventory pinned evidence

Use `package inventory-interface` only against explicitly pinned local input
supported by an implemented adapter. Record repository, immutable revision,
license, paths, selected declarations, and use sites. The adapter output is a
read-only normalization aid; it never writes a proposal or chooses a status.

If an ecosystem has no adapter, perform the same evidence collection manually
and record that the adapter is unavailable. Lack of an adapter does not waive
immutable revision, licensing, use-site, alternative, or exact-surface review.

### 3. Curate the exact NPA interface

Separate three kinds of information:

- usage observations justify module boundaries, names, and likely reuse;
- proof references may guide implementation strategy; and
- exact NPA terms define the proposed public and support surface.

For `proposed` and `adopted`, require complete parseable signatures. For an
adopted definition require its exact body; for an adopted inductive require the
complete generated family inventory. Every dependency and direct import must
be explicit. The proposal must use setoid formulations where an equivalence
relation is required and must not add quotient constructions.

### 4. Make one interface recommendation

Return exactly one recommendation:

- `Advance to observed`
- `Advance to proposed`
- `Adopt exact interface`
- `Keep current status`
- `Return to proposed for rework`
- `Withdraw interface`
- `Supersede interface`
- `Defer`
- `Reject for now`

Use `Adopt exact interface` only when all adoption gates below pass. `Defer`
means missing evidence, unresolved ownership, unresolved route, incomplete NPA
terms, licensing uncertainty, or available tooling that cannot yet represent
the intended record. `Reject for now` means the requested surface is
misleading, conflicts with namespace or axiom policy, depends on prohibited
construction, or cannot accurately express the intended mathematics.

This recommendation is separate from the artifact qualification outcomes
`Ingest into mutable catalog`, `Defer`, and `Reject for now`. Interface mode
must never emit `Ingest into mutable catalog`.

Use `Withdraw interface` only for `observed` or `proposed`. Use `Return to
proposed for rework` for same-module changes to an adopted proposal. Use
`Supersede interface` only for an adopted proposal replaced by one or more
different target proposals with complete reciprocal links.

### 5. Apply the adoption gates

Before recommending or writing `adopted`, require:

- a unique target module and accurate declaration names;
- complete exact signatures, definition bodies, inductive families, imports,
  support declarations, and dependency relations;
- resolvable immutable evidence coordinates and compatible licenses;
- an immutable observation for every public declaration or a documented
  foundation exception, plus a concrete downstream use for a non-foundational
  public surface;
- complete theorem proof-reference links or a documented exception;
- reviewed naming, signature, and module-boundary alternatives;
- no current catalog or active-proposal collision;
- an acyclic, resolvable proposed import graph;
- explicit curation authority for this exact surface; and
- successful current proposal validation in the temporary full-package copy.

Validator success is necessary but not sufficient for adoption.

### 6. Validate and write atomically at the workflow level

Prepare a full temporary copy of the `npa-mathlib` package, including its
manifest, checked package lock, and complete current proposal set, then replace
only the candidate proposal in that copy. Run the current-only validator with
the temporary package as `--root` and its confined `interface-proposals`
directory as `--proposal-root`. For a revision, also run transition validation
against the explicitly supplied immediately previous proposal root. Inspect
the deterministic proposal-file and proposal-set hashes.

Only after the candidate passes may an explicitly authorized action update the
tracked canonical proposal. Re-run the same validator against the tracked tree.
Do not infer the previous root from Git, fetch it implicitly, normalize bytes
before hashing, or hand-edit a generated proposal index.

### 7. Produce the implementation handoff

For an adopted proposal, record:

- canonical proposal path, revision, and exact SHA-256 file hash;
- artifact owner and selected route;
- target module and complete public/support declaration inventory;
- exact signatures, bodies, families, dependencies, and imports;
- permitted axioms, proof references, exclusions, and compatibility impact;
- current/transition validator results and proposal-set hash; and
- the condition that any changed proposal byte or target surface invalidates
  the handoff.

The handoff may be declared `handoff_pinned` only when the proposal is tracked,
adopted, current-valid, and its recorded hash matches the exact canonical
bytes. The artifact owner, one supported catalog route, current package axiom
boundary, exclusions, and compatibility impact must also be resolved. These
handoff requirements do not retroactively become proposal-adoption fields.

### 8. Check a prepared target when available

If a complete target already exists, run
`package check-interface-proposal-surface` with the exact proposal path, hash,
and target module. Require `status = "parity"` before passing the target to an
artifact route.

Surface parity compares the adopted contract to target declarations and is
still `proof_evidence = false`. Do not run reconciliation apply, claim package
verification, or advance an artifact stage in `interface` mode.

## Handoff To Artifact Routes

### Source-backed route

Use this route only after a concrete artifact exists in `npa-corpus` or an
`npa-project-*` package. Re-enter the existing `promote` qualification from the
beginning. The adopted proposal influences public mapping but substitutes for
none of the verified matrix, provenance, collision, import, axiom, temporary
materialization, attestation, or target gates.

The promotion audit must record the exact proposal path and hash. Until a
future promotion-plan schema binds that hash directly, the audit/handoff plus
pre- and post-materialization exact-surface checks provide traceability; they
must not be described as proof binding.

### Direct-target route

Use this route only after `npa-mathlib` owns a complete, strictly newer,
unpublished target with source, canonical certificates, metadata, replay,
manifest entries, generated projections, and applicable smoke fixtures.
Interface mode may verify parity, but target preparation and package gates are
outside this mode.

After separate authorization, the direct catalog workflow runs reconciliation
dry-run and apply against an explicit previous target. Reconciliation owns its
attestation and registry event and remains registry-last. It does not grant L2
maturity or publish a release.

## Failure Boundary

Before the first tracked proposal or handoff write, classify inventory,
curation, validation, hash, collision, or temporary-candidate failures as
`interface-candidate-local/pre-write` after confirming tracked repositories did
not change.

After the first tracked write, any proposal validation, hash mismatch,
concurrent-edit, handoff, Git, or surface-check failure is
`interface-campaign-stop/post-write`. Preserve diagnostics and do not enter an
artifact route. If unsure whether a write occurred, use the post-write class.

An interface failure never invalidates an already released artifact. A target
package or certificate failure belongs to the later artifact campaign and must
not rewrite interface evidence into proof evidence.

## Output Contract

Every `interface` run reports:

```text
Mode: interface
Action: survey | draft | adopt | lifecycle | handoff
Interface recommendation: ...
Proposal path and identity: ...
Proposal lifecycle: absent | observed | proposed | adopted | withdrawn | superseded
Current validation: not run | passed with proposal-set hash | failed
Transition validation: not requested | passed | failed
Curation authority: not requested | explicit | unresolved
Evidence and licensing: ...
Exact surface and imports: ...
Namespace/collision result: ...
Artifact owner and route: unresolved | source-backed | direct-target
Handoff: unavailable | pinned with proposal hash
Prepared-target surface: unavailable | parity | drift
Proof evidence: false
Artifact promotion stage: not entered
Tracked writes: ...
Next action: ...
```

Do not report `materialized_verified` merely because the proposal is adopted,
the handoff is pinned, or surface parity passes. If the same user request also
authorized an artifact phase, report that phase separately with the existing
artifact output contract.

## Required Skill Changes

Implementing this design requires these scoped skill-package changes:

1. Add `references/interface.md` to the skill with the operational form of
   this contract.
2. Extend the skill description to include interface research, proposal
   curation, adoption, and handoff without describing them as verified
   artifact ingestion.
3. Add `interface` to “Select the Mode,” including the read-only default and
   explicit adoption-authority boundary.
4. Make “Shared Preflight” conditional: artifact-package discovery remains
   mandatory for artifact modes, while interface mode requires the proposal
   root and only the evidence checkouts or artifact packages actually in
   scope.
5. Split “Decision Boundary” into artifact qualification and interface
   curation outcomes so `Ingest into mutable catalog` is never emitted from
   interface-only work.
6. Extend failure and completion reporting with the interface-specific
   boundary and output fields above.
7. Update `agents/openai.yaml` so the visible description mentions interface
   adoption while retaining verified artifact ingestion as a separate
   capability.
8. Cross-link this mode design from the interface-adoption design and the
   canonical proposal README; keep the skill reference operational rather than
   duplicating the full design rationale.

No Rust CLI or proposal-schema change is required for the first skill-only
integration. The implemented inventory, proposal-validation, hashing, and
surface-parity commands already provide the required mechanics.

## Scenario Tests

Review the implemented skill against at least these scenarios:

1. Bare `$promote-to-mathlib interface` selects read-only `survey`.
2. “Review this proposal” does not write or adopt it.
3. “Draft a proposal” may create `observed` or `proposed` but not `adopted`.
4. “Adopt this exact surface” fails closed when curation authority, exact NPA
   terms, alternatives, licensing, imports, or adoption evidence are
   unresolved.
5. Successful adoption reports `proof_evidence = false` and
   `Artifact promotion stage: not entered`.
6. Adoption may precede artifact-owner and route selection, but
   `handoff_pinned` remains blocked until both are resolved.
7. A changed proposal increments the revision and binds the exact previous
   bytes; a skipped revision or wrong hash fails before tracked apply.
8. A terminal proposal cannot be edited or reused.
9. Explicit rework, withdrawal, and supersession select `lifecycle` and obey
   the canonical transition and reciprocal-link rules.
10. A target collision or proposed import cycle blocks adoption.
11. A prepared target with surface drift returns to proposal rework and never
   enters artifact promotion.
12. A prepared target with parity still does not establish `verified`.
13. A source-backed artifact handoff re-enters full qualification and runs all
    existing certificate-first gates.
14. A direct target handoff does not invoke reconciliation apply without
    separate authorization.
15. A request combining adoption and promotion reports two independent phases
    and stops if the proposal hash changes between them.
16. Existing catalog modules remain valid without retroactive proposals.

## Acceptance Criteria

The mode design is correctly implemented when:

- the skill can select `interface` without pretending an artifact closure
  exists;
- mutation intent is explicit and adoption authority is stronger than draft
  authority;
- canonical proposal lifecycle, revision, hashing, validation, and evidence
  rules are reused unchanged;
- adoption does not require an artifact owner or route, while a ready handoff
  requires both;
- every interface-only completion explicitly reports non-proof status and no
  artifact promotion stage;
- handoff output binds the exact tracked adopted proposal bytes;
- source-backed and direct-target routes remain separate and retain all current
  artifact gates;
- a proposal or parity result can never establish catalog maturity, registry
  admission, or release state; and
- the scenario tests above are documented as passing without requiring a Rust
  CLI or schema change.

## Deferred Extensions

The following remain outside the first skill integration:

- a versioned machine-readable implementation-handoff schema;
- direct proposal-hash fields in promotion plan and materialization
  attestation schemas;
- canonical JSON projection of proposal records;
- additional prover-ecosystem inventory adapters;
- a versioned named-curator decision record;
- automated evidence-quality scoring; and
- an explicitly authorized multi-proposal research campaign that still
  requires separate human adoption decisions.

None of these extensions is required to add the bounded `interface` mode.
