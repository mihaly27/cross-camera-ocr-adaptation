# Experimental Protocol v0.1

**Status:** design version; not yet frozen for final experiments.

## 1. Objective

Measure when camera-specific OCR knowledge from a source camera reduces target-camera annotation cost, and determine how much target validation is required to avoid harmful transfer.

## 2. Controlled OCR setup

Final controlled experiments use:

- one OCR architecture;
- one common base checkpoint `M0`;
- one character vocabulary;
- one preprocessing recipe;
- one input size;
- one optimizer and LR schedule;
- one augmentation recipe;
- one early-stopping rule;
- one training/evaluation code revision.

No camera-specific hyperparameter tuning is allowed after protocol freeze.

## 3. Dataset split principle

The primary split is grouped by physical vehicle/plate identity.

The same identity must not appear across:

- source-camera training and target-camera test;
- target-camera training and target-camera test;
- target-camera guard and target-camera test.

Repeated crops from one vehicle/event are not independent samples.

## 4. Full-data camera references

For every camera `j`:

```text
M_j_full = FT(M0, D_j_train)
```

These are full-data camera-specific reference models, not mathematical oracles.

## 5. Label-efficiency experiment

Target annotation budgets:

```text
k = {25, 50, 100, 200}
```

Nested subsets are preferred:

```text
D25 ⊂ D50 ⊂ D100 ⊂ D200
```

Baseline:

```text
B_j(k) = FT(M0, D_j^k)
```

Transfer:

```text
T_i->j(k) = FT(M_i_full, D_j^k)
```

All directed source-target pairs are retained.

## 6. Guard-set / safe-transfer experiment

A small target-camera guard set is evaluated before full target fine-tuning.

Candidate guard sizes:

```text
g = {5, 10, 20, 40}
```

The guard set is disjoint from adaptation and held-out test identities.

The purpose is to estimate the smallest human verification effort that can distinguish useful from harmful source initialization.

Net cost must include guard cost.

## 7. Primary metrics

OCR level:

- exact plate match (EM);
- character error rate (CER).

Diagnostic metrics may include:

- abstention/empty output;
- substitutions/deletions/insertions;
- confidence distributions.

Confidence alone is not an accuracy metric.

## 8. Human-work outcome

For target performance level `p`:

```text
C_baseline(p) = target annotation cost needed from M0
C_transfer(p) = guard cost + target adaptation annotation cost from source model
```

Net saving:

```text
Saving = 1 - C_transfer / C_baseline
```

Where practical, annotation count will be supplemented by measured human annotation/verification time.

## 9. Preflight before the full matrix

Representative transfer directions:

- `G1 -> G2` — paired same scene;
- `G1 -> G3` — same site / different geometry;
- `T1 -> T2` — difficult-domain transfer;
- `G1 -> T1` — cross-domain transfer.

Initial preflight budgets:

```text
k = {25, 100}
seed = 1
```

The preflight validates data, convergence, metrics, manifests, runtime, and logging.

It must not be used to selectively remove scientifically inconvenient transfer directions.

## 10. Final training scale

After protocol freeze:

- 20 directed source-target camera pairs;
- 4 target-label budgets;
- baseline runs;
- 5 full-data reference models;
- 3 final random seeds.

Approximate total: about 315 training runs.

## 11. Node01 execution

Use four independent GPU workers.

One OCR training job uses one GPU.

Do not use distributed training unless a later engineering need explicitly justifies it.

## 12. Required experiment manifest

Every training run must record:

```text
experiment_id
source_camera
target_camera
k
guard_size
seed
base_model_hash
input_model_hash
output_model_hash
dataset_version
train_manifest_hash
validation_manifest_hash
test_manifest_hash
git_commit
training_config_hash
start_time
end_time
gpu_id
best_epoch
validation_metrics
test_metrics
```

## 13. Phase II — paired cross-camera pseudo-labels

This starts only after the supervised transfer and safe-transfer results are stable.

Initial focus: `G1 <-> G2`.

Compare:

1. no target adaptation;
2. manual target labels;
3. verified cross-camera pseudo-labels;
4. pseudo-labels + small manual target set.

Pseudo-label acceptance criteria must be fixed before final evaluation.
