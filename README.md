# Claude Code Skills

A collection of custom [Claude Code](https://docs.anthropic.com/en/docs/claude-code) skills for structured feature development workflows.

## Skills

### Feature Implementation Workflow

These skills guide a five-phase process for implementing new features:

| Phase | Skill | Command | Description |
|-------|-------|---------|-------------|
| 1 | Discovery | `/discovery feature-name` | Ask clarifying questions to capture requirements |
| 2 | Research | `/research feature-name` | Explore the codebase and propose implementation approaches |
| 3 | Planning | `/planning feature-name` | Create a detailed implementation plan with critical review |
| 4 | *(manual)* | — | Execute the plan, track progress |
| 5 | Code Review | `/code-review feature-name` | Review implementation against the plan |

### Utility Skills

| Skill | Command | Description |
|-------|---------|-------------|
| Test | `/test [args]` | Run tests in Docker with the correct image and flags |
| Resume | `/resume feature-name` | Restore context when resuming work on a feature |
| Progress | `/progress feature-name` | Update progress.md at end of session |
| Fix PR | `/fix-pr [PR# or branch]` | Check a GitHub PR for CI failures and fix them |
| Release | `/release [repo] [major\|minor\|patch]` | Automate version bump, changelog, tag, and GitHub release |
| Regression Test | `/regression-test [project]` | Run the full regression test suite in the browser via Playwright |

## Installation

Copy the `skills/` directory into your Claude Code configuration:

```bash
cp -r skills/* ~/.claude/skills/
```

Or symlink individual skills:

```bash
ln -s /path/to/this/repo/skills/discovery ~/.claude/skills/discovery
```

## How It Works

Each skill is a markdown file (`SKILL.md`) that provides structured instructions to Claude Code. When invoked via a slash command (e.g., `/discovery my-feature`), Claude follows the instructions to guide you through that phase.

All phase outputs are stored in `implementation_plan/<feature-name>/` within your project repository, creating a documented trail of decisions and progress.

## Requirements

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI
- Some skills assume Docker is available for running tests
- The `fix-pr` and `release` skills require the [GitHub CLI](https://cli.github.com/) (`gh`)
- The `regression-test` skill requires [Playwright MCP](https://github.com/anthropics/claude-code) for browser automation
