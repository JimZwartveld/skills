---
name: progress
description: Update progress.md for a feature at the end of a session. Logs completed work, issues, and next steps.
argument-hint: "[feature-name]"
disable-model-invocation: true
---

# Update Progress: $ARGUMENTS

You are wrapping up a session and need to update the progress log.

## Process

1. Read `implementation_plan/$ARGUMENTS/plan.md` to see the full task list
2. Read `implementation_plan/$ARGUMENTS/progress.md` to see what was previously logged
3. Review the current session's work:
   - Check `git log --oneline -20` for commits made this session
   - Check `git diff --stat` for any uncommitted changes
4. Update `progress.md` with a new session entry

## Session entry format

Append to the `## Session Log` section in `implementation_plan/$ARGUMENTS/progress.md`:

```markdown
### [Today's date]
**Completed:**
- Task N: [Brief description of what was implemented]
- Task M: [Brief description of what was implemented]

**Issues Encountered:**
- [Problem]: [How it was solved]

**Next Steps:**
- Task X: [What needs to happen next]
```

## Also do
- Update checkboxes in `plan.md` — mark completed tasks as `[x]`
- If all tasks are done, add a `## Final Evaluation` section to `progress.md`
- Warn if there are uncommitted changes that should be committed before ending the session

## Important
- Use today's date for the session header
- Be factual — log what actually happened, not what was planned
- If issues were encountered but NOT resolved, flag them clearly in Next Steps
- Keep entries concise — this is a log, not a narrative
