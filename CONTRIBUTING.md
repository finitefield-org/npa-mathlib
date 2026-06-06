# Contributing

`npa-mathlib` is certificate-first. Changes are accepted by source-free package
verification, not by parser, elaborator, tactic, automation, CI status, or
GitHub metadata.

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
[`docs/namespace-policy.md`](docs/namespace-policy.md). In particular, local
modules in this package must use `Mathlib.*`; `Std.*` stays in `npa-std`; and
released module names should be treated as stable public API.

For this initial release, keep the public module set limited to:

```text
Mathlib.Logic.Basic
Mathlib.Logic.Implication
Mathlib.Logic.Eq
Mathlib.Data.Nat.Basic
Mathlib.Core.Reduction
```
