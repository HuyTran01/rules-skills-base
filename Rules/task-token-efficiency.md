# Token Efficiency

Use this file to minimize context cost while preserving correctness.

## Required behavior
- Classify with `task-routing.md` before broad exploration.
- Re-route with `runtime-routing.md` when evidence changes task shape.
- Stay within `task-retrieval-budget.md`.
- Do not repeat information the user already confirmed unless it is needed as evidence.

## MCP-first cheapest-sufficient path

Default order — always try cheaper sources first:

1. **Memory lookup** — `search_nodes` from memory MCP (~50 tokens). Past knowledge, conventions, decisions.
2. **Graph query** — `search_graph`, `get_architecture`, `trace_path` from codebase-memory-mcp (~100 tokens). Structure, callers, architecture.
3. **Graph code snippet** — `get_code_snippet` from codebase-memory-mcp (~200-500 tokens). Exact source of a symbol.
4. **Bounded source read** — line ranges, targeted file reads (~500-2000 tokens).
5. **Scoped text search** — grep, search_code for patterns.
6. **Full-file reads** — only when narrower methods cannot answer the question (~5000+ tokens).

### Token comparison
| Method | Typical tokens | Use case |
|---|---|---|
| `search_graph` | ~100 | find function by name pattern |
| `trace_path` | ~200 | who calls this function |
| `get_code_snippet` | ~300 | read exact function source |
| `search_nodes` (memory) | ~50 | recall project conventions |
| grep + file read | ~5000+ | same information via raw search |

## Exceptions
Use raw local artifacts first when:
- the user gives an exact file or path
- the answer depends on raw config, docs, logs, or user-provided source
- graph indexing is unavailable
- the project profile suggests a local docs/config workflow

## Command output policy
- Keep output short by default.
- Use filters such as `head`, `tail`, `sed -n`, or exact search patterns.
- Do not dump generated files, lockfiles, vendor trees, minified bundles, or source maps unless required.

## Context discipline
Before reading more source:
- identify the next concrete question
- check memory MCP for existing knowledge
- check codebase-memory-mcp for structural answer
- read the smallest region that can answer the question
- summarize and narrow if repeated reads do not reduce ambiguity

## Response discipline
- Default to `compact-default`.
- Do not recap long tool output.
- Ask only specific questions when more information is required.
- Do not end with social filler.

## Stop conditions
Stop expanding context and narrow, summarize, or ask when:
- exploration stops producing new signal
- output size rises faster than certainty
- several steps in a row fail to resolve ambiguity
- search results remain broad and non-discriminating

## Relationship to other rules
- `task-routing.md`
- `runtime-routing.md`
- `task-retrieval-budget.md`
- `task-response-compact.md`
- `memory-governance.md`
