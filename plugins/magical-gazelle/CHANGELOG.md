# Changelog

All notable changes to the magical-gazelle plugin will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [3.0.0] - 2026-02-16

First release as magical-gazelle, forked from [compound-engineering-plugin v2.34.0](https://github.com/EveryInc/compound-engineering-plugin).

### Added

- Compound engineering philosophy doc (PHILOSOPHY.md)
- Python/TypeScript testing standards to kieran-python-reviewer and kieran-typescript-reviewer
- Simplicity and elegance standards to Python and TypeScript reviewers
- HH:MM timestamps to plan and brainstorm filenames

### Changed

- Renamed plugin from compound-engineering to magical-gazelle
- Simplified git-worktree skill to use native git commands (deleted 338-line wrapper script)
- Cleaned up all workflow commands for Python/TS stack (uv, pytest, ruff, agent-browser CLI)
- Replaced Playwright MCP references with agent-browser CLI throughout
- Updated all URLs and repo references to grbellar/magical-gazelle

### Removed

- Rails/Ruby agents: `kieran-rails-reviewer`, `dhh-rails-reviewer`, `schema-drift-detector`, `lint`
- `/xcode-test` command and all iOS simulator testing references
- Cursor, Droid, and Gemini CLI converter targets
- Co-Authored-By footers, Generated-with-Claude notes, and badges from templates
- Upstream documentation site and deployment commands
