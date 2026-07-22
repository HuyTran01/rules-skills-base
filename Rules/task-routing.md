# Task Routing

Use this file for shared task taxonomy, escalation semantics, and default MCP assignments.

Runtime execution decisions belong in `runtime-routing.md`.

## Taxonomy

### `TaskClass`
- `trivial-local`: exact target, low risk, no meaningful impact analysis needed.
- `scoped-change`: local change or answer that needs a few targeted reads.
- `shared-core-change`: shared logic, reusable APIs, multiple callers, or architecture-sensitive behavior.
- `investigation-debug`: debugging with hypotheses, evidence gathering, and cause validation.
- `long-running-context-heavy`: many steps, many files, or meaningful continuity needs.

### `RetrievalTier`
- `tier-0-graph`: codebase-memory-mcp queries — sub-ms, ~100 tokens (search_graph, get_architecture, trace_path).
- `tier-1-cheap`: metadata, index, symbol search, memory lookup, or compact summaries.
- `tier-2-bounded`: bounded snippets, line ranges, get_code_snippet, or a small number of targeted file reads.
- `tier-3-expensive`: full-file reads, broad recursive search, or wide exploration.

### `ContextPressureLevel`
- `low`
- `medium`
- `high`
- `critical`

### `ResponseMode`
- `compact-default`
- `detailed-on-demand`
- `verification-evidence`

## Default MCP set by TaskClass

| TaskClass | codebase-memory-mcp | sequential-thinking | memory MCP |
|---|---|---|---|
| `trivial-local` | `search_graph` if target unclear | — | — |
| `scoped-change` | `search_graph` + `get_code_snippet` | — | `search_nodes` at start |
| `shared-core-change` | full suite (trace_path, query_graph, detect_changes) | use for impact reasoning | `search_nodes` + save decisions |
| `investigation-debug` | `search_graph` + `trace_path` + `search_code` | use for hypothesis testing | recall past bugs + save solution |
| `long-running-context-heavy` | as needed per subtask | use for complex subtasks | full continuity protocol |

## Fast routing guide
- `trivial-local`: stay bounded, avoid heavy workflows, skip MCPs if target is exact.
- `scoped-change`: start cheap with graph query, then narrow to source.
- `shared-core-change`: stay impact-aware, use graph traces, reason with sequential-thinking.
- `investigation-debug`: use evidence and hypothesis loops, remember past solutions.
- `long-running-context-heavy`: use handoff and continuity, save state to memory.

## Escalation and downgrade
- `trivial-local` → `scoped-change` when the target is no longer exact.
- `scoped-change` → `shared-core-change` when shared logic or impact surface becomes relevant.
- `investigation-debug` → `long-running-context-heavy` when the task extends across many steps or sessions.
- Downgrade when the target narrows to a clear local path.

## Anti-patterns
- Treating every task as shared-core.
- Starting graph-heavy or plan-heavy workflows for exact one-step edits.
- Reading broadly before classifying the task.
- Skipping memory search at task start when project knowledge exists.
- Using sequential-thinking for trivial decisions.
