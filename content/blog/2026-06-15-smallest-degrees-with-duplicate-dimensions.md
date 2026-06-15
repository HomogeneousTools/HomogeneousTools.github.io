---
title: "Representations with the same degree: smallest collisions"
slug: "smallest-degrees-with-duplicate-dimensions"
date: 2026-06-15
author: "Pieter Belmans"
---
This is a follow-up to
[Representations with the same degree](/blog/2026/06/04/representations-with-the-same-degree/),
about [Frank Lübeck's preprint](https://arxiv.org/abs/2601.18786) on irreducible
representations of a simple algebraic group that share their dimension.
In the [corrections](/blog/2026/06/04/representations-with-the-same-degree/#corrections)
to that post I noted that the clean *construction* sequences (such as
[A000891](https://oeis.org/A000891) in type $\mathrm{A}_l$) are **not** the smallest
degrees at which two non-isomorphic, non-dual irreducibles coincide in dimension.
This post collects the genuine smallest degrees.

For each classical type, here is the smallest degree carrying two irreducible
representations of equal dimension that are *not* related by a diagram
automorphism, for the ranks $l$ shown. They are kept as plain text so they are easy
to copy into the OEIS:

```text
A, l = 2..25: 15, 20, 70, 105, 210, 1512, 4620, 4950, 8008, 924, 52052, 80080, 928200, 495040, 271320, 3060, 5155080, 1009470, 10296594, 14805560, 20366500, 2884200, 37001250, 48976200
B, l = 2..25: 35, 112, 2772, 23595, 715, 6636630, 23984688, 66927861, 9585192960, 669442048, 251608500, 93475514149875, 2583610029, 2184324842700, 1107568, 59454145018522800, 3359934925776, 3078010375997128, 1255300994827620, 334620762580894464675, 2664821443401, 23398175753138864128, 67778385492333035520, 37701404492850988890000
D, l = 4..25: 672, 210, 352, 24024, 1067040, 2494206, 5457408, 26334, 460337701824, 14880153600, 13571131392000, 54627300, 234371284992, 30408171847680, 20123126100, 2327117992710356160, 547214951521155808800, 52569351652198318080, 34600877643532200000, 53524680, 49180016759537664000, 502382039575652375139000
```

These are wildly irregular — not monotone in $l$, and nothing like the smooth
construction sequences (in type $\mathrm{A}$ they coincide with A000891 only at
$l=3$). The minima keep dipping back down: $\mathrm{A}_{11}$ falls to $924$ from the
coincidence $\dim\mathrm{V}(\omega_6)=\dim\mathrm{V}(\omega_1+2\omega_{11})$, and
$\mathrm{A}_{17}$ all the way to $3060$.

A witnessing pair for each entry (there can be more than two representations at the
minimal degree; two suffice), here for the first ranks $l\leq10$:

<div class="type-table">

| Dynkin type | first weight | second weight | common degree |
|---|---|---|---|
| $\mathrm{A}_{2}$ | $4\omega_{2}$ | $\omega_{1}+2\omega_{2}$ | 15 |
| $\mathrm{A}_{3}$ | $3\omega_{3}$ | $\omega_{2}+\omega_{3}$ | 20 |
| $\mathrm{A}_{4}$ | $4\omega_{4}$ | $\omega_{1}+2\omega_{4}$ | 70 |
| $\mathrm{A}_{5}$ | $2\omega_{4}$ | $\omega_{3}+\omega_{5}$ | 105 |
| $\mathrm{A}_{6}$ | $4\omega_{6}$ | $\omega_{4}+\omega_{6}$ | 210 |
| $\mathrm{A}_{7}$ | $\omega_{5}+2\omega_{7}$ | $\omega_{4}+\omega_{6}$ | 1512 |
| $\mathrm{A}_{8}$ | $\omega_{5}+2\omega_{8}$ | $2\omega_{2}+\omega_{8}$ | 4620 |
| $\mathrm{A}_{9}$ | $2\omega_{8}+\omega_{9}$ | $2\omega_{7}$ | 4950 |
| $\mathrm{A}_{10}$ | $6\omega_{10}$ | $\omega_{9}+3\omega_{10}$ | 8008 |
| $\mathrm{B}_{2}$ | $4\omega_{2}$ | $\omega_{1}+2\omega_{2}$ | 35 |
| $\mathrm{B}_{3}$ | $3\omega_{3}$ | $\omega_{2}+\omega_{3}$ | 112 |
| $\mathrm{B}_{4}$ | $4\omega_{4}$ | $\omega_{2}+2\omega_{4}$ | 2772 |
| $\mathrm{B}_{5}$ | $2\omega_{4}$ | $\omega_{3}+\omega_{4}$ | 23595 |
| $\mathrm{B}_{6}$ | $\omega_{4}$ | $\omega_{1}+\omega_{2}$ | 715 |
| $\mathrm{B}_{7}$ | $\omega_{6}+2\omega_{7}$ | $\omega_{5}+2\omega_{7}$ | 6636630 |
| $\mathrm{B}_{8}$ | $2\omega_{6}$ | $\omega_{5}+\omega_{6}$ | 23984688 |
| $\mathrm{B}_{9}$ | $2\omega_{2}+\omega_{5}$ | $8\omega_{1}+\omega_{2}$ | 66927861 |
| $\mathrm{B}_{10}$ | $\omega_{2}+3\omega_{10}$ | $\omega_{2}+\omega_{7}+\omega_{10}$ | 9585192960 |
| $\mathrm{D}_{4}$ | $5\omega_{4}$ | $\omega_{3}+3\omega_{4}$ | 672 |
| $\mathrm{D}_{5}$ | $\omega_{4}+\omega_{5}$ | $3\omega_{1}$ | 210 |
| $\mathrm{D}_{6}$ | $\omega_{1}+\omega_{6}$ | $3\omega_{1}$ | 352 |
| $\mathrm{D}_{7}$ | $\omega_{1}+\omega_{5}$ | $\omega_{1}+2\omega_{2}$ | 24024 |
| $\mathrm{D}_{8}$ | $2\omega_{2}+\omega_{3}$ | $4\omega_{1}+\omega_{3}$ | 1067040 |
| $\mathrm{D}_{9}$ | $2\omega_{1}+\omega_{6}$ | $3\omega_{1}+2\omega_{2}$ | 2494206 |
| $\mathrm{D}_{10}$ | $\omega_{5}+\omega_{10}$ | $2\omega_{2}+\omega_{10}$ | 5457408 |

</div>

The table and the sequences above come from a degree-bounded backtrack that
enumerates every dominant weight below a candidate degree, quotienting out the
diagram automorphisms — duality in type $\mathrm{A}$, the spinor-node swap in
$\mathrm{D}_l$ for $l\geq5$, and triality in $\mathrm{D}_4$:

```julia
# diagram automorphisms acting on coordinates
autos(::Type{TypeA{l}}) where l = [identity, reverse]
autos(::Type{TypeB{l}}) where l = [identity]
autos(::Type{TypeD{4}})         = [v -> v[[a,2,b,c]] for (a,b,c) in
    [(1,3,4),(1,4,3),(3,1,4),(3,4,1),(4,1,3),(4,3,1)]]   # triality
autos(::Type{TypeD{l}}) where l = [identity, v -> v[[1:l-2; l; l-1]]]

# bin all dominant weights of degree <= bound by degree (pruning: degree is
# increasing in each coordinate, so trailing zeros give a lower bound)
function weights(::Type{T}, bound) where T <: DynkinType
  l = rank(T); bins = Dict{BigInt,Vector{Vector{Int}}}(); v = zeros(Int, l)
  rec(i) = i > l ? push!(get!(() -> Vector{Int}[], bins, degree(T, v)), copy(v)) :
    (while degree(T, v) <= bound; rec(i + 1); v[i] += 1 end; v[i] = 0)
  rec(1); bins
end

# smallest degree shared by two weights in distinct diagram-automorphism orbits;
# the starting bound is only a guess — we keep growing it until a duplicate appears
function smallest_duplicate(::Type{T}) where T <: DynkinType
  G = autos(T); bound = big(2000)
  while true
    d = [k for (k, ws) in weights(T, bound)
           if length(unique(minimum(g(w) for g in G) for w in ws)) > 1]
    isempty(d) || return minimum(d)
    bound *= 4
  end
end

[smallest_duplicate(TypeA{l}) for l in 2:25]
[smallest_duplicate(TypeB{l}) for l in 2:25]
[smallest_duplicate(TypeD{l}) for l in 4:25]
```
