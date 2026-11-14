# Evaluation records

An experiment result needs enough context to explain what was compared and what the outcome establishes. This guide describes the evidence categories used in the research records. It contains no private run data, model-specific methods, or numerical results.

## What the records cover

| Evidence | What it helps establish |
| --- | --- |
| Research question and evaluation scope | Which behavior was investigated and which conclusions the experiment can support. |
| Reference and candidate identities | Which saved model versions produced the compared outputs. |
| Evaluation settings and data roles | Whether the comparison used matching conditions and whether examples were used for development or confirmation. |
| Baseline and control results | How the candidate compares with the unchanged model and relevant alternatives. |
| Target and preservation measurements | Whether the intended behavior improved and what losses or tradeoffs appeared elsewhere. |
| Recorded changes and output artifacts | How a result connects to the candidate that was evaluated. |
| Decision and limitations | Whether the study met its predefined requirement, failed it, or remained incomplete or inconclusive. |

The exact fields and checks depend on the study. This table describes their purpose rather than a released data schema or a claim that every historical run used an identical format.

## Interpreting the evidence

A checkpoint hash identifies an artifact; it does not establish that the model behaves correctly. A completed run establishes that the evaluation executed; it does not establish that a candidate met the scientific requirement.

A development improvement can justify a follow-up without establishing useful transfer. A confirmation result applies to the examples and conditions tested. Preservation measurements also have a defined scope, so unchanged measured behavior cannot establish that every model capability was preserved.

Negative and inconclusive outcomes remain part of the research record. Keeping those outcomes alongside successful checks helps explain which conclusions survived evaluation and which questions remain open.
