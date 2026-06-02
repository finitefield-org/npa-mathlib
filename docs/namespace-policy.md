# Namespace Policy

This document fixes the public module namespace policy for `npa-mathlib`.

Module names are proof-relevant package identifiers. They affect certificate
identity, export hashes, package locks, axiom reports, theorem indexes, publish
plans, release bundles, and downstream import fixtures. Treat a released module
name as stable public API.

## Top-Level Boundary

`npa-mathlib` owns the `Mathlib.*` namespace.

`npa-std` owns the `Std.*` namespace. Do not add `Std.*` modules to
`npa-mathlib`; import `Std.*` modules through hash-pinned package imports.

Historical `Proofs.Ai.*` names are corpus and seed names. They must not appear
in public `npa-mathlib` manifests, package locks, publish plans, source
modules, or downstream smoke fixtures.

Downstream packages should use their own package-local namespace. They should
not define new local `Mathlib.*` modules unless they are becoming part of
`npa-mathlib`.

## Stability Rules

Released module names are stable.

Do not rename, move, or repurpose a released module to mean something else. If a
better organization becomes necessary, add a new module and keep the old module
available until a separately documented breaking release policy exists.

Adding a module requires the generated package artifacts to be refreshed:

- `generated/package-lock.json`
- `generated/axiom-report.json`
- `generated/theorem-index.json`
- `generated/publish-plan.json`
- downstream import fixtures that consume the new public artifact

The package must continue to pass source-free reference verification after the
change.

## Path Rule

The filesystem path must match the module name:

```text
Mathlib.Logic.Basic      -> Mathlib/Logic/Basic/
Mathlib.Data.Nat.Basic   -> Mathlib/Data/Nat/Basic/
Mathlib.Core.Reduction   -> Mathlib/Core/Reduction/
```

Each module directory may contain authoring helpers such as `source.npa`,
`replay.json`, and `meta.json`, but proof acceptance depends on canonical
certificate bytes and verifier results, not on those helpers.

## Fixed Layer 0 Modules

The first public release is limited to these modules:

```text
Mathlib.Logic.Basic
Mathlib.Logic.Prop
Mathlib.Logic.Eq
Mathlib.Data.Nat.Basic
Mathlib.Core.Reduction
```

This mapping is fixed:

| Source corpus module | Public module | Public path |
| --- | --- | --- |
| `Proofs.Ai.Basic` | `Mathlib.Logic.Basic` | `Mathlib/Logic/Basic/` |
| `Proofs.Ai.Prop` | `Mathlib.Logic.Prop` | `Mathlib/Logic/Prop/` |
| `Proofs.Ai.Eq` | `Mathlib.Logic.Eq` | `Mathlib/Logic/Eq/` |
| `Proofs.Ai.Nat` | `Mathlib.Data.Nat.Basic` | `Mathlib/Data/Nat/Basic/` |
| `Proofs.Ai.Reduction` | `Mathlib.Core.Reduction` | `Mathlib/Core/Reduction/` |

## Category Rules

Use short, domain-oriented module names. Prefer adding one coherent module over
splitting tiny fragments.

Use `Mathlib.Logic.*` for propositional logic, equality facts, and structural
logical combinators.

Use `Mathlib.Data.<Type>.*` for concrete data types and their elementary
facts. The first file for a type should be `Basic`.

Use `Mathlib.Core.*` only for facts directly about NPA core behavior or
certificate-facing semantics, such as reduction examples. Do not place general
mathematics there.

Use `Mathlib.Algebra.*` for algebraic structures and algebraic theorem groups.
Prefer names such as `Mathlib.Algebra.Ring` and `Mathlib.Algebra.Square` for
the next small layer.

Use `Mathlib.Vector.*`, `Mathlib.Geometry.*`, `Mathlib.Analysis.*`, and similar
domain prefixes when the subject has grown beyond a single algebra or data
module.

Avoid package names, implementation strategies, proof generation methods,
authors, release numbers, or trust levels in module names. For example, do not
use `Mathlib.Ai.*`, `Mathlib.Generated.*`, `Mathlib.Release0.*`, or
`Mathlib.Verified.*`.

## Naming Checklist

Before adding a public module, check:

- The module starts with `Mathlib.`.
- The module path matches the dotted name.
- The name describes mathematical content, not provenance or tooling.
- The module is closed over declared package imports.
- No public artifact contains stale `Proofs.Ai.*` or seed names.
- The module can be consumed source-free by downstream packages.
