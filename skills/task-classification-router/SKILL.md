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
2. Look up the default MCP set from `task-routing.md`.
3. Check memory MCP: `search_nodes` for project/task context.
4. Choose the cheapest sufficient retrieval tier.
5. Apply project-profile bias without overriding task shape.
6. Re-classify when evidence changes the task.

## Decision tree

```
Is the target exact (single file, single line, known fix)?
  YES → trivial-local → skip MCPs, direct edit
  NO  ↓

Does it touch shared logic, multiple callers, or API contracts?
  YES → shared-core-change → full codebase-memory + sequential-thinking + memory
  NO  ↓

Is this a bug, error, or unexpected behavior?
  YES → investigation-debug → sequential-thinking + codebase-memory + memory (recall past bugs)
  NO  ↓

Will this take many steps or span sessions?
  YES → long-running-context-heavy → all 3 MCPs + continuity protocol
  NO  → scoped-change → search_graph + get_code_snippet + memory search
```

## Examples

### trivial-local
> "Fix the typo in line 42 of config.yaml"
- TaskClass: `trivial-local`
- MCPs: none
- Tier: direct file read + edit

### scoped-change
> "Add a loading spinner to the login button"
- TaskClass: `scoped-change`
- MCPs: `search_graph` (find login component) → `get_code_snippet` (read it)
- Tier: `tier-0-graph` → `tier-2-bounded`

### shared-core-change
> "Refactor the auth middleware to support JWT refresh tokens"
- TaskClass: `shared-core-change`
- MCPs: `trace_path` (find all callers) → `sequential-thinking` (reason about impact) → memory (save decision)
- Tier: `tier-0-graph` → escalate as needed

### investigation-debug
> "Users report 500 errors on the checkout page"
- TaskClass: `investigation-debug`
- MCPs: memory (`search_nodes` for past checkout bugs) → `search_graph` + `trace_path` (trace checkout flow) → `sequential-thinking` (hypothesize causes)
- Tier: `tier-0-graph` + `tier-0-memory` → escalate

### long-running-context-heavy
> "Migrate the entire API from Express to Fastify"
- TaskClass: `long-running-context-heavy`
- MCPs: all 3, with continuity protocol
- Tier: per-subtask, with memory state preservation

## Anti-patterns
- Classifying too late (reading files before knowing the task shape).
- Escalating before narrowing the next question.
- Treating project profile as an override.
- Adding MCP out of habit rather than evidence.
- Skipping memory search when starting a task in a known project.
- Using sequential-thinking for trivial yes/no decisions.
