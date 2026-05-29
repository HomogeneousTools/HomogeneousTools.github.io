---
title: "Verifying Kostant's conjecture using Semisimple.jl"
slug: "verifying-kostants-conjecture"
date: 2026-05-29
author: "Pieter Belmans"
---
Here is a blogpost that is the first installment of something I would like to try.

Looking at today's arXiv feed,
I noticed a new and interesting preprint:
[Rekha Biswal, Sam Jeralds: Components of $\mathrm{V}(m\rho)\otimes\mathrm{V}(n\rho)$)](https://arxiv.org/abs/2605.29802v1),
for which [Semisimple.jl](https://homogeneous.tools/Semisimple.jl/dev/)
can be used to experiment and verify the conjectures in some explicit cases.
Because I think it is useful to see _how_ to do such experiments,
let us quickly discuss how we can verify Kostant's conjecture using Semisimple.jl.

I plan this to be a series!
I can even take requests.

## Kostant's conjecture

**Conjecture** (Kostant)
_Let $\mathfrak{g}$ be a complex semisimple Lie algebra.
Let $\lambda\in\mathrm{P}^+$ be a dominant integral weight
such that $\lambda\leq 2\rho$ in the dominance order.
Then $\mathrm{V}(\lambda)\subset\mathrm{V}(\rho)\otimes\mathrm{V}(\rho)$._

This conjecture can be read as saying that
every irreducible representation that _could_
appear in this specific tensor product,
actually _does_ appear.
And it is a **perfect** conjecture to play around with in Semisimple.jl,
one simply writes:
```julia
using Semisimple

function kostant_conjecture_holds(::Type{DT}) where {DT<:DynkinType}
  ρ = weyl_vector(DT)
  T = tensor_product(ρ, ρ)
  return all(λ -> get(T.terms, λ, 0) > 0, dominant_weights(DT, 2 * ρ))
end
```
Instead of `tensor_product(ρ, ρ)`
in type A,
it would be much faster to use `Semisimple._brauer_klimyk_dominant`:
usually the Littlewood&ndash;Richardson approach in type A is much faster,
but this is a case where the generic Brauer&ndash;Klimyk is actually better!
For more on this, see [below](#semisimple).

We can then check it for all (simple) Dynkin types up to rank 7:
```julia
# simple Dynkin types of rank ≤ 7, sorted by rank then by name.
simple_types = sort!(
  SimpleDynkinType[
    (TypeA{r}() for r in 1:7)...,
    (TypeB{r}() for r in 2:7)...,
    (TypeC{r}() for r in 3:7)...,
    (TypeD{r}() for r in 4:7)...,
    (TypeE{r}() for r in 6:7)...,
    TypeF4(),
    TypeG2(),
  ];
  by=dt -> (rank(dt), string(dt)),
)

for dt in simple_types
  t = @elapsed @assert kostant_conjecture_holds(typeof(dt)) "Conjecture 1 fails for $dt"
  println(rpad(string(dt), 24), "  ", round(t; digits=2), "s")
end
```
This verifies Kostant's conjecture for all simple Dynkin types up to rank 7,
in a bit more than a minute on my laptop.
Cool!

I stopped before rank 8 because it'll take significant time for type $\mathrm{A}_8$
(because the default tensor product algorithm is bad in this case)
and $\mathrm{E}_8$
(because this is simply such a vast case to consider.

It is now easy to also experimentally verify the generalization that Biswal&ndash;Jeralds propose,
but this would make the blogpost longer, and let's try to keep things short.

## What this means for Semisimple.jl {#semisimple}

As an added bonus of doing this,
I discovered two interesting things:

1. In type A, Semisimple.jl provides 2 algorithms to compute tensor products;
one is the Brauer&ndash;Klimyk algorithm,
the other uses Littlewood&ndash;Richardson coefficients.
In small cases, the Littlewood&ndash;Richardson approach is vastly superior,
which is why it is the default.
However, starting from $\mathrm{A}_8$, I noticed that
the computation got very slow, and switching to Brauer&ndash;Klimyk was actually a good thing!
I will investigate this further,
maybe there is an improvement to be made in the Littlewood&ndash;Richardson approach,
maybe we should add the option to choose your algorithm in type A.
2. The computation for $\mathrm{E}_8$ used integers that got so big
they overflowed `Int64`:
I never expected Freudenthal's formula to compute multiplicities that got so big,
but it turns out, it does in some interesting cases!
This is now fixed by [`dcd0a7d`](https://github.com/HomogeneousTools/Semisimple.jl/commit/dcd0a7d155ec0ccd5abd68c341815bec768d1d41).
