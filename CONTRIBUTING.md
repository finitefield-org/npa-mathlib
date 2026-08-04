# Contributing

`npa-mathlib` is certificate-first. Changes are accepted by source-free package
verification, not by parser, elaborator, tactic, automation, command status, or
release metadata.

Before opening a pull request, run:

```sh
npa package check --root . --json
npa package build-certs --root . --check --json
npa package verify-certs --root . --checker reference --json
npa package check-hashes --root . --json
npa package axiom-report --root . --check --json
npa package index --root . --check --json
```

If `generated/publish-plan.json` changes, also run:

```sh
npa package publish-plan --root . --check --json
```

For downstream smoke changes, also run:

```sh
npa package check --root fixtures/downstream-smoke --json
npa package build-certs --root fixtures/downstream-smoke --check --json
npa package verify-certs --root fixtures/downstream-smoke --checker reference --json
npa package check-hashes --root fixtures/downstream-smoke --json
```

Do not add custom axioms, `sorry`-style placeholders, registry lookup, latest
version resolution, hidden package caches, plugin loading, or network package
fetching as part of proof acceptance.

Follow the public module namespace policy in
[`docs/namespace-policy.md`](docs/namespace-policy.md) and the mutable-catalog
contract in [`docs/catalog-policy.md`](docs/catalog-policy.md). Local modules in
this package use `Mathlib.*`, while `Std.*` stays in `npa-std`. Catalog HEAD is
not stable API; released snapshots and their artifact bytes are immutable.

The current released public module set and package version are enumerated in
[`README.md`](README.md) and `npa-package.toml`; the five-module initial-release
list is historical and is not the current boundary. New and changed catalog
entries must satisfy `AGENTS.md`: certificate-first verification, deterministic
hashes, explicit imports and axioms, accurate current naming, provenance, and
package gates establish `verified` maturity. L2 acceptance establishes the
optional `reviewed` label. If current registry tooling cannot represent the
entry, stop as `tooling-blocked`; do not bypass the canonical registry.
