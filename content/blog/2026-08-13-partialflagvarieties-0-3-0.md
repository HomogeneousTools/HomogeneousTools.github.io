---
title: "PartialFlagVarieties.jl 0.2.0 and then 0.3.0"
slug: "partialflagvarieties-0-3-0"
date: 2026-08-13
author: "Pieter Belmans"
---

Two releases since the [pre-release](/blog/2026/07/17/partialflagvarieties-pre-release/)
of [PartialFlagVarieties.jl](https://github.com/HomogeneousTools/PartialFlagVarieties.jl)
at `v0.1.0`. `0.2.0` and `0.2.1` were housekeeping; `0.3.0` adds maps between flag
varieties, and renames a few things while the leading zero still allows it.

## Maps between flag varieties

For $I \subseteq J$ we have $\mathrm{P}_J \subseteq \mathrm{P}_I$, hence a projection
$q \colon \mathrm{G}/\mathrm{P}_J \to \mathrm{G}/\mathrm{P}_I$. You can now pull
equivariant bundles back along $q$, and push them forward:

```julia
X, D = Gr(3, 6), flag_variety(6, [1, 3])

F = pullback(D, universal_subbundle(X))
rank.(graded_pieces(F))                       # [1, 2]
graded_pieces(F) == tautological_bundles(D)   # true

pushforward(X, structure_sheaf(D))            # R⁰ = E(0)
```

The pullback is only *filtered*: the unipotent radical of $\mathrm{P}_J$ acts on the
fibre, so $q^*\mathcal{E}$ does not split. Its associated graded is the branching of the
fibre from $\mathrm{L}_I$ to $\mathrm{L}_J$, and above it recovers the tautological
filtration $0 \to \mathcal{U}_1 \to \mathcal{U}_3 \to \mathcal{U}_3/\mathcal{U}_1 \to 0$
on $\mathrm{Fl}(1,3;6)$, subbundle first.

The pushforward is the Borel&ndash;Weil&ndash;Bott theorem for $\mathrm{L}_I$ instead of
for $\mathrm{G}$: the same $\rho$-shifted fold into a dominant chamber, reflecting only
in the nodes unmarked in $I$. Taking $I = \emptyset$, so that the target is a point,
gives back `cohomology`. Since $\mathbf{R}q_*$ of an irreducible sits in a single degree,
the Leray spectral sequence degenerates, and that identity is what the tests check across
every type.

## What moved

`0.x` means the names are not frozen, and several changed in `0.3.0`:

- `rank_bundle` is now `rank`, and `fiber_dimension` is now `degree`;
- `rank` of a variety is gone, since a rank belongs to the group: write
  `rank(dynkin_type(X))`;
- `Cohomology` stores its top degree as `max_degree` rather than `dim_variety`, since for
  higher direct images it is the relative dimension.

Two behaviours changed as well. Bundles now compare as unordered direct sums, a direct
sum having no order. And a weight that is not dominant for the Levi is rejected when a
bundle is constructed: it is the highest weight of nothing, and it used to be tolerated,
then read as zero by some functions and pushed through Borel&ndash;Weil&ndash;Bott by
others.

## Trying it out

The package is not in the General registry, because two of its dependencies are not
either, so those come first, and `rev` pins the release rather than tracking `main`:

```julia
using Pkg
Pkg.add(url="https://github.com/HomogeneousTools/Base62.jl")
Pkg.add(url="https://github.com/HomogeneousTools/ZeroLocus62", subdir="julia")
Pkg.add(url="https://github.com/HomogeneousTools/PartialFlagVarieties.jl",
        rev="5b162d4")   # v0.3.0
```

The [documentation](https://homogeneous.tools/PartialFlagVarieties.jl) has the full API.
Issues and suggestions on the
[repository](https://github.com/HomogeneousTools/PartialFlagVarieties.jl) remain very
welcome, and still cheap to act on.
