# VERSA compliance-check procedure

This is Algorithm 1 of the paper *A Revision-Aware Knowledge Graph Framework for Regulatory
Compliance Checking*, with the equations it uses. Equation numbers follow the paper. No
executable code is distributed in this repository.

## Algorithm 1: VERSA compliance check

```text
Require: internal article set C^int, external article set C^ext, knowledge graph G,
         weights λc and λe, retrieval depths k_struct and k_final, reasoner ρ
Ensure:  compliance report V

build active external subset C^ext_active = { c ∈ C^ext : status(c) = active },
      excluding superseded versions                                             (2)
preload K(c), e(c), and O(c) from G for every article in C^int and C^ext_active
for each internal article c_i in C^int do
    apply structural prefilter: take the top k_struct external sections by
        section-level embedding similarity; let C^pre be the articles they contain
    for each candidate c_j in C^pre do
        compute S_concept using (5), S_embedding using (6),
            and the hybrid score R(c_i, c_j) using (4)
    end for
    select P_i, the top k_final candidates in C^pre by descending R
    for each c_j in P_i do
        invoke reasoner ρ on the texts of c_i and c_j with O(c_i) and O(c_j) to obtain
            verdict v, reason code r, rationale s, matched M+, and missing M-
        append (c_i, c_j, v, r, s, M+, M-) to V
    end for
end for
return V
```

## Equations

Active external subset, where the status of each article version is materialized by the
revision layer at ingestion:

$$C^{ext}_{active} = \{\, c \in C^{ext} : \mathrm{status}(c) = \mathrm{active} \,\} \qquad (2)$$

Specificity of a concept $k$, with $V_{article}$ the set of article nodes in the graph:

$$\mathrm{spec}(k) = \log \frac{|V_{article}|}{|\{\, c : k \in K(c) \,\}| + 1} \qquad (3)$$

Hybrid retrieval score:

$$R(c_i, c_j) = \lambda_c\, S_{concept}(c_i, c_j) + \lambda_e\, S_{embedding}(c_i, c_j) \qquad (4)$$

Concept similarity, the specificity-weighted Jaccard coefficient of the two concept sets:

$$S_{concept}(c_i, c_j) = \frac{\sum_{k \in K(c_i) \cap K(c_j)} \mathrm{spec}(k)}{\sum_{k \in K(c_i) \cup K(c_j)} \mathrm{spec}(k)} \qquad (5)$$

Embedding similarity, the cosine of the two article embeddings:

$$S_{embedding}(c_i, c_j) = \frac{e(c_i) \cdot e(c_j)}{\lVert e(c_i) \rVert \, \lVert e(c_j) \rVert} \qquad (6)$$

## Notation

- `K(c)`: the concept set of article `c`; `e(c)`: its embedding; `O(c)`: its obligation set,
  each obligation carrying subject, action, object, condition, time constraint, modality, and
  sanction.
- `v`: the binary verdict, Comply or Non-Comply.
- `r`: the reason code, from a fixed vocabulary: full alignment when the internal provision
  satisfies the external one, and coverage gap, condition gap, threshold gap, weakened
  obligation, or direct conflict for the ways it can fail.
- `s`: the natural-language rationale.
- `M+` and `M-`: the matched and the missing obligation elements.

## Settings used in the paper

- Mixing weights λc = 0.2 and λe = 0.8.
- Retrieval depths k_struct = k_final = 3.
- Embeddings: OpenAI text-embedding-3-large at 3072 dimensions.
- Reasoner: gpt-5.4, with gpt-5.4-mini for extraction and gpt-5.4-nano for cross-references,
  all at temperature zero.
- Graph store: Neo4j Aura 5.28.
