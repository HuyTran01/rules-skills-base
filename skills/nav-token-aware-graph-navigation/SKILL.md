---
name: nav-token-aware-graph-navigation
description: Navigate code relationships via codebase-memory-mcp with minimal token cost. Use graph queries instead of grep/file reads.
---

# Token-Aware Graph Navigation

## When to activate
Use when code relationships, architecture, shared-core impact, callers, callees, or data flow are central to the task.

## Delegates to
- `../../Rules/nav-codebase-graph.md` — full tool reference, graph model, and Cypher patterns
- `../../Rules/runtime-routing.md`
- `../../Rules/task-retrieval-budget.md`
- `../../Rules/task-token-efficiency.md`

## Operating flow

### Phase 1: Orientation (tier-0)
1. `get_architecture` — understand the project: languages, packages, entry points, hotspots.
2. `get_graph_schema` — check what node labels and edge types are available.

### Phase 2: Discovery (tier-0)
3. `search_graph` — find target symbols by name pattern, label, or file scope.
4. Optional: `semantic_query` — vector search for conceptually related code, only if the installed codebase-memory-mcp exposes it (see `../../Rules/nav-codebase-graph.md`).
5. If multiple candidates → narrow with degree filters or file scope.

### Phase 3: Navigation (tier-1)
6. `trace_path` — trace callers/callees for impact analysis, or a specific call chain between two functions (alias `trace_call_path`).
7. `query_graph` — Cypher for complex structural questions.

### Phase 4: Source access (tier-2)
8. `get_code_snippet` — read exact function/class source (~300 tokens vs ~5000 for full file).
9. `search_code` — graph-augmented grep when pattern search is needed.

### Phase 5: Local fallback (tier-3)
10. Only use raw file reads when graph cannot answer the question.

## Tool selection quick guide

| I need to... | Use tool |
|---|---|
| Understand project structure | `get_architecture` |
| Find a function/class by name | `search_graph` |
| See who calls a function | `trace_path` (direction: inbound) |
| See what a function calls | `trace_path` (direction: outbound) |
| Natural language code search | `semantic_query` if the installed version exposes it; otherwise skip |
| Find call chain A→B | `trace_path` (alias `trace_call_path`) |
| Complex structural query | `query_graph` (Cypher) |
| Read a function's source | `get_code_snippet` |
| Search code patterns | `search_code` |
| Check uncommitted changes | `detect_changes` |
| Save an architecture decision | `manage_adr` |

## Index management
- If project is not indexed: suggest `index_repository` to the user.
- Check status with `index_status` during indexing.
- Re-index after major refactors.
- Use `list_projects` to see what's already indexed.

## Anti-patterns
- Forcing graph-first for exact local artifacts (configs, docs, logs).
- Trusting graph-first inbound analysis for dynamic hook/registry-based registrations (e.g., WordPress callback functions, event emitter listeners; always grep to verify — see `nav-codebase-graph.md`).
- Staying graph-heavy after the target narrows to a specific file.
- Reading full files when `get_code_snippet` would suffice.
- Running `query_graph` with overly broad queries.
- Not indexing the project before trying graph queries.
