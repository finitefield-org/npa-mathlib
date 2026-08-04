# Usage-Evidence-Driven Interface Adoption Todo

Source: `npa-mathlib/docs/usage-evidence-driven-interface-adoption-design.md`

## Scope

This task breakdown implements the usage-evidence-driven interface-adoption
workflow from manual proposal curation through read-only validation,
repository-inventory assistance, implementation handoff, surface-drift
detection, and one pilot catalog materialization.

The work is split so that an implementation agent can take exactly one
milestone, read its declared inputs, make only the listed changes, and verify
an observable result. A milestone must not begin until every dependency is
complete. When a dependency is a decision milestone, its recorded choices are
inputs rather than matters to decide again.

This plan does not weaken artifact promotion, direct catalog reconciliation,
L2 review, or snapshot-release requirements. It does not make proposal data
proof evidence, copy external proof source, add quotient-backed interfaces,
or promise compatibility for mutable catalog HEAD.

## Global Constraints

- Canonical proposal records live under
  `npa-mathlib/interface-proposals/Mathlib/**/*.toml`, not under `docs/`.
- Every proposal and derived audit record that has such a field uses
  `proof_evidence = false`. Validator success is curation status only.
- Keep the lifecycle vocabulary `observed`, `proposed`, `adopted`,
  `withdrawn`, and `superseded` disjoint from catalog maturity and promotion
  stage names.
- The validator is local, deterministic, read-only, and network-free. It does
  not invoke Git or resolve branches, tags, or remote repository objects.
- Proof authority remains canonical `.npcert` bytes plus kernel, source-free
  checker, hash, import, and axiom results. Proposals, surveys, generated
  indexes, handoffs, and command output remain untrusted sidecars.
- Keep `npa-core` self-contained. Its tests use compact fixtures under
  `npa-core/testdata/` and must not require sibling repositories.
- Outside `npa-web`, implementation code is Rust, Shell, or OCaml. This plan
  uses Rust for validator and adapter code and does not add GitHub Actions CI.
- Do not use Git LFS, add LFS pointer files, or add LFS attributes.
- Do not add quotient-backed public interfaces; use setoid formulations.
- Do not hand-edit `promotion-origins.json`, generated package projections, or
  transaction-owned promotion evidence.
- Publication, tag creation, release assets, and a `snapshot_released` claim
  require separate explicit authorization and are outside this plan.
- Exact resource-limit numbers, diagnostic reason codes, new command surfaces,
  adapter ownership, and the pilot artifact route must be frozen by the
  designated decision milestone before dependent implementation starts.

## Delivery Order

| Track | Milestones | Completion boundary |
| --- | --- | --- |
| Manual curation | `UIA-01` through `UIA-03` | One exact, pinned, manually reviewed pilot proposal exists. |
| Read-only validator | `UIA-04` through `UIA-11` | Current and previous proposal roots validate deterministically through the public CLI. |
| Inventory assistance | `UIA-12` and `UIA-13` | One selected ecosystem has a pinned, read-only inventory adapter with normalized output. |
| Implementation handoff | `UIA-14` through `UIA-17` | The pilot has a hash-bound handoff and automated surface-drift gate. |
| Proof and catalog | `UIA-18` through `UIA-21` | The independently authored pilot is materialized as a verified catalog artifact and documented, but not released. |

The validator critical path is:

```text
UIA-01 -> UIA-04
UIA-04 -> UIA-05 + UIA-06
UIA-05 + UIA-06 -> UIA-07 + UIA-09
UIA-05 + UIA-06 + UIA-07 -> UIA-08
UIA-07 + UIA-08 + UIA-09 -> UIA-10 -> UIA-11
```

The pilot critical path is:

```text
UIA-01 -> UIA-02 -> UIA-03 -> UIA-14 ----------------> UIA-17 -> UIA-18 -> UIA-19 -> UIA-20 -> UIA-21
                           UIA-10 -> UIA-15 -> UIA-16 --/
```

`UIA-12` and `UIA-13` may proceed after the pilot proposal is adopted;
they do not block manual adoption or the core validator. They must complete
before the overall Phase 3 acceptance claim.

## Milestones

### UIA-01 Freeze The V1 Manual Contract And Limits

- Status: Pending
- Depends on: None
- Inputs: design sections `Lifecycle`, `Canonical File Layout`, `Proposal File
  Contract`, `Adoption Criteria`, `Validation Tooling Design`, and `Deferred
  Decisions`; `npa-mathlib/AGENTS.md`, `docs/catalog-policy.md`, and
  `docs/namespace-policy.md`.
- Target files:
  - `npa-mathlib/interface-proposals/README.md`
  - `npa-mathlib/docs/usage-evidence-driven-interface-adoption-design.md` only
    if an implementation-blocking contradiction is discovered
- Deliverables:
  - Create the canonical proposal root and its `README.md`.
  - Transcribe the complete v1 field, lifecycle, path, change-kind,
    declaration-family, evidence, proof-reference, alternative, revision, and
    hash contracts without weakening the source design.
  - Freeze exact numeric limits for proposal files, proposal count,
    declarations, observations, proof references, alternatives, imports,
    paths, and strings. Record the unit and whether the bound is per file or
    per proposal set.
  - Freeze the stable validator diagnostic categories and lower-case reason
    codes needed by all design validation failures.
  - Freeze whether each CLI path option is required or defaulted and the exact
    deterministic command-result summary fields, field order, status counts,
    proposal rows, and proposal-set hash rendering.
  - State that the caller supplies the immediately preceding validated
    proposal set. Define the per-record skips the local two-root comparison can
    reject, and do not claim it can detect an omitted addition-only snapshot
    without Git or another history authority.
  - Freeze the local files the validator may read to resolve NPA interfaces;
    distinguish that read set from proof checking and prohibit remote evidence
    lookup, Git inspection, and unrelated authoring sidecars.
  - State that the v1 check command does not write files. Record that
    `generated/index.json` is optional and has no writer until a separate
    versioned write/check contract is accepted; the validator still computes
    the canonical ordered rows and proposal-set hash in memory.
  - Define manual adoption review steps and a review checklist covering
    naming, exact NPA terms, alternatives, use-site evidence, license status,
    support closure, proof references, imports, setoid policy, and collisions.
- Acceptance criteria:
  - Every mandatory design field and lifecycle rule is findable in the README.
  - Every resource category and every validation failure class has one exact
    limit or reason code; no dependent milestone has to invent either.
  - The README uses `interface-proposals/Mathlib/**/*.toml` as the only scan
    domain and excludes README/index files from hashes.
  - The README labels proposals, manual review, validator output, and hashes as
    non-proof evidence and does not claim catalog admission or L2 review.
- Verification:
  - `git diff --check -- npa-mathlib/interface-proposals/README.md`
  - `rg -n "npa.mathlib.interface_proposal.v1|proof_evidence|resource|reason|proposal_set|adopted|superseded" npa-mathlib/interface-proposals/README.md`
- Notes:
  - Keep TOML as the canonical v1 format. A canonical JSON proposal format,
    named curator schema, evidence score, and promotion-plan hash field remain
    deferred.

### UIA-02 Select And Audit One Pilot Interface

- Status: Pending
- Depends on: UIA-01
- Inputs: design sections `Repository Survey Procedure`, `Adoption Criteria`,
  `Naming And Module-Placement Rules`, and `Ownership, Provenance, And
  Licensing`; the UIA-01 manual checklist; current `npa-package.toml` and
  namespace policy.
- Target files:
  - One new survey record under
    `npa-mathlib/docs/interface-research/{pilot-slug}-survey.md`
- Deliverables:
  - Choose one bounded mathematical surface that NPA can express under current
    policy and whose intended catalog relation is representable by the one-file
    pilot in UIA-03. Do not select a catalog `split`, which requires two or
    more target proposal files in one `change_group`.
  - Record the proposed `Mathlib.*` target module and the reason it is an
    appropriate first pilot.
  - Pin at least one declaration repository and one concrete use site to full
    immutable Git commits or release digests.
  - Inventory relevant source declarations, module imports, direct uses,
    rewrites, instance/inference dependencies, and module-layout evidence.
  - Record repository URL, immutable revision, license, path, source module,
    source declaration, usage kind, and concise observation notes for every
    item that will enter the proposal.
  - Compare material module names, declaration names, statement shapes,
    argument orders, assumptions, bundled/unbundled forms, and
    quotient/setoid formulations.
  - Identify proof references separately from use evidence and state that no
    external source or proof term will be copied.
  - If the pilot changes current catalog content, inventory the current module
    as an observation and record the exact old/new surface and compatibility
    impact. If it is an `add`, confirm that no current catalog route exists.
- Acceptance criteria:
  - Every proposed public declaration has a concrete downstream use or an
    explicit candidate foundation exception.
  - All evidence needed for adoption is immutable and license-known; no
    floating branch, movable tag, search URL, or `UNKNOWN` license remains.
  - The survey gives enough information to write exact NPA signatures, exact
    definition bodies, complete inductive families, alternatives, and import
    boundaries without another repository-discovery pass.
  - The pilot is small enough to implement and source-free verify as one
    coherent module closure.
- Verification:
  - Run the UIA-01 manual evidence checklist against the survey.
  - `rg -n "revision|license|path|usage_kind|proof reference|alternative|setoid" npa-mathlib/docs/interface-research/{pilot-slug}-survey.md`
- Notes:
  - `{pilot-slug}` is determined by the selected mathematical topic in this
    milestone and is recorded once in the survey. Later milestones consume
    that exact value; they do not choose a second pilot.

### UIA-03 Create And Manually Adopt The Pilot Proposal

- Status: Pending
- Depends on: UIA-02
- Inputs: the UIA-01 contract, the UIA-02 survey, current catalog manifest,
  `docs/namespace-policy.md`, and exact NPA surface syntax accepted by the
  current frontend.
- Target files:
  - `npa-mathlib/interface-proposals/Mathlib/{module-path}.toml`
- Deliverables:
  - Create one revision-1 proposal at the module-mirrored path selected by the
    survey.
  - Fill every top-level field, declaration row, observation row, proof
    reference row, alternative row, and adoption field required by v1.
  - Use exact parseable NPA signatures. Include exact adopted bodies for every
    definition and exact ordered family members for every inductive.
  - Record same-module `depends_on` edges and include every support declaration
    or generated family member that the exported certificate will expose.
  - Record explicit imports, the selected alternatives, and exclusions in
    `scope` and rationale text. Confirm separately that the surface can be
    implemented under the current package axiom policy; v1 proposal TOML does
    not add an allowed-axioms field.
  - Conduct the manual adoption review and set `interface_status = "adopted"`
    only after every checklist item passes.
  - Commit the adopted revision so the implementation contract refers to exact
    Git-recorded bytes; leave no uncommitted change to the proposal file.
- Acceptance criteria:
  - `proposal_id` initially equals `module`, `proposal_revision = 1`,
    `change_kind` and `source_modules` match catalog reality, and
    `previous_proposal_hash` is absent.
  - Every public declaration has evidence or a valid foundation exception;
    every unevidenced support declaration has a valid reachable rationale.
  - Every theorem has a resolvable pinned proof reference or a justified proof
    reference exception.
  - The proposal contains no placeholder signature/body, no unresolved
    license, no quotient-backed public contract, and no undeclared import.
  - A second reviewer can reconstruct why the module boundary and every public
    name were chosen from the survey and proposal alone.
  - The adopted proposal path is tracked in Git and its working-tree bytes
    equal the committed revision.
- Verification:
  - Run the complete manual adoption checklist from UIA-01.
  - `test -f npa-mathlib/interface-proposals/Mathlib/{module-path}.toml`
  - `/usr/bin/git ls-files --error-unmatch npa-mathlib/interface-proposals/Mathlib/{module-path}.toml`
  - `/usr/bin/git diff --quiet -- npa-mathlib/interface-proposals/Mathlib/{module-path}.toml`
  - `rg -n "interface_status = \"adopted\"|proof_evidence = false|\[\[declarations\]\]|\[\[observations\]\]" npa-mathlib/interface-proposals/Mathlib`
- Notes:
  - A committed intermediate `proposed` revision is not required. If the
    canonical file changes after revision 1 is committed, increment the
    revision and bind the exact previous file hash.

### UIA-04 Add Strict V1 Data Types And TOML Parsing

- Status: Pending
- Depends on: UIA-01
- Inputs: UIA-01's frozen schema, limits, and reason codes; existing strict
  parser patterns in `npa-core/crates/npa-package/src/manifest.rs` and
  structured errors in `error.rs`.
- Target files:
  - `npa-core/crates/npa-package/src/interface_proposal.rs`
  - `npa-core/crates/npa-package/src/lib.rs`
  - focused unit tests in the new module
- Deliverables:
  - Add typed enums and structs for status, change kind, declarations,
    alternatives, observations, proof references, and proposal metadata.
  - Add a dedicated structured error type with stable category, TOML path,
    field, expected/actual values, and UIA-01 reason code.
  - Parse from UTF-8 TOML bytes without filesystem access.
  - Reject duplicate keys, unknown fields at every table depth, wrong types,
    missing required fields, unknown schema versions, and invalid enum values.
  - Enforce the file byte/string and nested collection limits before cloning
    strings or collecting typed nested vectors. Keep total parser allocation
    bounded by the frozen maximum file size.
  - Export only the minimal parser, typed model, error, hash, and validation
    entry points needed by the CLI.
- Acceptance criteria:
  - Parser tests cover every table kind, every optional field, all five
    statuses, all six change kinds, unknown fields, duplicate fields, wrong
    types, and each resource limit boundary.
  - The crate remains metadata-only and gains no dependency on the frontend,
    CLI, filesystem, Git, or network.
  - The new module documentation explicitly says proposal data is untrusted
    curation metadata and not proof evidence.
- Verification:
  - `cargo test --locked --offline -q --manifest-path npa-core/Cargo.toml -p npa-package interface_proposal`
  - `cargo check --locked --offline -q --manifest-path npa-core/Cargo.toml -p npa-package`
- Notes:
  - Reuse `PackageHash` and `package_file_hash`; do not introduce a second
    incompatible hash representation. The wire form remains `sha256:` followed
    by exactly 64 lowercase hexadecimal characters.

### UIA-05 Implement Confined Proposal Discovery And Set Hashing

- Status: Pending
- Depends on: UIA-04
- Inputs: design sections `Canonical File Layout`, `Proposal hash contract`,
  and `Validation Tooling Design`; UIA-01 path and resource rules; confinement
  patterns in `npa-core/crates/npa-cli/src/governance_writer.rs` and
  `generated_artifact_writer.rs`.
- Target files:
  - `npa-core/crates/npa-cli/src/package_interface_proposals.rs`
  - `npa-core/crates/npa-cli/src/lib.rs`
  - focused module tests
- Deliverables:
  - Resolve `--proposal-root` relative to `--root` and require a real confined
    directory.
  - Scan exactly `Mathlib/**/*.toml` in relative-path byte order.
  - Reject symlinks, non-regular entries, path escapes, invalid UTF-8 paths or
    bytes, tabs/newlines in relative paths, wrong extensions in the canonical
    tree, and proposal count/file-size limit violations before parsing.
  - Compute each exact UTF-8 TOML byte hash and the deterministic proposal set
    rows/hash from all active and terminal records. Git tracking remains a
    curation/adoption precondition, not something this network-free scanner
    infers.
  - Return an in-memory summary. Do not write `generated/index.json`.
- Acceptance criteria:
  - Repeated scans of identical bytes return identical ordered rows and hashes.
  - README and `generated/index.json` never become proposal-file rows.
  - Symlinked files/directories and a canonical path outside the proposal root
    fail before content validation.
  - Diagnostics use proposal-relative sanitized paths and do not leak absolute
    temporary paths.
- Verification:
  - `cargo test --locked --offline -q --manifest-path npa-core/Cargo.toml -p npa-cli package_interface_proposals::discovery`
  - `cargo test --locked --offline -q --manifest-path npa-core/Cargo.toml -p npa-package interface_proposal_hash`
- Notes:
  - This is read-only discovery; do not reuse a writer that creates missing
    directories or replaces files.

### UIA-06 Validate One Proposal In Isolation

- Status: Pending
- Depends on: UIA-04
- Inputs: design sections `Lifecycle`, `Proposal File Contract`, `Adoption
  Criteria`, and `Failure And Revision Policy`; UIA-01 reason-code catalog.
- Target files:
  - `npa-core/crates/npa-package/src/interface_proposal.rs`
  - focused unit tests
- Deliverables:
  - Validate schema, status-specific fields, positive revision and previous
    hash presence, `proof_evidence = false`, exact module/proposal identifiers,
    and status-appropriate signature availability.
  - Require `adoption_date` and `adoption_rationale` for `adopted`, preserve
    them for `superseded`, require `withdrawal_rationale` for `withdrawn`, and
    require sorted nonempty `superseded_by` for `superseded`.
  - Validate change-kind/source-module cardinality and `change_group` rules.
  - Validate unique declaration, observation, proof-reference, and alternative
    IDs plus all local references among them.
  - Validate declaration kind/surface, `depends_on`, evidence requirements,
    `foundation_exception`, support reachability and `support_rationale`,
    theorem `proof_reference_ids` and `proof_reference_exception`, definition
    bodies, and inductive family inventories.
  - Validate immutable revision locator shape, required source locations,
    usage/reference-role vocabularies, license presence, alternatives review,
    and status rationales.
  - Permit `license = "UNKNOWN"` only with the required follow-up note; reject
    an adopted or superseded surface when an observation or proof reference
    required by that decision still has unknown licensing.
  - Enforce the design-required sorting of `source_modules` and
    `superseded_by`, reject duplicates in set-like arrays, and preserve the
    semantic order of declarations, imports, and `family_members`.
  - Detect same-proposal declaration dependency cycles and duplicate family
    members.
- Acceptance criteria:
  - Valid fixtures exist for every lifecycle status and change kind.
  - `observed` and an incompletely specified `withdrawn` record may omit
    unresolved signatures; `proposed` and `adopted` may not. Superseded
    records retain the last adopted exact surface.
  - An empty public evidence set fails unless a foundation exception exists.
  - An unevidenced support declaration fails unless it is reachable and has a
    support rationale.
  - No function in this milestone reads a package root, certificate, source
    file, Git repository, or network resource.
- Verification:
  - `cargo test --locked --offline -q --manifest-path npa-core/Cargo.toml -p npa-package interface_proposal::tests`

### UIA-07 Parse And Resolve Exact NPA Surface Terms

- Status: Pending
- Depends on: UIA-05, UIA-06
- Inputs: design declaration/signature rules; current NPA frontend APIs in
  `npa-core/crates/npa-frontend`; manifest and verified-import loading patterns
  in `npa-core/crates/npa-cli/src/package_build.rs`.
- Target files:
  - `npa-core/crates/npa-cli/src/package_interface_proposals.rs`
  - narrowly factored reusable frontend/package helper only if existing
    visibility prevents safe reuse
  - focused tests and compact package fixtures
- Deliverables:
  - Parse every required signature as an NPA declaration signature with the
    proposed declaration name and kind.
  - Parse adopted definition bodies. For an adopted inductive, parse its exact
    signature and validate the ordered `family_members` as canonical public
    names; v1 does not contain a complete inductive declaration body to parse.
  - Resolve names only against the proposal's declared imports, same-module
    declarations reachable through the acyclic `depends_on` graph, immutable
    package imports, and other adopted proposal interfaces supplied by the
    validator. Proposal row order alone must not create an undeclared
    dependency rule.
  - Reject empty, placeholder, syntax-invalid, kind-mismatched, or
    import-unresolvable terms with distinct stable reason codes.
  - Preserve historical superseded syntax validation without resolving its
    retired imports against current catalog HEAD.
- Acceptance criteria:
  - Positive tests use real current NPA syntax and imported interfaces.
  - Negative tests distinguish syntax errors from unresolved names and
    undeclared imports.
  - The check performs no proof elaboration or certificate acceptance claim;
    it only validates that the intended interface terms are parseable and
    resolvable under the declared boundary.
  - No sibling repository is needed by `npa-core` tests.
- Verification:
  - `cargo test --locked --offline -q --manifest-path npa-core/Cargo.toml -p npa-cli package_interface_proposals::surface`
  - `cargo test --locked --offline -q --manifest-path npa-core/Cargo.toml -p npa-frontend human_parser`
- Notes:
  - Keep frontend-dependent validation in `npa-cli`; do not add an
    `npa-frontend` dependency to `npa-package`.

### UIA-08 Validate The Current Proposal Set Against The Catalog

- Status: Pending
- Depends on: UIA-05, UIA-06, UIA-07
- Inputs: current proposal snapshot, validated `npa-package.toml`, namespace
  policy, and design cross-proposal rules.
- Target files:
  - `npa-core/crates/npa-cli/src/package_interface_proposals.rs`
  - compact `npa-core/testdata/package/interface-proposals-*` fixtures
- Deliverables:
  - Enforce proposal file path/module correspondence and `Mathlib.*` ownership.
  - Enforce globally unique proposal IDs, reject reuse of a terminal proposal
    ID, and enforce unique active target modules while retaining every terminal
    historical record in scanning, hashing, and link resolution.
  - Check `add`, `revise`, `rename`, `split`, `merge`, and `replace` relations
    against current catalog modules and appropriate superseded proposals.
  - Enforce split-group completeness and reciprocal supersession links.
  - Detect collisions among catalog and active proposal declarations.
  - Resolve proposed/adopted imports against current local modules, immutable
    package imports, or adopted proposal modules.
  - Detect cycles in the proposed/adopted import graph and emit a stable,
    deterministically ordered cycle diagnostic.
- Acceptance criteria:
  - Positive fixtures cover all six change kinds and cross-proposal adopted
    imports.
  - Negative fixtures cover duplicate IDs/modules/declarations, catalog
    collisions, unresolved imports, bad source-module relations, incomplete
    split groups, nonreciprocal supersession, and graph cycles.
  - Before materialization, `proposed` and unmaterialized `adopted` `add`
    records cannot target a current module; an adopted `add` retained after
    materialization is the explicit read-only history exception and requires
    the exact-surface gate. `revise` targets exactly itself; rename/replace
    targets differ; split and merge cardinalities match v1.
  - Reorganizing an unmaterialized proposal uses proposal supersession with
    `change_kind = "add"`; it cannot claim a catalog split for a nonexistent
    artifact.
- Verification:
  - `cargo test --locked --offline -q --manifest-path npa-core/Cargo.toml -p npa-cli package_interface_proposals::catalog`

### UIA-09 Validate Previous-Snapshot Revision And Lifecycle Transitions

- Status: Pending
- Depends on: UIA-05, UIA-06
- Inputs: design lifecycle and previous-root rules; exact file hashes from
  UIA-05; UIA-01 transition reason codes.
- Target files:
  - `npa-core/crates/npa-cli/src/package_interface_proposals.rs`
  - paired previous/current fixtures
- Deliverables:
  - Canonicalize and reject identical current/previous proposal roots.
  - Validate each root independently before comparing it and reject a previous
    root that is not itself a valid proposal set.
  - Treat the supplied previous root as the caller-declared immediately
    preceding validated set. Check every changed record's revision increment
    and exact `previous_proposal_hash`, while reporting only the detectable
    per-record continuity guarantee frozen by UIA-01.
  - Reject record removal, revision reuse/skips, wrong hashes, changed records
    without revision increments, and edits to terminal records.
  - Require every newly introduced current record to start at revision 1 with
    no previous hash, and preserve proposal ID, canonical path, and target
    identity across an ordinary same-record revision.
  - Enforce same-module `adopted -> proposed` rework, reciprocal cross-module
    supersession, and valid observed/proposed/adopted/terminal transitions.
  - Require a transition to `withdrawn` to preserve the previous unadopted
    declaration/import surface while adding only the permitted revision,
    status, previous-hash, and withdrawal-rationale changes.
  - Permit `adopted -> adopted` only with refreshed adoption fields and a
    nonempty `re_adoption_rationale`.
- Acceptance criteria:
  - Valid pairs cover adopted-to-proposed rework, adopted-to-adopted
    re-adoption, withdrawal, and one-to-many supersession.
  - Invalid pairs cover skipped/reused revisions, wrong/missing previous hash,
    deleted records, changed terminal bytes, silent cross-module replacement,
    mutated withdrawal surfaces, and same-root comparison.
  - Transition diagnostics are deterministic regardless of filesystem
    enumeration order.
- Verification:
  - `cargo test --locked --offline -q --manifest-path npa-core/Cargo.toml -p npa-cli package_interface_proposals::transition`

### UIA-10 Wire The Public Read-Only CLI Command

- Status: Pending
- Depends on: UIA-07, UIA-08, UIA-09
- Inputs: intended command shape in the design; CLI parser/help/dispatch patterns
  in `args.rs`, `package.rs`, `diagnostic.rs`, and `package_cli_args.rs`.
- Target files:
  - `npa-core/crates/npa-cli/src/args.rs`
  - `npa-core/crates/npa-cli/src/package.rs`
  - `npa-core/crates/npa-cli/src/diagnostic.rs` if a new diagnostic kind is
    required by UIA-01
  - `npa-core/crates/npa-cli/tests/package_cli_args.rs`
  - `npa-core/crates/npa-cli/tests/package_interface_proposals.rs`
- Deliverables:
  - Add `PackageCheckInterfaceProposalsOptions`, the package-command enum arm,
    help topic, parser, command name, common-options plumbing, and dispatch.
  - Support common `--root`, required `--proposal-root`, optional
    `--previous-proposal-root`, `--json`, and `--help` exactly as designed.
  - Reject duplicate flags, missing values, unknown flags, and identical roots
    with stable usage/package errors.
  - Emit the exact deterministic ordered proposal rows, status counts, file
    hashes, proposal-set hash, `proof_evidence=false`, and diagnostics frozen
    by UIA-01 through the existing command-result JSON envelope.
  - State in help and human output that the command is network-free curation
    validation and not proof verification or catalog admission. Also state
    that immediate-predecessor selection is a caller precondition and that the
    command reports only locally detectable per-record continuity.
- Acceptance criteria:
  - Current-only mode validates no lifecycle transition.
  - Previous-root mode performs all UIA-09 checks without invoking Git.
  - JSON output is byte-identical across repeated runs on identical roots and
    contains no absolute temporary paths.
  - The command performs no writes and returns failure if any proposal fails.
- Verification:
  - `cargo test --locked --offline -q --manifest-path npa-core/Cargo.toml -p npa-cli package_cli_args`
  - `cargo test --locked --offline -q --manifest-path npa-core/Cargo.toml -p npa-cli package_interface_proposals`

### UIA-11 Close The Validator Fixture, Security, And Documentation Matrix

- Status: Pending
- Depends on: UIA-10
- Inputs: complete `Test Strategy` section, UIA-01 limits/reason codes, and all
  validator milestones.
- Target files:
  - `npa-core/testdata/package/interface-proposals-*`
  - `npa-core/crates/npa-cli/tests/package_interface_proposals.rs`
  - `npa-core/docs/npa-toolchain-reference-v0.7.0.md`
  - `npa-core/docs/README.md`
  - `npa-core/README.md`
  - `npa-mathlib/interface-proposals/README.md`
- Deliverables:
  - Audit the design's entire positive/negative fixture list and add every case
    not already covered by UIA-04 through UIA-10.
  - Add explicit symlink, non-regular entry, path escape, invalid UTF-8,
    wrong-extension, count/byte/string limit, floating revision, missing
    license, collision, import-cycle, and deterministic hash-vector tests.
  - Add a network-free/read-set guard proving the validator reads only the
    local files frozen by UIA-01, never dereferences proposal evidence URLs or
    invokes Git, and never presents any locally read source/interface data or
    certificate bytes as proof acceptance.
  - Document the implemented command and exact trust boundary only after all
    focused tests pass.
- Acceptance criteria:
  - A traceability checklist maps every bullet in the design `Test Strategy`
    to one named test.
  - Documentation examples run against compact in-repository fixtures.
  - No documentation describes an interface status as catalog maturity or a
    validator pass as proof evidence.
  - The focused tests, formatting check, and core fast gate pass, or a genuine
    resource failure is recorded with the narrower passing commands.
- Verification:
  - `cargo fmt --all --manifest-path npa-core/Cargo.toml -- --check`
  - `cargo test --locked --offline -q --manifest-path npa-core/Cargo.toml -p npa-package interface_proposal`
  - `cargo test --locked --offline -q --manifest-path npa-core/Cargo.toml -p npa-cli package_interface_proposals`
  - `npa-core/scripts/check-fast.sh`
  - `cargo run --locked --offline -q --manifest-path npa-core/Cargo.toml -p npa-cli -- package check-interface-proposals --root npa-mathlib --proposal-root interface-proposals --json`

### UIA-12 Freeze The Inventory Adapter Contract And First Ecosystem

- Status: Pending
- Depends on: UIA-03
- Inputs: design `Phase 3`, UIA-02 survey, deferred adapter decision, and the
  canonical observation/proof-reference fields.
- Target files:
  - `npa-mathlib/docs/interface-inventory-adapter-v1.md`
- Deliverables:
  - Select the first external prover ecosystem based on the pilot evidence and
    record why it gives the highest immediate value.
  - Freeze the owning Rust crate, command name/options, input pin format,
    output schema, diagnostic reason codes, and numeric resource limits.
  - Define normalized inventory rows for declarations, imports, and use sites
    while keeping prover-specific syntax outside canonical proposal TOML.
  - Require an explicit local checkout plus immutable revision supplied by the
    caller; define how the adapter verifies that input without fetching or
    resolving a floating ref.
  - Define deterministic sorting, path sanitization, unsupported-syntax
    behavior, license input, and the human-curation boundary.
- Acceptance criteria:
  - UIA-13 can be implemented from this document without choosing a crate,
    ecosystem, command surface, schema field, reason code, or limit.
  - Adapter output cannot set `interface_status = "adopted"`, mutate canonical
    proposals, or claim repository/proof authority.
  - The adapter is read-only and performs no network access.
- Verification:
  - Review every UIA-12 deliverable against the document and record zero open
    implementation choices.
  - `git diff --check -- npa-mathlib/docs/interface-inventory-adapter-v1.md`

### UIA-13 Implement And Verify The First Inventory Adapter

- Status: Pending
- Depends on: UIA-12
- Inputs: the frozen adapter contract and compact licensed fixtures for the
  selected ecosystem.
- Target files:
  - The exact Rust crate/module and CLI files fixed by UIA-12
  - compact fixtures under that owning repository's testdata tree
  - UIA-12 documentation examples
- Deliverables:
  - Parse only the selected ecosystem syntax and inventory declarations,
    imports, direct use sites, rewrites, and supported inference-driven uses
    promised by the contract.
  - Emit deterministic normalized rows with immutable revision, license,
    repository, path, source module/declaration, usage kind, and notes fields.
  - Reject floating/absent pins, path escapes, symlinks, oversized inputs,
    malformed source, and unsupported constructs using the frozen diagnostics.
  - Add a guard proving the command neither fetches repositories nor edits the
    checkout or canonical proposal tree.
- Acceptance criteria:
  - Running the adapter twice on the same pinned fixture produces identical
    bytes.
  - Positive fixture output can be copied through human review into v1
    observation rows without losing a required field.
  - False positives and unsupported syntax are diagnostics, not silently
    promoted observations.
  - Human or explicitly authorized curation remains required for adoption.
- Verification:
  - Run the exact focused test and example commands frozen by UIA-12.
  - Run the owning Rust workspace's formatting check.

### UIA-14 Define And Instantiate The Implementation Handoff

- Status: Pending
- Depends on: UIA-03
- Inputs: design `Implementation Handoff`, `Ownership, Provenance, And
  Licensing`, and `Traceability`; the exact pilot proposal bytes.
- Target files:
  - `npa-mathlib/docs/interface-implementation-handoff-template.md`
  - `npa-mathlib/docs/interface-research/{pilot-slug}-handoff.md`
- Deliverables:
  - Define a reviewable handoff template containing proposal-relative path,
    exact `sha256:` plus 64 lowercase hexadecimal proposal hash, artifact
    owner, target module, complete declarations, signatures, bodies, families,
    imports, allowed axioms, proof references, explicit exclusions, and catalog
    route.
  - State how the hash is recomputed and when any proposal-byte change
    invalidates the handoff.
  - Instantiate the template for the adopted pilot, except for artifact owner
    and route fields that UIA-17 will select; mark those two fields as a
    blocking decision owned by UIA-17 rather than guessing values.
  - State that proof terms may change while exported interface terms may not.
- Acceptance criteria:
  - The pilot handoff's path exists, its recorded hash equals the exact file
    bytes, the proposal is tracked in Git, and every adopted declaration
    appears exactly once.
  - A reader can distinguish evidence owner, proof-reference owner, and
    eventual artifact owner.
  - The handoff contains no permission to copy external code and makes no
    verification, maturity, or release claim.
- Verification:
  - Recompute the proposal SHA-256 with a local byte-hashing command and compare
    it to the handoff.
  - Diff the proposal declaration/import lists against the handoff lists.

### UIA-15 Freeze The Surface-Drift Comparison Contract

- Status: Pending
- Depends on: UIA-03, UIA-10
- Inputs: design `Implementation Handoff`, `Traceability`, Phase 4, and the
  surface-drift test requirement; current certificate, manifest, frontend, and
  package-artifact APIs.
- Target files:
  - `npa-core/docs/interface-proposal-surface-drift-v1.md`
- Deliverables:
  - Choose and document whether drift validation extends
    `check-interface-proposals` with target options or uses a separate package
    subcommand. Freeze the exact parser/help/JSON contract.
  - Define canonical comparison representations for module name, direct
    imports, declaration order/name/kind/surface, signatures, definition
    bodies, inductive family members, and exported support closure.
  - Define how Human proposal terms are elaborated and compared with prepared
    target core terms without treating source as proof evidence.
  - Freeze target inputs, diagnostic reason codes, resource limits, and
    behavior for missing/stale certificates, sources, manifests, or imports.
  - State that any drift blocks handoff and returns the proposal to `proposed`;
    the checker never edits the proposal or target.
- Acceptance criteria:
  - UIA-16 can be implemented without choosing command syntax, comparison
    semantics, source/certificate authority, reason codes, or limits.
  - The contract detects exported additions as well as removals or changes.
  - The comparison includes exact definition bodies and complete inductive
    families, not only declaration names or statement hashes.
  - The document keeps drift validation outside the trusted proof base.
- Verification:
  - Review every field in the design's surface-drift test bullet against one
    canonical comparison rule and one planned diagnostic.
  - `git diff --check -- npa-core/docs/interface-proposal-surface-drift-v1.md`

### UIA-16 Implement The Read-Only Surface-Drift Gate

- Status: Pending
- Depends on: UIA-15
- Inputs: frozen UIA-15 contract, validator model, frontend/package APIs, and
  compact prepared-target fixtures.
- Target files:
  - Exact CLI/library files selected by UIA-15
  - `npa-core/crates/npa-cli/tests/package_interface_proposal_surface.rs`
  - compact fixtures under `npa-core/testdata/package/`
- Deliverables:
  - Implement the frozen read-only comparison of one adopted proposal revision
    to one prepared target package module.
  - Bind the comparison to exact proposal path/hash and fail on hash mismatch.
  - Detect drift in module, imports, declaration names/order/kinds, signatures,
    definitions, inductive families, and exported support declarations.
  - Emit deterministic diagnostics and `proof_evidence=false` through the
    existing command-result boundary.
  - Add positive parity plus one negative fixture for each comparison axis and
    a guard that the command writes neither proposal nor target files.
- Acceptance criteria:
  - The exact-match fixture passes and all axis-specific drift fixtures fail
    with their designated reason code.
  - A newly exported support declaration is reported as drift rather than
    silently accepted.
  - Output contains no absolute temporary paths and repeated runs are
    byte-identical.
- Verification:
  - `cargo test --locked --offline -q --manifest-path npa-core/Cargo.toml -p npa-cli package_interface_proposal_surface`
  - `cargo fmt --all --manifest-path npa-core/Cargo.toml -- --check`

### UIA-17 Select The Pilot Artifact Owner And Catalog Route

- Status: Pending
- Depends on: UIA-14, UIA-16
- Inputs: adopted pilot, completed handoff template, current `npa-corpus` and
  project package capabilities, direct reconciliation contract, and source-
  backed promotion contract.
- Target files:
  - `npa-mathlib/docs/interface-research/{pilot-slug}-handoff.md`
- Deliverables:
  - Choose exactly one artifact owner: direct `npa-mathlib`, `npa-corpus`, or
    one named `npa-project-*` package.
  - Choose the matching route: direct target reconciliation or source-backed
    complete-module/declaration-selection promotion.
  - Record why the owner has the required imports, proof tooling, package
    closure, and sustainable semantic ownership.
  - Fill the handoff's owner/route fields and list exact owner-repository paths,
    build commands, source-free verification commands, permitted axioms,
    target mapping, and promotion/reconciliation commands.
  - Record the exact proposal hash again after confirming the proposal did not
    change during route selection.
- Acceptance criteria:
  - UIA-18 through UIA-20 contain no owner or route choice.
  - Direct route names a strictly newer target version and explicit previous
    target requirement. Source-backed route names the source module and
    complete-module or declaration-selection admission unit.
  - The choice does not treat observed repositories as artifact origins for
    independently authored content.
  - If neither route can represent the target, record `tooling-blocked` with
    the missing capability and stop; do not invent registry edits.
- Verification:
  - Review the filled handoff against every design handoff bullet.
  - Recompute and compare the proposal hash.

### UIA-18 Independently Implement The Pilot Artifact

- Status: Pending
- Depends on: UIA-17
- Inputs: final pilot handoff, selected owner repository guidance, adopted
  proposal, and pinned proof references.
- Target files:
  - Exact owner package module/source/certificate paths fixed by UIA-17
  - owner manifest and generated artifacts only through supported generation
    commands
  - focused owner tests or downstream fixture needed by the handoff
- Deliverables:
  - Author new NPA proofs for the complete adopted declaration and support
    closure without copying or mechanically translating external proof source.
  - Preserve exact adopted imports, declaration names/kinds, signatures,
    definition bodies, and inductive families. On the direct route, the owner
    module name is the adopted `Mathlib.*` target. On a source-backed route,
    the source module may keep the owner namespace recorded by UIA-17; its
    approved mapping must still produce the exact adopted target module.
  - Build canonical certificates and run the owner package's focused
    source-free reference verification, hash, import, and axiom gates.
  - Defer exact target comparison to UIA-19. UIA-18 creates the owner artifact;
    UIA-19 first completes a direct unpublished target or creates a
    source-backed temporary target with the supported transaction.
  - If implementation requires any exported surface change, stop, return the
    proposal to `proposed` in a new hash-chained revision, repeat manual
    adoption, validate the transition against the previous proposal root,
    restart at UIA-14, and re-run the UIA-17 owner/route decision before
    resuming this milestone.
- Acceptance criteria:
  - The selected owner contains a complete, independently authored, canonical
    artifact closure for every adopted declaration.
  - Source-free reference verification succeeds with caches disabled and the
    axiom surface matches the handoff.
  - Exact target surface drift remains explicitly pending until UIA-19 creates
    a complete target for the selected route.
  - No proposal status, validator result, source file, or proof reference is
    reported as proof authority.
- Verification:
  - Run every exact owner build/check command recorded by UIA-17.
- Notes:
  - This milestone prepares an artifact. It does not claim
    `structural_candidate`, `verified_candidate`, catalog approval,
    materialization, L2 review, or release unless the corresponding existing
    workflow has actually established it.

### UIA-19 Qualify The Exact Pilot For Its Selected Catalog Route

- Status: Pending
- Depends on: UIA-18
- Inputs: exact verified owner artifact, handoff proposal path/hash, catalog
  and namespace policies, and the selected route.
- Target files:
  - Route-owned promotion or reconciliation audit/request/plan sidecars
  - a disposable temporary mathlib target for source-backed qualification, or
    the complete unpublished `npa-mathlib` target for direct reconciliation
  - no hand-edited registry or generated evidence
- Deliverables:
  - Record the exact proposal-relative path and file hash in the route's audit
    sidecar while retaining `proof_evidence=false`.
  - For source-backed promotion, discover a closed artifact route, establish
    `structural_candidate`, verify the exact closure to
    `verified_candidate`, resolve namespace/collision/provenance/materializer
    capability, and establish `catalog_ingest_approved` only if every existing
    gate passes.
  - After source-backed approval, use the supported promotion plan to dry-run
    and apply into a temporary mathlib target, validate that temporary
    materialization independently, and run UIA-16 against the exact temporary
    target before any tracked catalog apply.
  - For direct implementation, prepare the complete unpublished strictly newer
    target, including source, certificate, metadata, replay, manifest,
    generated projections, and applicable downstream fixture. Run all current
    package gates, create any required lifecycle request, run UIA-16, and then
    run `reconcile-promotion-origin-registry` in dry-run mode with an explicit
    previous target and audit.
  - Stop as `tooling-blocked` if the chosen change or target L2 claim lacks a
    supported transaction. Do not hand-edit or bypass registry state.
- Acceptance criteria:
  - Qualification is bound to exact source/target artifact hashes and exact
    proposal bytes.
  - Source-backed status names are used only after their formal gates pass.
  - A source-backed temporary target passes independent materialization
    validation and UIA-16 for the exact proposal revision before tracked apply.
  - Direct dry-run is deterministic and proposes only the intended addition,
    revision, or explicit lifecycle relation.
  - No target is called `reviewed`; external proposal evidence and proof
    references do not provide target L2 acceptance.
- Verification:
  - Run the route-specific qualification or reconciliation dry-run commands
    recorded in UIA-17.
  - Run UIA-16 against the direct prepared target or source-backed temporary
    target immediately before accepting the qualification report.

### UIA-20 Materialize And Verify The Pilot Catalog Artifact

- Status: Pending
- Depends on: UIA-19
- Inputs: approved exact route, previous target snapshot where applicable,
  dry-run output, transaction-owned sidecars, and current target gates.
- Target files:
  - for source-backed promotion, materializer-owned `npa-mathlib` artifacts,
    manifest, generated projections, attestation, and promotion-origin registry
    changes
  - for direct reconciliation, the unchanged caller-prepared target artifacts
    from UIA-19 plus reconciliation-owned attestation and promotion-origin
    registry changes
  - the route-required downstream fixture, prepared separately and never
    attributed to the registry reconciliation transaction
- Deliverables:
  - For source-backed promotion, invoke the supported tracked materializer for
    the exact approved closure; do not reproduce its writes manually.
  - For direct implementation, apply the same approved reconciliation inputs
    used by dry-run; let the transaction write its attestation and registry
    last.
  - For source-backed promotion, update the applicable downstream smoke only
    after the target artifact exists, vendoring the exact public certificate
    closure without proposal or source sidecars. For the direct route, retain
    and verify the fixture prepared in UIA-19.
  - Run package check, certificate build check, cache-off source-free reference
    verification, hash check, axiom report, theorem index, theorem-premise
    report, export summary, publish-plan check, origin-registry validation, and
    applicable downstream smoke tests.
  - Re-run proposal surface-drift validation and verify the audit proposal hash.
  - Establish `materialized_verified` only after all required target gates pass.
- Acceptance criteria:
  - The catalog contains the exact adopted module surface with a strictly newer
    package version and deterministic current artifacts.
  - Registry history and every released snapshot remain immutable; the active
    target has exactly one valid origin owner.
  - Applicable downstream consumption succeeds from hash-pinned certificate
    imports without proposal/source sidecars.
  - No release tag, asset, or `snapshot_released` state is created.
- Verification:
  - Run the complete `npa-mathlib/README.md` verification command set.
  - Run the route transaction's post-apply validation/recovery check.
  - Run the applicable downstream smoke fixture checks.
  - Run the UIA-16 exact-surface comparison one final time.

### UIA-21 Close Documentation And Design Acceptance

- Status: Pending
- Depends on: UIA-11, UIA-13, UIA-20
- Inputs: all implemented contracts, pilot audit/handoff, validator/adapter
  behavior, and the source design acceptance criteria.
- Target files:
  - `npa-mathlib/README.md`
  - `npa-mathlib/interface-proposals/README.md`
  - `npa-mathlib/docs/usage-evidence-driven-interface-adoption-design.md`
  - relevant `npa-core` toolchain/docs indexes
- Deliverables:
  - Update documentation from proposed behavior to exact implemented behavior,
    without erasing design history or deferred decisions that remain open.
  - Add operator examples for manual survey/adoption, current-only validation,
    previous-root transition validation, inventory extraction, handoff hashing,
    surface drift, and the two catalog routes.
  - Audit every source-design acceptance criterion and record its implementing
    milestone, file, test, or intentionally deferred item.
  - Confirm existing catalog modules require no retroactive proposals and that
    immutable snapshots remain the downstream compatibility mechanism.
  - Run final documentation, Rust, package, proposal, registry, and LFS-pointer
    checks.
- Acceptance criteria:
  - The acceptance audit has no unmet non-deferred design criterion.
  - All docs agree on canonical paths, statuses, command options, trust
    boundary, artifact ownership, and release non-goals.
  - The pilot proposal, handoff, target artifact, and promotion/reconciliation
    audit form an exact path/hash trace without making the proposal proof
    evidence.
  - Working documentation does not advertise unimplemented index writing,
    adapter ecosystems, L2 mutation, recommendation metadata, or release state.
- Verification:
  - `git diff --check`
  - `rg -n '^version https://git-lfs.github.com/spec/v1$' .`
  - `cargo fmt --all --manifest-path npa-core/Cargo.toml -- --check`
  - `cargo test --locked --offline -q --manifest-path npa-core/Cargo.toml -p npa-package interface_proposal`
  - `cargo test --locked --offline -q --manifest-path npa-core/Cargo.toml -p npa-cli package_interface_proposals`
  - `npa-core/scripts/check-fast.sh`
  - Run the full `npa-mathlib/README.md` package verification set.
  - Run current-only proposal validation and one explicit previous-root
    transition validation.

## Explicitly Deferred Work

The following items remain outside these milestones unless the source design
is revised through a separate decision:

- deciding whether a later schema remains TOML-only or adds a canonical JSON
  projection;
- recording proposal-set hashes in promotion plan schemas;
- derived evidence-quality scoring;
- a machine-authoritative `recommended` maturity record;
- a named-curator or separate versioned curation-decision schema;
- automatic index writing without a separately designed write/check contract;
- additional inventory ecosystems beyond the one selected by UIA-12;
- exact-target L2 mutation when the required versioned transaction is absent;
- publication, immutable tag creation, release asset upload, or a
  `snapshot_released` claim.
