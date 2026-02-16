# Magical Gazelle

A personal fork of the [Compound Engineering Plugin](https://github.com/EveryInc/compound-engineering-plugin) — AI-powered development tools that make each unit of engineering work easier than the last.

## Install

```bash
/plugin marketplace add https://github.com/grbellar/magical-gazelle
/plugin install magical-gazelle
```

## Workflow

```
Plan → Work → Review → Compound → Repeat
```

| Command | Purpose |
|---------|---------|
| `/workflows:plan` | Turn feature ideas into detailed implementation plans |
| `/workflows:work` | Execute plans with worktrees and task tracking |
| `/workflows:review` | Multi-agent code review before merging |
| `/workflows:compound` | Document learnings to make future work easier |

Each cycle compounds: plans inform future plans, reviews catch more issues, patterns get documented.

## Philosophy

**Each unit of engineering work should make subsequent units easier—not harder.**

Traditional development accumulates technical debt. Every feature adds complexity. The codebase becomes harder to work with over time.

Compound engineering inverts this. 80% is in planning and review, 20% is in execution:
- Plan thoroughly before writing code
- Review to catch issues and capture learnings
- Codify knowledge so it's reusable
- Keep quality high so future changes are easy

## Learn More

- [Full component reference](plugins/magical-gazelle/README.md) - all agents, commands, skills
- [Upstream repo](https://github.com/EveryInc/compound-engineering-plugin) - original project by Every
