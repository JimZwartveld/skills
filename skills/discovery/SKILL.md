---
name: discovery
description: Start Phase 1 (Discovery) for a new feature. Ask clarifying questions to understand requirements before any code is written.
argument-hint: "[feature-name]"
disable-model-invocation: true
---

# Discovery: $ARGUMENTS

You are starting Phase 1 (Discovery) of the feature implementation process.

## Goal
Understand what the user really wants through dialogue. Do NOT assume requirements — ask clarifying questions.

## Process
1. Read existing files in `implementation_plan/$ARGUMENTS/` if they exist (resuming work)
2. Ask clarifying questions about the feature — cover scope, edge cases, constraints, and integration points
3. Continue the conversation until requirements are clear
4. Write the discovery document when the user confirms requirements are complete

## Output
Write to `implementation_plan/$ARGUMENTS/discovery.md`:

```markdown
# Discovery: $ARGUMENTS

## Questions & Answers

### Q1: [Your question]
**A:** [User's answer]

...

## Summary of Requirements
- [Requirement 1]
- [Requirement 2]
```

## Important
- Do NOT skip ahead to research or planning
- Do NOT make assumptions — ask
- Keep asking until you and the user agree that requirements are clear
- When done, tell the user they can proceed with `/research $ARGUMENTS`
