---
title: "Frassineti's Küchle-like Fano fourfolds in the key of B and D"
slug: "three-new-fano-fourfolds"
date: 2026-11-13
author: "Pieter Belmans"
draft: false
---

Time for another installment of the arXiv series!
Küchle's Fano fourfolds are in the key of $\mathrm{A}$: their ambient spaces are ordinary Grassmannians.
In [*Prime Fano fourfolds in classical and generalized Grassmannians*](https://arxiv.org/abs/2609.09310),
amongst other things, Frassineti finds three new families of Fano fourfolds of Picard rank 1 and index 1,
changing key to $\mathrm{B}$ and $\mathrm{D}$.
[PartialFlagVarieties.jl](https://homogeneous.tools/PartialFlagVarieties.jl) can compute all the kinds of invariants studied in the paper,
retaining symbolic parameters when a cohomology dimension remains undetermined.
Here we recover the invariants and local-completeness results in Propositions 5.1–5.3 automatically.
The defining bundles are all the input we need:

```julia
using PartialFlagVarieties

G1 = OGr(3, 9)
X1 = zero_locus(4*spinor_bundle(G1))

G2 = OGr(5, 10)
X2 = zero_locus(line_bundle(G2, 2) + 5*line_bundle(G2, 1))

G3 = OGr(2, 7)
X3 = zero_locus(symmetric_power(spinor_bundle(G3), 2))

Xs = [X1, X2, X3]
dimension.(Xs)   # [4, 4, 4]
fano_index.(Xs)  # [1, 1, 1]
```

The ambient Dynkin types are $\mathrm{B}_4$, $\mathrm{D}_5$, and $\mathrm{B}_3$, respectively.
For `OGr(5, 10)`, PFV chooses one component and uses the spinor embedding.
In proposition order, their ZL62 labels are [`I3.3333`](https://zl62.homogeneous.tools/decode/I3.3333), [`iF.444449`](https://zl62.homogeneous.tools/decode/iF.444449), and [`H1.5`](https://zl62.homogeneous.tools/decode/H1.5).

## The invariants

PFV handles the cohomology computations automatically:

```julia
hodge_numbers.(Xs)
cohomology.(anticanonical_bundle.(Xs))
euler_characteristic.(tangent_bundle.(Xs))
hilbert_polynomial.(Xs)
```

These recover all the numerical invariants listed in the propositions.
The Hilbert polynomials also give the volumes, since the polarization here is $-\mathrm{K}_X$:

| Proposition | $(-\mathrm{K}_X)^4$ | $\mathrm{h}^0(X,\omega_X^\vee)$ | $\chi(\mathrm{T}_X)$ | $\mathrm{h}^{1,2}$ | $\mathrm{h}^{1,3}$ | $\mathrm{h}^{2,2}$ |
|---|---:|---:|---:|---:|---:|---:|
| 5.1 | 116 | 30 | −12 | 0 | 0 | 8 |
| 5.2 | 24 | 11 | −65 | 0 | 16 | 112 |
| 5.3 | 72 | 21 | −14 | 3 | 0 | 8 |

In each case $\mathrm{h}^{1,1}=1$; the usual symmetries and vanishings specify the remaining entries.
PFV fully determines all three Hodge diamonds, and every value agrees with the paper!

The package takes care of the Koszul resolutions, conormal sequences, and Borel–Weil–Bott computations,
including the nonsplit tangent filtrations of the ambient Grassmannians.

For Chern numbers, [IntersectionTheory in OSCAR](https://docs.oscar-system.org/stable/Experimental/IntersectionTheory/intro/)
provides an independent route through intersection theory.
They can also be recovered from PFV's Euler characteristics using Hirzebruch–Riemann–Roch,
so we have two approaches available for checking these numbers.

## Local completeness

A model is *locally complete* if every small deformation of its fourfold can be obtained by varying the defining section.
PFV checks this too:

```julia
is_locally_complete.(Xs)
# [true, true, false]
```

The first two families are locally complete; the third is not.
Again, this agrees with Propositions 5.1–5.3.
The function returns `nothing` when the cohomology is inconclusive, but it certifies all three cases here.

These models will also make their way into Fanobase.
For the time being, [type-a.fanobase.net](https://type-a.fanobase.net/) covers only Dynkin type $\mathrm{A}$.

The [complete runnable script](https://gist.github.com/pbelmans/724d225841660c9e54c743fe15972338) checks all these results.
