# Input Data Specification v0.1

## Purpose

This document defines the material required before the first controlled Node01 preflight.

The current task is **collection and provenance reconstruction**, not training.

## Per-camera dataset package

For each of `G1`, `G2`, `G3`, `T1`, and `T2`, locate:

1. original OCR crops/images;
2. current labels;
3. existing train/validation lists;
4. existing training manifests;
5. dataset notes/readme;
6. current camera-specific OCR checkpoint(s);
7. training configuration where available;
8. representative run/replay export(s);
9. at least one replayable source clip;
10. pipeline/runtime configuration.

Do not clean, deduplicate, normalize, rename, or re-split the source material before the audit.

## Required private sample manifest fields

Minimum:

```text
sample_id
camera_id
image_path
plate_text
```

Desired where available:

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

## Identity requirements

`vehicle_identity` identifies repeated observations of the same physical plate/vehicle.

The final split must be grouped by identity so a target-test identity cannot appear in source or target training data.

`pair_id` is reserved for verified cross-camera correspondence.

## Model provenance

For every current OCR model record:

```text
camera_id
model_family
checkpoint_path
checkpoint_sha256
base_checkpoint
base_checkpoint_sha256
training_config
training_dataset_id
input_shape
character_dictionary
framework_version
notes
```

Unknown fields remain unknown.

## Replay provenance

For each representative run record:

```text
camera_id
run_id
source_video_id
source_video_sha256
model_sha256
pipeline_config_hash
software_git_revision
prediction_export
notes
```

## Public repository rule

Real plate text, plate crops, traffic/parking video and proprietary weights must remain outside the public repository unless explicitly anonymized and cleared for release.

Public Git should contain only:

- schemas;
- sanitized metadata;
- hashes;
- protocols;
- scripts;
- non-sensitive experiment definitions;
- publication-cleared evidence.
