---
name: planning
description: Start Phase 3 (Planning) for a feature. Create a detailed implementation plan with critical review.
argument-hint: "[feature-name]"
disable-model-invocation: true
---

# Planning: $ARGUMENTS

You are starting Phase 3 (Planning) of the feature implementation process.

## Prerequisites
Read these files first:
- `implementation_plan/$ARGUMENTS/discovery.md` — requirements
- `implementation_plan/$ARGUMENTS/research.md` — chosen approach

## Goal
Create a detailed, task-by-task implementation plan, then critically review it before finalizing.

## Process
1. Read discovery and research documents
2. Use a **Plan subagent** to create the implementation plan based on the chosen approach
3. Use an **Opus subagent** to critically review the plan:
   - Missing edge cases?
   - Better approaches overlooked?
   - Unclear steps?
   - Test coverage gaps?
4. Incorporate review feedback into the final plan
5. Write the plan document (subagents do NOT have write access — you must write the file)

## Output
Write to `implementation_plan/$ARGUMENTS/plan.md`:

```markdown
# Implementation Plan: $ARGUMENTS

## Overview
[Brief description of chosen approach]

## Tasks

- [ ] Task 1: [Description]
- [ ] Task 2: [Description]
- [ ] Task 3: [Description]
- [ ] Task 4: Write tests
- [ ] Task 5: Update documentation

## Review Notes
[Feedback from review agent and how it was addressed]
```

Also initialize `implementation_plan/$ARGUMENTS/progress.md`:

```markdown
# Progress: $ARGUMENTS

## Session Log

(No sessions yet)
```

## Important
- Tasks should be small, focused, and independently verifiable
- Always include testing and documentation tasks
- The review step is mandatory — do not skip it
- When done, tell the user they can start implementation (Phase 4 follows CLAUDE.md guidance)
