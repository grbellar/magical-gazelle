# Changelog

All notable changes to the magical-gazelle plugin will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [3.3.1] - 2026-02-23

### Changed

- `/mg:review` now requires tests for every fix — agents must write tests covering the bug or edge case before committing
- `pr-comment-resolver` agent now includes a dedicated test-writing step in its workflow
- `resolve-pr-parallel` skill explicitly instructs each fix agent to write tests

## [3.3.0] - 2026-02-20

### Changed

- `/mg:review` now fixes findings inline instead of creating todo files — synthesize, present numbered findings, user picks actions, parallel agents fix
- `/lfg` and `/slfg` no longer include a separate resolve step (review handles it)
- `/test-browser` failure handling simplified to "Fix now" or "Skip" (no todo option)

### Removed

- `file-todos` skill (file-based todo tracking system)
- `/triage` command
- `/resolve_todo_parallel` command

## [3.2.1] - 2026-02-20

### Improved

- agent-browser skill: Add browser settings (`set viewport`, `set device`, `set media`), cookie management, authentication patterns (login form, cookies, headers, persistent sessions), and gotchas section
- ui-audit skill: Add login redirect handling to workflow, remove "no auth support" limitation

## [3.2.0] - 2026-02-20

### Added

- New `ui-auditor` design agent — audits running web apps for UI inconsistencies (spacing, typography, alignment, visual hierarchy, color/contrast, overflow, consistency) against an opinionated best practice rubric
- New `ui-audit` skill — slash command entry point (`/ui-audit <url>`) with support for responsive audits, custom wait times, and design-iterator handoff for fixes
- UI audit rubric reference file with 8 categories, severity levels, and specific checks grounded in WCAG 2.1, 8pt grid, Gestalt principles, and Nielsen's heuristics

## [3.1.2] - 2026-02-20

### Changed

- Rename workflow commands from `workflows:` prefix to `mg:` prefix (mg:plan, mg:review, mg:work, mg:compound, mg:brainstorm)

## [3.1.1] - 2026-02-19

### Fixed

- agent-browser screenshots now save to `browser-screenshots/` in the project root (gitignored automatically) instead of the shell's working directory — affects test-browser, reproduce-bug, workflows:work, figma-design-sync, design-implementation-reviewer, and design-iterator

## [3.1.0] - 2026-02-19

### Added

- New `test-plan-analyst` research agent — scans codebases to identify specific tests needed for proposed changes (plan mode) or verify test coverage gaps (review mode)
- Testing section added to all plan templates (MINIMAL, MORE, A LOT) in workflows:plan
- test-plan-analyst integrated into workflows:plan Step 1 (parallel local research)
- test-plan-analyst integrated into workflows:review (always-run agents)

### Changed

- workflows:plan now requires a Testing section in all plan levels with specific test cases and edge cases
- workflows:plan SpecFlow step now feeds edge cases into the Testing section
- workflows:work "Test Continuously" rewritten as "Write and Run Tests" — test writing is non-optional and plan-driven
- workflows:work "Test As You Go" principle renamed to "Tests Ship With Features"
- workflows:work Quality Checklist now requires plan's Testing section to be fully covered
- workflows:work Final Validation checks all plan Testing items have corresponding tests

## [3.0.1] - 2026-02-17

### Changed

- Move todos/ directory to docs/todos/ for consistent workflow artifact layout
- Fix brainstorming skill to use HH:MM timestamp in filenames

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
