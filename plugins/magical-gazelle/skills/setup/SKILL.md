---
name: setup
description: Configure which review agents run for your project. Auto-detects stack and writes magical-gazelle.local.md.
disable-model-invocation: true
---

# Magical Gazelle Setup

Interactive setup for `magical-gazelle.local.md` — configures which agents run during `/mg:review` and `/mg:work`.

## Step 1: Check Existing Config

Read `magical-gazelle.local.md` in the project root. If it exists, display current settings summary and use AskUserQuestion:

```
question: "Settings file already exists. What would you like to do?"
header: "Config"
options:
  - label: "Reconfigure"
    description: "Run the interactive setup again from scratch"
  - label: "View current"
    description: "Show the file contents, then stop"
  - label: "Cancel"
    description: "Keep current settings"
```

If "View current": read and display the file, then stop.
If "Cancel": stop.

## Step 2: Detect and Ask

Auto-detect the project stack:

```bash
test -f tsconfig.json && echo "typescript" || \
test -f package.json && echo "javascript" || \
test -f pyproject.toml && echo "python" || \
test -f requirements.txt && echo "python" || \
echo "general"
```

Use AskUserQuestion:

```
question: "Detected {type} project. How would you like to configure?"
header: "Setup"
options:
  - label: "Auto-configure (Recommended)"
    description: "Use smart defaults for {type}. Done in one click."
  - label: "Customize"
    description: "Choose stack, focus areas, and review depth."
```

### If Auto-configure → Skip to Step 4 with defaults:

- **Python:** `[python-reviewer, security-sentinel, performance-oracle]`
- **TypeScript:** `[typescript-reviewer, security-sentinel, performance-oracle]`
- **General:** `[security-sentinel, performance-oracle]`

### If Customize → Step 3

## Step 3: Customize (2 questions)

**a. Stack** — confirm or override:

```
question: "Which stack should we optimize for?"
header: "Stack"
options:
  - label: "{detected_type} (Recommended)"
    description: "Auto-detected from project files"
  - label: "Python"
    description: "Python — adds Pythonic pattern, simplicity, and testing reviewer"
  - label: "TypeScript"
    description: "TypeScript — adds type safety, async correctness, and testing reviewer"
```

Only show options that differ from the detected type.

**b. Focus areas** — multiSelect:

```
question: "Which review areas matter most?"
header: "Focus"
multiSelect: true
options:
  - label: "Security"
    description: "Vulnerability scanning, auth, input validation (security-sentinel)"
  - label: "Performance"
    description: "N+1 queries, memory leaks, complexity (performance-oracle)"
  - label: "Data integrity"
    description: "Database migrations, data safety, transaction boundaries (data-reviewer)"
```

## Step 4: Build Agent List and Write File

**Stack-specific agents:**
- Python → `python-reviewer`
- TypeScript → `typescript-reviewer`
- General → (none)

**Focus area agents:**
- Security → `security-sentinel`
- Performance → `performance-oracle`
- Data integrity → `data-reviewer`

Write `magical-gazelle.local.md`:

```markdown
---
review_agents: [{computed agent list}]
---

# Review Context

Add project-specific review instructions here.
These notes are passed to all review agents during /mg:review and /mg:work.

Examples:
- "We use Turbo Frames heavily — check for frame-busting issues"
- "Our API is public — extra scrutiny on input validation"
- "Performance-critical: we serve 10k req/s on this endpoint"
```

## Step 5: Confirm

```
Saved to magical-gazelle.local.md

Stack:        {type}
Agents:       {count} configured
              {agent list, one per line}

Tip: Edit the "Review Context" section to add project-specific instructions.
     Re-run this setup anytime to reconfigure.
```
