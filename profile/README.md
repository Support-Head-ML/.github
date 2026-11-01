# Support Head ML

Typed-decision models return probabilities for declared questions: a choice
among options, a yes/no event, or a level on an ordered scale. An application
may ask several related questions about the same case in separate requests.
Each response can look valid on its own while the probabilities disagree when
considered together.

## The problem

Suppose an agent asks which department should handle a request and later asks,
separately, whether the request belongs to billing. If both questions use the
same case, policy and exhaustive set of departments, the probability assigned
to billing should agree. The same requirement applies to a question and its
negation: **p(A) + p(not A) = 1** when they refer to complementary events under
the same information.

Disagreement matters when different components act on those probabilities.
Two reports about the same event can cross a decision threshold and trigger
incompatible actions. These comparisons are meaningful only when the event,
time, policy and available information actually match.

## Published evidence

[Li et al. (2026)](https://arxiv.org/abs/2609.33209) tested 160 cases from
ChaosNLI and PubMedQA with ten related questions per case. Across 480 pairs
of a label and its negation, the Jev typed-decision model had a mean
complement residual of **0.064** (95% confidence interval **0.055–0.072**),
where the residual is `|p(A) + p(not A) - 1|`. The probabilities assigned
separately to the three mutually exclusive labels summed to **1.14** on
average. The [paper's data and analysis code](https://github.com/bro789/typed-decision-coherence)
are publicly available.

## Our diagnostic evidence

We ran the original 160-case battery with four existing model configurations.
Each answered all 1,600 requests. The table reports the mean complement
residual alongside Choice accuracy; a smaller residual measures agreement
between related reports, while accuracy measures a different property.

| Model configuration | Mean complement residual ↓ | Choice accuracy ↑ |
| --- | ---: | ---: |
| Decider 4B calibrated | 0.074 | 63.1% |
| Decider 2B calibrated | 0.294 | 54.4% |
| Laya English | 0.608 | 51.9% |
| system-one-270m | 0.557 | 30.6% |

The pattern also depends on the information in the case. In a separate suite
of explicitly worded service scenarios, Decider 4B had a mean complement
residual of **0.354** across 70 incomplete states. At a probability threshold
of 0.5, **42.2%** of related reports implied different actions. Its residual
was **0.032** across 50 states where the event was determinable.

These are preliminary diagnostic runs, not a ranking of models across tasks.
The service scenarios and their semantic relations have not received
independent blind review. The 270M model's binary output was obtained through
a two-option Choice adapter. The Jev figure above is from Li et al.; we did
not rerun Jev locally.
