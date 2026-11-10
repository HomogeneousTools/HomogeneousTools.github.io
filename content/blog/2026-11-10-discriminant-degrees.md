---
title: "Degrees of polar discriminants"
slug: "discriminant-degrees"
date: 2026-11-10
author: "Pieter Belmans"
---

Time for another installment of the arXiv series!
Today's preprint is
[Vladimiro Benedetti, Daniele Faenzi, Simone Marchesi: *Derivations tangent to polar discriminants*](https://arxiv.org/abs/2611.12413).
Among the examples in §6.4 are the projective duals of two Grassmannians,
a spinor variety, and a Lagrangian Grassmannian,
with degrees 120, 126, 240, and 72.
The authors recover these degrees using Saito matrices constructed from exceptional Lie algebra gradings.
Let us recover them geometrically using
[PartialFlagVarieties.jl](https://homogeneous.tools/PartialFlagVarieties.jl).

Let $X\subset\mathbb P^N$ be a smooth complex variety of dimension $n$,
whose projective dual $X^\vee$ is a hypersurface.
Write $Y$ for a general hyperplane section and $B$ for a general linear section of codimension two.
A Lefschetz pencil has base locus $B$ and smooth fibre $Y$,
and its singular fibres each have one ordinary double point.
Blowing up $B$ and comparing Euler characteristics gives the class formula,
discussed in [Tevelev's amazing book on projective duality](https://doi.org/10.1007/b138367)
(Example 7.5 in the [arXiv version](https://arxiv.org/abs/math/0112028)):

<div>
\begin{equation}
\deg(X^\vee)=(-1)^n\bigl(\chi(X)-2\chi(Y)+\chi(B)\bigr).
\end{equation}
</div>

Here $\chi$ is the **topological** Euler characteristic.
That is a computation the package already knows how to do:

```julia
using PartialFlagVarieties

function dual_degree(X)
    L = line_bundle(X, 1)
    Y = zero_locus(L)
    B = zero_locus(L + L)
    return (-1)^dimension(X) * (
        euler_characteristic(X) -
        2 * euler_characteristic(Y) +
        euler_characteristic(B)
    )
end

Xs = [Gr(3, 9), Gr(4, 8), OGr(8, 16), LGr(4, 8)]
@assert dual_degree.(Xs) == [120, 126, 240, 72]
```

The line bundle is the ample generator of the Picard group.
In particular, `OGr(8, 16)` chooses one component of the maximal orthogonal Grassmannian,
and we use its spinor embedding.
Here are the intermediate values:

| $X$ | $\chi(X)$ | $\chi(Y)$ | $\chi(B)$ | $\deg(X^\vee)$ |
|---|---:|---:|---:|---:|
| $\operatorname{Gr}(3,9)$ | 84 | 72 | 180 | 120 |
| $\operatorname{Gr}(4,8)$ | 70 | 56 | 168 | 126 |
| $\operatorname{OGr}^{+}(8,16)$ | 128 | 112 | 336 | 240 |
| $\operatorname{LGr}(4,8)$ | 16 | 8 | 72 | 72 |

All four degrees agree.
Good!

Underneath, the package computes Euler characteristics of exterior powers of the cotangent bundle,
using the conormal sequence, the Koszul resolution, and Borel–Weil–Bott.
Euler characteristics are additive,
so this works even when individual Hodge numbers remain undetermined.

This gives an independent check of the degrees.
This computation will be integrated into PartialFlagVarieties.jl soon
([issue #38](https://github.com/HomogeneousTools/PartialFlagVarieties.jl/issues/38)).
The feature will also compute the dual defect (the codimension of the dual minus one),
and work for smooth zero loci.

This little computation is by far the main point of the paper,
but it taught me that there's a nice feature to be added to the package,
which is an important reason for trying to apply the package to preprints.
