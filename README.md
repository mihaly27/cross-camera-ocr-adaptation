# Cross-Camera OCR Adaptation

Research repository for a five-camera OCR adaptation study built around the Heimdall camera/replay test system.

## Core scientific question

> **Under what conditions can knowledge from an already adapted source camera reduce the human annotation effort required to adapt a new target camera, and what validation safeguards are needed to avoid harmful transfer?**

This repository is intentionally separate from the earlier `ocr-segmentation` work. The present study focuses on:

- camera-specific OCR fine-tuning;
- cross-camera transfer;
- annotation-cost reduction;
- identification and rejection of harmful/negative transfer;
- later, cross-camera pseudo-label adaptation.

## Current phase

**Phase 0 — data and provenance audit.**

Do **not** start the full training matrix yet.

The immediate objective is to collect the five camera datasets, current model/configuration information, replay evidence, and pairing information in a form that allows a leakage-safe split and a controlled preflight experiment.

---

# Camera IDs

Use these research IDs consistently even if the production system uses different camera names.

| ID | Regime | Experimental role |
|---|---|---|
| `G1` | garage | parking-area view A; one side of the six-place paired scene |
| `G2` | garage | opposing view of the same six parking places |
| `G3` | garage | corridor / turning view; moving vehicles at the same site |
| `T1` | TrueGlass | moving street traffic through glass; difficult optical domain |
| `T2` | TrueGlass | second through-glass street view; different angle/view |

Predefined relation strata:

1. **paired same scene:** `G1 <-> G2`
2. **same site / different geometry:** `G1/G2 <-> G3`
3. **same difficult domain / different view:** `T1 <-> T2`
4. **cross-domain:** garage `<->` TrueGlass

These categories are defined before the final results are inspected.

---

# Monday start: exact collection workflow

The most important rule is:

> **Collect first. Do not clean, re-split, deduplicate, or retrain yet.**

## Step 1 — Fill the five-camera inventory

Open:

`metadata/cameras.csv`

For each of `G1`, `G2`, `G3`, `T1`, and `T2`, fill in:

- production/system camera name;
- environment type;
- stationary vs. moving vehicle regime;
- through-glass yes/no;
- same-scene partner, if any;
- resolution and FPS, if known;
- short viewing-geometry note;
- short provenance note.

**Do not guess unknown values.** Leave them blank and add a note if useful.

## Step 2 — Locate the existing OCR dataset for every camera

For each camera, locate the current OCR training material **exactly as it exists now**:

- OCR crops/images;
- labels;
- current train/validation lists;
- existing manifests;
- dataset README/notes;
- any historical training export that identifies what was used.

Do not reorganize, rename, deduplicate, or re-split the source dataset yet.

Keep actual image data on **Node01 or other controlled storage**, not in this public repository.

Suggested Node01 layout:

```text
cross-camera-ocr-adaptation-data/
  G1/
    dataset_original/
  G2/
    dataset_original/
  G3/
    dataset_original/
  T1/
    dataset_original/
  T2/
    dataset_original/
```

For each dataset, record in `metadata/camera_inventory.csv`:

- storage location;
- dataset/archive SHA-256 where practical;
- total files;
- labelled OCR crops;
- number of unique plate/vehicle identities if known;
- notes.

## Step 3 — Build a private sample manifest

Use:

`manifests/manifest_template.csv`

Minimum required fields:

```text
sample_id
camera_id
image_path
plate_text
```

Collect the following where available:

```text
session_id
run_id
source_video
frame_id
timestamp
vehicle_identity
event_id
pair_id
label_status
annotation_source
```

Definitions:

- `vehicle_identity`: repeated crops of the same physical plate/vehicle share one identity;
- `event_id`: one physical observation/parking/passage event;
- `pair_id`: verified cross-camera observations of the same physical vehicle/event.

Missing provenance stays missing. **Do not invent it.**

Because this repository is public, real plate text and raw image paths belong in a **private/local manifest**, not in committed public CSV files.

## Step 4 — Establish G1/G2 paired observations

This is a priority task.

For vehicles visible in both opposing garage cameras:

1. identify the same physical vehicle/event manually;
2. assign the same `vehicle_identity`;
3. assign a common `pair_id`;
4. keep both camera observations as separate samples;
5. record how the pairing was verified.

Automatic cross-camera tracking is **not required**.

Timestamp, parking position, appearance, and manual verification are acceptable if documented.

If a `G3` corridor observation can also be associated confidently with the same event, record it. If not, leave it unpaired.

## Step 5 — Collect current OCR model/checkpoint provenance

For every existing camera-specific OCR model, record:

- checkpoint/model path;
- model family/architecture;
- base checkpoint, if known;
- training configuration;
- character dictionary/alphabet;
- input shape;
- software/framework versions;
- model SHA-256;
- training dataset/manifest used, if recoverable.

The current camera models do **not** need to share one architecture. They are audit evidence.

The controlled scientific experiment will later use **one common OCR architecture and one common base checkpoint across all five cameras**.

## Step 6 — Collect representative replay/run evidence

For every camera collect at least:

- one representative run/replay export;
- preferably one difficult or atypical run as well;
- configuration export;
- prediction export;
- software/git revision if available;
- source-video reference/hash if available.

The provenance chain should eventually be traceable as:

```text
camera -> source -> configuration -> model -> predictions -> result
```

## Step 7 — Collect at least one replayable source clip per camera

For the first audit, one short reproducible source clip per camera is enough.

For `G1/G2`, prioritize an interval containing vehicles seen by both cameras.

For `T1/T2`, comparable periods are useful if available, but do not manufacture synchrony that is not actually present.

Record storage path and file hash.

## Step 8 — Document the current annotation process

Complete:

`docs/ANNOTATION_PROTOCOL.md`

We need to know:

- who creates the OCR ground truth;
- who verifies it;
- whether annotation is crop-level or frame-level;
- how uncertain characters are marked;
- how unreadable plates are marked;
- how foreign/non-Hungarian plates are handled;
- whether a second person verifies labels;
- whether corrections preserve an audit trail.

## Step 9 — Prepare an annotation-time sample

Prepare a mixed sample of approximately **100–200 OCR crops** spanning the five cameras.

This sample is used to estimate **real human annotation/verification time**.

It is not the final training dataset and should be kept separate from future held-out test identities.

## Step 10 — Stop before creating new train/validation/test splits

Do **not** manually create a new split yet.

We first need an identity-leakage audit across cameras.

The future primary split will be grouped by physical plate/vehicle identity so the same identity cannot leak from a source-camera training set into a target-camera held-out test set.

---

# Monday completion checklist

Phase 0 collection is complete when:

- [ ] all five cameras are entered in `metadata/cameras.csv`;
- [ ] the existing OCR dataset for each camera has been located;
- [ ] labelled crop counts are known or computable;
- [ ] unique plate/vehicle counts are known or computable;
- [ ] current OCR model/checkpoint provenance is recorded where available;
- [ ] at least one representative replay/run per camera is identified;
- [ ] at least one replayable source clip per camera is identified;
- [ ] `G1/G2` manual pairing feasibility has been checked;
- [ ] annotation rules and uncertainty handling are documented;
- [ ] a 100–200 crop annotation-time sample can be assembled;
- [ ] no new experiment split has been created yet.

After this checklist is complete, the next outputs are:

1. identity/leakage audit;
2. global split specification;
3. Preflight Experiment Matrix v0.1;
4. first Node01 training queue.

---

# What must NOT be committed publicly

Unless explicitly anonymized and cleared for publication, do not commit:

- raw traffic or parking video;
- license-plate crops containing real plates;
- private manifests containing actual plate strings;
- proprietary model weights;
- credentials or private production URLs;
- large run archives;
- non-cleared personal or operational data.

Use Git for:

- protocols;
- schemas;
- scripts;
- hashes;
- sanitized metadata;
- experiment definitions;
- publication-cleared evidence.

---

# Planned experimental logic

All controlled scientific models will eventually use the same OCR architecture and the same common base checkpoint `M0`.

For target camera `j` and target annotation budget `k`:

```text
Baseline:  B_j(k)    = FT(M0, D_j^k)
Transfer:  T_i->j(k) = FT(M_i, D_j^k)
```

Planned target annotation budgets:

```text
k = {25, 50, 100, 200}
```

Primary OCR metrics:

- exact plate match (EM);
- character error rate (CER).

Primary practical outcome:

> **Net target-camera human annotation cost required to reach a predefined recognition level, including the cost of transfer validation.**

Negative transfer is an experimental result, not a failed experiment.

Initial controlled preflight directions:

- `G1 -> G2`
- `G1 -> G3`
- `T1 -> T2`
- `G1 -> T1`

See the documents under `docs/`.

## Compute target

Primary compute node: **Node01**.

Use four independent GPU workers: one OCR training job per GPU, with independent experiment jobs in parallel rather than distributed training of one OCR model.

## Repository structure

```text
.
├── README.md
├── docs/
│   ├── RESEARCH_QUESTIONS.md
│   ├── INPUT_DATA_SPEC.md
│   ├── EXPERIMENT_PROTOCOL_v0.1.md
│   └── ANNOTATION_PROTOCOL.md
├── metadata/
│   ├── cameras.csv
│   └── camera_inventory.csv
├── manifests/
│   ├── manifest_template.csv
│   └── pairing_template.csv
└── experiments/
    └── preflight/
        └── README.md
```

## Status

**Protocol development — v0.1. Not yet frozen for final experiments.**
