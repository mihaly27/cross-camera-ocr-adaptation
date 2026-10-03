# Research Questions

## Primary research question

**Under what conditions can knowledge from an already adapted source camera reduce the human annotation effort required to adapt a new target camera, and what validation safeguards are needed to avoid harmful transfer?**

## RQ1 — Transfer utility

Under which predefined source-target camera relationships does initialization from a camera-adapted OCR model reduce the amount of target-camera annotation required to reach a fixed recognition quality?

Predefined relation strata:

- paired same scene: `G1 <-> G2`;
- same site / different geometry: `G1/G2 <-> G3`;
- same difficult domain / different view: `T1 <-> T2`;
- cross-domain: garage `<->` TrueGlass.

## RQ2 — Safe transfer

How much target-camera verification is required to identify beneficial versus harmful transfer before full adaptation, and what annotation savings remain after accounting for this verification cost?

The scientific target is **net human-work saving**, not raw transfer gain alone.

## RQ3 — Paired-observation utility

To what extent can paired observations of the same physical vehicle replace manual target-camera annotations through verified cross-camera pseudo-labels?

This phase begins only after the supervised transfer and guard-set experiments are stable.

## Primary hypothesis

A related source camera can reduce target-camera annotation cost, but the benefit is conditional rather than universal.

## Negative transfer

Negative transfer is explicitly retained and reported. A source camera that increases target annotation cost or degrades target performance is evidence about the limits of transfer, not an experiment failure.
