# Skill Routing Matrix

This is the canonical composition map for skills. It defines which skill is an entry point, which skills are follow-up steps, and which rules remain the source of truth for policy.

## Composition principles

- `task-classification-router` is the optional entry-point skill for task routing.
- Domain skills are activated only when their trigger is present; they do not replace runtime routing.
- `mcp-memory-markdown-mirror` is a post-write follow-up, not an independent memory workflow.
- `mcp-memory-continuity-resume` is a continuity branch for long-running or paused work.
- Canonical rules own policy. Skills own activation and execution sequencing.
- Do not activate `sequential-thinking-reasoning` merely because a task has multiple steps; use it when ambiguity, hypotheses, trade-offs, or non-obvious consequences justify the overhead.

## Task composition

| TaskClass | Entry skill | Supporting skills | Canonical rules | MCP posture |
|---|---|---|---|---|
| `trivial-local` | none | none | `task-routing.md`, `task-retrieval-budget.md` | Direct local tools; skip MCPs when the target is exact |
| `scoped-change` | `task-classification-router` | `nav-token-aware-graph-navigation` when target or relationships are unclear; `mcp-memory-workflow` when durable knowledge exists or is learned | `runtime-routing.md`, `task-routing.md`, `task-retrieval-budget.md`, `task-token-efficiency.md` | Cheapest sufficient graph/memory lookup, then bounded source access |
| `shared-core-change` | `task-classification-router` | `nav-token-aware-graph-navigation`, `sequential-thinking-reasoning`, `mcp-memory-workflow` | `nav-codebase-graph.md`, `task-retrieval-budget.md`, `conflict-resolution.md`, `memory-governance.md` | Impact-aware graph evidence, structured reasoning when risk or trade-offs justify it, save stable decisions |
| `investigation-debug` | `task-classification-router` | `mcp-memory-workflow`, `nav-token-aware-graph-navigation`, `sequential-thinking-reasoning` | `memory-governance.md`, `nav-codebase-graph.md`, `task-retrieval-budget.md`, `conflict-resolution.md` | Recall past bugs, trace evidence, test hypotheses, verify cause and fix effect |
| `long-running-context-heavy` | `task-classification-router` | `mcp-memory-workflow`, `mcp-memory-continuity-resume`, `nav-token-aware-graph-navigation`, `sequential-thinking-reasoning` as needed | `template-plan-task-handoff.md`, `memory-governance.md`, `memory-markdown-mirror.md` | Preserve continuity state; use each MCP per subtask rather than forcing all MCPs continuously |

## Skill relationship map

```text
task-classification-router
  ├─ selects TaskClass and retrieval posture
  ├─ delegates policy to runtime/task/retrieval/profile rules
  └─ activates domain skills only when the task evidence requires them

mcp-memory-workflow
  ├─ RECALL at task start when project knowledge may exist
  ├─ LEARN after verified reusable knowledge is discovered
  └─ triggers mcp-memory-markdown-mirror after a memory write

mcp-memory-continuity-resume
  ├─ RESTORE at session start for long-running work
  ├─ PRESERVE on pause/blocker
  └─ CLEAN UP continuity entities on completion

nav-token-aware-graph-navigation ── gathers structural evidence
sequential-thinking-reasoning ───── analyzes ambiguity, hypotheses, and trade-offs
figma-to-code ───────────────────── domain workflow for Figma/design input
```

## Activation boundaries

### Memory skills

- Use `mcp-memory-workflow` for general recall and durable learning.
- Add `mcp-memory-continuity-resume` only when the work needs cross-session state.
- Run `mcp-memory-markdown-mirror` only after a memory mutation. Never run it for read-only recall.

### Graph and reasoning skills

- Use `nav-token-aware-graph-navigation` for structural code questions, impact, callers, callees, and architecture.
- Use `sequential-thinking-reasoning` after enough evidence exists to support hypotheses or compare non-obvious options.
- If a graph answer is sufficient and the decision is obvious, stop before reasoning.

### Figma skill

- `figma-to-code` is independent of the memory continuity chain.
- It may use runtime routing and token-efficiency rules, but it must not activate memory or sequential-thinking unless the task class independently justifies them.

## Maintenance rule

When adding or renaming a skill, update this matrix, the catalog in `core-global-rules.md`, and the skill links in `README.md`. Keep detailed policy in the canonical rule file instead of copying it into this matrix or a skill.