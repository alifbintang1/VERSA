# VERSA

VErsion-aware Regulation System Architecture (VERSA): a revision-aware knowledge graph
framework for internal-external regulatory compliance checking.

This is the companion repository of the paper

> **A Revision-Aware Knowledge Graph Framework for Regulatory Compliance Checking** (under review)
> Alif Bintang Elfandra, Fariz Darari, Douglas Raevan Faisal, Berty Chrismartin Lumban Tobing,
> On Lee, and Jaycent Gunawan Ongris

VERSA represents regulatory documents through four graph layers (structural hierarchy, legal
concepts, structured obligations, and article-level revisions), restricts retrieval to the
articles currently in force, and emits a binary Comply or Non-Comply verdict with a reason code
from a fixed vocabulary and an auditable rationale.

## What this repository contains

It releases the documents, example outputs, and the procedure described in the paper. It does
not contain an executable implementation: the source code is part of a proprietary system.

```
docs/
  internal/             the six synthetic internal policy documents used as queries (Table 3)
  external/             the 14 external regulations of the corpus (Table 2)
examples/
  compliance/           example compliance-check records produced by the reasoner
PSEUDOCODE.md           the compliance-check procedure (Algorithm 1) and its equations
ANNOTATION_PROMPT.md    the prompt given to the two model annotators in the agreement check
CITATION.cff            how to cite the paper
```

### Document formats

Each document is provided in several forms:

- `*.pdf`: the original document. The three diagnostic policies are provided as text only,
  without a PDF.
- `*.md`: the cleaned text extraction.
- `*.bab-pasal.json`: the parsed chapter (Bab) to article (Pasal) structure.
- `*.llm-cleaned.json`: the normalized article text from which the knowledge graph is built.
- `*.amendment.json`: the articles an amending regulation declares it changes
  (PBI 22/22/PBI/2020 and PBI 10/2023).

### Internal policy documents

A bank's internal policies are confidential, so the six internal documents are synthetic:
written for this study, inspired by real bank policies, and each grounded in obligations from
the external regulations below. Only the query side is synthetic; the external corpus is
authentic. Table numbers refer to the paper.

| Paper label | Files (`docs/internal/`) | Domain | Chapters | Articles |
|---|---|---|---:|---:|
| DIR-BAS | `088_DIR_BAS_2024.*` | Liquidity, GWM, PLJP | 5 | 13 |
| KEP-DIR | `KEP-DIR_012_2026.*` | IT governance, digital banking | 12 | 23 |
| PD-DIR-BNP | `012_PD-DIR_BNP_I_2026.*` | Banking risk and operations | 9 | 13 |
| DIR-MIR | `doc_a_mirror.*` | IT governance (diagnostic: mirrors its external references verbatim) | 7 | 15 |
| DIR-CON | `doc_b_concept.*` | Cybersecurity and IT risk (diagnostic: same obligations in lexically distinct terms) | 7 | 15 |
| DIR-REV | `doc_c_revision.*` | Payment system services (diagnostic: cites provisions that have since been amended) | 5 | 15 |
| **Total** | | | **45** | **94** |

DIR-BAS, KEP-DIR, and PD-DIR-BNP are the operational policies; DIR-MIR, DIR-CON, and DIR-REV
are the diagnostic policies, each isolating one retrieval mechanism.

### External regulations

Official public documents issued by Bank Indonesia (Peraturan Bank Indonesia, PBI) and the
Financial Services Authority, Otoritas Jasa Keuangan (OJK): its regulations (POJK) and circular
letters (SEOJK), redistributed here for research reproducibility.

| Regulation | Files (`docs/external/`) | Topic | Chapters | Provisions |
|---|---|---|---:|---:|
| POJK 17/2023 | `POJK 17 Tahun 2023. PENERAPAN TATA KELOLA BAGI BANK UMUM.*` | Good corporate governance | 23 | 143 |
| POJK 11/2022 | `POJK 11 - 03 - 2022.*` | IT implementation | 14 | 73 |
| POJK 22/2022 | `POJK No.22_POJK.03_2022.*` | Capital participation | 10 | 42 |
| POJK 21/2023 | `POJK 21 Tahun 2023. Layanan Digital oleh Bank Umum.*` | Digital banking services | 7 | 40 |
| SEOJK 29/2022 | `SEOJK 29 SEOJK.03 2022.*` | Cybersecurity risk management | 10 | 49 |
| SEOJK 24/2023 | `SEOJK 24-SEOJK.03-2023. Penilaian Tingkat Maturitas Digital Bank Umum.*` | Digital maturity assessment | 4 | 13 |
| PBI 4/2023 | `PBI No. 4_2023.*` | Short-term liquidity facility (PLJP) | 14 | 38 |
| PBI 21/9/PBI/2019 | `Peraturan BI No. 21-9-PBI 2019.*` | Integrated commercial bank reporting | 12 | 48 |
| PBI 22/20/PBI/2020 | `22.20.PBI.2020.*` | Consumer protection | 10 | 55 |
| PBI 16/1/PBI/2014 | `Peraturan BI No. 16-1-PBI 2014.*` | Consumer protection (predecessor) | 9 | 32 |
| PBI 22/22/PBI/2020 | `22.22.PBI.2020.*` | Bank reporting amendment | 1 | 13 |
| PBI 22/15/PBI/2020 | `PBI No.22_15_PBI_2020.*` | PLJP amendment | 1 | 14 |
| PBI 23/16/PBI/2021 | `PBI No. 23_16_PBI_2021.*` | GWM amendment | 1 | 6 |
| PBI 10/2023 | `PBI No. 10_2023.*` | GWM/PLJP amendment | 1 | 3 |
| **Total** | | | **117** | **569** |

The last four PBI entries are amendment regulations; for these, only the amended articles are
counted. PBI 22/20/PBI/2020 replaces PBI 16/1/PBI/2014, and PBI 22/22/PBI/2020 amends
PBI 21/9/PBI/2019. Of the 569 provisions, 512 are in force and 57 have been superseded.
GWM is Giro Wajib Minimum, the statutory reserve requirement; PLJP is Pinjaman Likuiditas
Jangka Pendek, the short-term liquidity facility.

### Example outputs

- `examples/compliance/table1_record.json`: the record shown in Table 1 of the paper, DIR-REV
  Article 3 judged against PBI 21/9/PBI/2019 Article 17 in the version in force, from the full
  pipeline with gpt-5.4 as the reasoner.
- `examples/compliance/example_compliance_report.json`: 30 records for one internal policy
  document, produced with gpt-5.4-mini, the smaller reasoner reported in the paper.

Each record carries the verdict, the reason code, the rationale, the external obligation and
the internal position that were compared, the gap, and a recommended action. Records are
unedited model output. Ground-truth labels are not included.

## Not included

- Source code (part of a proprietary system).
- The prompts used for extraction.
- The 279 annotated internal-external pairs used as ground truth.

## Citation

If you use these materials, please cite the paper; see [`CITATION.cff`](CITATION.cff).

## License

Original materials in this repository (the pseudocode, the example outputs, and the internal
policy documents) are released under the MIT License (see [`LICENSE`](LICENSE)). The external
regulations under `docs/external/` are official public documents of their issuing authorities
and remain subject to those authorities' terms.
