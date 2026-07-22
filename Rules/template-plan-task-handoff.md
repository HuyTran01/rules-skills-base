# Plan, Handoff, and Verification Templates

Use this file to standardize plans, handoffs, and verification summaries.

## Plan template
Use when the user asks for a plan or when the task is large enough to benefit from structured execution.

Recommended structure:
1. objective
2. constraints
3. proposed steps
4. risks or open questions
5. verification approach

## Handoff template
Use when work is paused, resumed, or split across subtasks.

Recommended structure:
- current objective
- confirmed findings
- files or areas involved
- current blocker, if any
- next recommended action

### Memory snapshot on handoff
When pausing a long-running task, save continuation state to memory MCP:
1. Create or update a `continuity` entity: `{name: "task_<short_desc>", entityType: "continuity"}`
2. Add observations for: objective, findings, touched files, next step.
3. On resume: `search_nodes` for the continuity entity → restore context → continue.
4. Resync the project's `memory/memory.md` mirror (`memory-markdown-mirror.md`) so the handoff state is visible outside the graph.
Follow `memory-governance.md` for what qualifies.

## Verification summary template
Use when reporting completion or validation.

Recommended structure:
- Changed
- Verified
- Result
- Knowledge saved (if auto-learn triggered)
- Notes
