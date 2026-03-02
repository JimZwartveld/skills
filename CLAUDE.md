# Development Workflow

## Git Strategy

### Starting Work
1. Always pull latest changes before starting:
   ```bash
   git checkout main
   git pull origin main
   ```

2. Create a feature branch for any changes:
   ```bash
   git checkout -b <type>/<short-description>
   ```
   
   Branch types: `feature/`, `fix/`, `refactor/`, `docs/`, `test/`

### During Work
- Commit frequently with clear messages
- Keep commits focused on a single change
- Run linters/formatters before committing (check each repo's CLAUDE.md for specific commands — e.g., `ruff check --fix` + `black` for grillex)
- Run tests in Docker before committing (use `/test` skill for the right command)

### Finishing Work
1. Push the branch:
   ```bash
   git push -u origin <branch-name>
   ```

2. Do NOT merge to main directly—create a PR on GitHub

### What NOT to Do
- Don't commit directly to main
- Don't force push
- Don't leave uncommitted changes when ending a session

---

## Feature Implementation Process

All new features follow a five-phase process. Each phase has its own document stored in `implementation_plan/[feature-name]/`. Phases 1-3 and 5 are available as skills (slash commands):

| Phase | Skill | Description |
|-------|-------|-------------|
| 1. Discovery | `/discovery feature-name` | Ask clarifying questions, capture requirements |
| 2. Research | `/research feature-name` | Explore codebase, propose approaches |
| 3. Planning | `/planning feature-name` | Create detailed plan with critical review |
| 4. Implementation | *(follow guidance below)* | Execute the plan, track progress |
| 4.5. Integration Testing | *(part of implementation)* | Test the feature in the browser, add to regression suite |
| 5. Code Review | `/code-review feature-name` | Review implementation against the plan |

### Phase 4: Implementation (not a skill — too varied)

1. Work through tasks in `plan.md` in order
2. After completing each task: check it off in `plan.md` and update `progress.md`
3. Document errors and solutions as you go
4. Commit after each logical chunk

### Phase 4.5: Integration Testing (mandatory before code review)

After implementation is complete, the feature MUST be integration tested in the browser before moving to code review:

1. The plan's final task should define test scenarios (e.g., "Task X.N: Integration testing")
2. Execute each scenario using the browser (Playwright MCP)
3. Fix any bugs found during testing and commit the fixes
4. **Add the feature's test scenarios to the regression test suite** (`regression-tests/{project}.md`) so they are run on future features
5. Log test results in `progress.md`

Use `/regression-test` to run the full regression suite, which includes all accumulated test scenarios from previous features.

### Key rules
- Subagents (Explore, Plan) do NOT have file write access — the orchestrating agent must write all output files
- All phase outputs go in `implementation_plan/[feature-name]/`
- Use the same feature name consistently across all phases

---

## Utility Skills

| Skill | Description |
|-------|-------------|
| `/resume feature-name` | Restore context when resuming work on a feature (reads plan, progress, review) |
| `/test [optional args]` | Run tests in Docker with the correct image and flags for the current project |
| `/progress feature-name` | Update progress.md at end of session (logs completed work, issues, next steps) |
| `/regression-test [project]` | Run the full regression test suite for a project in the browser |

---

## Session Checklist

### Starting
- [ ] `git status` — check for uncommitted changes
- [ ] `git pull origin main` — get latest
- [ ] `/resume feature-name` if resuming an in-progress feature

### Ending
- [ ] All changes committed
- [ ] `/progress feature-name` if working on a feature
- [ ] Branch pushed if work is in progress
