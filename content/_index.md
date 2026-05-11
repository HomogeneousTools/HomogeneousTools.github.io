---
title: HomogeneousTools
---

> HomogeneousTools is a toolset to work with homogeneous varieties and homogeneous vector bundles on them, mostly with a view towards sheaf cohomology.

* [Lie.jl](https://homogeneous.tools/Lie.jl) ([GitHub](https://github.com/HomogeneousTools/Lie.jl))
* PartialFlagVarieties.jl (in progress)
* [ZeroLocus62](https://zl62.homogeneous.tools) ([GitHub](https://github.com/HomogeneousTools/ZeroLocus62))

### Recent posts

{{< blog-list >}}

## [Lie.jl](https://github.com/HomogeneousTools/Lie.jl)

[![tests](https://github.com/HomogeneousTools/Lie.jl/actions/workflows/tests.yml/badge.svg)](https://github.com/HomogeneousTools/Lie.jl/actions/workflows/tests.yml)
[![Docs](https://img.shields.io/badge/docs-homogeneous.tools/Lie.jl-blue)](https://homogeneous.tools/Lie.jl/)
[![Release](https://img.shields.io/github/v/release/HomogeneousTools/Lie.jl?color=green)](https://github.com/HomogeneousTools/Lie.jl/releases)

> A Julia package for computations with semisimple Lie algebras: root systems, Weyl groups, weight lattices, and representation-theoretic operations.

It is similar to [LiE](http://www-math.univ-poitiers.fr/~maavl/LiE/) and [LieART](https://lieart.hepforge.org/),
with a heavy focus on speed.
It does not yet match the features of these packages, but the basics are there.

## PartialFlagVarieties.jl

> A Julia package for computing with partial flag varieties $G/P$: equivariant vector bundles, sheaf cohomology via the Borel–Weil–Bott theorem, zero loci, Hodge numbers, Hochschild cohomology, exceptional collections, and more.

This is still a work-in-progress, and will be public soon.

## [ZeroLocus62](https://github.com/HomogeneousTools/ZeroLocus62)

[![Tests](https://github.com/HomogeneousTools/ZeroLocus62/actions/workflows/CI.yml/badge.svg)](https://github.com/HomogeneousTools/ZeroLocus62/actions/workflows/CI.yml)
[![Docs](https://img.shields.io/badge/docs-zl62.homogeneous.tools-blue)](https://zl62.homogeneous.tools)
[![Release](https://img.shields.io/github/v/release/HomogeneousTools/ZeroLocus62?color=green)](https://github.com/HomogeneousTools/ZeroLocus62/releases)

> ZeroLocus62 is a compact, canonical encoding for bundles, zero loci, and degeneracy loci of completely reducible vector bundles on partial flag varieties.

It allows one to say [`40.G`](https://zl62.homogeneous.tools/decode/40.G)
and this encodes a quintic threefold in a succinct way.
A more complicated example would be [`603.111`](https://zl62.homogeneous.tools/decode/603.111),
the [Fano 3-fold 1-10](https://www.fanography.info/1-10):
the zero locus of $(\bigwedge^2 \mathcal{U}^\vee)^{\oplus 3}$ on $\mathrm{Gr}(3,7)$.

Such a data format is very useful for efficiently encoding zero loci and communicating about them across systems.
