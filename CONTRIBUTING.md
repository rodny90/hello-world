# Contributing to repo-ready

Thanks for taking the time. Bug reports, new checks and documentation fixes are all welcome.

## Getting set up

```bash
git clone https://github.com/rodny90/repo-ready
cd repo-ready
python -m pip install -e ".[dev]"
```

## Running the checks locally

```bash
pytest              # the test suite
ruff check .        # lint
ruff format --check .
repo-ready . --min-score 100   # the tool audits itself
```

CI runs exactly these four commands, so a clean local run means a green pull request.

## Adding a new check

Checks live in `src/repo_ready/checks.py`. Each one is a function taking a `Repo` and returning `(passed, detail)`, registered as a `Check` in the `CHECKS` tuple.

1. Write the function. Keep it filesystem-only — `repo-ready` makes no network calls and has no dependencies, and both of those are deliberate.
2. Add a `Check(...)` entry with an `id`, a `title`, a `weight` and a `fix`. The `fix` is what the user reads when the check fails, so make it a concrete instruction rather than a restatement of the problem.
3. **Weights must still total 100.** Adjust the existing weights in the same pull request and say why in the description; `test_weights_sum_to_one_hundred` will fail otherwise.
4. Add tests for both the passing and the failing case.
5. Add a row to the check table in `README.md`, and a line to `CHANGELOG.md`.

A check should earn its weight. Ask whether a maintainer who fails it is meaningfully worse off — if not, it is probably a lint rule rather than a readiness check.

## Pull requests

- One logical change per pull request.
- Keep the public output formats stable: the JSON shape is something people script against, so a change to it needs a note in `CHANGELOG.md` under a new version.
- Describe the behaviour change, not the diff.

## Reporting bugs

Open an issue using the bug report template. A repository that reproduces the wrong result is worth more than a description of it — even a three-file example is enough.

## Code of conduct

This project follows the [Contributor Covenant](CODE_OF_CONDUCT.md).
