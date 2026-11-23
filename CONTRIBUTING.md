# Contributing

## Before opening a change

- Keep analysis offline: do not add DNS lookups, HTTP requests, redirects, browser automation, or third-party reputation submissions for submitted URLs.
- Use inert or synthetic URLs in tests. Do not add live suspicious destinations to fixtures.
- Preserve privacy: never log or expose credentials, tokens, private hostnames, or raw user-submitted URLs in generated reports.
- Add regression tests for parser, API, and evaluator changes.
- For research changes, document the hypothesis, split assumptions, leakage controls, metric definitions, and limitations. Never use synthetic test fixtures as empirical data.
- Check source and license conditions before adding any dataset, brand list, or external asset.

## Local checks

```bash
python -m compileall -q phishguard tests
python -m pytest -q
python -m pip check
pip-audit
python -m pip wheel . --no-deps --no-build-isolation -w dist
```

## Research integrity

Do not tune on a locked test set, selectively report metrics, invent dataset provenance, or claim that a heuristic score is a probability. Record protocol changes before rerunning experiments and report negative results.
