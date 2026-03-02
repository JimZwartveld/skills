---
name: research
description: Start Phase 2 (Research) for a feature. Explore the codebase and propose implementation approaches.
argument-hint: "[feature-name]"
disable-model-invocation: true
---

# Research: $ARGUMENTS

You are starting Phase 2 (Research) of the feature implementation process.

## Prerequisites
Read `implementation_plan/$ARGUMENTS/discovery.md` first to understand the requirements.

## Goal
Explore the codebase and propose 2-3 implementation approaches with trade-offs.

## Process
1. Read the discovery document to understand requirements
2. Explore the codebase — find relevant files, patterns, and integration points
3. Identify 2-3 viable approaches
4. Evaluate trade-offs for each
5. Make a recommendation
6. Write the research document

## Output
Write to `implementation_plan/$ARGUMENTS/research.md`:

```markdown
# Research: $ARGUMENTS

## Relevant Existing Code
- `path/to/file.py` — [why it's relevant]
- `path/to/other.py` — [why it's relevant]

## Proposed Approaches

### Option A: [Name]
**Description:** ...
**Pros:** ...
**Cons:** ...

### Option B: [Name]
**Description:** ...
**Pros:** ...
**Cons:** ...

## Recommendation
[Which option and why]
```

## Important
- Thoroughly explore the codebase before proposing approaches — use Explore agents for deep research
- Each approach should be genuinely different, not just minor variations
- Be specific about files that would need to change
- When done, tell the user they can proceed with `/planning $ARGUMENTS`
