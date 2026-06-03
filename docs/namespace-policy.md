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

## Released Modules

The `v0.1.0` release fixed these Layer 0 modules:

```text
Mathlib.Logic.Basic
Mathlib.Logic.Prop
Mathlib.Logic.Eq
Mathlib.Data.Nat.Basic
Mathlib.Core.Reduction
```

The `v0.1.1` release adds these Layer 1 modules:

```text
Mathlib.Algebra.Ring
Mathlib.Algebra.Square
Mathlib.Algebra.OrderedField
```

The `v0.1.2` release adds these Layer 2A modules:

```text
Mathlib.Vector.Basic
Mathlib.Vector.Dot
```

The `v0.1.3` release adds these Layer 2B modules:

```text
Mathlib.Geometry.RightTriangle
Mathlib.Geometry.Metric
```

The `v0.1.4` release adds these Layer 3A modules:

```text
Mathlib.Logic.EqReasoning
Mathlib.Algebra.Group.Basic
```

The `v0.1.5` release adds this Layer 3B module:

```text
Mathlib.Algebra.Group.Subgroup
```

The `v0.1.6` release adds this Layer 3C module:

```text
Mathlib.Algebra.Group.Subgroup.Order
```

The `v0.1.7` release adds these Layer 3D-A modules:

```text
Mathlib.Algebra.Group.Kernel
Mathlib.Algebra.Group.Image
```

The `v0.1.8` release adds these Layer 3D-B modules:

```text
Mathlib.Algebra.Group.Kernel.Quotient
Mathlib.Algebra.Group.Kernel.Quotient.Mul
Mathlib.Algebra.Group.Kernel.Quotient.Group
Mathlib.Algebra.Group.Kernel.Quotient.Hom
```

The `v0.1.9` release adds these Layer 3D-C modules:

```text
Mathlib.Algebra.Group.FirstIsomorphism
Mathlib.Algebra.Group.FirstIsomorphism.Image
```

The `v0.1.10` release adds these Layer 3D-D modules:

```text
Mathlib.Algebra.Group.Quotient
Mathlib.Algebra.Group.Quotient.Mul
Mathlib.Algebra.Group.Quotient.Group
```

The `v0.1.11` release adds these Layer 3D-E modules:

```text
Mathlib.Algebra.Group.SecondIsomorphism
Mathlib.Algebra.Group.SecondIsomorphism.Map
Mathlib.Algebra.Group.SecondIsomorphism.Kernel
Mathlib.Algebra.Group.SecondIsomorphism.Image
```

The `v0.1.12` release adds this Layer 3D-F module:

```text
Mathlib.Algebra.Group.ThirdIsomorphism
```

The `v0.1.13` release adds these Layer 3D-G modules:

```text
Mathlib.Algebra.Group.Correspondence.Basic
Mathlib.Algebra.Group.Correspondence.Order
Mathlib.Algebra.Group.Correspondence
Mathlib.Algebra.Group.Correspondence.Ordered
```

The `v0.1.14` release adds this Layer 3E module:

```text
Mathlib.Logic.Iff
```

The `v0.1.15` release adds this abstract ring foundation module:

```text
Mathlib.Algebra.Ring.Basic
```

The `v0.1.16` release adds these ring first-isomorphism and CRT modules:

```text
Mathlib.Algebra.Ring.FirstIsomorphism.Basic
Mathlib.Algebra.Ring.FirstIsomorphism
Mathlib.Algebra.Ring.ChineseRemainder
```

The `v0.1.17` release adds these ordered algebra and square-normalization
modules:

```text
Mathlib.Algebra.OrderedField.Basic
Mathlib.Algebra.OrderedField.Square
Mathlib.Algebra.OrderedField.ScalarIdentities
```

The Layer 0 mapping is fixed:

| Source corpus module | Public module | Public path |
| --- | --- | --- |
| `Proofs.Ai.Basic` | `Mathlib.Logic.Basic` | `Mathlib/Logic/Basic/` |
| `Proofs.Ai.Prop` | `Mathlib.Logic.Prop` | `Mathlib/Logic/Prop/` |
| `Proofs.Ai.Eq` | `Mathlib.Logic.Eq` | `Mathlib/Logic/Eq/` |
| `Proofs.Ai.Nat` | `Mathlib.Data.Nat.Basic` | `Mathlib/Data/Nat/Basic/` |
| `Proofs.Ai.Reduction` | `Mathlib.Core.Reduction` | `Mathlib/Core/Reduction/` |

The Layer 1 mapping is fixed:

| Source corpus module | Public module | Public path |
| --- | --- | --- |
| `Proofs.Ai.Algebra.Ring` | `Mathlib.Algebra.Ring` | `Mathlib/Algebra/Ring/` |
| `Proofs.Ai.Algebra.Square` | `Mathlib.Algebra.Square` | `Mathlib/Algebra/Square/` |
| `Proofs.Ai.OrderedField` | `Mathlib.Algebra.OrderedField` | `Mathlib/Algebra/OrderedField/` |

The Layer 2A mapping is fixed:

| Source corpus module | Public module | Public path |
| --- | --- | --- |
| `Proofs.Ai.Vector.Basic` | `Mathlib.Vector.Basic` | `Mathlib/Vector/Basic/` |
| `Proofs.Ai.Vector.Dot` | `Mathlib.Vector.Dot` | `Mathlib/Vector/Dot/` |

The Layer 2B mapping is fixed:

| Source corpus module | Public module | Public path |
| --- | --- | --- |
| `Proofs.Ai.Geometry.RightTriangle` | `Mathlib.Geometry.RightTriangle` | `Mathlib/Geometry/RightTriangle/` |
| `Proofs.Ai.Geometry.Metric` | `Mathlib.Geometry.Metric` | `Mathlib/Geometry/Metric/` |

The Layer 3A mapping is fixed:

| Source corpus module | Public module | Public path |
| --- | --- | --- |
| `Proofs.Ai.EqReasoning` | `Mathlib.Logic.EqReasoning` | `Mathlib/Logic/EqReasoning/` |
| `Proofs.Ai.Algebra.AbstractGroup` | `Mathlib.Algebra.Group.Basic` | `Mathlib/Algebra/Group/Basic/` |

The Layer 3B mapping is fixed:

| Source corpus module | Public module | Public path |
| --- | --- | --- |
| `Proofs.Ai.Algebra.AbstractGroupSubgroup` | `Mathlib.Algebra.Group.Subgroup` | `Mathlib/Algebra/Group/Subgroup/` |

The Layer 3C mapping is fixed:

| Source corpus module | Public module | Public path |
| --- | --- | --- |
| `Proofs.Ai.Algebra.AbstractGroupSubgroupOrder` | `Mathlib.Algebra.Group.Subgroup.Order` | `Mathlib/Algebra/Group/Subgroup/Order/` |

The Layer 3D-A mapping is fixed:

| Source corpus module | Public module | Public path |
| --- | --- | --- |
| `Proofs.Ai.Algebra.AbstractGroupKernel` | `Mathlib.Algebra.Group.Kernel` | `Mathlib/Algebra/Group/Kernel/` |
| `Proofs.Ai.Algebra.AbstractGroupImage` | `Mathlib.Algebra.Group.Image` | `Mathlib/Algebra/Group/Image/` |

The Layer 3D-B mapping is fixed:

| Source corpus module | Public module | Public path |
| --- | --- | --- |
| `Proofs.Ai.Algebra.AbstractGroupQuotient` | `Mathlib.Algebra.Group.Kernel.Quotient` | `Mathlib/Algebra/Group/Kernel/Quotient/` |
| `Proofs.Ai.Algebra.AbstractGroupQuotientMul` | `Mathlib.Algebra.Group.Kernel.Quotient.Mul` | `Mathlib/Algebra/Group/Kernel/Quotient/Mul/` |
| `Proofs.Ai.Algebra.AbstractGroupQuotientGroup` | `Mathlib.Algebra.Group.Kernel.Quotient.Group` | `Mathlib/Algebra/Group/Kernel/Quotient/Group/` |
| `Proofs.Ai.Algebra.AbstractGroupQuotientHom` | `Mathlib.Algebra.Group.Kernel.Quotient.Hom` | `Mathlib/Algebra/Group/Kernel/Quotient/Hom/` |

The Layer 3D-C mapping is fixed:

| Source corpus module | Public module | Public path |
| --- | --- | --- |
| `Proofs.Ai.Algebra.AbstractGroupFirstIsoFull` | `Mathlib.Algebra.Group.FirstIsomorphism` | `Mathlib/Algebra/Group/FirstIsomorphism/` |
| `Proofs.Ai.Algebra.AbstractGroupFirstIsoImage` | `Mathlib.Algebra.Group.FirstIsomorphism.Image` | `Mathlib/Algebra/Group/FirstIsomorphism/Image/` |

The Layer 3D-D mapping is fixed:

| Source corpus module | Public module | Public path |
| --- | --- | --- |
| `Proofs.Ai.Algebra.AbstractGroupNormalQuotient` | `Mathlib.Algebra.Group.Quotient` | `Mathlib/Algebra/Group/Quotient/` |
| `Proofs.Ai.Algebra.AbstractGroupNormalQuotientMul` | `Mathlib.Algebra.Group.Quotient.Mul` | `Mathlib/Algebra/Group/Quotient/Mul/` |
| `Proofs.Ai.Algebra.AbstractGroupNormalQuotientGroup` | `Mathlib.Algebra.Group.Quotient.Group` | `Mathlib/Algebra/Group/Quotient/Group/` |

The Layer 3D-E mapping is fixed:

| Source corpus module | Public module | Public path |
| --- | --- | --- |
| `Proofs.Ai.Algebra.AbstractGroupSecondIsoPhi` | `Mathlib.Algebra.Group.SecondIsomorphism.Map` | `Mathlib/Algebra/Group/SecondIsomorphism/Map/` |
| `Proofs.Ai.Algebra.AbstractGroupSecondIsoKernel` | `Mathlib.Algebra.Group.SecondIsomorphism.Kernel` | `Mathlib/Algebra/Group/SecondIsomorphism/Kernel/` |
| `Proofs.Ai.Algebra.AbstractGroupSecondIsoImage` | `Mathlib.Algebra.Group.SecondIsomorphism.Image` | `Mathlib/Algebra/Group/SecondIsomorphism/Image/` |
| `Proofs.Ai.Algebra.AbstractGroupSecondIsoFinal` | `Mathlib.Algebra.Group.SecondIsomorphism` | `Mathlib/Algebra/Group/SecondIsomorphism/` |

The Layer 3D-F mapping is fixed:

| Source corpus module | Public module | Public path |
| --- | --- | --- |
| `Proofs.Ai.Algebra.AbstractGroupThirdIso` | `Mathlib.Algebra.Group.ThirdIsomorphism` | `Mathlib/Algebra/Group/ThirdIsomorphism/` |

The Layer 3D-G mapping is fixed:

| Source corpus module | Public module | Public path |
| --- | --- | --- |
| `Proofs.Ai.Algebra.AbstractGroupCorrespondence` | `Mathlib.Algebra.Group.Correspondence.Basic` | `Mathlib/Algebra/Group/Correspondence/Basic/` |
| `Proofs.Ai.Algebra.AbstractGroupCorrespondenceOrder` | `Mathlib.Algebra.Group.Correspondence.Order` | `Mathlib/Algebra/Group/Correspondence/Order/` |
| `Proofs.Ai.Algebra.AbstractGroupCorrespondenceFinal` | `Mathlib.Algebra.Group.Correspondence` | `Mathlib/Algebra/Group/Correspondence/` |
| `Proofs.Ai.Algebra.AbstractGroupCorrespondenceOrderFinal` | `Mathlib.Algebra.Group.Correspondence.Ordered` | `Mathlib/Algebra/Group/Correspondence/Ordered/` |

The Layer 3E mapping is fixed:

| Source corpus module | Public module | Public path |
| --- | --- | --- |
| `Proofs.Ai.Logic.Iff` | `Mathlib.Logic.Iff` | `Mathlib/Logic/Iff/` |

The abstract ring foundation mapping is fixed:

| Source corpus module | Public module | Public path |
| --- | --- | --- |
| `Proofs.Ai.Algebra.AbstractRing` | `Mathlib.Algebra.Ring.Basic` | `Mathlib/Algebra/Ring/Basic/` |

The ring first-isomorphism and CRT mapping is fixed:

| Source corpus module | Public module | Public path |
| --- | --- | --- |
| `Proofs.Ai.Algebra.AbstractRingFirstIsoBase` | `Mathlib.Algebra.Ring.FirstIsomorphism.Basic` | `Mathlib/Algebra/Ring/FirstIsomorphism/Basic/` |
| `Proofs.Ai.Algebra.AbstractRingFirstIso` | `Mathlib.Algebra.Ring.FirstIsomorphism` | `Mathlib/Algebra/Ring/FirstIsomorphism/` |
| `Proofs.Ai.Algebra.AbstractRingChineseRemainder` | `Mathlib.Algebra.Ring.ChineseRemainder` | `Mathlib/Algebra/Ring/ChineseRemainder/` |

The ordered algebra and square-normalization mapping is fixed:

| Source corpus module | Public module | Public path |
| --- | --- | --- |
| `Proofs.Ai.Algebra.AbstractOrderedField` | `Mathlib.Algebra.OrderedField.Basic` | `Mathlib/Algebra/OrderedField/Basic/` |
| `Proofs.Ai.Algebra.AbstractSquareNormalize` | `Mathlib.Algebra.OrderedField.Square` | `Mathlib/Algebra/OrderedField/Square/` |
| `Proofs.Ai.Algebra.AbstractScalarDerive` | `Mathlib.Algebra.OrderedField.ScalarIdentities` | `Mathlib/Algebra/OrderedField/ScalarIdentities/` |

No separate `Mathlib.Algebra.Group.Hom` module is introduced in Layer 3D-A.
The stable homomorphism surface, including `GroupHomLawArgs`, `hom_mul`,
`hom_one`, and `hom_inv`, remains in `Mathlib.Algebra.Group.Basic`.

Layer 3D-B intentionally uses `Mathlib.Algebra.Group.Kernel.Quotient.*`
instead of a broad `Mathlib.Algebra.Group.Quotient.*` surface. The exported
definitions and theorems are specialized to quotienting by a homomorphism
kernel and should not be treated as the general normal-subgroup quotient API.

Layer 3D-C intentionally uses `FirstIsomorphism` instead of `FirstIso` in
public module names. `Proofs.Ai.Algebra.AbstractGroupFirstIso` remains
unpublished because it exposes the provisional `FirstIsoRepMvp` surface.

Layer 3D-D intentionally uses `Mathlib.Algebra.Group.Quotient.*` for quotient
groups by normal subgroups. The already released
`Mathlib.Algebra.Group.Kernel.Quotient.*` surface remains the specialized
kernel-relation quotient route for homomorphisms.

Layer 3D-E intentionally uses `SecondIsomorphism` instead of `SecondIso` in
public module names. `Proofs.Ai.Algebra.AbstractGroupSecondIsoPhi` is published
as `Mathlib.Algebra.Group.SecondIsomorphism.Map` so Greek-letter
implementation terminology does not become a public module identifier.

Layer 3D-F intentionally keeps the third-isomorphism route in one
`Mathlib.Algebra.Group.ThirdIsomorphism` module. The checked-in corpus route is
a single theorem-evidence module, and its helper definitions are tightly
coupled to the final evidence surface.

Layer 3D-G uses `Correspondence.Basic` for the normal-quotient image/preimage
construction surface, `Correspondence.Order` for monotonicity/equivalence facts,
`Correspondence` for the theorem-evidence surface, and `Correspondence.Ordered`
for the final ordered correspondence evidence.

Layer 3E keeps `Iff`, `And`, `Or`, `False`, and `Not` together in
`Mathlib.Logic.Iff`. A future `Mathlib.Logic.Connectives` split is deferred
until another checked corpus route needs the connectives without the `Iff` API.

The abstract ring foundation uses `Mathlib.Algebra.Ring.Basic` for arbitrary
`Scalar` law-package facts. The already released `Mathlib.Algebra.Ring` module
remains the concrete one-element ring route. Both surfaces contain some
declaration names such as `add_assoc` and `ring_normalize_add_mul3`; downstream
packages should import the abstract foundation or the concrete route according
to the proof surface they need, not both in the same source module unless they
have a deliberate disambiguation plan.

The v0.1.16 ring first-isomorphism and CRT route does not introduce a separate
`Mathlib.Algebra.Ring.Hom` module. The checked corpus base module bundles the
ring homomorphism law surface, image predicate, and kernel quotient
construction, so the public `RingHomLawArgs`, `RingImagePred`, and
`RingKerQuot*` names remain in
`Mathlib.Algebra.Ring.FirstIsomorphism.Basic`.

The v0.1.17 ordered algebra route uses `Mathlib.Algebra.OrderedField.Basic`
for the abstract ordered-field law-package surface. The already released
`Mathlib.Algebra.OrderedField` module remains the concrete one-element
ordered-field route. `Mathlib.Algebra.OrderedField.Square` contains the
abstract square-normalization facts, while
`Mathlib.Algebra.OrderedField.ScalarIdentities` contains derived scalar RHS
identities used by later inner-product and geometry routes.

`Proofs.Ai.Geometry.Pythagorean` is intentionally deferred because its current
corpus closure belongs to the abstract geometry / law-package track.

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
Prefer names such as `Mathlib.Algebra.Ring`, `Mathlib.Algebra.Square`, and
`Mathlib.Algebra.Group.Basic` for small foundational layers.

Use `Mathlib.Vector.*`, `Mathlib.Geometry.*`, `Mathlib.Analysis.*`, and similar
domain prefixes when the subject has grown beyond a single algebra or data
module.

Avoid package names, implementation strategies, proof generation methods,
authors, release numbers, or trust levels in module names. For example, do not
use `Mathlib.Ai.*`, `Mathlib.Generated.*`, `Mathlib.Release0.*`, or
`Mathlib.Verified.*`.

## Common Module Shape Examples

Use `Basic` for the first stable module in a domain when a more specific name
would be premature:

```text
Mathlib.Data.Nat.Basic
Mathlib.GroupTheory.Basic
Mathlib.Topology.Basic
Mathlib.Probability.Basic
```

Use short mathematical nouns for core objects:

```text
Mathlib.Algebra.Ring
Mathlib.Algebra.Field
Mathlib.Algebra.Polynomial
Mathlib.Category.Functor
Mathlib.Geometry.Manifold
Mathlib.MeasureTheory.Measure
Mathlib.Optimization.Convex
```

Use established subdomain names when the area is already large:

```text
Mathlib.AlgebraicGeometry.Scheme
Mathlib.Analysis.Functional.Banach
Mathlib.Analysis.Functional.Hilbert
Mathlib.DifferentialEquations.ODE
Mathlib.DifferentialEquations.PDE
Mathlib.NumberTheory.ModularArithmetic
Mathlib.Topology.Algebraic.Homology
```

Use a final topic component for specialized theorem groups:

```text
Mathlib.Algebra.Ring.Ideal
Mathlib.Algebra.Ring.Localization
Mathlib.Analysis.Complex.Holomorphic
Mathlib.Combinatorics.Graph.Coloring
Mathlib.Geometry.Convex.Polytope
Mathlib.GroupTheory.GroupAction
Mathlib.LinearAlgebra.Matrix.Determinant
```

Avoid names that describe how a proof was produced:

```text
Mathlib.Generated.Nat
Mathlib.Ai.Algebra
Mathlib.Tactic.Proved
Mathlib.Search.Result
```

Use instead:

```text
Mathlib.Data.Nat.Basic
Mathlib.Algebra.Basic
Mathlib.Algebra.Ring
Mathlib.Logic.Basic
```

## Field Inventory

The following inventory is based on the MSC2020 two-digit subject areas,
maintained by Mathematical Reviews and zbMATH. MSC2020 has 63 two-digit
classifications; this table covers those broad areas as a namespace review
checklist.

References:

- <https://mathscinet.ams.org/msc/msc2020.html>
- <https://siam-web.useast01.umbraco.io/publications/siam-news/articles/mathematics-subject-classification-2020/>

This inventory is not a commitment to create every prefix. When a field is not
yet represented by checked certificates, keep the entry as a naming example
only.

| MSC area | Field | Preferred prefixes and examples |
| --- | --- | --- |
| `00` | General and overarching mathematics | Avoid catch-all modules; prefer the specific mathematical domain. If unavoidable, use `Mathlib.Meta.*` only for formalized mathematical metadata. |
| `01` | History and biography | Normally documentation, not theorem modules. If formalized, prefer precise domain modules over `Mathlib.History.*`. |
| `03` | Logic and foundations | `Mathlib.Logic.*`, `Mathlib.Foundation.*`, `Mathlib.SetTheory.*`, `Mathlib.ModelTheory.*`, `Mathlib.ProofTheory.*`, `Mathlib.Computability.*`. |
| `05` | Combinatorics | `Mathlib.Combinatorics.Basic`, `Mathlib.Combinatorics.Graph`, `Mathlib.Combinatorics.Enumerative`, `Mathlib.Combinatorics.Matroid`, `Mathlib.Combinatorics.Design`. |
| `06` | Order and lattices | `Mathlib.Order.Basic`, `Mathlib.Order.Lattice`, `Mathlib.Order.BooleanAlgebra`, `Mathlib.Order.OrderedAlgebra`. |
| `08` | General algebraic systems | `Mathlib.Algebra.Universal`, `Mathlib.Algebra.Magma`, `Mathlib.Algebra.Semigroup`, `Mathlib.Algebra.AlgebraicSystem`. |
| `11` | Number theory | `Mathlib.NumberTheory.Basic`, `Mathlib.NumberTheory.Prime`, `Mathlib.NumberTheory.ModularArithmetic`, `Mathlib.NumberTheory.Diophantine`. |
| `12` | Field theory and polynomials | `Mathlib.Algebra.Field`, `Mathlib.Algebra.Polynomial`, `Mathlib.Algebra.Galois`, `Mathlib.Algebra.FiniteField`. |
| `13` | Commutative algebra | `Mathlib.Algebra.Commutative`, `Mathlib.Algebra.Ideal`, `Mathlib.Algebra.Localization`, `Mathlib.Algebra.Noetherian`. |
| `14` | Algebraic geometry | `Mathlib.AlgebraicGeometry.Scheme`, `Mathlib.AlgebraicGeometry.Variety`, `Mathlib.AlgebraicGeometry.Sheaf`, `Mathlib.AlgebraicGeometry.Projective`. |
| `15` | Linear algebra and matrix theory | `Mathlib.LinearAlgebra.Basic`, `Mathlib.LinearAlgebra.Matrix`, `Mathlib.LinearAlgebra.VectorSpace`, `Mathlib.LinearAlgebra.Tensor`, `Mathlib.LinearAlgebra.Determinant`. |
| `16` | Associative rings and algebras | `Mathlib.Algebra.Ring`, `Mathlib.Algebra.AssociativeAlgebra`, `Mathlib.Algebra.Module`, `Mathlib.Algebra.Representation`. |
| `17` | Nonassociative rings and algebras | `Mathlib.Algebra.Lie`, `Mathlib.Algebra.Jordan`, `Mathlib.Algebra.NonAssociative`. |
| `18` | Category theory and homological algebra | `Mathlib.Category.Basic`, `Mathlib.Category.Functor`, `Mathlib.Category.NaturalTransformation`, `Mathlib.Category.Homological`. |
| `19` | K-theory | `Mathlib.KTheory.Basic`, `Mathlib.KTheory.Algebraic`, `Mathlib.KTheory.Topological`. |
| `20` | Group theory | `Mathlib.GroupTheory.Basic`, `Mathlib.GroupTheory.Group`, `Mathlib.GroupTheory.Subgroup`, `Mathlib.GroupTheory.Permutation`, `Mathlib.GroupTheory.GroupAction`. |
| `22` | Topological groups and Lie groups | `Mathlib.TopologicalGroup.Basic`, `Mathlib.TopologicalGroup.Lie`, `Mathlib.LieGroup.Basic`, `Mathlib.LieGroup.LieAlgebra`. |
| `26` | Real functions | `Mathlib.Analysis.Real`, `Mathlib.Analysis.RealFunction`, `Mathlib.Analysis.Real.Continuity`, `Mathlib.Analysis.Real.Differentiability`. |
| `28` | Measure and integration | `Mathlib.MeasureTheory.Basic`, `Mathlib.MeasureTheory.Measure`, `Mathlib.MeasureTheory.Integration`, `Mathlib.MeasureTheory.ProbabilityMeasure`. |
| `30` | One complex variable | `Mathlib.Analysis.Complex`, `Mathlib.Analysis.Complex.Holomorphic`, `Mathlib.Analysis.Complex.Meromorphic`. |
| `31` | Potential theory | `Mathlib.Analysis.Potential`, `Mathlib.Analysis.Potential.HarmonicFunction`, `Mathlib.Analysis.Potential.Capacity`. |
| `32` | Several complex variables and analytic spaces | `Mathlib.Analysis.SeveralComplexVariables`, `Mathlib.Analysis.ComplexManifold`, `Mathlib.Analysis.AnalyticSpace`. |
| `33` | Special functions | `Mathlib.Analysis.SpecialFunctions`, `Mathlib.Analysis.SpecialFunctions.Gamma`, `Mathlib.Analysis.SpecialFunctions.Bessel`, `Mathlib.Analysis.SpecialFunctions.OrthogonalPolynomial`. |
| `34` | Ordinary differential equations | `Mathlib.DifferentialEquations.ODE`, `Mathlib.DifferentialEquations.ODE.Existence`, `Mathlib.DifferentialEquations.ODE.Stability`. |
| `35` | Partial differential equations | `Mathlib.DifferentialEquations.PDE`, `Mathlib.DifferentialEquations.PDE.Elliptic`, `Mathlib.DifferentialEquations.PDE.Parabolic`, `Mathlib.DifferentialEquations.PDE.Hyperbolic`. |
| `37` | Dynamical systems and ergodic theory | `Mathlib.Dynamics.Basic`, `Mathlib.Dynamics.ErgodicTheory`, `Mathlib.Dynamics.Chaos`. |
| `39` | Difference and functional equations | `Mathlib.Equations.Difference`, `Mathlib.Equations.Functional`, `Mathlib.Equations.Recurrence`. |
| `40` | Sequences, series, and summability | `Mathlib.Analysis.Sequence`, `Mathlib.Analysis.Series`, `Mathlib.Analysis.Summability`. |
| `41` | Approximation and expansions | `Mathlib.Approximation.Basic`, `Mathlib.Approximation.Interpolation`, `Mathlib.Approximation.Asymptotic`. |
| `42` | Euclidean harmonic analysis | `Mathlib.Analysis.Harmonic`, `Mathlib.Analysis.Harmonic.Fourier`, `Mathlib.Analysis.Harmonic.SingularIntegral`. |
| `43` | Abstract harmonic analysis | `Mathlib.Analysis.AbstractHarmonic`, `Mathlib.Analysis.AbstractHarmonic.LocallyCompactGroup`, `Mathlib.Analysis.AbstractHarmonic.Pontryagin`. |
| `44` | Integral transforms and operational calculus | `Mathlib.Analysis.IntegralTransform`, `Mathlib.Analysis.IntegralTransform.Laplace`, `Mathlib.Analysis.IntegralTransform.Fourier`. |
| `45` | Integral equations | `Mathlib.Equations.Integral`, `Mathlib.Equations.Integral.Fredholm`, `Mathlib.Equations.Integral.Volterra`. |
| `46` | Functional analysis | `Mathlib.Analysis.Functional`, `Mathlib.Analysis.Functional.Banach`, `Mathlib.Analysis.Functional.Hilbert`, `Mathlib.Analysis.Functional.NormedSpace`. |
| `47` | Operator theory | `Mathlib.Analysis.OperatorTheory`, `Mathlib.Analysis.OperatorTheory.Spectral`, `Mathlib.Analysis.OperatorTheory.CstarAlgebra`. |
| `49` | Variational methods, control, and optimization | `Mathlib.Optimization.Basic`, `Mathlib.Optimization.Variational`, `Mathlib.Optimization.OptimalControl`, `Mathlib.Optimization.Convex`. |
| `51` | Geometry | `Mathlib.Geometry.Basic`, `Mathlib.Geometry.Euclidean`, `Mathlib.Geometry.Projective`, `Mathlib.Geometry.Affine`. |
| `52` | Convex and discrete geometry | `Mathlib.Geometry.Convex`, `Mathlib.Geometry.Convex.Polytope`, `Mathlib.Geometry.Discrete`. |
| `53` | Differential geometry | `Mathlib.Geometry.Differential`, `Mathlib.Geometry.Differential.Manifold`, `Mathlib.Geometry.Differential.Riemannian`. |
| `54` | General topology | `Mathlib.Topology.Basic`, `Mathlib.Topology.TopologicalSpace`, `Mathlib.Topology.Separation`. |
| `55` | Algebraic topology | `Mathlib.Topology.Algebraic`, `Mathlib.Topology.Algebraic.Homotopy`, `Mathlib.Topology.Algebraic.Homology`, `Mathlib.Topology.Algebraic.Cohomology`. |
| `57` | Manifolds and cell complexes | `Mathlib.Topology.Manifold`, `Mathlib.Topology.CellComplex`, `Mathlib.Topology.Knot`. |
| `58` | Global analysis and analysis on manifolds | `Mathlib.Analysis.Global`, `Mathlib.Analysis.Manifold`, `Mathlib.Analysis.Global.EllipticOperator`. |
| `60` | Probability and stochastic processes | `Mathlib.Probability.Basic`, `Mathlib.Probability.RandomVariable`, `Mathlib.Probability.StochasticProcess`, `Mathlib.Probability.Martingale`. |
| `62` | Statistics | `Mathlib.Statistics.Basic`, `Mathlib.Statistics.Estimator`, `Mathlib.Statistics.HypothesisTesting`, `Mathlib.Statistics.Regression`. |
| `65` | Numerical analysis | `Mathlib.NumericalAnalysis.Basic`, `Mathlib.NumericalAnalysis.ErrorBounds`, `Mathlib.NumericalAnalysis.LinearAlgebra`. |
| `68` | Computer science | `Mathlib.ComputerScience.Basic`, `Mathlib.ComputerScience.Automata`, `Mathlib.ComputerScience.Complexity`, `Mathlib.ComputerScience.FormalLanguage`. |
| `70` | Mechanics of particles and systems | `Mathlib.Mechanics.Particles`, `Mathlib.Mechanics.Classical`, `Mathlib.Mechanics.Hamiltonian`. |
| `74` | Mechanics of deformable solids | `Mathlib.Mechanics.Solids`, `Mathlib.Mechanics.Solids.Elasticity`, `Mathlib.Mechanics.Continuum`. |
| `76` | Fluid mechanics | `Mathlib.Mechanics.Fluids`, `Mathlib.Mechanics.Fluids.EulerEquation`, `Mathlib.Mechanics.Fluids.NavierStokes`. |
| `78` | Optics and electromagnetic theory | `Mathlib.Physics.Electromagnetism`, `Mathlib.Physics.Electromagnetism.Maxwell`, `Mathlib.Physics.Optics`. |
| `80` | Thermodynamics and heat transfer | `Mathlib.Physics.Thermodynamics`, `Mathlib.Physics.HeatTransfer`, `Mathlib.Physics.HeatEquation`. |
| `81` | Quantum theory | `Mathlib.Physics.Quantum`, `Mathlib.Physics.Quantum.HilbertSpace`, `Mathlib.Physics.Quantum.QuantumLogic`. |
| `82` | Statistical mechanics and structure of matter | `Mathlib.Physics.StatisticalMechanics`, `Mathlib.Physics.StatisticalMechanics.PartitionFunction`, `Mathlib.Physics.Matter`. |
| `83` | Relativity and gravitational theory | `Mathlib.Physics.Relativity`, `Mathlib.Physics.Relativity.LorentzGeometry`, `Mathlib.Physics.Relativity.GeneralRelativity`. |
| `85` | Astronomy and astrophysics | `Mathlib.Astronomy.Basic`, `Mathlib.Astronomy.CelestialMechanics`, `Mathlib.Astronomy.Cosmology`. |
| `86` | Geophysics | `Mathlib.Geophysics.Basic`, `Mathlib.Geophysics.Seismology`, `Mathlib.Geophysics.FluidDynamics`. |
| `90` | Operations research and mathematical programming | `Mathlib.Optimization.OperationsResearch`, `Mathlib.Optimization.LinearProgramming`, `Mathlib.Optimization.IntegerProgramming`. |
| `91` | Game theory, economics, finance, and social sciences | `Mathlib.GameTheory.Basic`, `Mathlib.Economics.Basic`, `Mathlib.Finance.Mathematical`, `Mathlib.SocialChoice.Basic`. |
| `92` | Biology and other natural sciences | `Mathlib.Biology.Mathematical`, `Mathlib.Biology.PopulationDynamics`, `Mathlib.Biology.Epidemiology`. |
| `93` | Systems theory and control | `Mathlib.Control.Basic`, `Mathlib.Control.LinearSystems`, `Mathlib.Control.DynamicalSystems`. |
| `94` | Information theory, communication, and circuits | `Mathlib.InformationTheory.Basic`, `Mathlib.InformationTheory.CodingTheory`, `Mathlib.InformationTheory.Cryptography`, `Mathlib.Circuits.Basic`. |
| `97` | Mathematics education | Normally documentation, not theorem modules. If formalized as mathematics, place the theorem under its mathematical domain instead of `Mathlib.Education.*`. |

## Naming Checklist

Before adding a public module, check:

- The module starts with `Mathlib.`.
- The module path matches the dotted name.
- The name describes mathematical content, not provenance or tooling.
- The module is closed over declared package imports.
- No public artifact contains stale `Proofs.Ai.*` or seed names.
- The module can be consumed source-free by downstream packages.
