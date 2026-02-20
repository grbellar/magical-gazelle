---
name: ui-audit
description: Audit a running app's UI for visual inconsistencies and best practice violations. Use when reviewing your own app for spacing issues, typography problems, alignment drift, or general UI oddities. Triggers on "audit UI", "check UI", "review design", "visual audit".
---

# UI Audit

Audit a running web app against an opinionated rubric of UI best practices. Takes screenshots, analyzes the page visually and structurally, saves a report with findings, and offers to hand off to the design-iterator for fixes.

## Quick Start

```
/ui-audit http://localhost:3000
/ui-audit http://localhost:3000 --responsive
/ui-audit http://localhost:3000 --name dashboard --wait 5000
```

## Arguments

- **url** (required): The URL to audit. Typically a localhost dev server.
- **--responsive**: Also audit at mobile (375x812) and tablet (768x1024) viewports.
- **--wait \<ms\>**: Custom wait time in milliseconds after page load before screenshotting. Default: 2000ms after network idle.
- **--name \<name\>**: Custom name for the audit directory. Default: derived from URL path.

## Rubric

The audit evaluates 8 categories of UI quality. For the full checklist with severity levels, see [ui-audit-rubric.md](./references/ui-audit-rubric.md).

| Category | What it checks |
|----------|---------------|
| Spacing & Layout | 8pt grid, vertical rhythm, padding consistency, breathing room |
| Typography | Type scale, heading hierarchy, line heights, readable sizes |
| Alignment | Common edges, centering, pixel drift, grid alignment |
| Visual Hierarchy | Primary/secondary actions, reading flow, visual weight |
| Buttons & CTAs | Sizing consistency, touch targets (44px min), placement |
| Color & Contrast | WCAG AA (4.5:1 / 3:1), palette consistency, focus indicators |
| Overflow & Clipping | Horizontal scroll, text overflow, image aspect ratios |
| Consistency | Similar elements styled the same, border radius, shadows, icons |

## Workflow

1. **Open URL** with agent-browser. Wait for page readiness.
2. **Capture screenshots** at desktop (1440x900). If `--responsive`, also at mobile and tablet.
3. **Take accessibility snapshot** for DOM structure analysis.
4. **Read source files** when identifiable from component structure.
5. **Analyze** screenshots and DOM against the [rubric](./references/ui-audit-rubric.md).
6. **Save report** to `docs/audits/YYYY-MM-DD-HH:MM-<name>/audit-report.md` with screenshots in a `screenshots/` subdirectory.
7. **Present findings** summarized in chat (issue count by severity, top issues).
8. **Offer handoff** to design-iterator agent for iterative fixes.

## Output Structure

```
docs/audits/YYYY-MM-DD-HH:MM-<name>/
├── audit-report.md
└── screenshots/
    ├── desktop-full.png
    ├── mobile-full.png      (if --responsive)
    └── tablet-full.png      (if --responsive)
```

## Report Format

The audit report uses this structure:

```markdown
---
url: <audited-url>
date: YYYY-MM-DD
viewport: 1440x900
responsive: true/false
---

# UI Audit: <page-name>

## Summary

[Issue count by severity, overall impression]

## Issues

### [Category]: [Issue Title]

- **Severity**: Critical | Warning | Info
- **Element**: [Description or selector]
- **Problem**: [What's wrong]
- **Expected**: [What it should look like per rubric]
- **Screenshot**: [Reference to screenshot]
- **Suggestion**: [How to fix]

## Passing Checks

[Categories that look good]
```

## Design-Iterator Handoff

After presenting the report, offer to hand off to the design-iterator agent. The handoff works as follows:

1. Design-iterator receives the audit report path.
2. It works through issues by severity (critical first), one at a time.
3. After each fix, it takes a new screenshot to verify the change.
4. The user approves or rejects each fix.
5. The user can exit the loop at any time.

To trigger: "Fix the issues found in the audit" or accept the handoff offer after the report.

## Limitations (v1)

- Single URL per invocation. Multi-page audits require running the command multiple times.
- No authentication support. Navigate to the correct page state before auditing.
- Screenshots are not annotated with bounding boxes — issues are described in text.
- No diff mode against previous audits.
