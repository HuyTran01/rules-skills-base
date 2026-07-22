---
name: nav-token-aware-graph-navigation
description: >-
  Navigate code relationships via codebase-memory-mcp with minimal token cost.
  Use graph queries instead of grep/file reads.
disabled: true
---

# Token-Aware Graph Navigation

## When to activate
Use when code relationships, architecture, shared-core impact, callers, callees, or data flow are central to the task.

## Delegates to
- `../../Rules/nav-codebase-graph.md` — full tool reference, graph model, and Cypher patterns
- `../../Rules/runtime-routing.md`
- `../../Rules/task-retrieval-budget.md`
- `../../Rules/task-token-efficiency.md`
- `../../Rules/skill-routing-matrix.md`

## Operating flow

### Phase 1: Orientation (tier-0)
1. `get_architecture` — understand the project: languages, packages, entry points, hotspots.
2. `get_graph_schema` — check what node labels and edge types are available.

### Phase 2: Discovery (tier-0)
3. `search_graph` — find target symbols by name pattern, label, or file scope.
4. `semantic_query` — vector search for conceptually related code, if available in the installed version (see `nav-codebase-graph.md`).
5. If multiple candidates → narrow with degree filters or file scope.

### Phase 3: Navigation (tier-1)
6. `trace_path` — trace callers/callees for impact analysis, or a specific call chain between two functions (alias `trace_call_path`).
7. `query_graph` — Cypher for complex structural questions.

### Phase 4: Source access (tier-2)
8. `get_code_snippet` — read exact function/class source (~300 tokens vs ~5000 for full file).
9. `search_code` — graph-augmented grep when pattern search is needed.

### Phase 5: Local fallback (tier-3)
10. Only use raw file reads when graph cannot answer the question.

## Index management
- If project is not indexed: suggest `index_repository` to the user.
- Check status with `index_status` during indexing.
- Re-index after major refactors.
- Use `list_projects` to see what's already indexed.

## Notes
- The canonical graph model, fallback rules, and query patterns live in `nav-codebase-graph.md`.
