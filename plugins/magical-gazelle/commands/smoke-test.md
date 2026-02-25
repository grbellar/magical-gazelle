---
name: smoke-test
description: Full regression test of a web app - tests every route and user flow for errors
argument-hint: "[server URL, default from browser-flows.yml]"
---

# Smoke Test Command

<command_purpose>Test every route and user flow in a web app for regressions, console errors, and broken behavior.</command_purpose>

## CRITICAL: Use agent-browser CLI Only

**DO NOT use Chrome MCP tools (mcp__claude-in-chrome__*).**

This command uses the `agent-browser` CLI exclusively via Bash commands.

## Introduction

<role>QA engineer performing a full regression test of the application</role>

This command tests the entire app the way a human would: visit every page, walk through every critical flow, and catch anything that looks wrong — console errors, broken layouts, missing content, server errors, failed interactions.

## Prerequisites

<requirements>
- Local development server running
- agent-browser CLI installed
- `browser-flows.yml` manifest (recommended but not required)
</requirements>

## Setup

```bash
command -v agent-browser >/dev/null 2>&1 && echo "Ready" || (echo "Installing..." && npm install -g agent-browser && agent-browser install)
```

## Main Workflow

### 0. Verify agent-browser Installation

```bash
command -v agent-browser >/dev/null 2>&1 && echo "Ready" || (echo "Installing..." && npm install -g agent-browser && agent-browser install)
```

If installation fails, inform the user and stop.

### 1. Load Test Configuration

<test_config> $ARGUMENTS </test_config>

<load_config>

**Check for flow manifest:**

```bash
test -f browser-flows.yml && echo "Manifest found" || echo "No manifest"
```

**If manifest exists:**
- Parse `browser-flows.yml` for server URL, routes, flows, and auth config
- Use this as the test plan

**If no manifest exists:**
- Use the argument as the server URL, or ask user for it
- Discover routes by examining the codebase:
  ```bash
  # Next.js / React Router
  find . -path "*/app/*/page.*" -o -path "*/pages/*.tsx" -o -path "*/pages/*.jsx" 2>/dev/null

  # Django
  grep -r "path(" --include="*.py" -l 2>/dev/null

  # Rails
  cat config/routes.rb 2>/dev/null

  # Express
  grep -r "router\.\(get\|post\|put\|delete\)" --include="*.js" --include="*.ts" -l 2>/dev/null
  ```
- Present discovered routes and ask user to confirm or add to the list
- Offer to create a `browser-flows.yml` manifest for future runs

</load_config>

### 2. Ask Browser Mode

<ask_browser_mode>

Ask user if they want to watch the tests:

Use AskUserQuestion with:
- Question: "Do you want to watch the browser tests run?"
- Options:
  1. **Headed (watch)** - Opens visible browser window
  2. **Headless (faster)** - Runs in background

Store the choice and use `--headed` flag when user selects "Headed".

</ask_browser_mode>

### 3. Verify Server

<verify_server>

```bash
agent-browser open [server-url]
agent-browser snapshot -i
```

If server is not reachable:
```markdown
**Server not running at [URL]**

Start your development server, then run `/smoke-test` again.
```

</verify_server>

### 4. Authenticate (if configured)

<authenticate>

**If auth config exists in manifest:**

**login-form strategy:**
```bash
agent-browser open [server-url][auth.url]
agent-browser snapshot -i
# Find and fill credential fields using refs from snapshot
agent-browser fill @eN "[auth.credentials.email]"
agent-browser fill @eN "[auth.credentials.password]"
agent-browser click @eN  # Submit button
agent-browser wait 2000
agent-browser snapshot -i  # Verify login succeeded
```

**cookie strategy:**
```bash
agent-browser open [server-url]
agent-browser cookies set --name [cookie.name] --value [cookie.value] --domain localhost --path /
agent-browser reload
```

**header strategy:**
```bash
agent-browser --headers '[auth.headers as JSON]' open [server-url]
```

**If no auth config but pages require login:**
- Detect login redirect (page has login form when expected content)
- Ask user for credentials via AskUserQuestion
- Log in manually and continue

</authenticate>

### 5. Smoke Test All Routes

<smoke_test_routes>

For each route (public first, then protected after auth):

```bash
agent-browser open [server-url][route]
agent-browser wait [wait-ms or 2000]
agent-browser snapshot -i
```

**Check for each route:**

1. **Page loads** - snapshot returns content, not an error page
2. **No console errors** - check for JavaScript exceptions:
   ```bash
   agent-browser execute "JSON.stringify(window.__errors || [])"
   ```
3. **Expected content** - page has a title, main content area, navigation
4. **No broken state** - no "500", "Error", "undefined", "null" in visible text where it shouldn't be
5. **HTTP status** - page didn't redirect to an error page

**Record result for each route:** PASS, FAIL (with reason), or WARN (suspicious but not broken).

</smoke_test_routes>

### 6. Run User Flows

<run_flows>

For each flow defined in the manifest:

1. **Start the flow:**
   ```bash
   agent-browser open [server-url][first-step-path]
   agent-browser snapshot -i
   ```

2. **Execute each step** by interpreting the DSL:

   | Step DSL | agent-browser Translation |
   |----------|---------------------------|
   | `open /path` | `agent-browser open [server][/path]` |
   | `fill field "value"` | `snapshot -i` → find matching ref → `fill @eN "value"` |
   | `click "text"` | `snapshot -i` → find matching ref → `click @eN` |
   | `select field "option"` | `snapshot -i` → find matching ref → `select @eN "option"` |
   | `check "label"` | `snapshot -i` → find matching ref → `check @eN` |
   | `wait 2000` | `agent-browser wait 2000` |
   | `wait "text"` | `agent-browser wait "text"` |
   | `screenshot name` | `agent-browser screenshot browser-screenshots/name.png` |
   | `expect redirect /path` | `agent-browser get url` → verify contains path |
   | `expect text "content"` | `agent-browser snapshot` → verify contains text |
   | `expect element "sel"` | `agent-browser snapshot -i` → verify element exists |
   | `expect title "text"` | `agent-browser get title` → verify matches |
   | `expect no-console-errors` | `agent-browser execute "..."` → verify empty |

3. **Record flow result:** PASS if all steps succeed, FAIL at first failing step with details.

4. **On failure:**
   - Take screenshot: `agent-browser screenshot browser-screenshots/flow-[name]-failure.png`
   - Record which step failed and why
   - Continue to next flow (don't stop all testing)

</run_flows>

### 7. Check Server Logs

<check_server_logs>

After all browser tests complete, check for server-side errors:

```bash
# Check common log locations for errors during the test window
# Adapt to project's log setup

# Python/Django
tail -50 *.log 2>/dev/null | grep -i "error\|exception\|traceback" || true

# Node.js (if using pm2 or similar)
cat ~/.pm2/logs/*-error*.log 2>/dev/null | tail -20 || true

# Rails
tail -50 log/development.log 2>/dev/null | grep -i "error\|exception" || true

# Generic: check if server process wrote to stderr
# (depends on how the server was started)
```

If server logs aren't accessible, note it in the report and move on.

</check_server_logs>

### 8. Visual Spot Check

<visual_spot_check>

For key pages (homepage, dashboard, main content pages), take full-page screenshots and do a quick visual review:

```bash
mkdir -p browser-screenshots
grep -qxF 'browser-screenshots/' .gitignore 2>/dev/null || echo 'browser-screenshots/' >> .gitignore

agent-browser open [server-url]/
agent-browser screenshot --full browser-screenshots/smoke-homepage.png

agent-browser open [server-url]/dashboard
agent-browser screenshot --full browser-screenshots/smoke-dashboard.png
```

Review screenshots for obvious visual issues:
- Broken layouts (elements overlapping, missing sections)
- Unstyled content (CSS not loading)
- Missing images or icons
- Empty states that should have content

</visual_spot_check>

### 9. Report Results

<report_results>

Present a comprehensive summary:

```markdown
## Smoke Test Results

**App:** [server-url]
**Date:** [timestamp]
**Manifest:** [browser-flows.yml | auto-discovered]

### Route Tests: [passed]/[total]

| Route | Status | Notes |
|-------|--------|-------|
| `/` | PASS | |
| `/login` | PASS | |
| `/dashboard` | FAIL | Console error: TypeError |
| `/settings` | PASS | |
| `/profile` | WARN | Slow load (>3s) |

### Flow Tests: [passed]/[total]

| Flow | Status | Failed Step | Notes |
|------|--------|-------------|-------|
| signup | PASS | - | |
| create-post | FAIL | click "Publish" | Button not found |
| settings-update | PASS | - | |

### Console Errors: [count]

| Route/Flow | Error |
|------------|-------|
| `/dashboard` | TypeError: Cannot read property 'map' of undefined |

### Server Errors: [count]

| Error | Location |
|-------|----------|
| 500 Internal Server Error | /api/posts |

### Visual Issues: [count]

| Page | Issue |
|------|-------|
| `/dashboard` | Sidebar overlaps main content at viewport width |

### Screenshots

Saved to `browser-screenshots/`:
- smoke-homepage.png
- smoke-dashboard.png
- flow-create-post-failure.png

### Result: [PASS | FAIL | PARTIAL]
- **PASS**: All routes load, all flows complete, no console/server errors
- **PARTIAL**: Some routes/flows pass, non-critical issues found
- **FAIL**: Critical routes broken, flows failing, errors present
```

</report_results>

### 10. Offer Next Steps

<next_steps>

After presenting results:

```markdown
**What next?**
1. **Fix failures** - I'll help debug and fix the issues found
2. **Create/update manifest** - Save this test config for future runs
3. **Run UI audit** - Deeper visual analysis with `/ui-audit`
4. **Done** - Tests are complete
```

If no manifest exists and tests passed, offer to generate one:

```markdown
**No browser-flows.yml found.** Want me to create one based on the routes and flows I discovered? This makes future `/smoke-test` runs faster and consistent.
```

</next_steps>

## Quick Usage

```bash
# Test with manifest (recommended)
/smoke-test

# Test specific server
/smoke-test http://localhost:8000

# First time (no manifest)
/smoke-test http://localhost:3000
# → discovers routes, tests them, offers to create manifest
```

## agent-browser CLI Reference

**ALWAYS use these Bash commands. NEVER use mcp__claude-in-chrome__* tools.**

```bash
# Navigation
agent-browser open <url>
agent-browser back
agent-browser close

# Snapshots
agent-browser snapshot -i
agent-browser snapshot -i --json

# Interactions
agent-browser click @e1
agent-browser fill @e1 "text"
agent-browser select @e1 "option"
agent-browser check @e1
agent-browser press Enter

# Screenshots
agent-browser screenshot out.png
agent-browser screenshot --full out.png

# Info
agent-browser get url
agent-browser get title
agent-browser get text @e1

# Execute JS
agent-browser execute "document.title"

# Wait
agent-browser wait @e1
agent-browser wait 2000
agent-browser wait "text"

# Headed mode
agent-browser --headed open <url>
```
