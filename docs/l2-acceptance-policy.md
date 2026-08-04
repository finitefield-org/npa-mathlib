# L2 Acceptance Policy

Theorem-level semantic acceptance establishes the optional `reviewed` maturity
label defined by [`catalog-policy.md`](catalog-policy.md). It is additional
curation metadata on top of canonical certificate verification, not a
prerequisite for `verified` catalog admission. The canonical machine policy is
[`../policy/l2-acceptance-policy.json`](../policy/l2-acceptance-policy.json).
It is policy version 2, validator profile `npa.l2_acceptance.validator.v2`, and
review protocol `npa.l2.subagent-review.v2`.

Do not infer L2 from catalog presence, verifier success, naming, roadmaps, or a
`verified` label. Conversely, missing L2 acceptance means only that `reviewed`
has not been established; it does not by itself reject an otherwise valid
`verified` catalog artifact.

## Authority and artifacts

Two independent read-only sub-agents issue a unanimous 2-of-2 decision:

- `finitefield-org/npa-l2-semantic-review-subagent@2`;
- `finitefield-org/npa-l2-adversarial-review-subagent@2`.

The review/ingestion agent is a non-voting aggregator. It may neither cast nor
edit a vote. Both reviewers consume the exact same canonical
`npa.l2.review-input.v2` file and independently write canonical
`npa.l2.review-report.v1` files. A report binds the exact input path, input
file hash, input self-hash, policy bytes, authority/version, reviewer role,
task, decision ID, ordered required-check results, verdict, and rationale.

The source proof package owns those immutable files, normally under
`proofs/l2-reviews/`, and its canonical `proofs/l2-acceptance.json`. The ledger
uses `npa.l2_acceptance.v2`; every approval references the unchanged report by
package-relative path and exact file hash. Checks and rationales remain solely
in that immutable report. All governance artifacts set
`proof_evidence: false`.

The task families remain `/root/l2_semantic_*` and
`/root/l2_adversarial_*`; decision IDs begin `NPA-L2-SEM-` and
`NPA-L2-ADV-`. Missing, malformed, stale, repeated-task, rejected, deferred, or
disagreeing reports produce no acceptance entry.

## Reproducible workflow

Export one current theorem packet:

```sh
cargo run -q --manifest-path npa-core/Cargo.toml -p npa-cli -- \
  package prepare-l2-review-input \
  --root <source>/proofs \
  --policy npa-mathlib/policy/l2-acceptance-policy.json \
  --module <source-module> --declaration <theorem> \
  --out l2-reviews/<candidate>.input.json --json
```

After the two sub-agents have written their unchanged canonical reports,
aggregate them:

```sh
cargo run -q --manifest-path npa-core/Cargo.toml -p npa-cli -- \
  package aggregate-l2-acceptance \
  --root <source>/proofs \
  --policy npa-mathlib/policy/l2-acceptance-policy.json \
  --review-input l2-reviews/<candidate>.input.json \
  --review l2-reviews/<candidate>.semantic.json \
  --review l2-reviews/<candidate>.adversarial.json \
  --existing l2-acceptance.json --out l2-acceptance.json --json
```

Use `--replace MODULE::DECLARATION` only when intentionally superseding that
exact existing entry. Review inputs use create-or-identical writes. In-place
ledger replacement requires `--existing` and `--out` to name the same path.
`--check` never writes.

Validate current report bytes, their input bytes, policy binding, theorem
hashes, and complete selected-module coverage with:

```sh
cargo run -q --manifest-path npa-core/Cargo.toml -p npa-cli -- \
  package validate-l2-acceptance \
  --root <source>/proofs \
  --policy npa-mathlib/policy/l2-acceptance-policy.json \
  --acceptance <source>/proofs/l2-acceptance.json \
  --module <exact-source-module> --json
```

Repeat `--module` for the exact closure. Version 1 policy and acceptance files
remain parseable only for historical inspection; they cannot satisfy the
current version 2 policy.

## Trust boundary

These artifacts establish review-policy provenance, not a proof. Canonical
`.npcert` bytes, the NPA kernel/reference checker, and cache-off source-free
verification remain authoritative. Process authorities are declared
sub-agent identities, not cryptographic signers; signed orchestrator identity
would require a separately versioned future contract.
