# Codebase Memory MCP Reference

Use this file when code discovery, impact analysis, architecture understanding, or graph navigation is needed.

This is the primary reference for codebase-memory-mcp tools, graph data model, and query patterns.

## When to use codebase-memory-mcp
- Finding functions, classes, routes, variables by pattern
- Tracing who calls a function or what it calls
- Understanding architecture, packages, entry points
- Analyzing impact of a change before editing
- Detecting dead code
- Finding similar code patterns
- Cross-service linking (HTTP, gRPC, pub-sub)

## When NOT to use
- Searching for string literals, error messages, config values → use grep
- Searching non-code files (Dockerfiles, shell scripts, configs) → unless indexed
- When the user gives an exact file path → use direct file read
- For trivial-local tasks where the target is already exact

## Tool reference

codebase-memory-mcp ships 14 tools (v0.8.1). `trace_call_path` is an alias of `trace_path`, not a separate tool — treat them as one entry.

### Discovery (tier-0, sub-ms)
| Tool | Purpose | When to use |
|---|---|---|
| `get_architecture` | Languages, packages, entry points, routes, hotspots, layers, clusters | First call on a new project; orientation |
| `search_graph` | Find nodes by name pattern, label, degree, file scope | Finding functions, classes, routes by name |
| `get_graph_schema` | Node labels, edge types, counts, relationship patterns | Understanding what's in the graph; run first |

### Navigation (tier-1, fast)
| Tool | Purpose | When to use |
|---|---|---|
| `trace_path` (alias `trace_call_path`) | Callers, callees, dependency chains, BFS depth 1-5 | Impact analysis, understanding data flow, debugging call chains |
| `query_graph` | Read-only Cypher-like queries for complex patterns | Advanced structural queries |
| `detect_changes` | Map uncommitted git diff to affected symbols with risk classification | Pre-edit risk assessment |

### Source access (tier-2)
| Tool | Purpose | When to use |
|---|---|---|
| `get_code_snippet` | Read exact source of a symbol by qualified name | Reading function/class source without full file |
| `search_code` | Graph-augmented grep over indexed files | Pattern search with structural context |

### Management
| Tool | Purpose | When to use |
|---|---|---|
| `index_repository` | Index a new project or re-index | First time, or after major changes |
| `list_projects` | List indexed projects with node/edge counts | Checking what's available |
| `delete_project` | Remove a project from the graph | Cleanup |
| `index_status` | Check indexing progress | During indexing |
| `manage_adr` | CRUD for Architectural Decision Records | Saving design decisions |
| `ingest_traces` | Ingest runtime traces to validate `HTTP_CALLS` edges | Confirming cross-service call edges with real traffic |

Note: some installed versions may also expose `semantic_query` (vector search over the graph) — check `get_graph_schema` or your installed version's tool list if it's unclear whether it's available.

## Graph data model

### Node labels
`Project`, `Package`, `Folder`, `File`, `Module`, `Class`, `Function`, `Method`, `Interface`, `Enum`, `Type`, `Route`, `Resource`

### Edge types
| Edge | Meaning |
|---|---|
| `CALLS` | Function calls another function |
| `IMPORTS` | File imports from another file/package |
| `DEFINES` | File defines a class/function/type |
| `DEFINES_METHOD` | Class defines a method |
| `IMPLEMENTS` | Class implements an interface |
| `INHERITS` | Class inherits from another |
| `HTTP_CALLS` | Cross-service HTTP call |
| `ASYNC_CALLS` | Async cross-service call |
| `EMITS` / `LISTENS_ON` | Pub-sub channel patterns |
| `DATA_FLOWS` | Data flow with arg-to-param mapping |
| `SIMILAR_TO` | Near-clone detection (MinHash) |
| `TESTS` | Test file tests a source file |
| `USES_TYPE` | Function/method uses a type |
| `CONFIGURES` | Config references a component |

### Qualified names
`get_code_snippet` uses qualified names: `<project>.<path_parts>.<name>`.
Use `search_graph` to discover qualified names first.

## Common Cypher query patterns

### Find all callers of a function
```cypher
MATCH (caller:Function)-[:CALLS]->(target:Function)
WHERE target.name = 'handleRequest'
RETURN caller.name, caller.file
```

### Find dead functions (no callers, not entry points)
```cypher
MATCH (f:Function)
WHERE NOT EXISTS { (f)<-[:CALLS]-() }
  AND NOT f.name IN ['main', 'init', 'setup']
RETURN f.name, f.file
```

### Find all methods of a class
```cypher
MATCH (c:Class)-[:DEFINES_METHOD]->(m:Method)
WHERE c.name = 'UserService'
RETURN m.name
```

### Trace import chain
```cypher
MATCH path = (a:File)-[:IMPORTS*1..3]->(b:File)
WHERE a.name = 'index.ts'
RETURN path
```

### Find functions with high fan-out (many calls)
```cypher
MATCH (f:Function)-[:CALLS]->(g:Function)
WITH f, count(g) AS call_count
WHERE call_count > 10
RETURN f.name, f.file, call_count
ORDER BY call_count DESC
```

### Find cross-service HTTP calls
```cypher
MATCH (a:Function)-[:HTTP_CALLS]->(r:Route)
RETURN a.name, a.file, r.name
```

## Preferred workflow
1. Classify the task — is graph navigation justified?
2. Start with `get_architecture` for orientation on new projects.
3. Use `search_graph` to find target symbols.
4. Use `trace_path` to understand callers and impact.
5. Use `get_code_snippet` to read exact source — cheaper than full file reads.
6. Use `query_graph` for complex structural questions.
7. Fall back to `search_code` or local file reads only when graph cannot answer.

## Indexing management
- Index a project on first use: ask user or auto-index if the skill/hook supports it.
- Re-index after major refactors or dependency changes.
- Use `detect_changes` to understand uncommitted changes before a commit.
- Use `manage_adr` to persist architectural decisions.

## Static AST Hook-based Callers Fallback (Dynamic Registry / Event-Driven Systems)

> [!WARNING]
> Static AST analyzers, including tree-sitter, map explicit static call graphs (`CALLS` edge). They DO NOT automatically map dynamic registry callbacks or string-keyed dispatch — this applies to any hook, event, or pub-sub system where the caller and callback are connected by a string name at runtime rather than a direct function call. Examples: WordPress hooks (`add_action('hook_name', 'callback')`, `do_action('hook_name')`), event emitters (`.on('event', handler)` / `.emit('event')`), framework signal/listener systems, plugin registries.

**Fallback rules:**
1. **Never trust empty `trace_path` on hook-driven systems:** If the project profile indicates a hook/registry/event-driven pattern (see `project-profile-detection.md`) and a `trace_path` inbound query returns empty for what appears to be a callback function, DO NOT assume the function is dead.
2. **Mandatory grep verification:** Must perform a text `grep` search for strings corresponding to the callback name or the hook/event name to check if they are registered or dispatched elsewhere.

## Related rules
- `task-routing.md`
- `runtime-routing.md`
- `task-retrieval-budget.md`
- `task-token-efficiency.md`
