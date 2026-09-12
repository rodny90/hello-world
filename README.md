# repo-ready

[![CI](https://github.com/rodny90/repo-ready/actions/workflows/ci.yml/badge.svg)](https://github.com/rodny90/repo-ready/actions/workflows/ci.yml)
[![PyPI](https://img.shields.io/pypi/v/repo-ready.svg)](https://pypi.org/project/repo-ready/)
[![Python versions](https://img.shields.io/pypi/pyversions/repo-ready.svg)](https://pypi.org/project/repo-ready/)
[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

**Score a repository on the things contributors and users actually look for, and find out what to fix first.**

A repo can have great code and still get ignored: no licence, so companies can't touch it; no usage example, so nobody gets past the first minute; no CI badge, so nobody trusts a pull request will be reviewed. `repo-ready` checks for those twelve things in under a second, gives you a weighted score out of 100, and orders the failures by how much they cost you.

It has **no dependencies**, makes **no network calls**, and reads nothing but your files.

```
repo-ready  .

  PASS  Has a LICENSE                    found LICENSE
  PASS  Has a substantial README         found README.md (2841 characters)
  PASS  README covers install and usage  README documents installation and usage
  PASS  Has automated tests              found tests in tests/
  PASS  Runs CI on every push            found 1 GitHub Actions workflow(s)
  FAIL  Has CONTRIBUTING guidance        no CONTRIBUTING file found
  PASS  Has a security policy            found SECURITY.md
  PASS  Has a package manifest           found pyproject.toml
  PASS  Has a CHANGELOG                  found CHANGELOG.md
  PASS  Has a code of conduct            found CODE_OF_CONDUCT.md
  PASS  Has a .gitignore                 found .gitignore
  PASS  Has an issue template            found .github/ISSUE_TEMPLATE/

Score: 92/100  (grade A)

Fix these first:
  [ 8 pts] Has CONTRIBUTING guidance
           Add CONTRIBUTING.md describing how to set up the project, run tests and open a pull request.
```

## Installation

```bash
pip install repo-ready
```

Or run it once without installing anything:

```bash
pipx run repo-ready .
```

Requires Python 3.9 or newer.

## Usage

Audit the current directory:

```bash
repo-ready
```

Audit somewhere else, in any of three formats:

```bash
repo-ready ~/code/my-project
repo-ready . --format json
repo-ready . --format markdown
```

### Example: gate a pull request on it

`--min-score` makes the command exit `1` when the score drops below your bar, so it works as a CI step. Add this to `.github/workflows/ci.yml`:

```yaml
- name: Check repository health
  run: pipx run repo-ready . --min-score 80
```

### Example: put the table in your README

```bash
repo-ready . --format markdown >> README.md
```

### Example: track the score over time

The JSON output is stable and scriptable:

```bash
repo-ready . --format json | jq '.score'
```

```json
{
  "path": ".",
  "score": 92,
  "grade": "A",
  "checks": [
    { "id": "license", "title": "Has a LICENSE", "passed": true, "weight": 15,
      "detail": "found LICENSE", "fix": "" }
  ]
}
```

## The checks

Scores are weighted, not counted — a missing licence costs far more than a missing issue template.

| Check | Weight | Passes when |
| --- | ---: | --- |
| `license` | 15 | `LICENSE`, `LICENCE` or `COPYING` exists and holds real licence text |
| `readme` | 15 | A `README` exists with at least 300 characters |
| `tests` | 15 | A `tests/` directory or files named by convention (`test_*.py`, `*_test.go`, `*.test.ts`, ...) |
| `ci` | 15 | A GitHub Actions workflow, or GitLab / Travis / CircleCI / Azure config |
| `contributing` | 8 | A `CONTRIBUTING` file exists |
| `security` | 7 | A `SECURITY` file exists |
| `readme-sections` | 5 | The README has both an install and a usage section |
| `manifest` | 5 | A package manifest exists (`pyproject.toml`, `package.json`, `Cargo.toml`, `go.mod`, ...) |
| `changelog` | 5 | A `CHANGELOG`, `CHANGES` or `HISTORY` file exists |
| `code-of-conduct` | 5 | A `CODE_OF_CONDUCT` file exists |
| `gitignore` | 3 | A `.gitignore` exists |
| `issue-templates` | 2 | `.github/ISSUE_TEMPLATE/` is present and non-empty |

Grades: **A** ≥ 90, **B** ≥ 75, **C** ≥ 60, **D** ≥ 40, **F** below that.

Files are found case-insensitively and are also looked for in `.github/` and `docs/`, matching where GitHub itself looks. Vendored directories (`node_modules/`, `vendor/`, `third_party/`, build output) are skipped, so a dependency's test suite can't make your repo look tested.

## Options

| Flag | Effect |
| --- | --- |
| `-f`, `--format {text,json,markdown}` | Output format (default `text`) |
| `--min-score N` | Exit `1` if the score is below `N` |
| `--no-fixes` | Omit the suggested fixes from text output |
| `--version` | Print the version |

Colour is used when stdout is a terminal and is disabled by [`NO_COLOR`](https://no-color.org).

## What it is not

`repo-ready` checks that the scaffolding of a healthy project is *present*. It cannot tell you whether your README is any good, whether your tests are meaningful, or whether your licence suits your goals. It is a pre-flight checklist, not a review.

## Contributing

Bug reports and new checks are welcome — see [CONTRIBUTING.md](CONTRIBUTING.md). Adding a check is one entry in the `CHECKS` tuple in `src/repo_ready/checks.py` plus a test.

## License

MIT — see [LICENSE](LICENSE).
