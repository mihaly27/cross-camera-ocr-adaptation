# Annotation Protocol v0.1

Complete this document during the initial data audit.

## 1. Annotation unit

Current annotation unit:

- [ ] OCR crop
- [ ] full frame
- [ ] event/lifetime
- [ ] other:

## 2. Annotator(s)

Who creates the ground-truth text?

Who verifies it?

Is independent second review used?

## 3. Ground-truth transcription rule

Record the visible plate text without silently forcing it into Hungarian syntax.

Recommended canonical comparison form for OCR evaluation:

- uppercase;
- whitespace removed;
- separators/hyphens removed;
- character content otherwise unchanged.

Example:

```text
AAKT-740 -> AAKT740
```

Do not use Hungarian plate grammar itself as proof that a recognition is correct.

## 4. Uncertainty

Define explicit labels for:

- fully readable;
- uncertain character(s);
- unreadable plate;
- no plate;
- non-plate text/graphic.

Do not replace uncertain ground truth with model predictions.

## 5. Foreign plates

Foreign plates remain valid OCR targets if confidently readable.

Record country/format information separately if useful, but do not rewrite their character sequence to fit Hungarian grammar.

## 6. Corrections and audit trail

If a label is corrected, preserve:

- previous value;
- corrected value;
- annotator;
- reason;
- date/version.

## 7. Annotation-time measurement

Prepare a mixed 100–200 crop sample.

For each annotation/verification session record:

```text
annotator_id
sample_count
active_time_seconds
corrections_count
uncertain_count
notes
```

The aim is to estimate human work per verified target label.
