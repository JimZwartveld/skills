---
name: code-review
description: Start Phase 5 (Code Review) for a completed feature. Review implementation against the plan.
argument-hint: "[feature-name]"
disable-model-invocation: true
---

# Code Review: $ARGUMENTS

You are starting Phase 5 (Code Review) of the feature implementation process.

## Prerequisites
Read these files first:
- `implementation_plan/$ARGUMENTS/plan.md` — what was planned
- `implementation_plan/$ARGUMENTS/progress.md` — what was done

## Goal
Critically review the implemented code against the implementation plan.

## Process
1. Read the plan and progress documents
2. Use an **Opus subagent** to perform the review:
   - Read the plan and all changed/created files
   - Assess whether each task was completed correctly
   - Check for deviations from the plan (and whether they were justified)
   - Evaluate code quality: readability, maintainability, consistency
   - Identify bugs, edge cases, or error handling gaps
   - Verify test coverage matches what was planned
3. Write the review document (subagents do NOT have write access — you must write the file)
4. If critical issues are found, fix them before considering the feature complete
5. Document accepted trade-offs in `progress.md`

## Output
Write to `implementation_plan/$ARGUMENTS/review.md`:

```markdown
# Code Review: $ARGUMENTS

## Plan Compliance
| Task | Status | Notes |
|------|--------|-------|
| Task 1 | Completed | [Any observations] |
| Task 2 | Completed with deviation | [What changed and why] |
| Task 3 | Missing/Incomplete | [What's missing] |

## Code Quality Assessment
- **Readability:** [Assessment]
- **Maintainability:** [Assessment]
- **Consistency with codebase:** [Assessment]

## Issues Found
### Critical (must fix)
- [Issue]: [Description and suggested fix]

### Minor (recommended)
- [Issue]: [Description and suggested fix]

## Test Coverage
- [Assessment of whether tests adequately cover the implementation]

## Overall Verdict
[Pass / Pass with minor issues / Needs changes]
```

## Important
- Be thorough and honest — the point is to catch issues before they ship
- Critical issues must be fixed; minor issues are documented
- When done, summarize the verdict for the user
