---
title: "Representations with the same degree"
slug: "representations-with-the-same-degree"
date: 2026-06-04
author: "Pieter Belmans"
toc: true
---
Time for the next installment of the arXiv series.
Today's preprint is from earlier this year, and it's
[Frank Lübeck: Representations with the same degree](https://arxiv.org/abs/2601.18786).
In fact, whilst preparing this blogpost, I discovered that
this question was also already addressed by Andy Huchala
in [his 2018 undergraduate thesis](https://ahuchala.com/files/undergrad/Lie_Algebra_Representation_Thesis.pdf).
In what follows, I will (mostly) refer to the arXiv preprint.

As before, we will use [Semisimple.jl](https://homogeneous.tools/Semisimple.jl/dev/)
to play with the constructions in the paper.

Added on June 15 2026: <ins>Frank Lübeck spotted two errors in the original post —
see the [Corrections](#corrections) at the end.</ins>

## Infinitely many pairs

**Theorem**
_Let $G$ be a connected reductive simply-connected algebraic group over $\mathbb{C}$ of rank $\geq2$.
Then $G$ has infinitely many pairs of irreducible rational representations $\rho_1,\rho_2$
that are not related by an automorphism of $G$
but for which $\dim\rho_1=\dim\rho_2$._

The rank-$1$ assumption is necessary:
in type $\mathrm{A}_1$ the irreducible representations are determined by their dimension.
Beyond that, Lübeck establishes the theorem by exhibiting one explicit same-degree pair in every simple type,
and combining this with the following observation:
the Weyl dimension formula immediately gives
$$\dim\mathrm{V}(k(\lambda+\rho)-\rho)=\dim\mathrm{V}(\lambda)\cdot k^N$$
for any $k\in\mathbb{Z}_{>0}$,
where $N$ is the number of positive roots.
So one same-degree pair $(\lambda,\mu)$ produces infinitely many at degrees $d, d\cdot 2^N, d\cdot 3^N,\ldots$

This is a **perfect** paper to play around with in Semisimple.jl,
because the only ingredient we really need is `degree`.

## Proposition 2: an explicit pair in each exceptional type, and in A₂, B₂

Lübeck's Proposition 2 collects the following same-degree pairs,
found by brute force over the dominant weights with small coordinates:
```julia
using Semisimple

# (type, λ, μ, common degree)
explicit = [
  (TypeA{2}, [1, 2],                   [0, 4],                       15),
  (TypeB{2}, [1, 2],                   [0, 4],                       35),
  (TypeG2,   [3, 0],                   [0, 2],                       77),
  (TypeF4,   [1, 0, 0, 1],             [2, 0, 0, 0],                 1053),
  (TypeE{6}, [2, 0, 0, 0, 0, 0],       [0, 0, 1, 0, 0, 0],           351),
  (TypeE{7}, [0, 0, 0, 1, 1, 0, 0],    [0, 0, 0, 0, 0, 2, 3],        1903725824),
  (TypeE{8}, [1, 0, 1, 0, 0, 0, 0, 0], [1, 0, 0, 0, 0, 0, 1, 1],     8634368000),
]

for (DT, λ, μ, d) in explicit
  @assert degree(DT, λ) == degree(DT, μ) == d
end
```
Note that in type $\mathrm{E}_6$ the non-trivial diagram automorphism
gives a _second_ same-degree pair at $d=351$,
namely $\mathrm{V}(2\omega_6)$ and $\mathrm{V}(\omega_5)$;
the other six pairs have no such automorphic explanation.

One can reproduce this brute-force search using Semisimple.jl:
<a name="smallest_pair"></a>
```julia
# diagram automorphism on coordinates; trivial except for A_2 and E_6.
outer(::Type{TypeA{2}}, v) = reverse(v)
outer(::Type{TypeE{6}}, v) = (v[6], v[2], v[5], v[4], v[3], v[1])
outer(::Type{<:DynkinType}, v) = v

# smallest dimension realised by two dominant weights of DT
# in different outer-automorphism orbits, searching coordinates in 0:N.
function smallest_pair(::Type{DT}; N=4) where {DT<:DynkinType}
  l = rank(DT)
  bins = Dict{BigInt,Vector{NTuple{l,Int}}}()
  for v in Iterators.product(ntuple(_ -> 0:N, l)...)
    push!(get!(() -> NTuple{l,Int}[], bins, degree(DT, collect(v))), v)
  end
  pairs = [(d, ws) for (d, ws) in bins
                   if length(unique(min(v, outer(DT, v)) for v in ws)) >= 2]
  return argmin(first, pairs)
end

# the cases considered in Proposition 2
for DT in (TypeA{2}, TypeB{2}, TypeG2, TypeF4, TypeE{6}, TypeE{7}, TypeE{8})
  d, ws = smallest_pair(DT)
  println(rpad(string(DT), 12), "  d = ", lpad(string(d), 12), "    ", join(ws, "  "))
end
```

Added on June 15 2026: <ins>as written, [`smallest_pair`](#smallest_pair) does
**not** actually prove that the pair it returns is the smallest one — see
[Corrections](#corrections) below.</ins>

## Theorem 3: infinite families in the classical types

For the classical types Lübeck exhibits closed-form families.
In types $\mathrm{A}_l,\mathrm{B}_l,\mathrm{D}_l$ the pair is always of the shape
$\mathrm{V}\bigl((c-1)\omega_2\bigr)$ vs.&nbsp;$\mathrm{V}\bigl(\omega_1+(c-2)\omega_2\bigr)$,
where $c$ depends on the type;
verifying them up to any reasonable rank is straightforward.
For $\mathrm{A}_l$ it would be as in Theorem&nbsp;3(a):
```julia
for l in 2:15
  λ = zeros(Int, l); λ[2] = l - 1
  μ = zeros(Int, l); μ[1] = 1; μ[2] = l - 2
  expected =
    BigInt(2l - 1) *
    prod(BigInt(k)^2 for k in (l + 1):(2l - 2); init=BigInt(1)) ÷
    factorial(BigInt(l - 1))^2
  @assert degree(TypeA{l}, λ) == degree(TypeA{l}, μ) == expected
end
```
The same pattern works for $\mathrm{B}_l$ and $\mathrm{D}_l$ with the closed forms
in Theorem&nbsp;3(b) and 3(c) of the paper.


Type $\mathrm{C}_l$ takes an unexpectedly arithmetic turn:
Lübeck shows that, again for $\lambda=a\omega_1+b\omega_2$ and $\mu=(a-2)\omega_1+(b+1)\omega_2$,
the equality $\dim\mathrm{V}(\lambda)=\dim\mathrm{V}(\mu)$ holds
if and only if the generalised Pell equation
$$c^2-(4l-5)\,a^2=(2l-3)^2$$
admits a solution with $a\geq 3$,
in which case $b=\tfrac12(c+1-a-2l)$.
Because $4l-5\equiv 3\pmod 4$ is never a perfect square,
this Pell equation has infinitely many solutions for every $l\geq 3$,
and the construction has the curious feature that the smallest $(a,b)$ one obtains
can be _enormous_:
for $l=159$ the corresponding common degree has 15728 decimal digits.
Computing that degree directly in Semisimple.jl just works,
since `degree` returns `BigInt`.

## Some comments

* The seven Proposition 2 pairs, together with the three Theorem 3 families,
  are part of the Semisimple.jl test suite,
  giving a slightly unusual but pretty cool set of unit tests!
* Huchala claims in his Theorem 7 that there is an upper bound on how many irreducible representations of a given dimension can exist in type $\mathrm{B}_2$ and $\mathrm{G}_2$. For $\mathrm{A}_2$, a fun argument using the rank of an elliptic curve (explicit in Huchala's thesis, whilst attributed to Deligne but not given in Lübeck's preprint) shows that for any positive integer $m$, you can find at least $m$ non-isomorphic irreducible representations of the same degree.

  However, the proof of the upper bound in types B and G is not correct: there is a plane curve _for every given degree_, on which Faltings' theorem gives a finite number of points.
  But to prove the claim, one would need that there is a uniform bound on the number of points all those plane curves, simultaneously.
  So there seems to be a fun open number-theoretical question here,
  unless I'm missing something?
* There are fun OEIS sequences to add here:
  the degrees of Lübeck's explicit family in type $\mathrm{A}_l$ are exactly
  [A000891](https://oeis.org/A000891), and the analogous family degrees in
  types B and D are missing from the OEIS (in type C the chaotic behavior makes
  it hard to produce any clean sequence).
  The entries for these family sequences are computed both by Lübeck and Huchala.
  Added on June 15 2026: <ins>beware that these are the degrees of Lübeck's
  *construction*, not the smallest degrees at which a duplicate occurs — again
  see [Corrections](#corrections).</ins>

  If anyone is interested in adding these, here is some Julia code to determine them:
  ```julia
  function dim_B(l::Integer)
    @assert l >= 2
    l == 2 && return 35
    l = BigInt(l)
    num = 3 * (4l - 5) * (6l - 5) * (6l - 7) *
          prod(k^2 for k in (2l):(4l - 6); init=BigInt(1))
    den = factorial(2l - 3)^2
    q, r = divrem(num, den);  @assert iszero(r)
    return q
  end

  function dim_D(l::Integer)
    @assert l >= 4
    l = BigInt(l)
    num = 3 * (3l - 4) * (3l - 5) * (4l - 7) *
          prod(k^2 for k in (2l - 1):(4l - 8); init=BigInt(1))
    den = (l - 2)^2 * factorial(2l - 5)^2
    q, r = divrem(num, den);  @assert iszero(r)
    return q
  end
    ```
    which gives
    * `35, 3003, 383724, 58790875, 10011037452, 1827174287820, 350280152218800, 69656361253789275, 14250522671900707500, 2982164406170216424300, 635707916954453388942000, 137613009450274251664451148`, resp.
    * `32928, 4671810, 759230472, 134282273216, 25166658696000, 4919891369426550, 993186502108515000, 205625998084534750800, 43449470935521085094400, 9336731949069856461585000, 2034842637042393404135380128, 448826044126481544919237242240`.

## Corrections

After this post first appeared, Frank Lübeck kindly pointed out two mistakes.
Both are corrected above; this section spells out what went wrong and how to fix
it.

### `smallest_pair` does not certify minimality

[`smallest_pair`](#smallest_pair) only loops over the box `0:N` (with `N=4`),
and nothing about it rules out that some weight with a coordinate larger than `N`
has a smaller degree and forms a smaller pair. There is a cheap certificate, though. The Weyl dimension
formula is strictly increasing in each coordinate, so every weight with some
coordinate `> N` has degree at least $\min_i\deg\bigl((N+1)\omega_i\bigr)$; if that
minimum already exceeds the degree `d` of the pair we found, then no weight outside
the box can match or beat it, and the box was provably large enough:

```julia
function certified(::Type{DT}; N=4) where {DT<:DynkinType}
  d, ws = smallest_pair(DT; N)
  l = rank(DT)
  @assert minimum(degree(DT, [i == j ? N+1 : 0 for j in 1:l]) for i in 1:l) > d
  return d, ws
end
```

This check passes for $\mathrm{A}_2,\mathrm{B}_2,\mathrm{G}_2,\mathrm{F}_4,\mathrm{E}_6$,
but it *fails* for $\mathrm{E}_7$ and $\mathrm{E}_8$ at `N=4`. The cleanest example
is in $\mathrm{E}_8$: already $\deg(5\omega_8)=2\,642\,777\,280$ is smaller
than the pair degree $8\,634\,368\,000$ from Proposition&nbsp;2, so the box `0:4`
is not certified — the search simply hasn't looked at enough weights to know.
A degree-bounded backtrack that enumerates *every* dominant weight whose degree is
below the candidate settles it: doing so confirms that the tabulated $\mathrm{E}_7$
and $\mathrm{E}_8$ values really are the smallest, but that is a fact the box search
alone never establishes.

### A000891 is not the sequence of smallest degrees

I described [A000891](https://oeis.org/A000891) as the smallest degrees in type
$\mathrm{A}_l$ at which a duplicate occurs. That is wrong: A000891 is exactly the
sequence of degrees of Lübeck's *explicit construction*, and those are in general
much larger than the genuine smallest — for instance the construction gives $175$
in $\mathrm{A}_4$, whereas a same-degree pair already occurs at $70$. The type B
and D family sequences listed earlier are construction degrees in the same way,
not the smallest.

Added on June 15 2026: <ins>the genuine smallest-degree sequences for types
$\mathrm{A}$, $\mathrm{B}$ and $\mathrm{D}$ — with witnessing weights and the code
that finds them — are tabulated in a [follow-up post](/blog/2026/06/15/smallest-degrees-with-duplicate-dimensions/).</ins>

*Thanks to Frank Lübeck for both corrections!*
