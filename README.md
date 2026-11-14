# AI Model Diagnostics and Repair Research

I’m investigating whether AI agents can diagnose failures in trained models and propose targeted weight changes that improve their behavior. A major part of the project is developing Python/PyTorch evaluation harnesses: the software that runs controlled experiments, validates model changes, and records the evidence behind each result.

A useful change needs to do more than improve a score on the examples used to select it. It also needs to hold up on new examples, avoid unacceptable regressions, and justify its added complexity compared with simpler approaches. Those questions guide the evaluation work.

The work also includes reviewing related research, screening candidate problems for measurable failures, and checking published model artifacts before attempting behavioral reproduction.

## Evaluation harness development

The harness makes the experimental rules part of the software. It needs to compare the intended model versions under matching conditions, control which data a worker can access, and preserve evaluation budgets across errors and restarts. These are engineering requirements as well as research safeguards: a changed preprocessing step can invalidate a comparison, and restarting a process must not reset an experiment’s allowance.

Implemented capabilities across the study-specific tooling include:

| Component | What it does |
| --- | --- |
| Controlled model evaluation | Uses fixed preprocessing, runtime settings, and task scoring; checks repeatability and rejects unexpected model-state changes. |
| Checkpoint validation | Checks changes against the starting checkpoint, limits changes to permitted model state, and associates them with recorded hypotheses. |
| Persistent evaluation accounting | Tracks consumed evaluation allowances across failures and restarts, accounts for paired comparisons, and stops on ambiguous execution instead of silently retrying it. |
| Restricted evaluation workers | On supported platforms, limits data and network access, keeps evaluation inputs read-only, and confines outputs to the run’s designated location. |
| Baseline and control comparisons | Compares candidates with the unchanged model, random controls, and simpler alternatives, alongside checks for regressions. |
| Experiment records | Connects checkpoint identities, settings, outputs, and decisions so a result can be traced back to the run that produced it. |

A separate Python workflow layer connects reviewed launch plans to declared input files, checks for changed dependencies before execution, and records stage completion. This newer tooling is intended for prospective studies; earlier experiments retain their original runners and protocols.

The harnesses share these engineering concerns, while model-specific evaluation and each study’s experimental rules remain separate.

Verification is part of the development work. Depending on the study, it includes synthetic rehearsals, regression tests, and independent recomputation of recorded results and pass/fail decisions.

![The evaluation harness compares reference and candidate checkpoints under fixed settings, then checks target behavior, preservation, and controls before recording a decision.](assets/evaluation-workflow.svg)

*The comparison path within the evaluation harness. Candidate-generation methods remain private.*

## Evaluation approach

The workflow starts by measuring the unchanged model and defining the behavior being investigated. Candidate changes are compared under matching conditions, with the relevant metric, baseline, and decision rule recorded for the study.

### Task-specific evaluation

Different model types and tasks produce different outputs, so their evaluation needs different data, execution settings, and scoring rules. The same model can also need several evaluations for different capabilities.

| Task | What its evaluation measures or controls |
| --- | --- |
| Retrieval and embeddings | Ranking and coverage of supported results within a retrieved candidate set. Incomplete relevance judgments and candidate availability limit what those scores establish. |
| Object detection | Classification and localization of predicted boxes, with fixed preprocessing, matching, postprocessing, and metric computation. |
| Image–text matching or classification | Matching scores or decisions under a defined prompt and candidate setup, alongside separate checks for capabilities the change should preserve. |
| Generative-model reproduction | Planned answer and behavior scoring under fixed prompts, sampling, stopping rules, and generation limits. Artifact checks are complete; behavioral replay remains pending. |

Each study defines its evaluation unit, success criteria, controls, and preservation checks. Scores from these different tasks are not interchangeable.

Development results and independent confirmation answer different questions. Development examples can help choose a candidate, while fresh confirmation checks whether that fixed choice holds up beyond those examples. The records distinguish these stages rather than treating every measured improvement as an independently confirmed result.

Preservation checks look for losses outside the behavior being targeted. In relevant studies, the analysis includes individual corrections and regressions alongside aggregate scores, because an unchanged average can hide changes in which examples the model gets right.

The [evaluation record guide](docs/evaluation-records.md) explains the categories of evidence retained for interpreting a result.

## Results and limitations

Completed studies have produced controlled improvements as well as unsuccessful candidates and failed transfer checks. The summaries below describe separate studies without disclosing their model identities, task construction, or repair methods.

| Study | Observed success | Limits |
| --- | --- | --- |
| Controlled repair | An agent-proposed weight edit reduced errors on held-out controlled examples and met the study’s predefined success criteria. | Some preservation measurements worsened within the allowed thresholds. A later evaluation of the frozen edit on fresh, more natural data did not establish useful improvement. |
| Frozen-edit follow-up | A previously fixed edit improved target-task accuracy on new controlled examples and passed its predefined screen. Measured control decisions stayed unchanged. | Control score margins weakened, and another prespecified comparator performed better. Practical superiority remains unproved. |
| Planted-damage recovery | Repeated agent sessions recovered most of the measured performance lost to deliberately introduced damage and met the recovery requirement. | Sessions reused one easy damaged condition containing a detectable clue; some required documented accounting exceptions. This does not establish recovery from arbitrary faults. |

Reproduction preparation also verified the identity of published model artifacts through independent checks. Behavioral replay remains pending; artifact agreement is an engineering result, not a reproduced model improvement.

These results demonstrate narrow controlled successes and working evaluation infrastructure. The research has not established a generally useful repair method or a consistent practical advantage over simpler alternatives. Candidates that miss their predefined requirements remain recorded as failures.

## Development

I use coding agents throughout the research process to design experiments, build evaluation tools, investigate failures, and review results. I guide the research questions and use the recorded comparisons to decide which ideas are worth pursuing.

## Inspiration

This work was inspired by [Sparks of Weight-Space Programming](https://rikiyat.github.io/blog/weight-space-programming/), a research write-up by Rikiya Takehi, Jayden Teoh, Alex Zhang, and Omar Khattab at MIT CSAIL. Their experiments explore whether LLMs can inspect and directly edit neural network weights. My project examines that broader question through separate model-diagnosis and evaluation studies.
