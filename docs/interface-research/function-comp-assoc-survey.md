# UIA-02 pilot survey: `Function.comp_assoc`

Status: manually audited for UIA-02 on 2026-08-02. This record is survey
evidence only; it is not an adopted proposal, an NPA proof, a certificate, a
catalog admission, an L2 decision, or a release claim.

## Pilot identity and decision

- **Pilot slug:** `function-comp-assoc`
- **Proposed target module:** `Mathlib.Logic.Function.Basic`
- **Intended change kind:** `add`
- **Selected public declaration:** `comp_assoc` (source spelling
  `Function.comp_assoc`)
- **Current catalog version audited:** `npa-mathlib` `0.2.4`
- **Current catalog revision:** `baad2646a6fc6cf4962ed3011144b9edc23a9589`
- **Current catalog result:** neither `Mathlib.Logic.Function.Defs` nor a
  catalog declaration named `comp_assoc` is present in
  `npa-mathlib/npa-package.toml` or the tracked `Mathlib/` artifact tree.
  The intended relation is therefore an addition, not a revise, rename,
  replace, split, or merge.

The first pilot is deliberately one theorem whose statement is a structural
identity of ordinary function composition. It is small enough to form one
source-free NPA module closure: the only intended proof term is an
independently authored reflexivity term, and its only support term is a local
definition of ordinary composition. It exercises the important
interface-adoption questions—universe binders, argument order, exact function
types, a pinned downstream rewrite, an explicit import boundary, and a new
module path—without dragging Nat arithmetic, typeclass inference, quotient
constructions, or a large theorem family into the first implementation.

The upstream declaration is in a broad definitions file, so this survey
intentionally selects one bounded declaration closure rather than importing
the entire file's public surface. The NPA target uses the policy-preferred
`Basic` layer for the first public function foundation; it does not mechanically
copy the upstream `Defs` path. The target NPA module will not claim to
reproduce the unselected `dcomp`, `prod`, `onFun`, `Bijective`, or other
declarations. A later proposal may consider those independently when usage
evidence and NPA support justify them.

## Immutable repository evidence

All external rows below use the same full Git commit. No branch, default
branch, movable tag, search URL, or unpinned generated documentation is used
as adoption evidence.

| Field | Declaration repository and use-site repository |
| --- | --- |
| repository URL | `https://github.com/leanprover-community/mathlib4` |
| immutable revision | `c5ea00351c28e24afc9f0f84379aa41082b1188f` (`v4.30.0` commit, pinned here by full SHA) |
| license | `Apache-2.0`, confirmed from the repository `LICENSE` at the same revision and the file headers below |
| declaration source URL | `https://github.com/leanprover-community/mathlib4/blob/c5ea00351c28e24afc9f0f84379aa41082b1188f/Mathlib/Logic/Function/Defs.lean` |
| use-site source URL | `https://github.com/leanprover-community/mathlib4/blob/c5ea00351c28e24afc9f0f84379aa41082b1188f/Mathlib/Logic/Function/Iterate.lean` |

The repository is the declaration authority for the observed upstream
interface. It is not an NPA artifact origin: the eventual NPA source and
certificate must be authored independently and must pass the existing
certificate-first catalog gates.

## Declaration observation

At `Mathlib/Logic/Function/Defs.lean:65-66`, inside `namespace Function`, the
upstream declaration is:

```lean
theorem comp_assoc (f : φ → δ) (g : β → φ) (h : α → β) :
    (f ∘ g) ∘ h = f ∘ g ∘ h :=
  rfl
```

The source header declares `α`, `β`, `φ`, and `δ` as independent `Sort*`
variables. The source declaration therefore has four universe parameters and
the following complete target-side NPA surface after the unqualified
declaration name `comp_assoc`. The source name `Function.comp` is represented
by the unqualified, non-public support definition `comp` because the current
NPA reference environment exposes only its documented core primitives
(`Nat`, `Eq`, and their generated families), not a `Function.comp` primitive.
The `imax u1 u4` expression is the equality universe of the compared function
type:

```text
forall (alpha : Sort u1), forall (beta : Sort u2), forall (phi : Sort u3), forall (delta : Sort u4), forall (f : forall (x : phi), delta), forall (g : forall (x : beta), phi), forall (h : forall (x : alpha), beta), @Eq.{imax u1 u4} (forall (x : alpha), delta) (@comp.{u1,u2,u4} alpha beta delta (@comp.{u2,u3,u4} beta phi delta f g) h) (@comp.{u1,u3,u4} alpha phi delta f (@comp.{u1,u2,u3} alpha beta phi g h))
```

The support closure and independently authored NPA body planned for UIA-03
are:

```text
def comp.{u1,u2,u3} :
  forall (alpha : Sort u1), forall (beta : Sort u2), forall (gamma : Sort u3), forall (f : forall (x : beta), gamma), forall (g : forall (x : alpha), beta), forall (x : alpha), gamma :=
  fun alpha => fun beta => fun gamma => fun f => fun g => fun x => f (g x)
```

```text
fun alpha => fun beta => fun phi => fun delta => fun f => fun g => fun h => @Eq.refl.{imax u1 u4} (forall (x : alpha), delta) (@comp.{u1,u2,u4} alpha beta delta (@comp.{u2,u3,u4} beta phi delta f g) h)
```

The body is a proposed NPA term, not copied external proof source. Its
definitional-equality justification is that the local `comp` body reduces both
compositions to the same lambda application. The support definition is
reachable from the public theorem signature, has no direct external evidence,
and is admitted only with that support rationale. UIA-03 records this mapping;
the later certificate gates must confirm that the exact syntax is accepted by
the current frontend.

The public declaration is a theorem rather than a definition. Its only
same-module dependency is the non-public support definition `comp`; it has no
same-module theorem dependency, no inductive family, and no typeclass or
instance dependency. Its argument order is `f`, then `g`, then `h`, matching
the upstream statement. The target uses ordinary function equality; it does
not introduce a quotient or a quotient-backed public interface. No bundled
function structure is involved, and no unbundled/bundled alternative changes
the selected surface.

## Import and module-layout observations

The pinned upstream declaration file has these imports:

```text
public import Mathlib.Init
import Mathlib.Tactic.Attr.Register
```

The attribute import is not part of the selected theorem's mathematical
surface. The NPA proposal's direct import boundary is intentionally narrower:
`Mathlib.Logic.Eq`, an existing current-catalog module whose hash-pinned
`Std.Logic.Eq` closure supplies equality syntax. The target defines the
source-mapped `comp` support term locally instead of assuming an unavailable
`Function.comp` import. UIA-03 must record and verify this exact boundary and
must not add an undeclared remote or upstream import.

The module path follows the NPA namespace policy: `Mathlib.Logic.*` is the
structural-logic category, and `Basic` is the policy-preferred first abstract
foundation for a public function API. This is an intentional target-side
placement decision; the declaration observation remains pinned to the
upstream `Defs` file. The corresponding canonical proposal path will be:

```text
npa-mathlib/interface-proposals/Mathlib/Logic/Function/Basic.toml
```

The catalog currently has `Mathlib.Logic.Basic`, `Mathlib.Logic.Eq`, and
`Mathlib.Logic.EqReasoning`, but neither `Mathlib.Logic.Function.Basic` nor a
catalog declaration named `comp_assoc` is present. Existing
`Mathlib.Logic.Basic.compose_assoc` is a different application-level theorem:
it returns an equality after applying three functions to an argument and does
not collide with the proposed function-equality theorem. It is recorded as an
alternative, not as a source or compatibility dependency.

## Concrete downstream use evidence

At `Mathlib/Logic/Function/Iterate.lean:96-98`, the pinned downstream theorem
`Function.iterate_invariant` rewrites with `← comp_assoc` in its successor
case:

```lean
theorem iterate_invariant {g : α → β} (h : g ∘ f = g) (n : ℕ) :
    g ∘ f^[n] = g := match n with
  | 0 => by rw [iterate_zero, comp_id]
  | m + 1 => by rwa [iterate_succ, ← comp_assoc, iterate_invariant h m]
```

This is a concrete theorem-level rewrite use, not merely a module import or a
documentation mention. The same file imports
`Mathlib.Logic.Function.Conjugate`, which imports
`Mathlib.Logic.Function.Basic`, which publicly imports the selected
`Mathlib.Logic.Function.Defs` layer. The import chain and the rewrite are
recorded separately so use evidence is not confused with a proof reference.

The use site relies on the exact right-associated composition shape and on the
source argument order. It does not use an instance, typeclass, inference
side-effect, generated family member, or bundled function object. No separate
term-level application of `comp_assoc` appears outside the rewrite; the
successor rewrite is the reason the pilot records both a `module_import` and a
`rewrite` observation for the same immutable use-site revision.

## Normalized observation inventory

The following rows are the complete evidence inventory intended to enter the
UIA-03 proposal. Each row has a full immutable revision, known license,
repository path, source module/declaration where applicable, usage kind, and a
bounded note.

| id | path | source module | source declaration | usage_kind | observation notes |
| --- | --- | --- | --- | --- | --- |
| `mathlib-defs-layout` | `Mathlib/Logic/Function/Defs.lean` | `Mathlib.Logic.Function.Defs` | — | `module_layout` | The upstream function structural-logic definitions boundary contains `comp_assoc`; the NPA target places the selected closure in the policy-preferred `Mathlib.Logic.Function.Basic` module. |
| `mathlib-defs-declaration` | `Mathlib/Logic/Function/Defs.lean` | `Mathlib.Logic.Function.Defs` | `Function.comp_assoc` | `declaration` | The declaration has four `Sort*` parameters, ordered arguments `f`, `g`, `h`, and an exact function-equality statement. |
| `mathlib-defs-import-init` | `Mathlib/Logic/Function/Defs.lean` | `Mathlib.Logic.Function.Defs` | — | `module_import` | The source imports `Mathlib.Init`; NPA maps the unavailable core Function.comp name to the locally authored non-public comp support definition. |
| `mathlib-iterate-import` | `Mathlib/Logic/Function/Iterate.lean` | `Mathlib.Logic.Function.Iterate` | — | `module_import` | The downstream file reaches the selected layer through its public function-module import chain. |
| `mathlib-iterate-rewrite` | `Mathlib/Logic/Function/Iterate.lean` | `Mathlib.Logic.Function.Iterate` | `Function.iterate_invariant` | `rewrite` | The successor branch uses `← comp_assoc` to reassociate `g ∘ f^[m] ∘ f`; this is the concrete downstream use requiring the proposed theorem. |

Every row above has:

```text
repository = https://github.com/leanprover-community/mathlib4
revision_kind = git_commit
revision = c5ea00351c28e24afc9f0f84379aa41082b1188f
license = Apache-2.0
```

The local catalog collision check is additionally pinned to the current NPA
commit listed at the top of this survey. It is a package-state observation,
not a substitute for the external declaration/use evidence.

## Proof references (separate from use evidence)

The adopted theorem will use one separate proof-reference row:

| id | repository | revision | license | path | source module | source declaration | reference_role | notes |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `mathlib-comp-assoc-proof-structure` | `https://github.com/leanprover-community/mathlib4` | `c5ea00351c28e24afc9f0f84379aa41082b1188f` | `Apache-2.0` | `Mathlib/Logic/Function/Defs.lean` | `Mathlib.Logic.Function.Defs` | `Function.comp_assoc` | `proof_structure` | The pinned source records a reflexivity proof for the composition identity. It may guide independent proof design only; no external proof term or source block will be copied. |

The proof-reference row is not one of the observation IDs above. It does not
claim an NPA certificate, a kernel verdict, source-free verification, a
promotion origin, or permission to translate upstream proof code. The eventual
NPA proof will be authored separately and checked through the normal
certificate workflow.

## Alternatives and boundary decisions

| kind | candidate | disposition | rationale | evidence |
| --- | --- | --- | --- | --- |
| `module_name` | `Mathlib.Logic.Function.Defs` | `rejected` | This mirrors the upstream file too literally; NPA's namespace policy prefers `Basic` for the first public abstract function foundation, while the selected declaration remains pinned to `Defs` as its declaration evidence. | `mathlib-defs-layout`, `mathlib-iterate-import` |
| `module_name` | `Mathlib.Algebra.Function.Basic` | `rejected` | Function composition is structural logic, not an algebraic law package; the namespace policy places it under `Mathlib.Logic.*`. | `mathlib-defs-layout` |
| `declaration_name` | `compose_assoc` | `rejected` | The name collides semantically with the current catalog's application-level `Mathlib.Logic.Basic.compose_assoc`; `comp_assoc` preserves the observed API name and avoids a misleading duplicate. | `mathlib-defs-declaration` |
| `signature` | applied pointwise equality `forall x, ...` | `rejected` | The downstream rewrite consumes equality of functions, not merely a theorem requiring a fresh point argument; weakening the statement would not match the observed rewrite shape. | `mathlib-defs-declaration`, `mathlib-iterate-rewrite` |
| `module_boundary` | copy the complete upstream `Defs` file | `rejected` | The first pilot must be one coherent, evidence-backed closure; unobserved declarations such as `dcomp`, `prod`, `onFun`, and `Bijective` are outside this proposal. | `mathlib-defs-layout` |

No quotient or quotient-backed alternative was considered necessary. A setoid
formulation would be materially unrelated to this function-equality identity.
No bundled function structure or instance-driven formulation is selected.

## Adoption checklist result

The UIA-01 manual checklist was run against this survey:

1. **Naming and path — pass.** The target starts with `Mathlib.`, mirrors the
   filesystem path, and belongs to the structural-logic category. `Basic` is
   the policy-preferred first abstract function foundation, and the target is
   one coherent theorem closure even though its declaration evidence is in
   upstream `Defs`.
2. **Exact NPA terms — pass pending UIA-03 parser confirmation.** The complete
   universe-expanded signature, argument order, result shape, local support
   body, and independent reflexivity body are recorded. No placeholder, source
   notation, quotient, or copied proof term is used. Frontend parseability is
   an explicit UIA-03 gate, not claimed by this survey.
3. **Public/support boundary — pass.** There is one public theorem and one
   reachable support definition (`comp`) with an explicit support rationale;
   there is no inductive family. The theorem has direct use-site evidence and
   no same-module theorem dependency.
4. **Use-site evidence — pass.** The declaration repository and concrete
   downstream rewrite site are pinned to the same full commit. Declaration,
   import, direct-application, rewrite, and layout rows are separated.
5. **Alternatives — pass.** Module, name, pointwise-vs-function equality, and
   whole-file boundary alternatives are recorded with dispositions.
6. **Licensing and provenance — pass.** The repository license is known as
   Apache-2.0 and every external row carries the full revision. Upstream is
   evidence authority only; eventual artifact ownership remains undecided.
7. **Proof references — pass.** The proof reference is separate from use
   evidence, pinned, and explicitly disclaims proof-source copying.
8. **Imports and policy — pass pending UIA-03 frontend confirmation.** The
   intended direct NPA import is the existing `Mathlib.Logic.Eq` boundary; no
   remote lookup or undeclared import is proposed. The interface uses no
   quotient and claims no axiom policy result.
9. **Collisions and catalog relation — pass.** The current package has no
   target module or `comp_assoc` declaration; the relation is `add` with an
   empty `source_modules` list.
10. **Decision record — pass.** The pilot slug, target, scope, evidence,
    alternatives, exact surface, and follow-up gates are fixed here. Adoption
    itself is deferred to UIA-03 and must be recorded in the canonical TOML
    proposal, not inferred from this survey.

## Explicit non-claims and next task

This survey does not claim that the NPA frontend currently accepts the exact
universe-expanded term, that a certificate exists, that the package manifest
has changed, or that the catalog has admitted the module. It does not copy
upstream source or proof terms. UIA-03 must create the one revision-1 adopted
proposal at the mirrored path, confirm the exact signature/body/imports with
the current frontend, and complete the manual adoption review before any
validator or artifact implementation treats this surface as a contract.
