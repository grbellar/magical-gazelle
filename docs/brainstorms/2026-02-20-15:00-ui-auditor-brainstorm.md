---
date: 2026-02-20
topic: ui-auditor
---

# UI Auditor Agent + Skill

## What We're Building

A UI auditor that browses your running app, takes screenshots, analyzes against an opinionated rubric of UI best practices, and produces a structured report of issues saved to `docs/audits/`. Screenshots are saved alongside the report for review. After reporting, it hands off to the design-iterator agent for iterative fixes with user approval.

## Why This Approach

Existing design tooling (design-implementation-reviewer, design-iterator) either requires a Figma reference or assumes you're actively building. Nothing does a standalone audit of a running app against general UI best practices. This fills that gap — point it at a URL, get a report of what looks off.

## Components

| Component | Path | Purpose |
|-----------|------|---------|
| Agent | `agents/design/ui-auditor.md` | Orchestrates audit workflow, analyzes screenshots + DOM |
| Skill | `skills/ui-audit/SKILL.md` | Slash command entry point (`/ui-audit`) |
| Reference | `skills/ui-audit/references/ui-audit-rubric.md` | Detailed rubric of what to check |

## Workflow

1. User invokes `/ui-audit http://localhost:3000` (or `/ui-audit` with URL from context)
2. Agent opens URL with `agent-browser`, takes desktop screenshot
3. Takes accessibility snapshot for DOM structure
4. Optionally checks mobile (375px) and tablet (768px) if user requests responsive audit
5. Reads relevant source files (CSS, components)
6. Analyzes against rubric — both visual (screenshot) and structural (DOM + code)
7. Saves screenshots to `docs/audits/YYYY-MM-DD-HH:MM-<name>/screenshots/`
8. Writes structured report to `docs/audits/YYYY-MM-DD-HH:MM-<name>/audit-report.md`
9. Presents findings with severity levels
10. Offers to hand off to design-iterator agent for iterative fixes with user approval

## Audit Report Structure

```
docs/audits/YYYY-MM-DD-HH:MM-<name>/
├── audit-report.md        # Structured findings
└── screenshots/
    ├── desktop-full.png   # Full page
    ├── desktop-viewport-1.png  # Key sections
    ├── mobile-full.png    # (if responsive audit)
    └── tablet-full.png    # (if responsive audit)
```

## Rubric Categories

Opinionated checklist drawn from 8pt grid, modular type scales, WCAG 2.1 AA, Gestalt principles, Nielsen's heuristics, Fitts's Law:

- **Spacing**: 8pt grid consistency, vertical rhythm, padding/margin uniformity, breathing room
- **Typography**: Type scale consistency, line heights, heading hierarchy, min readable size
- **Alignment**: Common edges, consistent centering, no pixel drift
- **Visual hierarchy**: Clear primary/secondary/tertiary actions, logical reading flow
- **Buttons & CTAs**: Consistent sizing, placement conventions, 44px min touch targets
- **Color & contrast**: WCAG AA (4.5:1 normal, 3:1 large), consistent palette
- **Overflow**: No horizontal scroll, text fits containers, images maintain aspect ratio
- **Consistency**: Similar elements styled the same, no one-off variants

## Key Decisions

- **Report to file** — saved to `docs/audits/` with screenshots for review
- **Design-iterator integration** — after report, hand off to design-iterator for fixes
- **Desktop by default** — responsive audit is opt-in
- **Source-aware** — reads code alongside screenshots since it's your own app
- **Rubric as reference file** — easy to update principles without touching agent logic

## Next Steps

-> `/mg:plan` for implementation details
