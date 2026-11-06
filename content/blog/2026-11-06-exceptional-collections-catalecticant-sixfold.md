---
title: "Exceptional collections on the catalecticant sixfold"
slug: "exceptional-collections-catalecticant-sixfold"
date: 2026-11-06
author: "Pieter Belmans"
draft: true
---

Back in May, I [wrote about verifying Kostant’s conjecture using Semisimple.jl](https://homogeneous.tools/blog/2026/05/29/verifying-kostants-conjecture/), and promised to make this into a series.
Here is another installment in that series!

In his new preprint [*The odd biisotropic symplectic Grassmannian $\operatorname{S}_2\operatorname{Gr}(3,7)$*](https://arxiv.org/abs/2611.07079),
Sasha Kuznetsov constructs a full strong exceptional collection on a certain Fano sixfold.
It is no secret that I am a big fan of Fano varieties.
And the cohomology computations appearing in his Section 4
are a good excuse to try out [PartialFlagVarieties.jl](https://homogeneous.tools/PartialFlagVarieties.jl) some more.
So let us combine two of my loves in this post!

## The catalecticant sixfold

Let us first construct the variety.
Take a general pencil of skew-symmetric forms on a 7-dimensional vector space.
We are interested in the 3-dimensional subspaces which are isotropic for both forms.
Each form gives a section of $\bigwedge^2\mathcal U^\vee$ on $\operatorname{Gr}(3,7)$, so we can write:

```julia
using PartialFlagVarieties

G = Gr(3, 7)
U = universal_subbundle(G)
Ud = dual(U)
A = exterior_power(Ud, 2)

X = zero_locus(A ⊕ A)
```

That is all the input we need.
The package records the defining bundle; the choice of general section is implicit.
Here the zero locus is smooth, and the two copies of a rank-3 bundle cut the 12-dimensional Grassmannian down to a sixfold.

```julia
dimension(X)   # 6
fano_index(X)  # 3
```

Kuznetsov calls this the *catalecticant sixfold*.
The name comes from an open subset isomorphic to the space of catalecticant matrices, whose entries are constant along the anti-diagonals.
There is quite a bit of interesting geometry attached to this description,
but <del>we are not interested in any of that</del>
<ins>we are here to do some sheaf cohomology computations</ins>.

## Some sheaf cohomology computations

We can start with the Hodge numbers:

```julia
H = hodge_numbers(X)
[H[p + 1, p + 1] for p in 0:6]
# [1, 1, 2, 2, 2, 1, 1]

all(H[p, q] == 0 for p in 1:7, q in 1:7 if p != q)  # true
```

All the off-diagonal entries vanish.
Thus the Hodge numbers agree with those of $\operatorname{Gr}(2,5)$, even though this variety looks rather different
(although so far the only reason for it being different is the index being 3, instead of 5).
In particular:

```julia
euler_characteristic(X)  # 10
```

We can also recover the degree and the dimension of the space of Plücker sections.
The Hilbert polynomial is returned as its coefficients in increasing order, so its leading coefficient multiplied by the factorial of the dimension gives the degree:

```julia
P = hilbert_polynomial(X)
Int(factorial(dimension(X)) * last(P))      # 61
cohomology(restrict(X, line_bundle(G, 1)))  # H⁰ = 21
```

Writing this post made me realise that the package should provide `degree(X)` directly.
This is now implemented in [PR #36](https://github.com/HomogeneousTools/PartialFlagVarieties.jl/pull/36), which should be merged soon.
The default polarization here is the Plücker line bundle.

These recover the numbers in Remark 4.1.
So far, so good.
But what we really want is to construct an exceptional collection.

## The exceptional collection

The package can check exceptionality directly, including after restricting a bundle to a zero locus:

```julia
O_G = structure_sheaf(G)

is_exceptional(Ud, X)               # true
is_exceptional_pair(O_G, Ud, X)     # true
```

The first computation says that $\mathcal U_X^\vee$ is exceptional.
The second checks that all Ext groups from $\mathcal U_X^\vee$ to $\mathcal O_X$ vanish, as required for the ordered pair $\langle\mathcal O_X,\mathcal U_X^\vee\rangle$.

The collection in Proposition 4.2 starts with $\bigwedge^2\mathcal U_X^\perp$, followed by three copies of the block $\langle\mathcal U_X,\mathcal O_X,\mathcal U_X^\vee\rangle$, twisted by $0$, $1$, and $2$.
Here $\mathcal U^\perp=Q^\vee$, where $Q$ is the universal quotient bundle.
We can enter the whole collection as follows:

```julia
Q = universal_quotient_bundle(G)
B = [U, O_G, Ud]

C = vcat(
    [exterior_power(dual(Q), 2)],
    [twist(F, 1, t) for t in 0:2 for F in B]
)
```

The `1` in `twist(F, 1, t)` refers to the unique marked node of the Grassmannian; `t` is the amount of twisting.
Now comes the useful part:

```julia
is_strong_exceptional_sequence(C, X)  # true
```

Cool!
This checks that each bundle is exceptional, that all Ext groups from right to left vanish, and that the forward Ext groups are concentrated in degree zero.

Underneath, this uses the same ingredients as the paper.
The Koszul resolution expresses restriction cohomology in terms of homogeneous bundles on the Grassmannian, where Borel–Weil–Bott applies.
The resulting spectral sequences can sometimes leave dimensions undetermined.
For this collection, every Ext dimension is determined.

Writing this post also prompted a small improvement to the exceptional sequence code.
In a future version, this will become even more transparent: we can work directly with bundles on the zero locus.
This is implemented in [PR #37](https://github.com/HomogeneousTools/PartialFlagVarieties.jl/pull/37), which should be merged soon:

```julia
CX = [restrict(X, F) for F in C]
is_strong_exceptional_sequence(CX)  # true
```

The bundles already record that they live on $X$, so we only have to supply the collection.

There is also a nice detail in Corollary 4.4 that we can see directly.
Compare the morphisms from $\mathcal U^\vee$ to $\bigwedge^2\mathcal U^\vee$ on the Grassmannian and on our sixfold:

```julia
F = dual(Ud) ⊗ A
cohomology(F)               # H⁰ = 7
cohomology(restrict(X, F))  # H⁰ = 8
```

There is an extra morphism after restriction!
The paper identifies this additional one-dimensional contribution and constructs the corresponding morphism.
Our computation detects the dimension jump, which is already a pleasant thing to see in the output.

Finally, we can also check the collection in Theorem 1.1.
It places $\bigwedge^2Q_X$ after the first block:

```julia
D = vcat(
    B,
    [exterior_power(Q, 2)],
    [twist(F, 1, t) for t in 1:2 for F in B]
)

is_strong_exceptional_sequence(D, X)  # true
```

What remains is fullness.
We have ten exceptional objects, which agrees with the Euler characteristic computed above.
That is a useful numerical check, but it does not prove that these objects generate the derived category.
For that we need the geometry and exact sequences constructed in §5 of the paper.

So there is still plenty of mathematics to do.
But the cohomological part of the example fits into a handful of short Julia snippets,
and it is easy to start experimenting: change a twist, replace a bundle, or try a different zero locus.

All the code in a single code snippet is provided in [this gist](https://gist.github.com/pbelmans/2c3b573b1d01130eb1a3e5ec031d8e56).
