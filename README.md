# PhishGuard 5.0

PhishGuard is an offline-first URL-text triage and research project. It analyses the submitted string only: it does not open, resolve, crawl, redirect to, or submit the destination to an external service.

**Research status:** 5.0 adds a reproducible evaluation framework. It does not bundle a real-world labelled dataset, a trained production model, or empirical evidence of detection accuracy. No performance claim should be made until the protocol in `docs/RESEARCH_PROTOCOL.md` has been completed on a legally usable corpus.

## What the project contains

- Strict HTTP/HTTPS URL parsing, IDNA handling, privacy-aware report redaction, and explainable heuristic findings.
- FastAPI API, CLI, and static browser interface.
- Deterministic numeric lexical feature extraction (`phishguard.features`).
- Optional research evaluator comparing a majority-class baseline, fixed heuristic baseline, logistic regression, and random forest.
- Strict chronological holdout: test domains are first observed at/after the cutoff, training observations precede it, and later rows from training-side domains are purged.
- Metrics including precision, recall, F1, specificity, FPR/FNR, balanced accuracy, MCC, ROC-AUC, average precision, partial AUC at low FPR, TPR at low FPR, confusion matrix, subgroup analyses, and domain-cluster bootstrap intervals.
- Research protocol, dataset card, model card, threat model, and paper outline.

## Install

Python 3.11 or newer is required.

```bash
python -m pip install .
```

Development and research dependencies:

```bash
python -m pip install -e '.[dev,research]'
python -m pytest -q
```

## Run the local analyzer

```bash
uvicorn phishguard.api:app --host 127.0.0.1 --port 8000
```

Open `http://127.0.0.1:8000`. Keep the service bound to localhost unless it is deployed behind an appropriately secured reverse proxy.

CLI:

```bash
phishguard example.com
phishguard 'https://paypalverify.example' --json
```

API:

```bash
curl -X POST http://127.0.0.1:8000/api/analyze \
  -H 'content-type: application/json' \
  -d '{"url":"https://example.com/login"}'
```

Batch API accepts 1–25 URL strings:

```bash
curl -X POST http://127.0.0.1:8000/api/analyze/batch \
  -H 'content-type: application/json' \
  -d '{"urls":["example.com","https://paypalverify.example/login"]}'
```

## Run a research evaluation

Prepare a UTF-8 CSV based on `data-template.csv` with these columns:

| Column | Meaning |
|---|---|
| `url` | URL text; exclude credentials, tokens, personal and private URLs |
| `label` | `0`/`benign` or `1`/`phishing` |
| `observed_at` | ISO date or timestamp when the label was observed |
| `source` | Traceable source name/version or documented collection method |
| `license` | Exact applicable license or documented permission basis |

Every record needs traceable provenance and rights for the intended research use. The evaluator rejects duplicate normalised URLs, malformed inputs, missing provenance, and insufficient domain diversity.

```bash
phishguard-evaluate path/to/labelled_urls.csv \
  --output reports/evaluation.json \
  --test-fraction 0.20 \
  --bootstrap 500
```

The evaluator assigns each registrable domain to its first-observed date, then creates a chronological domain-disjoint holdout. It fails if either partition lacks one of the two classes. It does not silently switch to a random row split. The output includes the dataset SHA-256, cutoff, split counts, model settings, metrics, and cluster-bootstrap intervals.

**Do not use the synthetic unit-test fixtures as research data.** The repository intentionally contains no bundled real-world corpus. See `docs/RESEARCH_PROTOCOL.md`, `docs/PUBLICATION_READINESS.md`, and `docs/RELATED_WORK.md` before collecting data or interpreting metrics.

## Interpreting the output

- `risk_score` is a capped additive heuristic score, not a probability.
- A low heuristic score is not evidence that a URL is safe.
- Brand references, uncommon TLDs, Punycode, and URL shorteners can occur in legitimate URLs.
- A supervised model evaluated on one corpus is not automatically generalisable to other languages, countries, campaigns, or time periods. The evaluator now purges post-cutoff rows from pre-cutoff training domains, but cannot identify every related campaign or sibling domain.
- Do not call model scores probabilities unless calibration has been measured on independent data.

## Security and privacy boundaries

- Submitted destinations are never fetched and DNS is not queried.
- Reports redact URL user-info, common secret-bearing query parameters, and URL fragments. Redaction is best-effort; secrets embedded in path segments or unusual parameter names may remain.
- The API limits request bodies to 64 KiB, rejects unknown JSON fields, and applies browser security headers.
- The OpenAPI schema is available at `/openapi.json`; hosted interactive API documentation is disabled under the strict Content Security Policy. The API has no authentication, CSRF protection for a logged-in context, or distributed rate limiter. It is intended for local use. Do not expose it directly to the public internet without TLS, authentication, rate limiting, monitoring, a data-retention policy, and deployment-specific security review.
- Research evaluation runs locally on CSV input and does not contact reputation services.

## Quality checks

```bash
python -m compileall -q phishguard tests
python -m pytest -q
python -m pip check
python -m pip wheel . --no-deps --no-build-isolation -w dist
```

For a research release, additionally record the exact Python and dependency versions, preserve dataset provenance and licensing evidence, inspect false positives and false negatives, preregister the evaluation protocol, and archive the generated evaluation report. See `docs/PUBLICATION_READINESS.md` and the rest of `docs/`.
