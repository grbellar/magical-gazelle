---
name: browser-flows
description: Define testable user flows for browser-based regression testing. Use when setting up smoke tests, defining E2E flows, or configuring browser-flows.yml for a project.
---

# Browser Flows

Define all testable user flows and routes for a project in a single manifest file. Used by `/smoke-test` for full regression testing and `/mg:work` for post-change verification.

## Quick Start

Create `.claude/browser-flows.yml` in the project root:

```yaml
server: http://localhost:3000

routes:
  - /
  - /login
  - /dashboard
  - /settings

flows:
  - name: signup
    steps:
      - open /signup
      - fill email "test@example.com"
      - fill password "testpassword"
      - click "Sign up"
      - expect redirect /dashboard
      - expect text "Welcome"
```

Then run `/smoke-test` to test everything.

## Manifest Spec

The manifest file lives at `.claude/browser-flows.yml` in the project root.

### Top-Level Fields

```yaml
# Required: dev server URL
server: http://localhost:3000

# Optional: auth config for pages that require login
auth:
  strategy: login-form | cookie | header | none
  url: /login
  credentials:
    email: test@example.com
    password: testpassword
  # For cookie strategy:
  cookie:
    name: sessionid
    value: abc123
  # For header strategy:
  headers:
    Authorization: "Bearer token123"

# Optional: milliseconds to wait after each page load (default: 2000)
wait: 2000

# Required: routes to smoke test (just check they load without errors)
routes:
  - /
  - /login
  - /signup
  - /dashboard
  - /settings
  - /profile

# Optional: user flows to test end-to-end
flows:
  - name: flow-name
    steps:
      - ...

# Optional: routes that need auth (tested after login)
protected_routes:
  - /dashboard
  - /settings
  - /profile
  - /billing

# Optional: routes that should be publicly accessible (tested before login)
public_routes:
  - /
  - /login
  - /signup
  - /about
  - /pricing
```

### Flow Steps

Each flow is a named sequence of actions. Steps use a simple DSL:

```yaml
flows:
  - name: user-signup
    steps:
      # Navigation
      - open /signup

      # Form interactions (use label text, placeholder, or semantic locator)
      - fill email "test@example.com"
      - fill password "securepassword"
      - fill "Company name" "Acme Inc"
      - select "Country" "United States"
      - check "I agree to terms"

      # Actions
      - click "Sign up"
      - click "Get started"

      # Waits
      - wait 2000
      - wait "Welcome"           # Wait for text to appear

      # Assertions
      - expect redirect /dashboard
      - expect text "Welcome"
      - expect element "nav"
      - expect no-console-errors
      - expect title "Dashboard"

      # Screenshots (saved to browser-screenshots/)
      - screenshot signup-result

  - name: create-post
    auth: true                    # Login first using auth config
    steps:
      - open /posts/new
      - fill "Title" "Test Post"
      - fill "Body" "This is a test post."
      - click "Publish"
      - expect text "Post published"
      - expect redirect /posts
```

### Step Reference

| Step | Description |
|------|-------------|
| `open <path>` | Navigate to server + path |
| `fill <field> "value"` | Find input by label/placeholder/name and fill it |
| `click "text"` | Click element by visible text or label |
| `select <field> "option"` | Select dropdown option |
| `check "label"` | Check a checkbox |
| `uncheck "label"` | Uncheck a checkbox |
| `wait <ms>` | Wait milliseconds |
| `wait "text"` | Wait for text to appear on page |
| `screenshot <name>` | Save screenshot to browser-screenshots/ |
| `expect redirect <path>` | Verify URL changed to path |
| `expect text "content"` | Verify text appears on page |
| `expect element "selector"` | Verify element exists |
| `expect title "text"` | Verify page title |
| `expect no-console-errors` | Verify no JS errors in console |

## How the Agent Interprets Flows

The manifest is **not a test runner config** — it's a human-readable description of flows that the AI agent interprets and executes using `agent-browser` CLI commands.

When the agent reads `fill email "test@example.com"`, it:

1. Runs `agent-browser snapshot -i` to find the email input ref
2. Runs `agent-browser fill @eN "test@example.com"` with the matching ref
3. Re-snapshots if needed

When the agent reads `expect text "Welcome"`, it:

1. Runs `agent-browser snapshot` to get page content
2. Checks if "Welcome" appears in the snapshot
3. Reports pass/fail

The DSL is intentionally simple and forgiving. The agent uses semantic matching — `fill email` matches an input with label "Email", placeholder "email", name "email", etc.

## Creating a Manifest for an Existing Project

To create a manifest for a project that doesn't have one:

1. **Discover routes**: Check the project's router/URL config, view files, or route definitions
2. **Identify key flows**: What would a human test after every deploy? Signup, login, core CRUD operations, checkout, etc.
3. **Define auth**: How does the app authenticate? Login form, API token, session cookie?
4. **List all routes**: Include every page, even simple ones — smoke testing catches regressions everywhere
5. **Write flows for critical paths**: Focus on flows where breakage would be most impactful

```bash
# Helpful discovery commands
grep -r "path:" src/app/ --include="*.ts"          # Next.js routes
grep -r "urlpatterns" --include="*.py"              # Django routes
grep -r "get\|post\|put\|delete" routes/ --include="*.rb"  # Rails routes
```

## Integration Points

### With `/smoke-test`

`/smoke-test` reads this manifest and tests everything:
- All routes load without errors
- All flows complete successfully
- Console errors are captured
- Server logs are checked

### With `/mg:work`

During Phase 3 (Quality Check), `/mg:work` reads this manifest to:
- Test routes affected by changed files
- Run flows that touch modified components
- Verify no regressions in related pages

### With `/test-browser`

`/test-browser` can use the manifest's auth config and route mappings instead of guessing which routes map to which files.
