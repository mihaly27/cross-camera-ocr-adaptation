# Preflight Experiments

Do not start these runs until the Phase 0 dataset/provenance audit is complete.

## Planned representative directions

- `G1 -> G2`
- `G1 -> G3`
- `T1 -> T2`
- `G1 -> T1`

## Initial settings

```text
k = {25, 100}
seed = 1
```

## Objectives

The preflight exists to:

- verify leakage-safe data selection;
- verify convergence;
- verify EM/CER evaluation;
- measure Node01 runtime;
- verify experiment manifests;
- verify model/configuration hashing;
- verify replay compatibility where applicable;
- identify implementation failures before the final matrix.

Preflight results may trigger technical fixes, but must not be used to drop scientifically inconvenient camera pairs.
