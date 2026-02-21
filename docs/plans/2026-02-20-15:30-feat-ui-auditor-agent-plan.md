---
title: "feat: Add UI Auditor Agent and Skill"
type: feat
status: completed
date: 2026-02-20
---

# feat: Add UI Auditor Agent and Skill

## Overview

Add a UI auditor that browses a running app, takes screenshots, analyzes against an opinionated rubric of UI best practices, and produces a structured report saved to `docs/audits/`. After reporting, it hands off to the design-iterator agent for iterative fixes with user approval.

## Problem Statement

Existing design tooling requires either a Figma reference (design-implementation-reviewer) or assumes active building (design-iterator). Nothing does a standalone audit of a running app against general UI best practices. Developers need a way to point an agent at a URL and get back a structured report of visual inconsistencies — the things a human designer would spot in seconds.

## Proposed Solution

Three new files in the plugin:

| Component | Path | Purpose |
|-----------|------|---------|
| Agent | `agents/design/ui-auditor.md` | Audit workflow orchestration |
| Skill | `skills/ui-audit/SKILL.md` | Slash command entry point |
| Reference | `skills/ui-audit/references/ui-audit-rubric.md` | Detailed rubric of what to check |

## Technical Approach

### Design Decisions (from SpecFlow analysis)

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Page readiness | Wait for network idle + 2s buffer, user can pass wait time | Avoids screenshotting loading spinners |
| Desktop viewport | 1440x900 default | Common dev resolution, catches most layout issues |
| Responsive viewports | Mobile: 375x812, Tablet: 768x1024 | Standard device sizes |
| Audit directory name | Sanitized URL path, user can override with `--name` | Deterministic, human-readable |
| Screenshots | Full-page + viewport of key sections | Full-page catches everything, section shots for focused analysis |
| Multi-page | Single URL per invocation (v1) | Keep scope manageable; multi-page is a future enhancement |
| Auth-gated pages | Document as limitation; user navigates to correct state first | Auth flows are complex and app-specific |
| Screenshot format | PNG via agent-browser | Lossless, standard |
| Report-to-iterator contract | Report markdown structure with `## Issues` section containing structured issue blocks | Design-iterator parses the report to find issues to fix |
| Gitignore | Add `docs/audits/` to .gitignore guidance in skill | Screenshots can be large; user decides whether to track |

### Phase 1: Reference File — `skills/ui-audit/references/ui-audit-rubric.md`

The rubric is the core value. Covers 8 categories, each with specific things to check:

**Spacing & Layout**
- Consistent spacing system (check for 4px/8px grid adherence)
- Vertical rhythm between sections
- Padding/margin uniformity across similar elements
- Adequate breathing room (nothing crammed)
- Container max-widths appropriate for content

**Typography**
- Heading hierarchy (h1 > h2 > h3 — sizes should decrease logically)
- Consistent type scale (not random font sizes)
- Line heights appropriate for font size (1.4-1.6 for body, 1.1-1.3 for headings)
- Minimum readable font size (14px body minimum)
- Consistent font families (no mixing without purpose)
- Letter spacing appropriate for uppercase/display text

**Alignment**
- Elements share common edges (left-aligned content should align)
- Consistent centering (centered elements actually centered)
- No subpixel drift between similar elements
- Form labels and inputs aligned properly
- Grid items aligned consistently

**Visual Hierarchy**
- Clear primary action on each screen (one obvious CTA)
- Secondary actions visually subordinate
- Logical reading flow (F-pattern or Z-pattern)
- Important content has more visual weight
- Decorative elements don't compete with content

**Buttons & Interactive Elements**
- Consistent button sizing across similar contexts
- Touch targets minimum 44x44px
- Primary/secondary/tertiary button styling distinction
- Consistent placement (e.g., primary action right in dialogs)
- Hover/focus states visible and consistent

**Color & Contrast**
- WCAG AA contrast: 4.5:1 for normal text, 3:1 for large text
- Consistent color palette (no one-off colors)
- Color used meaningfully (not just decoratively)
- Sufficient contrast for interactive element borders
- Links distinguishable from body text

**Overflow & Clipping**
- No unintended horizontal scrolling
- Text fits within containers (no overflow hidden cutting text)
- Images maintain aspect ratio
- Long words/URLs don't break layouts
- Scrollable areas have visible scroll indicators

**Consistency**
- Similar elements styled identically (all cards look the same)
- Consistent border radius across components
- Consistent shadow treatment
- Icon sizing uniform
- No "one-off" styling variants without clear purpose

Each category includes severity guidance:
- **Critical**: Broken layout, unreadable text, inaccessible contrast
- **Warning**: Inconsistent spacing, misaligned elements, hierarchy confusion
- **Info**: Minor polish opportunities, subjective improvements

### Phase 2: Skill — `skills/ui-audit/SKILL.md`

Entry point for `/ui-audit`. Frontmatter:

```yaml
---
name: ui-audit
description: Audit a running app's UI for visual inconsistencies and best practice violations. Use when reviewing your own app for spacing issues, typography problems, alignment drift, or general UI oddities. Triggers on "audit UI", "check UI", "review design", "visual audit".
---
```

Skill body defines:
- How to invoke: `/ui-audit <url>` with optional flags
- Flags: `--responsive` (add mobile + tablet), `--wait <ms>` (custom wait time), `--name <name>` (custom audit name)
- Links to rubric: `[ui-audit-rubric.md](./references/ui-audit-rubric.md)`
- Output format and directory structure
- Handoff instructions for design-iterator

### Phase 3: Agent — `agents/design/ui-auditor.md`

Follows established agent pattern (frontmatter → examples → role → workflow → guidelines).

```yaml
---
name: ui-auditor
description: "Audits running web apps for UI inconsistencies against best practice rubric. Use when reviewing your own app for spacing, typography, alignment, or visual hierarchy issues."
model: inherit
---
```

**Agent workflow (6 steps):**

1. **Setup** — Open URL with agent-browser. Wait for page readiness (network idle + configurable delay). Set viewport to 1440x900 (or specified size).

2. **Capture** — Take full-page screenshot. Take accessibility snapshot (`agent-browser snapshot -i --json`). If responsive audit requested, resize viewport and repeat for mobile (375x812) and tablet (768x1024). Create output directory: `docs/audits/YYYY-MM-DD-HH:MM-<name>/screenshots/`.

3. **Analyze** — Evaluate screenshots against each rubric category. Cross-reference visual findings with DOM structure from accessibility snapshot. Optionally read source files if they can be identified from component structure.

4. **Report** — Write structured report to `docs/audits/YYYY-MM-DD-HH:MM-<name>/audit-report.md`. Report structure:

```markdown
---
url: <audited-url>
date: YYYY-MM-DD
viewport: 1440x900
responsive: true/false
---

# UI Audit: <page-name>

## Summary

[Brief overview: X issues found — N critical, N warning, N info]

## Issues

### [Category]: [Issue Title]

- **Severity**: Critical | Warning | Info
- **Element**: [Description or selector]
- **Problem**: [What's wrong]
- **Expected**: [What it should look like per rubric]
- **Screenshot**: [Reference to screenshot file]
- **Suggestion**: [How to fix]

---

[Repeat for each issue]

## Passing Checks

[Brief list of rubric categories that look good]
```

5. **Present** — Print summary in chat (issue count by severity, top issues). Reference saved report path.

6. **Handoff** — Offer to hand off to design-iterator. If accepted, design-iterator receives the report path and works through issues by severity (critical first), one at a time, taking screenshots after each fix to verify. User approves/rejects each fix. User can exit the loop at any time.

### Phase 4: Version Bump and Docs

Adding 1 agent + 1 skill = MINOR version bump: `3.1.2` → `3.2.0`.

Updated counts:
- Agents: 25 → 26 (design: 3 → 4)
- Skills: 16 → 17

Files to update:
- `plugins/magical-gazelle/.claude-plugin/plugin.json` — version + description counts
- `.claude-plugin/marketplace.json` — version + description counts
- `plugins/magical-gazelle/README.md` — component counts + add to agent/skill tables
- `plugins/magical-gazelle/CHANGELOG.md` — document new components

## Acceptance Criteria

- [x] `/ui-audit http://localhost:3000` opens the URL, takes screenshots, and produces a report
- [x] Report is saved to `docs/audits/` with screenshots in a subdirectory
- [x] Report follows the structured format with severity levels per issue
- [x] Each rubric category is evaluated and appears in the report
- [x] Responsive audit works when `--responsive` flag is passed
- [x] User is offered handoff to design-iterator after report
- [x] Design-iterator can read the report and work through issues
- [x] Version bumped to 3.2.0 in plugin.json and marketplace.json
- [x] README.md tables and counts updated
- [x] CHANGELOG.md documents the new components

## Testing

No automated tests required (plugin content is not tested per existing test infrastructure). Manual verification:

- [x] Skill frontmatter validates (`name` and `description` present)
- [x] Agent frontmatter validates (`name`, `description`, `model` present)
- [x] Reference file is properly linked from SKILL.md
- [x] JSON files parse cleanly (`cat plugin.json | jq .`)
- [x] Component counts match actual file counts

## Implementation Order

1. `skills/ui-audit/references/ui-audit-rubric.md` — the rubric (core value)
2. `skills/ui-audit/SKILL.md` — the skill entry point
3. `agents/design/ui-auditor.md` — the agent
4. Version bump + docs updates (plugin.json, marketplace.json, README.md, CHANGELOG.md)

## References

- Brainstorm: `docs/brainstorms/2026-02-20-15:00-ui-auditor-brainstorm.md`
- Existing agent pattern: `plugins/magical-gazelle/agents/design/design-iterator.md`
- Existing agent pattern: `plugins/magical-gazelle/agents/design/design-implementation-reviewer.md`
- Browser skill: `plugins/magical-gazelle/skills/agent-browser/SKILL.md`
- Plugin metadata: `plugins/magical-gazelle/.claude-plugin/plugin.json`
- Marketplace: `.claude-plugin/marketplace.json`

## Future Enhancements (Not v1)

- Multi-page audit (`--pages /,/dashboard,/settings`)
- Auth support (cookie injection or login flow)
- Diff mode against previous audit
- Dark mode / theme variant auditing
- Custom rubric overrides per project
- Annotated screenshots (bounding boxes on issues)
