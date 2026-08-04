# Interface Inventory Adapter v1

Status: contract frozen by `UIA-12` on 2026-08-02. The selected adapter is
read-only inventory assistance. Its output is untrusted curation metadata,
not an NPA source module, certificate, proof result, catalog admission, L2
decision, or release record.

## Decision

The first ecosystem is Lean 4 `mathlib4`, identified by the exact ecosystem
ID `lean4-mathlib4`. This choice is based on the UIA-02
[`Function.comp_assoc` survey](interface-research/function-comp-assoc-survey.md):
the pilot has a declaration file, a downstream rewrite use, a module-import
use, and a complete full-SHA/license pin at the same repository revision. It
therefore exercises all three normalized inventory row families required by
the pilot without selecting a large theorem family, a typeclass-heavy surface,
or a second prover syntax.

The adapter does not make Lean or mathlib4 authoritative for NPA. The pinned
checkout is evidence input supplied by the caller; eventual NPA declarations,
proof terms, certificates, and artifact ownership remain independently
authored and subject to the existing package gates.

## Owning crate and command

UIA-13 owns the implementation in the existing Rust `npa-cli` crate:

```text
npa-core/crates/npa-cli/src/package_interface_inventory.rs
```

No new crate, Lean runtime, Lean package manager, Git library, or network
client is permitted. The command is:

```text
npa package inventory-interface
```

The command emits JSON to stdout only. `--json` is required and there is no
`--out` option. Standard CLI argument failures occur before the adapter result
schema is produced; once the command starts, all adapter failures use the
schema and reason codes below.

## Frozen invocation and input pin

All options below are required unless marked repeatable. There are no defaults
for the checkout, repository, revision, license, source paths, or selectors.

```sh
npa package inventory-interface \
  --ecosystem lean4-mathlib4 \
  --root <local-mathlib4-checkout> \
  --repository https://github.com/leanprover-community/mathlib4 \
  --revision c5ea00351c28e24afc9f0f84379aa41082b1188f \
  --license Apache-2.0 \
  --path Mathlib/Logic/Function/Defs.lean \
  --path Mathlib/Logic/Function/Iterate.lean \
  --declaration Function.comp_assoc \
  --declaration Function.iterate_invariant \
  --json
```

| Option | Contract |
| --- | --- |
| `--ecosystem` | Required exact string `lean4-mathlib4`; every other value is `unsupported_ecosystem`. |
| `--root` | Required local checkout directory. It must already exist, be a real directory, and not be a symlink. The adapter never creates, canonicalizes through, or modifies it. |
| `--repository` | Required nonempty repository identity, at most 1024 UTF-8 bytes, with no whitespace/control characters. The v1 example uses the canonical HTTPS mathlib4 URL. The value is recorded but never opened or resolved. |
| `--revision` | Required `git_commit` locator: exactly 40 lowercase hexadecimal ASCII characters. `HEAD`, branch names, tags, abbreviated hashes, URLs, and floating refs are rejected. |
| `--license` | Required nonempty license identifier, at most 256 UTF-8 bytes. `UNKNOWN` is permitted only with `--license-note`; it cannot be used as adopted evidence without later human resolution. |
| `--license-note` | Optional at most 4096 UTF-8 bytes. It is legal only with `--license UNKNOWN` and must contain one of `follow`, `pending`, `resolve`, `review`, or `confirm` case-insensitively. |
| `--path` | Required and repeatable, one to 128 times. Each value is a literal checkout-relative UTF-8 path, not a glob. It must be below `Mathlib/`, end in `.lean`, contain no `.` or `..` component, tab, or newline, and be unique by UTF-8 bytes. |
| `--declaration` | Required and repeatable, one to 256 times. Each value is a unique fully qualified Lean declaration name using ASCII identifier components separated by `.`. It selects the exact top-level declaration to inventory; the declaration must occur in one selected path. |
| `--json` | Required. Human output is not a v1 adapter surface. |

The caller supplies the local checkout and the full commit SHA. The adapter
verifies the root/path confinement, pin grammar, and exact source bytes; it
computes `source_set_hash` over the selected files. It does not invoke Git and
therefore does not prove that the checkout's current `HEAD` equals the
caller-supplied SHA. That relation is a caller attestation and a human review
responsibility, explicitly represented as `revision_binding =
"caller_attested"` in the output. No floating revision is ever resolved.

The adapter reads the caller-provided license identity; it does not fetch or
inspect a remote `LICENSE` file. License confirmation remains part of human
curation.

## Read set and trust boundary

The permitted read set is limited to:

1. metadata for `--root` and its literal parent components needed to reject
   symlinks and non-directories;
2. metadata for each selected `--path`; and
3. the exact bytes of each selected `.lean` file.

The adapter never reads `.git`, invokes `git`, follows Lean imports, reads
`lakefile` or package manifests, loads compiled environments, reads
certificates/source sidecars outside the selected paths, fetches a repository,
or resolves a URL. It performs no writes, including no cache, generated index,
proposal edit, or checkout edit. The root and selected paths are checked with
symlink-aware metadata before file bytes are read.

`proof_evidence` is always the literal JSON boolean `false`. The adapter does
not set or infer `interface_status`, does not generate proposal TOML, and does
not promote an inventory row to `observed`, `proposed`, or `adopted`. A human
must review the pinned source, license, exact NPA surface, alternatives,
imports, and usage classification before copying any row into a canonical
proposal.

## Source subset and normalization

The adapter is a bounded UTF-8 lexical scanner, not a Lean elaborator and not a
proof checker. It removes Lean line comments (`--`) and nested block comments
(`/- ... -/`) while preserving line numbers, recognizes quoted strings and
character literals for delimiter balancing, and never emits raw source text
or an AST in the normalized result.

The supported command forms are deliberately fixed:

- `import Module.Name` and `public import Module.Name`, one module per command;
- namespace/context commands `namespace`, `section`, and matching `end`;
- context commands `universe`, `variable`, `include`, `omit`, `open`,
  `open scoped`, `export`, `attribute`, `set_option`, `local`, and `scoped`;
- selected top-level `def`, `theorem`, and `lemma` declarations, with optional
  `protected` or `private` modifiers and ordinary attributes immediately
  before the declaration;
- explicit references to a selected declaration in its selected declaration
  type/body; and
- references in explicit `rw`, `rwa`, `simp`, or `simp_rw` rule lists,
  normalized as `usage_kind = "rewrite"`.

The scanner emits `usage_kind = "direct_application"` for another explicit
reference that is not in one of those rule lists. It emits no inference-driven
row in v1. Typeclass search, dot-notation resolution, coercion insertion,
implicit theorem selection, generated declaration discovery, transitive
dependency inference, and tactic behavior other than the listed rule-list
forms are unsupported. A selected declaration or reference whose syntax
cannot be classified without guessing produces an `invalid` result with an
`unsupported_reference` or `unsupported_inference_use` diagnostic. It is never
silently emitted as an observation.

Unselected declarations in a selected file are not inventory targets and do
not produce declaration or use-site rows. Their surrounding source is scanned
only far enough to locate selected declarations and imports. An unsupported
construct inside a selected declaration is an error. This bounded selector
contract makes the pilot's broad `Defs.lean` file usable without pretending to
inventory its entire public API.

The module name for a selected path is derived mechanically by removing the
`.lean` suffix and replacing `/` with `.`. For example,
`Mathlib/Logic/Function/Iterate.lean` gives
`Mathlib.Logic.Function.Iterate`. An unqualified reference is accepted only
when its enclosing namespace makes the selected fully qualified declaration
unique; otherwise the scanner requires the fully qualified name and reports
`ambiguous_reference`.

## Normalized row schema

The adapter result uses schema `npa.mathlib.interface_inventory.v1`. Every row
has exactly these fields, in this order:

```json
{
  "row_kind": "declaration",
  "id": "declaration:Mathlib/Logic/Function/Defs.lean:65:Function.comp_assoc",
  "path": "Mathlib/Logic/Function/Defs.lean",
  "line": 65,
  "repository": "https://github.com/leanprover-community/mathlib4",
  "revision_kind": "git_commit",
  "revision": "c5ea00351c28e24afc9f0f84379aa41082b1188f",
  "license": "Apache-2.0",
  "source_module": "Mathlib.Logic.Function.Defs",
  "source_declaration": "Function.comp_assoc",
  "referenced_declaration": null,
  "usage_kind": "declaration",
  "declaration_kind": "theorem",
  "import_visibility": null,
  "notes": "declaration_kind=theorem"
}
```

`row_kind` is one of `module_layout`, `module_import`, `declaration`, or
`use_site`. `usage_kind` is one of the v1 proposal values
`module_layout`, `module_import`, `declaration`, `direct_application`, or
`rewrite`. The remaining proposal usage kinds (`instance_dependency` and
`transitive_dependency`) are not emitted by this adapter.

- A `module_layout` row has one row per selected file, `source_module` set to
  the path-derived module, and both declaration fields null.
- A `module_import` row has the selected file in `path`, the imported Lean
  module in `source_module`, `source_declaration` and
  `referenced_declaration` null, and `import_visibility` set to `public` or
  `private`.
- A `declaration` row has the selected declaration in `source_declaration`,
  `declaration_kind` set to `def`, `theorem`, or `lemma`, and
  `referenced_declaration` and `import_visibility` null.
- A `use_site` row has the enclosing declaration in `source_declaration`, the
  referenced selected declaration in `referenced_declaration`,
  `declaration_kind` null, and `import_visibility` null.

`line` is a one-based UTF-8 source line and is not a proof or source authority.
`notes` is normalized ASCII key/value metadata only; it contains no source
snippet, proof term, signature, certificate bytes, or unbounded diagnostic
text. Every nullable JSON field is present as `null`; fields are never
silently omitted. The repository, revision, and license values are repeated in
each row so a row can be manually copied into an observation without losing
its provenance fields.

Row IDs are deterministic and bounded:

```text
module:<path>
import:<path>:<line>:<imported-module>
declaration:<path>:<line>:<fully-qualified-name>
use:<path>:<line>:<enclosing-name>:<referenced-name>:<usage-kind>:<ordinal>
```

`<ordinal>` is the one-based lexical occurrence among same-line use rows after
normalization. It prevents collisions without embedding source text.

## Result schema and deterministic hashes

The top-level JSON field order is fixed as follows:

```json
{
  "schema": "npa.mathlib.interface_inventory.v1",
  "proof_evidence": false,
  "status": "ok",
  "pin": {
    "ecosystem": "lean4-mathlib4",
    "repository": "https://github.com/leanprover-community/mathlib4",
    "revision_kind": "git_commit",
    "revision": "c5ea00351c28e24afc9f0f84379aa41082b1188f",
    "license": "Apache-2.0",
    "license_note": null,
    "revision_binding": "caller_attested"
  },
  "input_files": [
    {
      "path": "Mathlib/Logic/Function/Defs.lean",
      "file_hash": "sha256:<64 lowercase hex>",
      "byte_count": 1234
    }
  ],
  "source_set_hash": "sha256:<64 lowercase hex>",
  "rows": [],
  "diagnostics": []
}
```

The `pin` object is null until all pin fields pass shape validation. On any
adapter diagnostic, `status` is `invalid` and `rows` is an empty array. Files
already read before a syntax/normalization failure remain listed in
`input_files` and contribute to `source_set_hash`; a failure never presents
partial rows as successful inventory. `diagnostics` is empty only for `ok`.

`input_files` are sorted by relative path UTF-8 bytes. `rows` are sorted by
`path`, `line`, row-kind order (`module_layout`, `module_import`,
`declaration`, `use_site`), `source_declaration`,
`referenced_declaration`, `usage_kind`, and `id`, all by UTF-8 byte order except
the numeric line. Repeated runs over identical selected bytes and arguments
must be byte-identical.

`file_hash` is SHA-256 of the exact selected UTF-8 file bytes, rendered as
`sha256:` plus 64 lowercase hexadecimal characters. The set hash input is the
exact ASCII/UTF-8 byte sequence:

```text
npa.mathlib.interface_inventory_source_set.v1
<path>\t<file_hash>\t<byte_count>
```

with one final newline and one row per sorted input file. The resulting
SHA-256 is `source_set_hash`. This hash binds the inventory input bytes; it is
not a proof hash and is not written to a proposal automatically.

## Diagnostic contract

Every diagnostic has exactly these fields, in this order:

```json
{
  "category": "syntax",
  "reason": "unsupported_reference",
  "path": "Mathlib/Logic/Function/Iterate.lean",
  "line": 98,
  "field": null,
  "expected": "explicit selected declaration reference",
  "actual": "inference-driven reference"
}
```

`path`, `line`, `field`, `expected`, and `actual` are nullable. Diagnostic
values are sanitized, contain no absolute root, and are capped at 256 UTF-8
bytes. Reason codes are stable API; prose is not.

| Category | Exact reason codes |
| --- | --- |
| `input` | `unsupported_ecosystem`, `missing_repository`, `invalid_repository`, `missing_revision`, `floating_revision`, `invalid_revision`, `missing_license`, `license_unknown_without_note`, `license_note_unexpected`, `duplicate_path`, `duplicate_selector`, `invalid_identifier` |
| `filesystem` | `root_not_directory`, `root_symlink`, `path_missing`, `path_escape`, `path_invalid_utf8`, `path_contains_control`, `wrong_extension`, `symlink_entry`, `non_regular_entry`, `read_failed` |
| `resource` | `source_file_count_exceeded`, `selector_count_exceeded`, `source_file_bytes_exceeded`, `source_set_bytes_exceeded`, `row_count_exceeded`, `path_bytes_exceeded`, `string_bytes_exceeded`, `identifier_bytes_exceeded`, `note_bytes_exceeded`, `diagnostic_count_exceeded` |
| `syntax` | `invalid_utf8`, `malformed_import`, `unsupported_command`, `declaration_not_found`, `unsupported_declaration`, `unsupported_reference`, `unsupported_inference_use`, `ambiguous_reference`, `malformed_comment_or_literal` |
| `normalization` | `module_path_mismatch`, `duplicate_row`, `unresolved_selected_declaration` |

The adapter fails closed on the first resource or filesystem boundary that
prevents a safe bounded scan, and may collect later independent syntax
diagnostics only up to `max_diagnostics`. It never converts a diagnostic into
a row.

## Frozen resource limits

All byte limits count input UTF-8 bytes. The limits are checked before the
corresponding output collection is grown:

| Limit | Value | Scope |
| --- | ---: | --- |
| `max_source_files` | `128` | selected `.lean` files per invocation |
| `max_declaration_selectors` | `256` | `--declaration` values per invocation |
| `max_source_file_bytes` | `16777216` | exact bytes per selected file (16 MiB) |
| `max_source_set_bytes` | `67108864` | exact bytes across selected files (64 MiB) |
| `max_rows` | `8192` | all normalized rows per invocation |
| `max_path_bytes` | `1024` | each checkout-relative path |
| `max_identifier_bytes` | `1024` | each module or declaration identifier |
| `max_repository_bytes` | `1024` | caller repository identity |
| `max_license_bytes` | `256` | caller license identifier |
| `max_note_bytes` | `4096` | normalized notes and license follow-up note |
| `max_diagnostics` | `1024` | diagnostics per invocation |
| `max_diagnostic_value_bytes` | `256` | each rendered diagnostic value |

These are adapter limits, not Lean compiler limits. The adapter does not
allocate an unbounded source, row, or diagnostic collection while reporting a
limit failure.

## Human curation handoff

Inventory output is a review aid. The required manual steps are:

1. verify the checkout/revision relationship outside the adapter and confirm
   the repository license at that revision;
2. check every path, module, declaration, import, and use-site classification
   against the pinned source;
3. compare the normalized rows with the exact NPA signatures, definitions,
   family members, imports, alternatives, and proof-reference separation in
   the proposal survey;
4. add only reviewed rows to canonical TOML observations, omitting JSON null
   fields as required by the v1 proposal schema; and
5. obtain explicit human/authorized curation before changing any proposal to
   `adopted`.

The adapter never edits `interface-proposals/`, never writes a proposal hash
or status, and never claims that a repository observation is proof authority.
Unsupported syntax and possible false positives remain diagnostics for human
review; they are not silently promoted observations.

## Frozen UIA-13 verification surface

The focused Rust test target is:

```sh
cargo test --locked --offline -q \
  --manifest-path npa-core/Cargo.toml -p npa-cli interface_inventory
```

The compact example uses the exact pilot command above with a local fixture
checkout created under `npa-core/testdata/interface-inventory/lean4-mathlib4`.
UIA-13 must include the fixture files, their license input, one positive
declaration/import/rewrite case, and negative cases for a floating revision,
missing pin, path escape, symlink, oversized source, malformed UTF-8/Lean
source, and unsupported inference syntax. The exact example command is:

```sh
cargo run --locked --offline -q \
  --manifest-path npa-core/Cargo.toml -p npa-cli -- \
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

The owning crate must pass `cargo fmt --all --manifest-path
npa-core/Cargo.toml -- --check` and the focused test before UIA-13 can claim
the adapter is implemented.

## UIA-12 closure checklist

| Required decision | Frozen answer |
| --- | --- |
| First ecosystem | Lean 4 `mathlib4`, exact ID `lean4-mathlib4`, selected from the `Function.comp_assoc` pilot survey. |
| Owning Rust crate/module | Existing `npa-cli`, `package_interface_inventory.rs`; no new crate or Lean dependency. |
| Command/options | `package inventory-interface` with the required options in the input table; JSON stdout only. |
| Pin verification | Caller supplies local root, repository identity, full lower-case commit SHA, and license; adapter validates shape/confinement and hashes exact selected bytes without Git/network. |
| Output schema | `npa.mathlib.interface_inventory.v1`, exact pin/input-file/row/diagnostic field order above. |
| Normalized rows | `module_layout`, `module_import`, `declaration`, and `use_site`, with canonical proposal provenance fields repeated on every row. |
| Diagnostics | Exact category/reason table above; unsupported syntax fails closed. |
| Limits | Exact UIA-12 resource table above. |
| Human boundary | Read-only, `proof_evidence=false`, no proposal/status/catalog mutation, explicit human review before canonical adoption. |

There are no open implementation choices for UIA-13. Additional ecosystems
remain explicitly deferred until a later decision; they do not alter this v1
contract.
