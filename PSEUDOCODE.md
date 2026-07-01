# VERSA — Compliance-Check Procedure (Pseudocode)

This is the reference procedure for VERSA compliance checking, reproduced from
**Algorithm 1** of the companion paper *VERSA: A Knowledge Graph-Centric
Framework for Regulatory Compliance Checking*.

No executable code is distributed in this repository. This pseudocode, together
with the paper (which gives the full definitions of the similarity functions and
equations referenced below), fully specifies the method.

## Inputs

- `C_int` — internal article set
- `C_ext` — external article set
- `G` — knowledge graph (structure, concept, obligation, and revision layers)
- `λ_c, λ_e` — concept / embedding weights for the hybrid retrieval score
- `k_struct, k_final` — structural-prefilter depth and final-candidate depth
- `Φ` — structured-output reasoner

## Output

- `V` — compliance report: a set of records `(c_i, c_j, v, ρ, τ, M⁺, M⁻)`

## Procedure

```text
Algorithm 1  VERSA Compliance Check

 1  V ← ∅                                              # empty compliance report
 2  C_ext_active ← { c ∈ C_ext : status(c) = active }  # exclude superseded versions

 3  for each article c ∈ C_int ∪ C_ext_active:
 4      pre-load concept set K(c), embedding e(c), and obligation set O(c) from G

 6  for each internal article c_i ∈ C_int:
 7      C_pre_i ← structural prefilter: the top-k_struct external articles
                  by section-level embedding similarity
 8      for each candidate c_j ∈ C_pre_i:
 9          S_concept(c_i, c_j) ← specificity-weighted Jaccard on K(c_i), K(c_j)   (Eq. 7)
10          S_emb(c_i, c_j)     ← cosine( e(c_i), e(c_j) )                          (Eq. 8)
11          R(c_i, c_j)         ← λ_c · S_concept(c_i, c_j) + λ_e · S_emb(c_i, c_j) (Eq. 6)
13      P_i ← top-k_final candidates in C_pre_i ranked by R(c_i, ·), descending

14      for each candidate external article c_j ∈ P_i:
15          payload ← texts of c_i, c_j and obligation sets O(c_i), O(c_j)
16          (v, ρ, τ, M⁺, M⁻) ← Φ(payload)   # verdict, reason code, rationale,
                                              # matched and missing obligations
17          append record (c_i, c_j, v, ρ, τ, M⁺, M⁻) to V

20  return V
```

## Notation

- `v` ∈ {`COMPLY`, `NON-COMPLY`} — binary compliance verdict
- `ρ` — controlled reason code drawn from a fixed vocabulary (see paper)
- `τ` — natural-language rationale
- `M⁺` / `M⁻` — matched / missing obligations
- `status(c)` — active vs. superseded article version (revision layer)

`S_concept` (Eq. 7), `S_emb` (Eq. 8), the hybrid retrieval score `R` (Eq. 6), and
the reason-code vocabulary are defined in the paper.
