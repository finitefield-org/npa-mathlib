# L2 Namespace Transport Policy

The canonical policy is
[`../policy/l2-namespace-transport-policy.json`](../policy/l2-namespace-transport-policy.json).
It permits an accepted `Proofs.Ai.*` closure to retain the `reviewed` maturity
label after becoming `Mathlib.*` only when a
canonical `npa.l2_namespace_transport_request.v1` declares a one-to-one module
and global-name map and decoded certificate semantics are structurally equal
after applying that map.

`npa package validate-l2-namespace-transport` validates the current v2 source
acceptance and referenced reports, requires selected target modules to be
absent from the clean baseline, requires dependency modules to be unchanged,
rejects unrelated target-manifest changes, resolves certificate term/level and
global-reference indexes into an ID-independent typed projection, and runs the
source-free reference verifier against source and target packages. It emits an
immutable source-owned `npa.l2_namespace_transport_attestation.v2` with exact
policy, mapping, acceptance, snapshot, certificate, and normalized-closure
hashes.

Module split/merge, declaration deletion, changed types or bodies, additional
selected declarations, or incomplete mappings require fresh exact target L2
review before the target may be called `reviewed`. They do not necessarily bar
`verified` catalog admission under `catalog-policy.md`. Transport policy
evidence always has `proof_evidence: false` and does not replace certificate
verification.

In particular, declaration-level promotion is not an L2 transport mechanism.
Its verified materialization attestation may retain source L2 identifiers as
historical provenance, but neither that attestation nor normalized closure
equality establishes target `reviewed` maturity.
