# Compound Engineering Philosophy

> This plugin is a personal fork of [compound-engineering-plugin](https://github.com/EveryInc/compound-engineering-plugin), created by Kieran O'Hare at [Every](https://every.to). The philosophy below was written by Kieran and captures the "why" behind everything in this repo. It explains the principles, workflow loop, and development ladder that inform every agent, command, and skill in this plugin.

## Core Principle

> Each unit of engineering work should make subsequent units easier — not harder.

Traditional development accumulates debt. Every feature adds complexity. Every shortcut creates future work. The codebase gets harder to understand, harder to modify, harder to trust.

Compound engineering inverts this. Every feature teaches the system. Every bug fix prevents future bugs. Every pattern you codify becomes a tool for future work.

The question isn't "how do I ship this feature?" It's "how do I ship this feature in a way that makes the next one easier?"

## The Main Loop

```
Plan -> Work -> Review -> Compound -> Repeat
```

80% of time is in Plan and Review. 20% is in Work and Compound. Most of the thinking happens before and after the code gets written.

### Plan

Turn an idea into a blueprint. Research the codebase, research externally, design the solution, validate the plan. The better the plan, the better the result.

Agents involved: `framework-docs-researcher`, `best-practices-researcher`, `repo-research-analyst`, `git-history-analyzer`

### Work

Execute the plan. Set up isolation (worktrees/branches), implement step by step, run validations, track progress. Trust the plan — if it was good, the execution will be good.

### Review

Multi-agent review catches issues before they ship. Multiple specialized reviewers examine code in parallel, findings are prioritized (P1/P2/P3), and the agent fixes issues based on feedback.

### Compound

Turn this cycle's learnings into next cycle's advantages. Capture solutions, make them findable, update the system. Every bug fix documented prevents the same bug from costing time twice.

## Key Beliefs

### Extract your taste into the system

You have preferences about what good code looks like. Right now, that taste lives in your head. Extract it. Write it down. Turn it into agents, prompts, CLAUDE.md instructions. When the system shares your taste, it produces code you like without you having to fix it.

### Spend more time planning, less time implementing

The AI can execute brilliantly. It writes code fast, it doesn't get tired, it doesn't make typos. What it can't do is know what to build. That's your job. Invest in planning. Get the plan right. Then let the AI implement.

### Trust the process, build safety nets

You can't scale AI assistance if you're reviewing every line. Build systems that catch problems — tests that fail when things break, review agents that flag issues. If you don't trust a step, add a system that makes that step trustworthy.

### The $100 Rule

When something fails that should have been prevented, spend the effort on the permanent fix — a test, a rule, an eval. Feel the sting once, fix it forever. Every failure becomes an investment in prevention.

### Make your environment agent-native

If you can do something as a developer, the agent should be able to do it too. Every capability you withhold from the agent is a capability that requires your manual intervention.

### Build for future models, not current limitations

Lean into agentic architectures over rigid workflows. Workflows break when the model changes. Agents adapt. Build for the model you'll have in six months.

### Parallelization is your friend

Run multiple agents in parallel. Have three features being developed simultaneously. Review, test, and document at the same time. The old constraint was human attention. The new constraint is computer resources.

## The AI Development Ladder

A progression from basic AI assistance to fully autonomous engineering:

| Stage | Description                      | Your Role                                               |
| ----- | -------------------------------- | ------------------------------------------------------- |
| 0     | Not using AI                     | Write everything manually                               |
| 1     | Chat-based AI                    | Copy-paste snippets, review every line                  |
| 2     | Agentic with line-by-line review | Approve every action                                    |
| 3     | Plan and review PR only          | Plan thoroughly, review at PR level                     |
| 4     | Idea to PR                       | Describe what you want, review the PR                   |
| 5     | Parallel in the cloud            | Multiple agents, multiple features, you steer the fleet |

**Stage 3 is where compound engineering really starts.** Plans get better because the agent learns your patterns. Reviews get faster because you trust the process.

## The Flywheel

Each cycle makes the next one better:

- Your first code review is slow — you're teaching the agent what to look for
- Your tenth code review catches things you used to miss
- Your hundredth code review runs in parallel with five others

The work compounds. The system gets smarter. You ship more value while typing less code.

## What to Compound

- **Bug fixes**: Especially ones that took time to diagnose
- **Patterns**: Solutions that could apply elsewhere
- **Mistakes**: Things to avoid next time
- **Preferences**: Choices you made about approach

Everywhere you do repeated work, ask: how do I do this once and have the system do it forever?

## Origin

This philosophy was developed while building Cora, Every's AI email assistant. The original compound engineering plugin was open-sourced at [github.com/EveryInc/compound-engineering-plugin](https://github.com/EveryInc/compound-engineering-plugin). This fork adapts the tooling for personal Python/TypeScript workflows while preserving the core philosophy.
