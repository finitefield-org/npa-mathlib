# NPA Mathlib Interface Proposals

Status: v1 contract implemented and frozen by `UIA-01` through `UIA-06` and
`UIA-16` on 2026-08-02. The contract remains the curation boundary for future
proposals; it does not add a proposal-specific proof or release state.

This directory contains manually curated interface proposals for future
`npa-mathlib` modules. A proposal is a curation sidecar, not an NPA source
module, certificate, proof, catalog admission, L2 review, or release record.
Every proposal and every derived audit record that has a `proof_evidence`
field must set it to the literal boolean `false`.

The public catalog continues to own only the `Mathlib.*` namespace. Catalog
admission, package-version changes, promotion-origin reconciliation, L2
acceptance, and snapshot publication remain governed by the existing catalog
and namespace policies. This document fixes the v1 proposal contract and the
read-only validator contract; it does not authorize any catalog mutation.
The proposed skill orchestration is specified separately in
[`promote-to-mathlib-interface-mode-design.md`](../docs/promote-to-mathlib-interface-mode-design.md).

## Canonical layout and scan domain

The canonical root is `npa-mathlib/interface-proposals/`. The only proposal
records are UTF-8 TOML files in the exact scan domain
`interface-proposals/Mathlib/**/*.toml`:

```text
interface-proposals/
├── README.md
├── Mathlib/
│   └── <module path>.toml
└── generated/
    └── index.json                 # optional disposable projection
```

The module-to-file mapping is exact. For example,
`Mathlib.Data.Nat.Parity` maps to
`interface-proposals/Mathlib/Data/Nat/Parity.toml`. The proposal root is
case-sensitive and uses `/` separators. A canonical proposal path must be
valid UTF-8, contain no tab or newline, and contain no `.` or `..` path
component. `README.md`, files below `generated/`, source files, replay files,
metadata, certificates, and files outside the `Mathlib/**/*.toml` domain are
not proposal records and are excluded from proposal-file and proposal-set
hashes.

The v1 check command never creates or updates `generated/index.json`. It
computes the ordered rows and proposal-set hash in memory. A writer or a
canonical JSON projection requires a separately versioned contract.

Proposal records do not belong in `npa-package.toml`,
`promotion-origins.json`, `generated/theorem-index.json`, or
`docs/namespace-policy.md`; those files describe package artifacts, transaction
owned provenance, implemented declarations, and global namespace rules.

## Frozen resource limits

All byte limits below count UTF-8/input bytes, not Unicode scalar values. The
raw TOML file-size limit is checked before parsing. Collection limits are
checked before nested values are cloned into typed vectors. These limits apply
independently to the current and previous proposal sets.

| Limit | Exact bound | Unit and scope |
| --- | ---: | --- |
| `max_proposal_files` | `4096` | proposal files per scanned set |
| `max_proposal_file_bytes` | `262144` | raw bytes per TOML proposal file (256 KiB) |
| `max_proposal_set_bytes` | `67108864` | raw bytes across one scanned set (64 MiB) |
| `max_declarations` | `256` | declaration rows per proposal |
| `max_observations` | `512` | observation rows per proposal |
| `max_proof_references` | `256` | proof-reference rows per proposal |
| `max_alternatives` | `128` | alternative rows per proposal |
| `max_imports` | `128` | direct imports per proposal |
| `max_links_per_array` | `256` | entries in each `depends_on`, `evidence_ids`, `proof_reference_ids`, `family_members`, `supersedes`, `superseded_by`, or alternative `evidence_ids` array |
| `max_path_bytes` | `1024` | each canonical, repository-source, or proof-reference path value |
| `max_string_bytes` | `16384` | each non-path scalar string value |
| `max_interface_file_bytes` | `16777216` | each local certificate/interface file read for name or signature resolution (16 MiB) |
| `max_diagnostics` | `1024` | diagnostics emitted for one command result |
| `max_diagnostic_value_bytes` | `256` | each rendered `expected` or `actual` diagnostic value after sanitization |

`max_links_per_array` is also the bound for any future v1 set-like link array
whose field name is already part of this contract; an unlisted collection is
not silently unlimited. A file that reaches any limit fails with the matching
resource reason code. The validator must not allocate an unbounded nested
collection while reporting that failure.

## Proposal file contract

Every canonical TOML file uses exactly the schema string
`npa.mathlib.interface_proposal.v1`. Unknown fields at any table depth,
duplicate keys, wrong TOML types, invalid UTF-8, and an unknown schema version
are errors. Empty strings are not accepted where a field is required. Array
order is meaningful unless this document explicitly says that an array is
sorted; the validator preserves declaration, import, and inductive-family
order.

### Top-level fields

The top-level fields are:

| Field | TOML type | Contract |
| --- | --- | --- |
| `schema` | string | Required; exactly `npa.mathlib.interface_proposal.v1`. |
| `proposal_id` | string | Required stable logical ID. On revision 1 it equals `module`; it must not be silently reused for unrelated mathematics after withdrawal or supersession. |
| `proposal_revision` | positive integer | Required; starts at `1` and increases by exactly one for every changed canonical record. |
| `previous_proposal_hash` | string | Omitted at revision 1. Required at every later revision and equal to the immediately previous canonical file hash. |
| `module` | string | Required exact target module name beginning with `Mathlib.`. |
| `change_kind` | string enum | Required; one of `add`, `revise`, `rename`, `split`, `merge`, or `replace`. |
| `source_modules` | string array | Required, unique and sorted by UTF-8 byte order; cardinality is determined by `change_kind`. |
| `change_group` | string | Required and nonempty only for `split`; omitted for every other change kind. |
| `interface_status` | string enum | Required; one of `observed`, `proposed`, `adopted`, `withdrawn`, or `superseded`. |
| `proof_evidence` | boolean | Required; exactly `false`. |
| `summary` | string | Required one-sentence mathematical description. It must not claim proof, verification, maturity, or release. |
| `scope` | string | Required description of what belongs in the module and, when useful, what is excluded. |
| `imports` | string array | Required direct public import boundary. Entries are unique, retain semantic dependency order, and must resolve to current catalog `Mathlib.*` modules, other adopted proposal `Mathlib.*` modules, or hash-pinned `Std.*` imports. |
| `adoption_date` | string | Required for `adopted` and retained for `superseded`; ISO calendar date `YYYY-MM-DD`, with no time or timezone. |
| `adoption_rationale` | string | Required for `adopted` and retained for `superseded`; explains the selected surface and alternatives. |
| `re_adoption_rationale` | string | Required only when a previous `adopted` record becomes `adopted` again in a two-root comparison; explains the completed rework. |
| `withdrawal_rationale` | string | Required for `withdrawn`; explains why the unadopted record is not pursued. |
| `alternatives_review` | string | Required for `proposed`, `adopted`, and `superseded`; states the naming, signature, and module-boundary alternatives considered, including an explicit no-material-alternative statement when applicable. |
| `supersedes` | string array | Required; proposal IDs replaced by this proposal. It is duplicate-free and may be empty. |
| `superseded_by` | string array | Required only for `superseded`; nonempty, duplicate-free, and sorted by UTF-8 byte order. Omitted for other statuses. |
| `declarations` | array of tables | Logical collection of target declarations; absent means empty. Nonempty for `proposed` and `adopted`. |
| `observations` | array of tables | Logical collection of pinned use and declaration observations; absent means empty. |
| `proof_references` | array of tables | Logical collection of pinned proof references; absent means empty and permitted before adoption. |
| `alternatives` | array of tables | Optional rejected or deferred alternatives; absent means empty. |

The following array-of-table collections are logical fields. An absent
collection means an empty collection; any present rows must use exactly the
field contracts below: `declarations`, `observations`,
`proof_references`, and `alternatives`. `declarations` must be nonempty for a
`proposed` or `adopted` record. An `observed` or early `withdrawn` record may
have no declaration rows while its surface is still unresolved. Observations
may be empty while an `observed` or `proposed` record is still being
researched. For `adopted` and `superseded`, observations may be empty only when
every public declaration has a valid foundation exception. Proof references
are optional before adoption; alternatives are optional at every status,
subject to the required `alternatives_review` text.

The conditional fields above are rejected when present in a status where they
do not apply, except that an adopted decision's fields are retained by its
`superseded` historical record. A revision changes the exact canonical bytes;
the previous hash is over those immediately preceding bytes, not over a
normalized or parsed representation. Proposal IDs are globally unique in one
proposal set, and active target modules are unique across the set. A terminal
proposal ID cannot reappear as a new record.

### Change kinds and source-module cardinality

`change_kind` is relative to current catalog HEAD. It does not authorize the
corresponding catalog transaction.

| Change kind | `source_modules` rule | Meaning |
| --- | --- | --- |
| `add` | empty | Before materialization, the target has no current catalog route. After the target is admitted, the adopted record remains as historical curation metadata and is rechecked by the exact-surface gate. |
| `revise` | exactly `[module]` | The module name remains, but its exported surface, imports, proof artifact, or other identity-bearing content changes. |
| `rename` | exactly one old module, different from `module` | One current module moves to a meaning-equivalent target name. |
| `replace` | exactly one old module, different from `module` | One current module is retired for a materially different successor. |
| `split` | exactly one old module | Two or more target proposals in the same nonempty `change_group` replace one current module. |
| `merge` | at least two old modules | Two or more current modules are retired for one target. |

An unmaterialized proposal reorganized into new targets remains an `add`
proposal and uses proposal supersession links; it does not claim a catalog
`split`. Rename, replace, split, merge, revise, and retirement still require
the versioned promotion-origin registry transaction. A catalog `split` is
valid only when the set contains at least two target proposals carrying the
same nonempty `change_group`.

An adopted `add` proposal is retained after its target is materialized. The
read-only proposal validator accepts that one post-materialization state so a
current-only check can continue to validate the canonical proposal set. It
does not inspect certificates or prove that the target matches the proposal;
the operator must run `check-interface-proposal-surface` with the exact
proposal hash before and after the catalog transaction. An `observed` or
`proposed` `add` whose target already exists in the current catalog still fails
the current-catalog relation check. An adopted `add` whose target is absent
remains the ordinary valid pre-materialization state.

### Exact NPA surface terms

For `proposed` and `adopted` records, every declaration has an exact
`signature` and exact same-module `depends_on` list. The signature is a
complete NPA type expression after the declaration name and colon; it is not a
Markdown fragment, source-language quotation, abbreviated notation, or proof
body. It must be parseable by the current NPA frontend against the explicitly
resolved import environment. Binder order, universe binders, implicit
parameters, premises, typeclass-like law arguments, and result shape are part
of the interface.

An adopted `definition` additionally has `body`, a complete NPA term accepted
after `:=`. An adopted `inductive` additionally has `family_members`, the
complete ordered list of constructor, recursor, projection, and other
generated public names that the target certificate is expected to expose. A
family list is not alphabetized; its semantic/generated order is preserved and
duplicates are rejected. `superseded` retains the last adopted signatures,
bodies, and family lists. `observed` may omit unresolved signatures; a
`withdrawn` record may omit them if it was withdrawn before a complete
proposal and otherwise retains the last unadopted surface. `proposed` and
`adopted` may not omit them. Superseded signatures, bodies, and family lists
must remain syntactically valid, but their names and imports need not resolve
against current catalog HEAD after the historical route has been retired.

The v1 public surface contains only `inductive`, `definition`, and `theorem`
declarations. A declaration name is an unqualified NPA identifier. A
definition body or signature containing `...`, prose placeholders, unresolved
source notation, or a quotient-backed public construction is invalid. Public
interfaces must use setoid formulations where an equivalence relation is
needed; this proposal workflow does not add quotient constructions.

### Declaration rows

Each `[[declarations]]` row has the following fields:

| Field | Requiredness and meaning |
| --- | --- |
| `name` | Required unqualified NPA identifier, unique within the proposal. |
| `kind` | Required enum: `inductive`, `definition`, or `theorem`. |
| `surface` | Required enum: `public` or `support`. |
| `signature` | Required for `proposed`, `adopted`, and `superseded`; forbidden only when the status contract permits an unresolved surface. |
| `body` | Required for an adopted or superseded `definition`; exact NPA body. |
| `family_members` | Required for an adopted or superseded `inductive`; complete ordered generated family. |
| `semantic_role` | Required explanation of why the declaration belongs in this module. |
| `depends_on` | Required for `proposed`, `adopted`, and `superseded`; same-module declaration names required by the signature or intended proof surface. |
| `evidence_ids` | Required observation IDs supporting the declaration. A public declaration needs at least one unless `foundation_exception` is present. |
| `foundation_exception` | Optional nonempty rationale for a foundational public primitive for which direct downstream use is not expected. |
| `support_rationale` | Required for an unevidenced `support` declaration; it explains the required support closure. |
| `proof_reference_ids` | Required and nonempty for every `theorem` in `adopted` or `superseded`, unless `proof_reference_exception` is present. |
| `proof_reference_exception` | Optional nonempty explanation for an immediately constructible theorem with no separate source proof. |

An empty public evidence set fails unless the foundation exception is valid. A
support declaration may have empty evidence only when it is reachable from a
public declaration's dependency/family closure and has a nonempty support
rationale. The adopted list is the intended exported surface; implementation
discovery of an additional exported support declaration or family member
requires return to `proposed` and re-adoption.

### Observation rows

Each `[[observations]]` row is a proposal-local, unique evidence record with:

| Field | Contract |
| --- | --- |
| `id` | Required unique local ID. |
| `repository` | Required repository URL or local repository identity; it is provenance, not a network lookup instruction. |
| `revision_kind` | Required enum: `git_commit` or `release_digest`. |
| `revision` | Required immutable locator. A Git revision is a full 40-character lowercase hexadecimal commit; a release digest is `sha256:` followed by exactly 64 lowercase hexadecimal characters. |
| `license` | Required known license identifier or the literal `UNKNOWN`. `UNKNOWN` requires a follow-up note and cannot support an adopted decision when the record is required by that decision. |
| `path` | Required source location, relative to the pinned repository or release; path limit and control-character rules apply. |
| `source_module` | Required when the usage identifies a module; omitted only when genuinely inapplicable. |
| `source_declaration` | Required for `declaration`, `direct_application`, `rewrite`, `instance_dependency`, and `transitive_dependency`; omitted for `module_import` and `module_layout`. |
| `usage_kind` | Required enum: `declaration`, `module_import`, `direct_application`, `rewrite`, `instance_dependency`, `transitive_dependency`, or `module_layout`. |
| `notes` | Required concise observation explaining the exact use or boundary. |

Floating branches, movable tags, default branches, search URLs, and prose with
no resolvable location are insufficient for an adopted record. Observations
are design evidence only and never proof evidence.

### Proof-reference rows

Each `[[proof_references]]` row is separate from usage evidence and has a
proposal-local unique `id`, `repository`, `revision_kind`, immutable
`revision`, `license`, `path`, `source_declaration`, `reference_role`, and
`notes`. `source_module` may be included when needed to identify the proof
location. `reference_role` is one of `proof_structure`, `lemma_choice`,
`induction_scheme`, or `normalization_strategy`.

At adoption, every theorem in an adopted or superseded proposal resolves all
of its `proof_reference_ids`, unless its documented proof-reference exception
applies. A proof reference may guide a new NPA proof, but it is not an NPA
certificate, proof verification result, source-backed promotion origin, or
permission to copy source whose licensing is unresolved.

### Alternative rows

`[[alternatives]]` is optional, but `alternatives_review` is required at the
reviewable statuses. Each alternative row has:

- `kind`: `module_name`, `declaration_name`, `signature`, or
  `module_boundary`;
- `candidate`: the rejected or deferred candidate;
- `disposition`: `rejected` or `deferred`;
- `rationale`: why it was not selected now; and
- `evidence_ids`: relevant observation IDs.

Alternative evidence IDs must resolve locally. An explicit statement that no
material alternative was found is valid when the survey supports it.

## Lifecycle and revision rules

The lifecycle vocabulary is intentionally disjoint from catalog maturity
(`verified`, `reviewed`, `recommended`) and promotion stages
(`structural_candidate`, `verified_candidate`, `catalog_ingest_approved`,
`materialized_verified`, and `snapshot_released`).

```text
observed -> proposed -> adopted
    |           |          |  \
    v           v          v   v
 withdrawn   withdrawn  proposed  superseded
                         (rework)
```

- `observed` means evidence exists but the complete target interface is not
  decided.
- `proposed` means a target module and complete surface are available for
  review, but adoption has not been approved.
- `adopted` means the exact surface is an implementation contract; it is not
  a proof or catalog artifact.
- `withdrawn` is terminal history for an unadopted record.
- `superseded` is terminal history for an adopted record replaced by one or
  more different target proposals.

Same-module implementation rework uses `adopted -> proposed`, not
`superseded`. Before materialization this is a status-only return to review;
after materialization the canonical record also uses
`change_kind = "revise"` and `source_modules = [module]`. A persisted
comparison may show `adopted -> adopted` only when
the revision increments by one, `previous_proposal_hash` binds the old bytes,
fresh adoption fields are present, and `re_adoption_rationale` explains the
completed re-adoption. Cross-module replacement uses explicit reciprocal
`supersedes`/`superseded_by` links. A terminal record remains byte-identical
after it first becomes terminal, and a terminal proposal ID is never reused.

Implementation state is not another proposal status. Package manifests,
canonical certificates, promotion records, registries, and released snapshots
remain authoritative for artifact state.

Revision 1 has no `previous_proposal_hash`. Every later changed record has
exactly the previous revision number plus one and a hash equal to the exact
previous file bytes. A parsed/normalized hash is never substituted for the
raw-byte hash.

## Hash contracts

The proposal-file hash is SHA-256 over the exact tracked UTF-8 TOML bytes,
rendered as `sha256:` followed by exactly 64 lowercase hexadecimal characters.

The proposal-set hash is SHA-256 over these exact UTF-8 bytes, including the
final newline on every row:

```text
npa.mathlib.interface_proposal_set.v1\n
<relative-path>\t<proposal-file-hash>\n
...
```

Rows are sorted by relative-path byte order and include every canonical TOML
proposal, including `withdrawn` and `superseded` records. The hash uses the
rendered `sha256:<hex>` file hash in each row. README files, generated indexes,
and noncanonical files do not contribute rows. The set hash is curation
traceability only and cannot establish proof authority, catalog maturity, or
release identity.

## Read-only validator command

The v1 public command is:

```sh
npa package check-interface-proposals \
  --root <root> \
  [--proposal-root <path>] \
  [--previous-proposal-root <path>] \
  --json
```

Path-option rules are fixed as follows:

- `--root` is required. It identifies the local NPA package root and is the
  anchor for the default proposal root.
- `--proposal-root` is optional and defaults to `interface-proposals`. A
  relative value is resolved against `--root`; the resulting current proposal
  root must be a real, confined directory, not a symlink or a path escape.
- `--previous-proposal-root` is optional and has no default. A relative value
  is resolved against `--root`; it must identify a distinct local proposal
  root. The caller supplies the immediately preceding validated proposal-set
  snapshot. It may be a sibling snapshot outside the current package root,
  but it must still be a real directory and is read only through the same
  canonical scan rules.
- `--json` is required in v1. There is no second human-output format and no
  implicit network, Git, or branch-resolution mode.

The command is read-only and network-free. It does not write files, invoke Git,
inspect branches/tags/remotes, fetch repositories, resolve remote objects,
run a proof checker, run `build-certs`, or treat command success as proof
verification or catalog admission.

## Operator recipes

### Survey and manual adoption

Inventory a pinned local checkout with the selected UIA-13 adapter, then review
the normalized rows against the exact declaration, use-site, license, and
alternative evidence before editing a canonical TOML proposal:

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

The adapter never writes a proposal or status. A curator copies only reviewed
rows into `interface-proposals/Mathlib/**/*.toml`, completes exact NPA terms,
alternatives, imports, and proof-reference separation, then changes
`interface_status` to `adopted` only after the manual checklist below passes.

### Current-only and previous-root validation

Validate the current proposal set without consulting history:

```sh
cargo run --locked --offline -q --manifest-path npa-core/Cargo.toml -p npa-cli -- \
  package check-interface-proposals \
  --root npa-mathlib --proposal-root interface-proposals --json
```

For a transition, the caller supplies the immediately preceding validated
proposal root. The previous root is read-only, distinct, and is not inferred
from Git:

```sh
cargo run --locked --offline -q --manifest-path npa-core/Cargo.toml -p npa-cli -- \
  package check-interface-proposals \
  --root npa-mathlib --proposal-root interface-proposals \
  --previous-proposal-root ../../npa-mathlib-0.2.4/interface-proposals --json
```

Both results are curation metadata with `proof_evidence = false`. The two-root
mode rejects locally detectable revision, identity, lifecycle, terminal-record,
and hash-chain errors; it cannot detect an omitted unrelated addition-only
snapshot.

### Handoff hash and exact surface

Before implementation, pin the canonical proposal bytes and later compare the
adopted surface to the owner target:

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

The surface command is read-only and must return `status = "parity"` before a
route is accepted. A byte change requires a new proposal revision and a new
handoff; changing only a handoff hash is not an authorization.

### Local read set for interface resolution

The validator may read only:

1. the current and optional previous proposal roots, their directory entries,
   and exact bytes of files in `Mathlib/**/*.toml`;
2. `<root>/npa-package.toml` to resolve current local module names, direct
   imports, declaration names/kinds, and the package axiom policy;
3. `<root>/generated/package-lock.json` and the explicitly referenced local
   package-lock metadata needed to resolve hash-pinned import identities; and
4. canonical `.npcert` files named by those validated local package metadata
   records, including an explicitly vendored external certificate, only to
   inspect exported names, kinds, signatures, family members, and imports for
   interface resolution.

Certificate bytes are not verified by this command. The canonical certificate,
Rust kernel, cache-off source-free checker, hash, import, and axiom gates remain
the only proof authority. The validator must not read or depend on
`source.npa`, `replay.json`, `meta.json`, generated theorem indexes,
promotion-origin registries, arbitrary documentation, unrelated authoring
sidecars, `.git`, or any remote URL to resolve an interface. It must not read
an arbitrary path merely because a proposal mentions it as evidence.

### Deterministic JSON result

The JSON object has this exact top-level field order:

1. `schema`
2. `proof_evidence`
3. `status`
4. `current`
5. `previous`
6. `diagnostics`

The values are:

```json
{
  "schema": "npa.mathlib.interface_proposal_check.v1",
  "proof_evidence": false,
  "status": "ok",
  "current": {
    "proposal_count": 0,
    "status_counts": {
      "observed": 0,
      "proposed": 0,
      "adopted": 0,
      "withdrawn": 0,
      "superseded": 0
    },
    "proposal_rows": [],
    "proposal_set_hash": "sha256:<64 lowercase hex>"
  },
  "previous": null,
  "diagnostics": []
}
```

`current` and, when supplied, `previous` have the exact nested field order
`proposal_count`, `status_counts`, `proposal_rows`, `proposal_set_hash`.
`proposal_count` counts discovered canonical TOML files. Each
`proposal_rows` entry is ordered by path bytes and has fields in this order:
`path` (relative to the proposal root), `file_hash`, `proposal_id`, `module`, `proposal_revision`,
`interface_status`. The last four values are `null` when discovery succeeded
but parsing did not produce that metadata. `status_counts` counts successfully
parsed records only and always uses the five lifecycle keys in the order
shown. The set hash is `null` only when the complete canonical file scan could
not be completed.

`previous` is `null` when no previous root was supplied. When supplied it has
the same shape as `current`, including its own proposal rows and set hash. The
result contains no absolute paths, timestamps, process IDs, Git identities,
environment values, or remote locators.

`status` is `ok` exactly when no diagnostic exists; otherwise it is `invalid`.
The v1 exit status is `0` for `ok`, `1` for a readable input with one or more
validation diagnostics, and `2` for command usage or an unreadable required
root. A JSON result is still emitted for a structured input/validation failure
when the output stream is available.

Diagnostics are ordered by sanitized proposal-relative `path` bytes, then
`category`, `reason`, and `field` bytes. Every diagnostic has this exact field
order and shape:

```json
{
  "category": "contract",
  "reason": "missing_field",
  "path": "Mathlib/Data/Nat/Parity.toml",
  "field": "summary",
  "expected": "nonempty string",
  "actual": null
}
```

`path`, `field`, `expected`, and `actual` are JSON strings or `null`.
Diagnostic values are sanitized, bounded by `max_diagnostic_value_bytes`, and
never contain an absolute filesystem path. Reason codes are the stable API;
human wording may be added only in a later result-schema version.

### Implemented command and UIA-11 test traceability

The v1 command is implemented by the source `npa-cli 0.7.x` package command.
The compact repository fixture used by the documentation and command tests is:

```sh
cargo run --locked --offline -q --manifest-path npa-core/Cargo.toml -p npa-cli -- \
  package check-interface-proposals \
  --root npa-core/testdata/package/interface-proposals-valid \
  --proposal-root proposals --json
```

This command reads the selected package manifest and canonical proposal tree
only. It does not dereference the `repository` URLs in observations or proof
references, inspect `.git`, invoke Git or a checker, read source/replay/meta
sidecars, or write generated output. A successful result is curation metadata
with `proof_evidence = false`; it is not proof verification, catalog admission,
or an adoption/maturity decision.

The design `Test Strategy` is covered by the following named tests. Tests
under `npa-package` cover parser and record validation; tests under `npa-cli`
cover filesystem, frontend, catalog, transition, and public-command behavior.

| Design case | Named test |
| --- | --- |
| Five lifecycle statuses and six change kinds | `interface_proposal::tests::parses_every_status_and_change_kind`; `interface_proposal::tests::validates_every_status_and_change_kind_fixture`; `package_interface_proposals::catalog::accepts_all_change_kinds_and_adopted_proposal_imports` |
| Adopted rework, cross-module supersession, and re-adoption | `package_interface_proposals::transition::accepts_rework_readoption_withdrawal_and_one_to_many_supersession` |
| Current-only and previous-root validation | `package_interface_proposals::pilot_json_is_stable_and_has_no_absolute_paths`; `package_interface_proposals::previous_snapshot_mode_checks_identical_valid_snapshots_without_git`; `package_interface_proposals::transition::rejects_revision_identity_removal_and_new_record_continuity_errors` |
| Schema, unknown fields, proof boundary | `interface_proposal::tests::rejects_unknown_schema_and_unknown_fields_at_every_table_depth`; `package_interface_proposals::compact_invalid_fixture_rejects_proof_boundary_metadata` |
| Symlink, non-regular entry, path escape, invalid UTF-8, wrong extension | `package_interface_proposals::discovery::rejects_missing_or_escaped_roots_without_creating_paths`; `package_interface_proposals::discovery::rejects_wrong_extensions_invalid_utf8_and_control_paths`; `package_interface_proposals::discovery::rejects_symlinked_entries_and_non_directory_module_root` |
| Count, byte, string, and nested collection limits | `package_interface_proposals::discovery::enforces_file_and_set_byte_limits`; `package_interface_proposals::discovery::enforces_proposal_count_limit`; `interface_proposal::tests::enforces_file_string_and_path_limits_before_typed_clones`; `interface_proposal::tests::enforces_nested_collection_limits_before_collecting_rows` |
| Module/path, namespace, and declaration contracts | `package_interface_proposals::catalog::rejects_path_ids_and_declaration_contract_collisions`; `package_interface_proposals::surface::rejects_kind_mismatched_definition_body` |
| Duplicate modules/IDs/declarations and catalog collisions | `package_interface_proposals::catalog::rejects_path_ids_and_declaration_contract_collisions`; `package_interface_proposals::catalog::rejects_bad_catalog_relations_imports_and_incomplete_split_groups` |
| Missing/placeholder/syntax-invalid/import-unresolvable surfaces | `package_interface_proposals::surface::distinguishes_surface_syntax_local_dependency_and_import_failures`; `package_interface_proposals::surface::rejects_an_undeclared_import_before_frontend_resolution`; `interface_proposal::tests::permits_unresolved_surfaces_only_for_observed_and_early_withdrawn` |
| Definition bodies and inductive families | `package_interface_proposals::surface::keeps_superseded_validation_syntax_only_and_checks_inductive_kind`; `package_interface_proposals::surface::classifies_definition_body_resolution_failures_at_the_body_path`; `interface_proposal::tests::rejects_duplicate_families_alternative_identities_and_unsorted_sets` |
| Foundation exceptions and support rationales | `interface_proposal::tests::enforces_evidence_foundation_and_support_reachability` |
| Theorem proof references and exceptions | `interface_proposal::tests::validates_revision_locators_licenses_cycles_and_proof_references` |
| Alternatives review and evidence links | `interface_proposal::tests::rejects_duplicate_families_alternative_identities_and_unsorted_sets`; `interface_proposal::tests::parses_all_table_kinds_and_optional_fields` |
| Floating revisions and missing licenses | `interface_proposal::tests::validates_revision_locators_licenses_cycles_and_proof_references` |
| Transition metadata, revisions, hashes, terminal records, removals | `package_interface_proposals::transition::rejects_revision_identity_removal_and_new_record_continuity_errors`; `package_interface_proposals::transition::rejects_invalid_lifecycle_surfaces_terminal_edits_and_same_roots` |
| Source-module cardinality and split groups | `package_interface_proposals::catalog::rejects_bad_catalog_relations_imports_and_incomplete_split_groups` |
| Unresolved imports and import cycles | `package_interface_proposals::catalog::rejects_bad_catalog_relations_imports_and_incomplete_split_groups`; `package_interface_proposals::catalog::rejects_nonreciprocal_supersession_and_import_cycles_deterministically` |
| Exact file and proposal-set hash vectors | `package_interface_proposals::discovery::scans_only_canonical_toml_files_in_relative_byte_order`; `interface_proposal::tests::interface_proposal_hashes_exact_bytes_and_bounds_diagnostic_values` |
| Public JSON rows, no-write, path sanitization, and trust boundary | `package_interface_proposals::pilot_json_is_stable_and_has_no_absolute_paths`; `package_interface_proposals::malformed_proposal_returns_null_metadata_and_does_not_write`; `package_interface_proposals::compact_fixture_command_is_local_network_free_and_no_write` |
| Surface drift comparison | Implemented by UIA-15/UIA-16; coverage is `package_interface_proposals::surface::accepts_the_current_pilot_surface_with_supplied_import_context`, `package_interface_proposals::surface::preserves_universe_parameters_when_building_pilot_surface_source`, and `package_interface_proposals::surface::distinguishes_surface_syntax_local_dependency_and_import_failures`. |

### Previous-snapshot comparison boundary

The caller, not the validator, supplies the immediately preceding validated
proposal set. With no previous root, v1 validates only the current snapshot.
With a previous root, v1 additionally validates lifecycle transitions,
terminal-record preservation, cross-snapshot identity, and hash-bound
revision chains.

The local two-root comparison rejects these detectable per-record skips and
changes:

- a previous canonical record is removed from the current set;
- a current record changes its proposal ID or module identity without the
  permitted explicit supersession relation;
- a changed record does not increment `proposal_revision` by exactly one;
- a later record omits `previous_proposal_hash` or binds the wrong previous
  file hash;
- a terminal record is edited or a terminal proposal ID is reused;
- a status transition is not one of the lifecycle transitions above;
- same-module adopted rework does not pass through `proposed`, or an
  `adopted -> adopted` update lacks fresh adoption fields and
  `re_adoption_rationale`;
- a withdrawal changes the previous unadopted surface instead of retaining it;
  or
- supersession links are missing, unsorted where required, or not reciprocal.

The comparison cannot detect that an unrelated addition-only snapshot was
omitted between the two supplied roots. It does not invoke Git, a history
ledger, a remote repository, or any other history authority, and it must not
claim to detect that omission.

## Stable diagnostic categories and reason codes

Every v1 validation failure uses one category and one lower-case reason code
from this table. The category and reason are machine-readable and stable;
future implementations must not invent a free-form replacement. If a future
failure cannot be represented by this catalog, the command result schema must
be versioned before the new failure is exposed.

| Category | Reason codes |
| --- | --- |
| `io` | `root_not_directory`, `proposal_root_not_directory`, `previous_root_not_directory`, `previous_root_same_as_current`, `read_failed`, `catalog_metadata_missing`, `catalog_metadata_invalid` |
| `discovery` | `symlink_entry`, `non_regular_entry`, `path_escape`, `invalid_path_utf8`, `path_contains_tab_or_newline`, `noncanonical_extension`, `proposal_count_exceeded`, `proposal_file_bytes_exceeded`, `proposal_set_bytes_exceeded` |
| `syntax` | `invalid_utf8`, `invalid_toml`, `duplicate_key`, `unknown_field`, `wrong_type`, `unknown_schema` |
| `resource` | `declaration_count_exceeded`, `observation_count_exceeded`, `proof_reference_count_exceeded`, `alternative_count_exceeded`, `import_count_exceeded`, `link_count_exceeded`, `path_bytes_exceeded`, `string_bytes_exceeded`, `interface_file_bytes_exceeded`, `diagnostic_count_exceeded` |
| `contract` | `missing_field`, `empty_value`, `invalid_enum`, `invalid_identifier`, `invalid_module_name`, `module_path_mismatch`, `invalid_revision`, `invalid_hash`, `invalid_date`, `proof_evidence_not_false`, `invalid_change_kind`, `invalid_source_modules`, `invalid_change_group`, `invalid_superseded_by_order`, `invalid_signature`, `placeholder_signature`, `invalid_definition_body`, `placeholder_definition_body`, `invalid_family`, `duplicate_family_member`, `incomplete_family`, `duplicate_declaration_name`, `duplicate_set_member`, `forbidden_quotient_interface` |
| `lifecycle` | `invalid_status`, `status_metadata_missing`, `status_surface_incomplete`, `withdrawal_rationale_missing`, `supersession_metadata_missing`, `readoption_metadata_missing`, `proposal_id_reused`, `terminal_id_reused` |
| `evidence` | `duplicate_evidence_id`, `unresolved_evidence_id`, `missing_public_evidence`, `invalid_foundation_exception`, `invalid_support_rationale`, `support_not_reachable`, `missing_source_location`, `invalid_revision_locator`, `floating_revision`, `license_unknown_without_note`, `license_unknown_blocks_adoption`, `invalid_usage_kind`, `duplicate_proof_reference_id`, `unresolved_proof_reference_id`, `invalid_reference_role`, `missing_proof_reference`, `invalid_proof_reference_exception`, `missing_alternatives_review`, `invalid_alternative_evidence`, `invalid_alternative_disposition` |
| `graph` | `unresolved_dependency`, `dependency_cycle`, `import_unresolved`, `import_cycle`, `public_support_closure_incomplete`, `family_closure_incomplete` |
| `catalog` | `catalog_module_collision`, `catalog_declaration_collision`, `catalog_target_missing`, `catalog_target_exists`, `catalog_source_cardinality`, `forbidden_import`, `active_module_collision` |
| `comparison` | `previous_snapshot_invalid`, `previous_record_removed`, `record_identity_changed`, `revision_not_incremented`, `previous_hash_mismatch`, `terminal_record_changed`, `invalid_status_transition`, `withdrawn_surface_changed`, `supersession_not_reciprocal`, `adopted_rework_not_readopted` |

The `expected` value identifies the frozen rule or bound, and `actual` records
the sanitized observed value when useful. Reason codes do not assert proof
failure: they describe invalid curation metadata, an invalid local snapshot,
or an invalid comparison.

## Manual adoption review checklist

The reviewer completes this checklist before changing a record to
`interface_status = "adopted"`. A second reviewer must be able to reconstruct
the decision from the survey and proposal without another repository search.

1. **Naming and path.** Confirm that the module starts with `Mathlib.`, its
   path mirrors the dotted module name, the name describes current
   mathematical meaning, and the module is one coherent semantic layer. Check
   `Basic`, `Derived`, `Carrier`, `UnitModel`, and other suffixes against the
   namespace policy rather than the source repository's directory layout.
2. **Exact NPA terms.** Parse every signature in the intended imported
   environment. Check binder order, universe parameters, premises,
   typeclass-like law arguments, result types, exact definition bodies, and
   complete inductive family members. Reject pseudo-signatures, placeholders,
   copied source syntax, unsupported constructs, and quotient-backed public
   contracts; use setoids where required.
3. **Public/support boundary.** Confirm that every public declaration has a
   concrete observation or a justified foundation exception. Confirm that
   every support declaration is reachable from the public/family closure and
   has a support rationale when it lacks direct evidence. Check declaration
   dependencies for cycles and unintended exported helpers.
4. **Use-site evidence.** Inspect actual downstream applications, rewrites,
   imports, instance/inference dependencies, and repeated patterns, not only
   declaration definitions. For a non-foundational public surface, confirm at
   least one pinned declaration repository and one concrete downstream use
   site. Confirm that each evidence row has a full immutable revision, source
   path, usage kind, and concise notes.
5. **Alternatives.** Review module names, declaration names, signatures,
   bundled/unbundled forms, argument orders, premise strength, and module
   boundaries. Confirm rejected/deferred alternatives and evidence links, or
   the explicit no-material-alternative statement.
6. **Licensing and provenance.** Confirm repository URLs/identities, full Git
   commits or release digests, known licenses, and required attribution. Keep
   interface evidence ownership, proof-reference ownership, and eventual NPA
   artifact ownership separate. Do not copy external proof source by default.
7. **Proof references.** Review theorem proof-reference links separately from
   use evidence. Confirm each adopted theorem has a pinned proof reference or
   a specific valid exception, and that no reference is represented as NPA
   proof authority or promotion provenance.
8. **Imports and policy.** Confirm every direct import is explicit, resolvable
   from current catalog, another adopted proposal, or hash-pinned `Std.*`
   imports, and acyclic. Confirm the intended implementation route obeys the
   current package axiom policy; v1 proposal TOML does not add an
   `allowed_axioms` field.
9. **Collisions and catalog relation.** Compare module and declaration names
   with current catalog HEAD and active proposals. Check `change_kind`,
   `source_modules`, `change_group`, and supersession links against the exact
   current catalog relation. Resolve collisions before adoption.
10. **Decision record.** Confirm `summary`, `scope`, `alternatives_review`,
    adoption date, rationale, status-specific fields, and proposal revision
    are complete. Confirm the exact file bytes are the reviewed bytes; record
    the proposal hash in any later implementation handoff. Adoption remains a
    curation decision and must not be described as `verified`, `reviewed`,
    `recommended`, or released.

The checklist does not require an NPA proof, certificate, L2 vote, downstream
snapshot, or API-stability claim. Those remain later implementation and catalog
gates.

## Relationship to later work

UIA-02 and UIA-03 consume this contract for survey and manual adoption. UIA-04
through UIA-11 must use the exact limits, reason codes, path rules, read set,
comparison boundary, and JSON field order above. The validator remains a
local curation tool. It must preserve the existing certificate-first catalog
workflow and may not hand-edit package manifests, generated package
projections, promotion registries, or transaction-owned evidence.
