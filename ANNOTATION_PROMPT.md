# VERSA — LLM-as-Judge Annotation Prompt

This is the exact prompt used to obtain the two independent large-language-model
compliance annotations reported in the paper for the inter-annotator agreement
analysis (Section IV-A). The **same prompt** was run, separately and blind, over
all 279 internal–external provision pairs with two models from different
families — **Claude (Anthropic)** and **Gemini (Google)** — at `temperature = 0`.
Neither model was shown the domain-expert labels or any VERSA prediction. The
`verdict` field of each response is that model's binary label; the resulting
labels were compared against the domain-expert ground truth using Cohen's κ
(pairwise) and Fleiss' κ (three annotators).

## Prompt

```
You are an expert banking-compliance annotator. Judge whether an INTERNAL
bank-policy article complies with the obligation imposed by an EXTERNAL
regulatory provision. Decide independently from the two texts alone — do not
assume access to any system output, model prediction, or pre-existing label.

Assess the pair at the level of seven obligation elements:
  1. Regulated subject — who is bound (e.g., the Bank, a specific work unit).
  2. Action          — the required act (e.g., report, maintain, disclose).
  3. Object          — what the action applies to (e.g., a liquidity ratio).
  4. Condition       — the circumstance under which the obligation applies.
  5. Time constraint — deadline, frequency, or effective period.
  6. Modality        — obligation / prohibition / permission (norm strength).
  7. Sanction        — the consequence of non-fulfilment, if specified.

Decision rule (binary):
  - NON_COMPLY — the internal article OMITS, WEAKENS, or CONTRADICTS any
    element that the external obligation requires.
  - COMPLY — every required element of the external obligation is satisfied by
    the internal article (equivalent-or-stricter wording is acceptable).

Return STRICT JSON only:
{
  "elements": {
    "subject": "...", "action": "...", "object": "...", "condition": "...",
    "time": "...", "modality": "...", "sanction": "..."
  },                       // one short note per element:
                           // satisfied | weakened | omitted | contradicted | n/a
  "verdict": "COMPLY" | "NON_COMPLY",
  "justification": "one sentence"
}

INTERNAL ARTICLE:
{internal_text}

EXTERNAL PROVISION:
{external_text}
```

## How the labels were produced

1. The prompt above was issued once per provision pair, independently for
   **Claude** and **Gemini**, at `temperature = 0`, with no expert label or
   VERSA prediction in the context (blind annotation). Both models come from
   families different from the reasoner used in the paper, so agreement is not
   inflated by a model corroborating its own outputs.
2. The `verdict` field of each response gives the binary label
   (`COMPLY` / `NON_COMPLY`).
3. Reliability was quantified with Cohen's κ for each annotator pair
   (expert–Claude, expert–Gemini, Claude–Gemini) and Fleiss' κ across the three
   annotators. The domain-expert labels remain the evaluation ground truth; the
   two model annotators serve only to quantify label reliability.
