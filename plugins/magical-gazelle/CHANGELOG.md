# Changelog

All notable changes to the magical-gazelle plugin will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [2.34.0] - 2026-02-16

### Changed

- Forked from [compound-engineering-plugin v2.34.0](https://github.com/EveryInc/compound-engineering-plugin)
- Removed Rails/Ruby agents (`kieran-rails-reviewer`, `dhh-rails-reviewer`, `lint`)
- Added Python/TypeScript testing standards to reviewers
- Added simplicity and elegance section to Python and TypeScript reviewers
- Added Philosophy section to Python CLAUDE.md template
- Cleaned up all commands for Python/TS stack:
  - Replaced Playwright MCP with agent-browser CLI
  - Updated default port to localhost:8000
  - Updated tooling references to uv, pytest, ruff
  - Replaced stale agent references with current equivalents
  - Updated file-to-route mappings for Python/generic projects
- Removed Co-Authored-By footers, Generated-with-Claude notes, and badges from templates
- Updated all URLs and repo references to grbellar/magical-gazelle
