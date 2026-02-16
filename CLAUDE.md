# Magical Gazelle

Personal fork of [compound-engineering-plugin](https://github.com/EveryInc/compound-engineering-plugin). AI-powered development tools for Python/TypeScript workflows.

## Repository Structure

```
magical-gazelle/
├── .claude-plugin/
│   └── marketplace.json          # Marketplace catalog
├── plugins/
│   └── magical-gazelle/          # The plugin
│       ├── .claude-plugin/
│       │   └── plugin.json       # Plugin metadata
│       ├── agents/               # 24 specialized AI agents
│       ├── commands/             # 22 slash commands
│       ├── skills/               # 16 skills
│       ├── README.md
│       └── CHANGELOG.md
├── src/                          # CLI tool (Bun/TypeScript)
│   ├── commands/                 # convert, install, list, sync
│   ├── converters/               # Claude → opencode, codex, pi
│   ├── targets/                  # Output writers per target
│   ├── sync/                     # Config sync per target
│   ├── types/                    # Type definitions
│   └── index.ts                  # CLI entry point
└── tests/                        # Bun test suite
```

## CLI Tool

Converts Claude Code plugins into other agent platform formats.

```bash
# Convert plugin to another format
bun run src/index.ts convert ./plugins/magical-gazelle --to codex

# Install plugin (fetches from GitHub if not local)
bun run src/index.ts install magical-gazelle --to opencode

# Sync local Claude config to another platform
bun run src/index.ts sync codex

# List available plugins
bun run src/index.ts list
```

Supported targets: `opencode`, `codex`, `pi`.

## Updating the Plugin

When agents, commands, or skills are added/removed:

### 1. Count components

```bash
find plugins/magical-gazelle/agents -name '*.md' | wc -l
find plugins/magical-gazelle/commands -name '*.md' | wc -l
ls -d plugins/magical-gazelle/skills/*/ | wc -l
```

### 2. Update counts in all locations

- [ ] `plugins/magical-gazelle/.claude-plugin/plugin.json` → `description`
- [ ] `.claude-plugin/marketplace.json` → plugin `description`
- [ ] `plugins/magical-gazelle/README.md` → intro paragraph

### 3. Bump version

- [ ] `plugins/magical-gazelle/.claude-plugin/plugin.json` → `version`
- [ ] `.claude-plugin/marketplace.json` → plugin `version`

### 4. Update docs

- [ ] `plugins/magical-gazelle/README.md` → component tables
- [ ] `plugins/magical-gazelle/CHANGELOG.md` → document changes

### 5. Validate JSON

```bash
cat .claude-plugin/marketplace.json | jq .
cat plugins/magical-gazelle/.claude-plugin/plugin.json | jq .
```

## Running Tests

```bash
bun test
```

## Resources

- [Claude Code Plugin Documentation](https://docs.claude.com/en/docs/claude-code/plugins)
- [Plugin Marketplace Documentation](https://docs.claude.com/en/docs/claude-code/plugin-marketplaces)
- [Plugin Reference](https://docs.claude.com/en/docs/claude-code/plugins-reference)
