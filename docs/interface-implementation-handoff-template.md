# Interface Implementation Handoff Template

Status: v1 handoff template implemented by UIA-14; the owner/route boundary was
validated by UIA-17. This document is a review aid and a non-proof sidecar. It
does not authorize implementation, catalog admission, L2 review, maturity, or
release.

Use one completed copy for each adopted interface proposal. The completed
handoff must preserve the adopted proposal's exact exported surface. A theorem
proof term may be independently rewritten during implementation, but an
adopted definition body, inductive family, declaration signature, import, or
module name may not change without returning the proposal to `proposed` and
creating the required new proposal revision.

## 1. Handoff identity and proposal pin

Fill every field. `proposal_relative_path` is relative to the canonical
`npa-mathlib/interface-proposals/` root and uses `/` separators.

| Field | Value |
| --- | --- |
| Handoff slug | `<pilot-slug>` |
| Handoff status | `<ready | blocked | superseded>` |
| Proposal-relative path | `<Mathlib/.../Module.toml>` |
| Proposal repository path | `npa-mathlib/interface-proposals/<proposal-relative-path>` |
| Proposal ID | `<exact proposal_id>` |
| Proposal revision | `<positive integer>` |
| Proposal SHA-256 | `sha256:<64 lowercase hexadecimal characters>` |
| Proposal Git tracking | `tracked canonical proposal file; verify with git ls-files --error-unmatch` |
| `proof_evidence` | `false` |

The proposal hash is SHA-256 over the exact tracked proposal bytes. Do not
normalize TOML, trim whitespace, add a final newline, or hash a parsed
representation. Recompute it from the repository root with:

```sh
shasum -a 256 npa-mathlib/interface-proposals/<proposal-relative-path>
```

The recorded value must be rendered as `sha256:` followed by exactly 64
lowercase hexadecimal characters. A path move, any byte change (including
whitespace or TOML formatting), a revision change, or a proposal status or
surface change invalidates this handoff. The implementation owner must stop,
recompute the hash, and obtain a new handoff/re-adoption decision; changing
only this document's hash is not an approval for proposal drift.

## 2. Ownership and provenance

Record the roles separately even when two roles point to the same external
repository. The evidence and proof-reference owners are not artifact owners.

| Role | Required value and boundary |
| --- | --- |
| Interface evidence owner | Immutable repository/revision containing the observed declarations and use sites, with license and paths. It is evidence authority only. |
| Proof-reference owner | Immutable repository/revision containing the consulted proof idea, with reference role and license. It supplies implementation guidance only; no proof source or code may be copied. |
| Artifact owner | The NPA package that will own the independently authored source and certificate. Leave blocked until the current handoff selects it. |

## 3. Target module and complete adopted surface

| Field | Value |
| --- | --- |
| Target module | `<Mathlib.*>` |
| Target source path | `<module path selected by the eventual artifact owner>` |
| Change kind | `<add | revise | rename | split | merge | replace>` |
| Direct imports, in proposal order | `<exact adopted import list>` |
| Permitted custom axioms | `none`; proposal v1 has no `allowed_axioms` field, so any different package-policy requirement blocks the handoff pending separate policy review |
| Inductive families | `none`, or the complete ordered family-member inventory below |

List every adopted declaration exactly once. Support declarations must be
identified as support and must not be silently promoted to public API.

### Declaration inventory

| # | Name | Kind | Surface | Same-module dependencies | Evidence/proof-reference IDs |
| ---: | --- | --- | --- | --- | --- |
| 1 | `<exact declaration name>` | `<definition | theorem | inductive>` | `<public | support>` | `<exact ordered list>` | `<exact IDs>` |

### Exact exported terms

For each definition, include the complete adopted signature and exact body.
For each theorem, include the complete adopted signature; its proof term may be
independently authored and changed without changing this handoff. For each
inductive, include the complete family and generated public members.

```text
declaration: <name>
kind: <kind>
signature: <complete NPA signature>
body: <complete adopted definition body, or "theorem proof term independently authored">
family_members: <complete ordered list, or "none">
```

## 4. Proof boundary and explicit exclusions

### Proof references

| ID | Repository | Revision | License | Path | Source declaration | Reference role | Permitted use |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `<proof-reference-id>` | `<immutable repository>` | `<full revision>` | `<license>` | `<path>` | `<declaration>` | `<proof_structure | other>` | Consult proof shape only; author the NPA proof independently. |

Proof references remain separate from use-site observations. This handoff does
not grant permission to copy external source, proof terms, or code blocks.
State every unselected declaration, alternative, import, generated family,
source file, or proof technique that is outside the closure.

### Explicit exclusions

- `<unselected declaration or family>`
- `<external source/proof code>`
- `<unselected import or module boundary>`
- `<catalog, maturity, L2, or release claim>`

## 5. Artifact owner and catalog route decision

Do not guess these fields. Until the current handoff decision resolves both,
they are blocking and the handoff is not ready for implementation.

| Field | Required state before owner/route selection | Decision owner |
| --- | --- | --- |
| Artifact owner | `BLOCKED: unselected` | Current interface handoff |
| Catalog route | `BLOCKED: unselected; choose source-backed promotion or direct target reconciliation only after owner selection` | Current interface handoff |

After the decision, replace the blocking values with the selected owner, exact
owner-repository paths, and one supported route. Keep the proposal path and
hash unchanged unless a new proposal revision is adopted.

## 6. Handoff review checklist

- [ ] The proposal-relative path exists under the canonical proposal root.
- [ ] The proposal is tracked in Git and its exact bytes recompute to the
      recorded `sha256:` value.
- [ ] The proposal is `adopted` with `proof_evidence = false`.
- [ ] Every adopted declaration appears exactly once in the inventory.
- [ ] Signatures, definition bodies, imports, and inductive families match the
      proposal exactly.
- [ ] The three ownership roles are distinct and understandable.
- [ ] The proof reference is pinned and does not authorize source copying.
- [ ] Explicit exclusions prevent silent surface expansion.
- [ ] Artifact owner and catalog route are either explicitly blocked or filled
      by the current handoff decision; a ready handoff has both filled.
- [ ] No verification, maturity, L2, catalog-admission, or release claim is
      made by this handoff.
