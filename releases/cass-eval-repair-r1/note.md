---
bundle: decision-note/v1
noteId: cass-eval-repair
revision: r1
title: "The embedding-model win that mostly disappeared"
date: 2026-09-08
previous: null
---

# The embedding-model win that mostly disappeared

**Check the [current revision and correction history](https://github.com/StartupBros/pi-evals-evidence/blob/decision-notes/index.md) before applying this historical result.**

Our initial evaluation reported a meaningful embedding-model win. The retained summary reported a 0.0775 nDCG@10 advantage for the candidate labeled `bench-embed-qwen3-4b` over the candidate labeled `local-embed`, and recorded the result as significant.

That inference was too confident. The evaluation mixed two problems into the comparison: generated queries could carry answer-like overlap from their source material, and the first relevance setup assumed one positive document per query. Regenerating the queries and rebuilding the relevance labels changed the result enough that the useful conclusion is about evaluation repair, not a model winner.

## What changed in the result

| Evaluation state | Evidence level here | `bench-embed-qwen3-4b` | `local-embed` | A minus B | Inference supported here |
| --- | --- | ---: | ---: | ---: | --- |
| Original generated queries, single positive | Reported-only retained aggregate | 0.5484 | 0.4709 | +0.0775 | The historical artifact reported a significant advantage; it is not recomputed here. |
| Regenerated queries, single positive | Reported-only retained aggregate | 0.2485 | 0.2153 | +0.0333 | The historical artifact reported the difference as not significant; it is not recomputed here. |
| Regenerated queries, rebuilt multi-positive labels | Recomputed from retained paired scores | 0.4707 | 0.4640 | +0.0067 | The point estimate is small and its paired bootstrap interval crosses zero. The difference remains unresolved. |

The corrected row is recomputed from 132 retained score pairs. Candidate A was higher on 67 pairs, lower on 61, and tied on 4. A seeded paired-percentile bootstrap of the mean gap produced a 95% interval from -0.0563 to +0.0705.

Those three states show how the recorded inference weakened as the evaluation changed. They do not isolate how much of the movement came from query regeneration versus label rebuilding, and I have not replayed the original experiment or recomputed its significance test.

## How to inspect the corrected calculation

- [Inspect the paired evidence](./evidence.json).
- [Download the raw JSON](https://raw.githubusercontent.com/StartupBros/pi-evals-evidence/refs/heads/decision-notes/releases/cass-eval-repair-r1/evidence.json).

The attachment removes query identifiers and text. It sorts pairs numerically by candidate A score and then candidate B score, preserves duplicate score pairs, and assigns fresh ordinal labels. The means use all 132 pairs without rounding in JSON.

For the interval, each bootstrap resample draws 132 paired score differences with replacement. The deterministic generator uses seed `20260907`; the attachment records 10,000 resamples and the exact 32-bit linear-congruential generator rule. The interval is the nearest-rank value at 2.5% and 97.5% after sorting the 10,000 bootstrap mean gaps.

## Checks I would apply before acting on a retrieval win

1. **Retain paired per-query scores.** Aggregate means alone cannot show whether an advantage is broad, concentrated, or sensitive to a few queries.
2. **Check generated queries for source leakage.** A fluent synthetic query can still contain language that makes retrieval artificially easy for one representation.
3. **Challenge single-positive relevance.** In a private corpus, several passages may answer the same query. Treating all unchosen passages as irrelevant can reward the wrong behavior.
4. **Recompute after every repair.** Do not carry a significance label or winner statement forward from an earlier query or relevance set.
5. **Keep the decision narrower than the benchmark.** A repaired historical comparison can invalidate confidence in a claimed winner without proving equivalence, identifying a current winner, or demonstrating that a deployed model should be reversed.

## Limits

This was historical evidence from one user's corpus. The evaluation used model-generated queries alongside human-written queries, and the rebuilt relevance labels were model-judged rather than human-audited. Removing identifiers and text from the attachment reduces disclosure, but the numeric projection does not validate the private relevance judgments or prove anonymization.

The candidate names are labels recorded at evaluation time. They are not claims about immutable weights, exact artifact provenance, or what either label denotes today. This note does not establish current model superiority, statistical equivalence, or a deployment reversal.

<!-- release:attribution-footer -->
## About and contact

This note is part of an independent evaluation record. After the evidence, questions or corrections can be directed through [Will Mitchell's homepage](https://willmitchell.com/?utm_source=pi-evals-evidence&utm_medium=decision-note&utm_campaign=cass-eval-repair-r1).

Authored note prose is intended for CC BY-ND 4.0 publication; code and tooling remain MIT.
<!-- /release:attribution-footer -->
