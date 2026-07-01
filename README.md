# VERSA

**VE**rsion-aware **R**egulation **S**ystem **A**rchitecture — a revision-aware,
knowledge-graph framework for internal–external regulatory compliance checking.

This is the **companion artefact repository** for the paper:

> *VERSA: A Knowledge Graph-Centric Framework for Regulatory Compliance Checking*
> (under review).

VERSA represents regulatory documents through four layers (structure, concepts,
obligations, and article-level revisions), restricts retrieval to the currently
*active* version of the external corpus, and emits a binary `COMPLY` /
`NON-COMPLY` verdict with a controlled reason code and an auditable rationale.

## What this repository contains

This repository releases **documents and example outputs only**; it does not
contain an executable implementation. The compliance-checking procedure is given
in full as pseudocode in **[`PSEUDOCODE.md`](PSEUDOCODE.md)** (Algorithm 1 of the
paper), and the paper contains the complete method and evaluation.

```
docs/
  internal/    Internal policy documents
  external/    External regulations — Bank Indonesia (PBI) and OJK (POJK)
examples/
  compliance/  Example compliance-check outputs from the reasoner
PSEUDOCODE.md  Compliance-check procedure (Algorithm 1)
ANNOTATION_PROMPT.md  LLM-as-judge annotation prompt (Claude & Gemini)
CITATION.cff   How to cite the work
```

### Documents

Each document under `docs/` is provided in several forms:

- `*.pdf` — the original document,
- `*.md` — a cleaned Markdown text extraction,
- `*.bab-pasal.json` — the parsed chapter → article (Bab → Pasal) structure,
- `*.llm-cleaned.json` — a normalised, cleaned representation of the text.

External regulations are official public documents issued by Bank Indonesia
(*Peraturan Bank Indonesia*, PBI) and the Financial Services Authority
(*Peraturan OJK*, POJK), redistributed here for research reproducibility.

### Example outputs

`examples/compliance/` contains representative reasoner records of the form
`(internal article, external article, verdict, reason code, rationale, matched /
missing obligations)`, produced by the compliance-check procedure. **Ground-truth
labels are not included.**

## Not included

- Source code / reference implementation.
- LLM extraction prompts.
- The full annotated compliance benchmark (ground-truth labels).

## Citation

If you use these materials, please cite the companion paper — see
[`CITATION.cff`](CITATION.cff).

## License

Original materials in this repository (the pseudocode, example outputs, and
internal policy documents) are released under the MIT License (see
[`LICENSE`](LICENSE)). External regulations under `docs/external/` are official
public documents of their respective issuing authorities and remain subject to
those authorities' terms.
