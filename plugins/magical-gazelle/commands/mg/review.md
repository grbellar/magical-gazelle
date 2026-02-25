---
name: mg:review
description: Perform exhaustive code reviews using multi-agent analysis, ultra-thinking, and worktrees
argument-hint: "[PR number, GitHub URL, branch name, or latest]"
---

# Review Command

<command_purpose> Perform exhaustive code reviews using multi-agent analysis, ultra-thinking, and Git worktrees for deep local inspection. </command_purpose>

## Introduction

<role>Senior Code Review Architect with expertise in security, performance, architecture, and quality assurance</role>

## Prerequisites

<requirements>
- Git repository with GitHub CLI (`gh`) installed and authenticated
- Clean main/master branch
- Proper permissions to create worktrees and access the repository
- For document reviews: Path to a markdown file or document
</requirements>

## Main Tasks

### 1. Determine Review Target & Setup (ALWAYS FIRST)

<review_target> #$ARGUMENTS </review_target>

<thinking>
First, I need to determine the review target type and set up the code for analysis.
</thinking>

#### Immediate Actions:

<task_list>

- [ ] Determine review type: PR number (numeric), GitHub URL, file path (.md), or empty (current branch)
- [ ] Check current git branch
- [ ] If ALREADY on the target branch (PR branch, requested branch name, or the branch already checked out for review) → proceed with analysis on current branch
- [ ] If DIFFERENT branch than the review target → offer to use worktree: `git worktree add ../$(basename $(pwd))-review-XXXX -b review/pr-XXXX main` then `cd` into it and `gh pr checkout XXXX`
- [ ] Fetch PR metadata using `gh pr view --json` for title, body, files, linked issues
- [ ] Set up language-specific analysis tools
- [ ] Prepare security scanning environment
- [ ] Make sure we are on the branch we are reviewing. Use gh pr checkout to switch to the branch or manually checkout the branch.

Ensure that the code is ready for analysis (either in worktree or on current branch). ONLY then proceed to the next step.

</task_list>

#### Protected Artifacts

<protected_artifacts>
The following paths are magical-gazelle pipeline artifacts and must never be flagged for deletion, removal, or gitignore by any review agent:

- `docs/plans/*.md` — Plan files created by `/mg:plan`. These are living documents that track implementation progress (checkboxes are checked off by `/mg:work`).
- `docs/solutions/*.md` — Solution documents created during the pipeline.

If a review agent flags any file in these directories for cleanup or removal, discard that finding during synthesis.
</protected_artifacts>

#### Parallel Agents to review the PR:

<parallel_tasks>

Run all configured review agents in parallel using Task tool. For each agent in the `review_agents` list:

```
Task {agent-name}(PR content + review context from settings body)
```

Additionally, always run these regardless of settings:

- Task repo-research-analyst(PR content) - Search docs/solutions/ for past issues related to this PR's modules and patterns
- Task test-plan-analyst(PR diff) - Review mode: verify changed behavior has corresponding tests, flag coverage gaps

</parallel_tasks>

#### Conditional Agents (Run if applicable):

<conditional_agents>

These agents are run ONLY when the PR matches specific criteria. Check the PR files list to determine if they apply:

**MIGRATIONS: If PR contains database migrations or data backfills:**

- Task data-reviewer(PR content) - Validates migration safety, ID mappings, rollback plans, and produces Go/No-Go deployment checklists

**When to run:**

- PR includes migration files (alembic, Django migrations, SQL scripts, etc.)
- PR modifies columns that store IDs, enums, or mappings
- PR includes data backfill or transformation scripts
- PR title/body mentions: migration, backfill, data transformation, ID mapping

**What this agent checks:**

- Migration safety: reversibility, data loss, NULL handling, locking, idempotency
- Mapping validation: verifies hard-coded mappings match production reality, checks for swapped IDs, orphaned associations
- Deployment verification: produces executable pre/post-deploy checklists with SQL queries, rollback procedures, and monitoring plans

</conditional_agents>

### 4. Ultra-Thinking Deep Dive Phases

<ultrathink_instruction> For each phase below, spend maximum cognitive effort. Think step by step. Consider all angles. Question assumptions. And bring all reviews in a synthesis to the user.</ultrathink_instruction>

<deliverable>
Complete system context map with component interactions
</deliverable>

#### Phase 3: Stakeholder Perspective Analysis

<thinking_prompt> ULTRA-THINK: Put yourself in each stakeholder's shoes. What matters to them? What are their pain points? </thinking_prompt>

<stakeholder_perspectives>

1. **Developer Perspective** <questions>
   - How easy is this to understand and modify?
   - Are the APIs intuitive?
   - Is debugging straightforward?
   - Can I test this easily? </questions>

2. **Operations Perspective** <questions>
   - How do I deploy this safely?
   - What metrics and logs are available?
   - How do I troubleshoot issues?
   - What are the resource requirements? </questions>

3. **End User Perspective** <questions>
   - Is the feature intuitive?
   - Are error messages helpful?
   - Is performance acceptable?
   - Does it solve my problem? </questions>

4. **Security Team Perspective** <questions>
   - What's the attack surface?
   - Are there compliance requirements?
   - How is data protected?
   - What are the audit capabilities? </questions>

5. **Business Perspective** <questions>
   - What's the ROI?
   - Are there legal/compliance risks?
   - How does this affect time-to-market?
   - What's the total cost of ownership? </questions> </stakeholder_perspectives>

#### Phase 4: Scenario Exploration

<thinking_prompt> ULTRA-THINK: Explore edge cases and failure scenarios. What could go wrong? How does the system behave under stress? </thinking_prompt>

<scenario_checklist>

- [ ] **Happy Path**: Normal operation with valid inputs
- [ ] **Invalid Inputs**: Null, empty, malformed data
- [ ] **Boundary Conditions**: Min/max values, empty collections
- [ ] **Concurrent Access**: Race conditions, deadlocks
- [ ] **Scale Testing**: 10x, 100x, 1000x normal load
- [ ] **Network Issues**: Timeouts, partial failures
- [ ] **Resource Exhaustion**: Memory, disk, connections
- [ ] **Security Attacks**: Injection, overflow, DoS
- [ ] **Data Corruption**: Partial writes, inconsistency
- [ ] **Cascading Failures**: Downstream service issues </scenario_checklist>

### 6. Multi-Angle Review Perspectives

#### Technical Excellence Angle

- Code craftsmanship evaluation
- Engineering best practices
- Technical documentation quality
- Tooling and automation assessment

#### Business Value Angle

- Feature completeness validation
- Performance impact on users
- Cost-benefit analysis
- Time-to-market considerations

#### Risk Management Angle

- Security risk assessment
- Operational risk evaluation
- Compliance risk verification
- Technical debt accumulation

#### Team Dynamics Angle

- Code review etiquette
- Knowledge sharing effectiveness
- Collaboration patterns
- Mentoring opportunities

### 4. Findings Synthesis and Resolution

#### Step 1: Synthesize All Findings

<thinking>
Consolidate all agent reports into a categorized list of findings.
Remove duplicates, prioritize by severity and impact.
</thinking>

<synthesis_tasks>

- [ ] Collect findings from all parallel agents
- [ ] Surface repo-research-analyst results: if past solutions are relevant, flag them as "Known Pattern" with links to docs/solutions/ files
- [ ] Discard any findings that recommend deleting or gitignoring files in `docs/plans/` or `docs/solutions/` (see Protected Artifacts above)
- [ ] Categorize by type: security, performance, architecture, quality, etc.
- [ ] Assign severity levels: P1 (Critical), P2 (Important), P3 (Nice-to-Have)
- [ ] Remove duplicate or overlapping findings
- [ ] Estimate effort for each finding (Small/Medium/Large)

</synthesis_tasks>

#### Step 2: Present Numbered Findings

Present all findings as a numbered list grouped by severity:

```markdown
## Review Findings

**Review Target:** PR #XXXX - [PR Title]
**Branch:** [branch-name]

### P1 - Critical (Blocks Merge)
1. **[Title]** — `file:line` — [Description]
2. **[Title]** — `file:line` — [Description]

### P2 - Important
3. **[Title]** — `file:line` — [Description]
4. **[Title]** — `file:line` — [Description]

### P3 - Nice to Have
5. **[Title]** — `file:line` — [Description]
6. **[Title]** — `file:line` — [Description]

---
**Actions:** Reply with numbers and actions, e.g.:
- `Fix 1, 2, 3` — spawn parallel agents to fix
- `Fix all` — fix everything
- `Ignore 5, 6` — drop those findings
- Mix and match: `Fix 1-3, ignore 5`
```

#### Step 3: User Chooses Actions

Wait for the user to respond with which findings to fix, ignore, or otherwise handle. Parse their response for:

- **Fix [numbers]** — queue those findings for parallel fixing
- **Fix all** — queue all findings for fixing
- **Ignore [numbers]** — drop those findings, no action needed

#### Step 4: Execute Fixes in Parallel

For each finding marked "fix", spawn a `pr-comment-resolver` agent in parallel. Each agent receives:

- The finding title and description
- The file path and line number
- The full context of what needs to change
- **Mandatory instruction to write tests**

```
Task pr-comment-resolver("Fix: [finding title]. File: [path:line]. Issue: [description]. Apply the fix and verify it works. REQUIRED: Write a test that covers this bug or edge case. The test must fail without the fix and pass with it. Place tests in the appropriate test file following the project's existing test conventions.")
```

Launch all fix agents in parallel for speed.

<test_requirement>
**Every fix MUST include a corresponding test.** This is non-negotiable. The test should:

1. **Reproduce the issue** — exercise the exact bug, edge case, or vulnerability that was found
2. **Verify the fix** — assert the correct behavior after the fix is applied
3. **Follow project conventions** — use the same test framework, naming patterns, and file locations as existing tests
4. **Be specific** — test the exact scenario from the finding, not a generic happy-path test

If a finding cannot be meaningfully tested (e.g., documentation-only changes, formatting fixes), the agent must explicitly state why no test was written. This exception should be rare.
</test_requirement>

#### Step 5: Commit and Report

After all fix agents complete:

1. Review the changes made by each agent
2. Run the test suite to confirm all new and existing tests pass
3. Commit the fixes and tests together
4. Report results:

```markdown
## Review Complete

**Fixed:** [count] findings
**Ignored:** [count] findings
**Tests written:** [count] new tests

### Fixes Applied:
- [Finding 1 title] — fixed in [file] — test: [test file:test name]
- [Finding 2 title] — fixed in [file] — test: [test file:test name]

### Ignored:
- [Finding 5 title] — user chose to skip
```

### 7. End-to-End Testing (Optional)

<offer_testing>

After presenting the Summary Report, offer browser testing:

```markdown
**"Want to run browser tests on the affected pages?"**
1. Yes - run `/smoke-test`
2. No - skip
```

</offer_testing>

#### If User Accepts Testing:

Spawn a subagent to run smoke tests (preserves main context):

```
Task general-purpose("Run /smoke-test for the affected pages. Test all routes, check for console errors, handle failures by fixing.")
```

The subagent will:

1. Read `browser-flows.yml` if it exists, or auto-discover routes
2. Navigate to each affected page and capture snapshots (using agent-browser CLI)
3. Check for console errors
4. Test critical interactions
5. Run user flows that touch modified components
6. Fix any failures and retry until all tests pass

**Standalone:** `/smoke-test`

### 8. Important: P1 Findings Block Merge

Any **P1 (Critical)** findings must be addressed before merging the PR. Present these prominently and ensure they're resolved before accepting the PR.

```

```
````
