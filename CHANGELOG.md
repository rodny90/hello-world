# Changelog

All notable changes to this project are documented here. The format follows
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres to
[Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [0.1.0] - 2026-09-12

First release.

### Added

- Twelve weighted repository readiness checks: `license`, `readme`, `readme-sections`,
  `tests`, `ci`, `contributing`, `security`, `manifest`, `changelog`, `code-of-conduct`,
  `gitignore` and `issue-templates`.
- A weighted score out of 100 with an A–F grade, and failures ordered by weight so the
  most valuable fix comes first.
- A concrete suggested fix for every failing check.
- Three output formats: `text` (coloured when attached to a terminal, honouring `NO_COLOR`),
  `json` and `markdown`.
- `--min-score N`, which exits `1` below the threshold so the tool can gate CI.
- Case-insensitive file lookup that also searches `.github/` and `docs/`, matching where
  GitHub looks for community files.
- Vendored and build directories are skipped, so a dependency's test suite cannot make a
  repository look tested.

[Unreleased]: https://github.com/rodny90/repo-ready/compare/v0.1.0...HEAD
[0.1.0]: https://github.com/rodny90/repo-ready/releases/tag/v0.1.0
