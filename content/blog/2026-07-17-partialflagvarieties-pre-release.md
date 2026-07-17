---
title: "PartialFlagVarieties.jl, a first pre-release"
slug: "partialflagvarieties-pre-release"
date: 2026-07-17
author: "Pieter Belmans"
---

[PartialFlagVarieties.jl](https://github.com/HomogeneousTools/PartialFlagVarieties.jl)
is now public, as a pre-release: `v0.1.0`.
It is the second piece of the [HomogeneousTools](https://homogeneous.tools) project,
built on top of [Semisimple.jl](https://github.com/HomogeneousTools/Semisimple.jl),
and it is the one we have wanted to release for a long time.

Where Semisimple.jl handles the representation theory of semisimple Lie algebras,
PartialFlagVarieties.jl uses it to do algebraic geometry on the partial flag varieties $G/P$:
equivariant vector bundles, sheaf cohomology via the Borel&ndash;Weil&ndash;Bott theorem,
zero loci, Hodge numbers, Hochschild cohomology, exceptional collections, and more.

Here is the kind of thing it lets you write:

```julia
using PartialFlagVarieties

X = Gr(2, 5)             # the Grassmannian Gr(2,5)
dimension(X)             # 6
euler_characteristic(X)  # 10

T = tangent_bundle(X)    # its tangent bundle
cohomology(T)            # H⁰ = 24

L = line_bundle(X, 1)
Z = zero_locus(L)        # a hypersurface in Gr(2,5)
dimension(Z)             # 5
is_calabi_yau(Z)         # false
```

By default `cohomology` returns dimensions, so the tangent bundle line gives
$\mathrm{h}^0 = 24$. Pass `characters=true` and the Borel&ndash;Weil&ndash;Bott theorem
hands back the sheaf cohomology as an honest $G$-representation:

```julia
cohomology(T; characters=true)   # H⁰ = A4(1, 0, 0, 1)
```

that is $\mathrm{H}^0(\mathrm{Gr}(2,5), \mathrm{T}) = \mathfrak{sl}_5$, the adjoint
representation, of dimension $24 = \dim \mathrm{Aut}(\mathrm{Gr}(2,5))$.

## What "pre-release" means here

The version number is `0.1.0`, and the leading zero is the important part.
Under [semantic versioning](https://semver.org/) a `0.x.y` version signals that the public API
is not yet frozen: anything may still change before the first stable `1.0`.

So "pre-release" is not a warning that the code is broken.
The functionality listed below works, is covered by tests, and gives correct answers
on the examples we have tried.
What it means is that the *names and signatures* are not set in stone.
A function might get renamed, an argument order might change,
or a return type might be reworked before `1.0`.
If you pin `0.1` in your own `Project.toml`, an update will never silently break your code.

We are releasing it now, rather than waiting for a polished `1.0`,
because the package is already useful for real computations,
and because feedback on the API is far more valuable while it can still change cheaply.

## What is in v0.1.0

The core is in place:

- partial flag varieties $G/P$ for all simple types, with named constructors
  (`Gr`, `OGr`, `SGr`, `LGr`, `flag_variety`, `projective_space`, `quadric`,
  `cayley_plane`, `freudenthal_variety`, `adjoint_variety`, and more);
- equivariant vector bundles: tautological and spinor bundles, line bundles,
  tangent and cotangent bundles, exterior and symmetric powers, duals, twists, tensor products;
- sheaf cohomology via the Borel&ndash;Weil&ndash;Bott theorem, both dimension- and character-valued;
- zero loci of sections, with Koszul resolutions and Calabi&ndash;Yau detection;
- Hodge numbers, twisted Hodge numbers, and Hochschild cohomology;
- exceptional collections (Beilinson, Kapranov, Kapranov&ndash;Orlov).

The [documentation](https://homogeneous.tools/PartialFlagVarieties.jl) has the full list,
with worked examples for each.

We expect to tag a stable `v1.0` after the summer holidays,
and soon after that a `v1.1` (or even a `v2`) with some more features.

## Trying it out

Since this is a pre-release, the surest way to install it is directly from the repository:

```julia
using Pkg
Pkg.add(url="https://github.com/HomogeneousTools/PartialFlagVarieties.jl")
```

If you compute with homogeneous varieties and their vector bundles,
we would love to hear what works, what is missing, and what feels awkward.
Issues and suggestions on the
[repository](https://github.com/HomogeneousTools/PartialFlagVarieties.jl) are very welcome:
this is exactly the moment when they can shape the `1.0`.
