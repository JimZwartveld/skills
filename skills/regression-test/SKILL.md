---
name: regression-test
description: Run the full regression test suite for a project in the browser using Playwright MCP.
argument-hint: "[project-name, e.g. grillex-webapp]"
disable-model-invocation: true
---

# Regression Test: $ARGUMENTS

Run the cumulative integration test suite for a project. This suite grows over time as each feature adds its test scenarios.

## Step 1: Determine the project

If `$ARGUMENTS` is provided, use it as the project name. Otherwise, infer from the current working directory (e.g., `grillex-webapp`).

## Step 2: Read the test suite

Read the regression test suite file from the project repo:
```
{project-repo}/regression-tests/{project}.md
```

For example: `~/code/grillex-webapp/regression-tests/grillex-webapp.md`

If it doesn't exist yet, tell the user no regression tests are defined and offer to create the file with an initial structure.

## Step 3: Ensure prerequisites

Before running tests:

1. **Verify the dev containers are running:**
   - Backend health: `curl -sf http://localhost:8000/api/health`
   - Frontend: `curl -sf http://localhost:5173`
2. **If not running**, tell the user and suggest:
   ```bash
   cd ~/code/grillex-webapp
   docker compose -f docker-compose.dev.yml up --build
   ```
   Also remind them that PostgreSQL must be running on `localhost:5432`.
3. **Navigate to the app** in the browser using Playwright MCP (`browser_navigate` to `http://localhost:5173`)

## Step 4: Execute test scenarios

Work through each test scenario in the suite file sequentially:

1. Read the scenario's steps
2. Execute each step using Playwright MCP (browser_navigate, browser_click, browser_type, browser_snapshot, etc.)
3. After each scenario, record the result: PASS, FAIL, or SKIP (with reason)
4. If a test fails, capture the error/state but **continue with remaining tests** — don't stop on first failure
5. Between test scenarios, reset the app state if needed (reload page, create fresh model, etc.)

## Step 5: Report results

After all tests are run, output a summary table:

```
## Regression Test Results — {project} ({date})

| # | Scenario | Status | Notes |
|---|----------|--------|-------|
| T8 | Add Symmetric Wing Plate | PASS | |
| T9 | Edit Wing Plate Properties | FAIL | thickness not updating |
| T10 | Delete Wing Plate | SKIP | blocked by T9 failure |

**Result: X/Y passed, Z failed, W skipped**
```

If any tests FAIL:
- List the failures with details (what was expected vs what happened)
- Suggest whether these are regressions (worked before) or known issues
- Ask the user if they want to investigate/fix

## Adding New Tests

When a feature is completed (Phase 4.5), its integration test scenarios should be appended to the regression test file. Each scenario should include:
- A descriptive name with a T-number
- Clear numbered steps that can be executed via Playwright MCP
- What to verify after each action

## Important
- Tests must be **deterministic** — same steps produce same results
- Tests must be **independent** — each scenario resets state as needed
- Tests should be **fast** — focus on critical paths, not exhaustive edge cases
- Keep scenarios **UI-focused** — these test the full stack through the browser, not individual functions
