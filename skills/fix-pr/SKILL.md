---
name: fix-pr
description: Check a GitHub PR for CI failures (lint, tests, doctests, type errors) and fix them.
argument-hint: "[optional: PR number or branch name]"
disable-model-invocation: true
---

# Fix PR: $ARGUMENTS

Check the GitHub pull request for CI failures and fix all errors.

## Step 1: Identify the PR

- If `$ARGUMENTS` is a PR number (e.g., `159`), use that directly
- If `$ARGUMENTS` is a branch name, find the PR for that branch: `gh pr list --head $ARGUMENTS --json number`
- If `$ARGUMENTS` is empty, use the current branch: `gh pr view --json number`

## Step 2: Check CI status

```bash
gh pr checks <PR_NUMBER> --json name,state,conclusion,detailsUrl
```

If all checks pass, report success and stop.

If checks are failing or pending, wait briefly then re-check:
```bash
gh pr checks <PR_NUMBER> --watch --fail-fast
```

## Step 3: For each failing check, diagnose the error

### Get the failure logs
```bash
# List workflow runs for this PR's head branch
gh run list --branch <BRANCH> --limit 5 --json databaseId,status,conclusion,name

# View the failed run's logs
gh run view <RUN_ID> --log-failed
```

If `--log-failed` output is too large, narrow it down:
```bash
gh run view <RUN_ID> --log-failed 2>&1 | tail -100
```

### Common failure types and how to fix them

**Lint errors (flake8, ruff, black, isort):**
- Read the exact error lines from the log
- Fix the files directly using Edit tool
- Common issues: trailing whitespace, unused imports, line length, import order

**Type errors (mypy, pyright):**
- Read the error messages showing file:line and the type mismatch
- Fix type annotations or add `# type: ignore` comments with specific codes

**Test failures (pytest):**
- Read the traceback to identify the failing test and root cause
- Fix either the test or the source code depending on what's wrong

**Doctest failures (sphinx-build -b doctest):**
- Read which doctest in which file failed and what output was expected vs actual
- Fix the docstring example to match actual behavior

**Build failures (compilation, packaging):**
- Read the build log for the specific error
- Fix build configuration or source files

## Step 4: Verify fixes locally

Run the same checks locally before pushing:

```bash
# For grillex projects:
docker run --rm -v $(pwd):/app grillex-test pytest tests/python/ -v --no-header -o 'addopts='

# For doctests:
docker run --rm -v $(pwd):/app grillex-test sphinx-build -b doctest docs docs/_build/doctest

# For lint (if ruff/flake8 is configured):
docker run --rm -v $(pwd):/app grillex-test ruff check src/
```

Adapt commands to the project's actual CI configuration. Check the workflow files if unsure:
```bash
cat .github/workflows/*.yml
```

## Step 5: Commit and push fixes

- Stage only the files you changed
- Commit with a clear message describing what CI errors were fixed
- Push to the same branch (the PR will update automatically)

```bash
git add <fixed-files>
git commit -m "Fix CI errors: <brief description>"
git push
```

## Step 6: Verify CI passes

```bash
gh pr checks <PR_NUMBER> --watch
```

If new failures appear, repeat from Step 3.

## Behavior
- Be systematic: fix ALL failures in one pass if possible, not one at a time
- If a failure is in code you didn't write (pre-existing), note it but still fix it if it's on the PR's diff
- If a failure is flaky (passes on retry), note it and move on
- Report a clear summary at the end: what failed, what was fixed, final CI status
