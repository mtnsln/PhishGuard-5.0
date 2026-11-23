# Changelog

## 5.0.0 — Research framework release

- Add deterministic, offline lexical feature extraction for reproducible experiments.
- Add a provenance-required CSV ingestion path with duplicate URL checks and dataset SHA-256.
- Add strict chronological, registrable-domain-disjoint holdout evaluation with forward purging of later records from training-side domains; evaluation fails if both classes cannot be represented on each side.
- Add majority-class, fixed heuristic, logistic-regression, and random-forest comparators.
- Add lexical-only/full/heuristic-score ablations and production-rule baselines.
- Report precision, recall, F1, specificity, FPR, FNR, balanced accuracy, MCC, ROC-AUC, average precision, normalized partial AUC at 1% FPR, TPR at 1%/0.1% FPR, confusion matrices, subgroup counts/metrics, and domain-cluster bootstrap intervals.
- Add research protocol, dataset/model cards, threat model, privacy/ethics guidance, dataset manifest and experiment templates, publication checklist, related-work map, and citation metadata.
- Expand report URL redaction to cover nested secret parameter names and fragments.
- Record exact runtime versions and dataset/source metadata in research reports; redact sensitive URL components before feature extraction.
- Add automated dependency vulnerability auditing to CI.
- Explicitly distinguish software tests from empirical detection-quality evidence.

## 4.0.0

- Add bounded batch analysis, report URL redaction, mixed-script hostname signal, and score-breakdown metadata.

## 3.1.0

- Canonicalize IDN host output, validate numeric IPv4 candidates, refine brand edit-distance checks, and add browser security headers.

## 3.0.0

- Add stricter URL parsing, offline brand/IDN signals, request-size controls, package assets, and expanded tests.

## 2.0.0

- Previous release; see project history for details.
