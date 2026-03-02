---
name: resume
description: Resume work on an in-progress feature. Reads all implementation plan files and summarizes current state.
argument-hint: "[feature-name]"
disable-model-invocation: true
---

# Resume: $ARGUMENTS

You are resuming work on an in-progress feature. Your goal is to restore full context quickly.

## Process

1. Read all existing files in `implementation_plan/$ARGUMENTS/` (discovery, research, plan, progress, review — whichever exist)
2. Check `git log --oneline -20` and `git branch` to understand the current branch and recent commits
3. Check `git status` for any uncommitted changes

## Output

Provide a concise summary covering:

### Where we are
- Which phase is active (Discovery/Research/Planning/Implementation/Review)
- What has been completed so far (reference task numbers from plan.md)

### What's next
- The specific next task(s) to work on
- Any blockers or open questions from previous sessions

### Key decisions made
- Chosen approach (from research.md)
- Any deviations from the plan noted in progress.md

### Uncommitted state
- Any uncommitted changes or work-in-progress on the current branch

## Important
- Be concise — this is a status briefing, not a retelling of every document
- If `progress.md` has "Issues Encountered", highlight unresolved ones
- If no `implementation_plan/$ARGUMENTS/` folder exists, list available features from `implementation_plan/` and ask which one to resume
