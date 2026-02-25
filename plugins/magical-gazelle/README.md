# Magical Gazelle

A personal fork of the [Compounding Engineering Plugin](https://github.com/EveryInc/compound-engineering-plugin) — AI-powered development tools that get smarter with every use.

## Components

| Component   | Count |
| ----------- | ----- |
| Agents      | 14    |
| Commands    | 13    |
| Skills      | 13    |
| MCP Servers | 1     |

## Agents

Agents are organized into categories for easier discovery.

### Review (5)

| Agent                  | Description                                                               |
| ---------------------- | ------------------------------------------------------------------------- |
| `python-reviewer`      | Python code review for simplicity, Pythonic patterns, and type safety     |
| `typescript-reviewer`  | TypeScript code review for simplicity, type safety, and async correctness |
| `data-reviewer`        | Database migrations, data safety, and deployment verification             |
| `performance-oracle`   | Performance analysis and optimization                                     |
| `security-sentinel`    | Security audits and vulnerability assessments                             |

### Research (3)

| Agent                       | Description                                                              |
| --------------------------- | ------------------------------------------------------------------------ |
| `framework-docs-researcher` | Research framework docs, best practices, and implementation patterns     |
| `repo-research-analyst`     | Research repo structure, git history, conventions, and past solutions     |
| `test-plan-analyst`         | Scan codebase to identify tests needed for proposed changes or review    |

### Design (3)

| Agent             | Description                                                            |
| ----------------- | ---------------------------------------------------------------------- |
| `design-reviewer` | Compare UI implementations against Figma designs and fix discrepancies |
| `design-iterator` | Iteratively refine UI through systematic design iterations             |
| `ui-auditor`      | Audit running apps for UI inconsistencies against best practice rubric |

### Workflow (3)

| Agent                        | Description                                            |
| ---------------------------- | ------------------------------------------------------ |
| `bug-reproduction-validator` | Systematically reproduce and validate bug reports      |
| `pr-comment-resolver`        | Address PR comments and implement fixes                |
| `spec-flow-analyzer`         | Analyze user flows and identify gaps in specifications |

## Commands

### Workflow Commands

Core workflow commands use `mg:` prefix to avoid collisions with built-in commands:

| Command                 | Description                                         |
| ----------------------- | --------------------------------------------------- |
| `/mg:brainstorm` | Explore requirements and approaches before planning |
| `/mg:plan`       | Create implementation plans                         |
| `/mg:review`     | Run comprehensive code reviews                      |
| `/mg:work`       | Execute work items systematically                   |
| `/mg:compound`   | Document solved problems to compound team knowledge |

### Utility Commands

| Command                  | Description                                                     |
| ------------------------ | --------------------------------------------------------------- |
| `/lfg`                   | Full autonomous engineering workflow                            |
| `/deepen-plan`           | Enhance plans with parallel research agents for each section    |
| `/changelog`             | Create engaging changelogs for recent merges                    |
| `/heal-skill`            | Fix skill documentation issues                                  |
| `/reproduce-bug`         | Reproduce bugs using logs and console                           |
| `/resolve_parallel`      | Resolve TODO comments in parallel                               |
| `/smoke-test`            | Full regression test of every route and user flow               |
| `/feature-video`         | Record video walkthroughs and add to PR description             |

## Skills

### Architecture & Design

| Skill                       | Description                                                  |
| --------------------------- | ------------------------------------------------------------ |
| `agent-native-architecture` | Build AI agents using prompt-native architecture             |
| `ui-audit`                  | Audit running apps for UI inconsistencies and best practices |

### Development Tools

| Skill                 | Description                                          |
| --------------------- | ---------------------------------------------------- |
| `compound-docs`       | Capture solved problems as categorized documentation |
| `create-agent-skills` | Expert guidance for creating Claude Code skills      |
| `frontend-design`     | Create production-grade frontend interfaces          |

### Content & Workflow

| Skill                 | Description                                                        |
| --------------------- | ------------------------------------------------------------------ |
| `brainstorming`       | Explore requirements and approaches through collaborative dialogue |
| `document-review`     | Improve documents through structured self-review                   |
| `git-worktree`        | Manage Git worktrees for parallel development                      |

### Multi-Agent Orchestration

| Skill                  | Description                                            |
| ---------------------- | ------------------------------------------------------ |
| `orchestrating-swarms` | Comprehensive guide to multi-agent swarm orchestration |

### File Transfer

| Skill    | Description                                                        |
| -------- | ------------------------------------------------------------------ |
| `rclone` | Upload files to S3, Cloudflare R2, Backblaze B2, and cloud storage |

### Browser Automation

| Skill            | Description                                               |
| ---------------- | --------------------------------------------------------- |
| `agent-browser`  | CLI-based browser automation using Vercel's agent-browser |
| `browser-flows`  | Define testable user flows and routes for regression testing |

### Image Generation

| Skill             | Description                                        |
| ----------------- | -------------------------------------------------- |
| `gemini-imagegen` | Generate and edit images using Google's Gemini API |

**gemini-imagegen features:**

- Text-to-image generation
- Image editing and manipulation
- Multi-turn refinement
- Multiple reference image composition (up to 14 images)

**Requirements:**

- `GEMINI_API_KEY` environment variable
- Python packages: `google-genai`, `pillow`

## MCP Servers

| Server     | Description                                 |
| ---------- | ------------------------------------------- |
| `context7` | Framework documentation lookup via Context7 |

### Context7

**Tools provided:**

- `resolve-library-id` - Find library ID for a framework/package
- `get-library-docs` - Get documentation for a specific library

Supports 100+ frameworks including Rails, React, Next.js, Vue, Django, Laravel, and more.

MCP servers start automatically when the plugin is enabled.

## Browser Automation

This plugin uses **agent-browser CLI** for browser automation tasks. Install it globally:

```bash
npm install -g agent-browser
agent-browser install  # Downloads Chromium
```

The `agent-browser` skill provides comprehensive documentation on usage.

## Installation

### 1. Add the marketplace

```bash
claude /install-plugin https://github.com/grbellar/magical-gazelle
```

### 2. Install the plugin

```bash
claude /install-plugin magical-gazelle
```

## Known Issues

### MCP Servers Not Auto-Loading

**Issue:** The bundled Context7 MCP server may not load automatically when the plugin is installed.

**Workaround:** Manually add it to your project's `.claude/settings.json`:

```json
{
  "mcpServers": {
    "context7": {
      "type": "http",
      "url": "https://mcp.context7.com/mcp"
    }
  }
}
```

Or add it globally in `~/.claude/settings.json` for all projects.

## Version History

See [CHANGELOG.md](CHANGELOG.md) for detailed version history.

## License

MIT
