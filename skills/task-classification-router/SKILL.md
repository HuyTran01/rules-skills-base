---
name: task-classification-router
description: >-
  Classify task shape, choose the default MCP set, and pick the cheapest
  retrieval tier — all in one pass.
disabled: true
---

# Task Classification Router

## When to activate
Use at task start, after scope changes, or when the current workflow feels too heavy or too weak.

## Delegates to
- `../../Rules/runtime-routing.md`
- `../../Rules/task-routing.md`
- `../../Rules/project-profile-detection.md`
- `../../Rules/task-retrieval-budget.md`
- `../../Rules/task-token-efficiency.md`
- `../../Rules/skill-routing-matrix.md`

## Operating flow
1. Classify the current `TaskClass`.
2. Select the default MCP posture from `task-routing.md` and `skill-routing-matrix.md`.
3. Follow the canonical memory-start policy from `memory-governance.md` and `task-retrieval-budget.md`.
4. Apply project-profile bias without overriding task shape.
5. Re-classify when evidence changes the task.

## Notes
- Keep the canonical routing details in `runtime-routing.md`, `task-routing.md`, and `task-retrieval-budget.md`.
