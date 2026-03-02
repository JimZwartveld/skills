---
name: release
description: Automate the full release workflow — version bump, changelog, tag, and GitHub release.
argument-hint: "[optional: repo-name] [optional: major|minor|patch]"
disable-model-invocation: true
---

# Release: $ARGUMENTS

Automate the full release workflow.

## Step 0: Parse arguments and resolve repo

`$ARGUMENTS` may contain a repo name and/or a bump type, in any order. Parse them:

- If a word matches `major`, `minor`, or `patch` → that's the bump type
- If a word matches a directory name under `~/code/` → that's the repo
- If no repo is specified, use the current working directory

If a repo name is provided, `cd` into it before proceeding:
```bash
cd ~/code/<repo-name>
```

Verify the directory exists and is a git repository. If not, stop with an error.

## Step 1: Pre-flight checks

Verify all preconditions before proceeding:

```bash
# Must be on main branch
git branch --show-current
# Working tree must be clean
git status --porcelain
# Fetch remote tags to ensure local tags are up to date
git fetch origin --tags
# Check CI status for latest commit on main
gh run list --branch main --limit 3 --json status,conclusion,name,headSha
```

- If NOT on `main`, stop and tell the user to switch to main first.
- If working tree is dirty, stop and tell the user to commit or stash changes.
- If CI checks are failing or pending, warn the user and ask whether to proceed.

### Code review gate

Check whether a `/code-review` has been performed for the changes being released:

1. Look at the commits since the last tag to identify which feature(s) are being released
2. Check if `implementation_plan/<feature-name>/progress.md` exists and contains a code review section (e.g., "Code Review", "Review Notes", or a Phase 5 entry)
3. If a feature was implemented through the implementation plan workflow (has `plan.md` and `progress.md`) but has **no evidence of a code review**, warn the user:
   ```
   Warning: No code review found for feature '<feature-name>'.
   Run `/code-review <feature-name>` before releasing, or confirm you want to skip.
   ```
4. Ask the user whether to proceed without code review or stop to do it first.

This check is best-effort — it only applies when changes follow the implementation plan workflow. Ad-hoc fixes and small patches without a plan don't require a formal code review.

## Step 2: Determine version

Cross-reference local tags with GitHub releases to ensure consistency:

```bash
# Get latest tag (after fetching remote tags)
git describe --tags --abbrev=0
# Verify against GitHub releases
gh release list --limit 5
# Get commits since last tag
git log <last-tag>..HEAD --oneline
```

If the latest GitHub release is newer than the latest local tag, something is wrong — investigate before proceeding.

If `$ARGUMENTS` is `major`, `minor`, or `patch`, use that bump type directly. Otherwise, analyze commit messages to determine bump type:

- **major**: any commit contains "BREAKING" or has `!` after the type prefix (e.g., `feat!:`)
- **minor**: any commit starts with "Add", branch was `feature/`, or message contains "new feature"
- **patch**: everything else (fixes, refactors, docs, etc.)

Parse the current version from the latest tag (e.g., `v0.5.0` → `0.5.0`), apply the bump, and show:

```
Current version: 0.5.0
Commits since last release:
  - abc1234 Add transient solver support
  - def5678 Fix density default
Proposed bump: minor → 0.6.0
```

**Ask the user to confirm** the proposed version before proceeding. If they want a different version, use that instead.

## Step 3: Update CHANGELOG.rst

Read the existing `CHANGELOG.rst` to match its RST format exactly. The format uses:

```rst
v0.6.0 (YYYY-MM-DD)
===================

Added
-----

- Item description

Changed
-------

- Item description

Fixed
-----

- Item description
```

Group commits into sections based on their message:
- **Added**: commits starting with "Add", "Implement", "New", or from `feature/` branches
- **Changed**: commits starting with "Update", "Refactor", "Change", "Restructure"
- **Fixed**: commits starting with "Fix", "Correct", "Resolve"
- **Removed**: commits starting with "Remove", "Delete", "Drop"

Only include sections that have entries. Use today's date in `YYYY-MM-DD` format.

Insert the new version section directly before the first existing version entry (look for the first line matching `v\d+\.\d+\.\d+`). Preserve all existing content below.

Show the proposed changelog entry to the user and **ask for confirmation** before writing. Let them edit the text if needed.

## Step 4: Generate release notes

Create GitHub release notes in markdown matching the established format:

```markdown
## Highlights

- **Feature Name** — brief description of what it does
- **Bug Fix** — what was fixed and why it matters

See [CHANGELOG.rst](https://github.com/<owner>/<repo>/blob/main/CHANGELOG.rst) for full details.
```

Derive the repo owner/name from `gh repo view --json nameWithOwner --jq .nameWithOwner`.

Summarize the most important changes as bullet points. Keep it concise — this is a summary, not the full changelog.

Show the proposed release notes to the user and **ask for confirmation**.

## Step 5: Commit changelog and update version

```bash
git add CHANGELOG.rst
git commit -m "Update changelog for v<VERSION> release"
```

If the project has a `pyproject.toml` with a hardcoded `version` field, update it too:
```bash
# Check if version is in pyproject.toml (match "version = " exactly, not version_scheme etc.)
grep -nP '^version\s*=' pyproject.toml
```
If a hardcoded version is found (not dynamic via setuptools-scm or similar), update it in `pyproject.toml`, stage it, and include in the commit:
```bash
git add pyproject.toml CHANGELOG.rst
git commit -m "Release v<VERSION>"
```

## Step 6: Create tag and GitHub release

```bash
git push origin main
git tag v<VERSION>
git push origin v<VERSION>
```

Create the GitHub release using the release notes from Step 4:
```bash
gh release create v<VERSION> --title "v<VERSION>" --notes "<release_notes>"
```

Use a heredoc for the notes to preserve formatting:
```bash
gh release create v<VERSION> --title "v<VERSION>" --notes "$(cat <<'EOF'
## Highlights

- **Feature** — description

See [CHANGELOG.rst](...) for full details.
EOF
)"
```

## Step 7: Report

Show a final summary:

```
Release complete!
  Version: v<VERSION>
  Tag: v<VERSION>
  Release: <URL from gh release create output>
  Changelog: Updated CHANGELOG.rst

CI will automatically build wheels and publish to PyPI.
```

## Behavior

- **Ask for confirmation at three points**: (1) the proposed version, (2) the changelog entry, (3) the release notes. After the user approves the release notes, proceed with commit + tag + push + GitHub release as a single sequence without additional confirmation prompts.
- If any step fails, stop and report the error — do not continue to the next step
- If there are no commits since the last tag, tell the user there's nothing to release
- Be conservative: default to patch bumps unless there's clear evidence of a feature or breaking change
- The user can override any proposed version, changelog text, or release notes
