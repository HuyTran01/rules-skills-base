# Retrieval Budget

Use this file as the canonical policy for how much exploration is justified before narrowing, escalating, or asking.

## MCP-aware retrieval hierarchy

Always prefer higher tiers (cheaper) before falling to lower tiers:

| Tier | Source | Cost | Examples |
|---|---|---|---|
| **tier-0-graph** | codebase-memory-mcp | sub-ms, ~100 tokens | `get_architecture`, `search_graph`, `trace_path` |
| **tier-0-memory** | memory MCP | fast, ~50 tokens | `search_nodes`, `open_nodes` |
| **tier-1-cheap** | graph snippets, indexes | low | `get_code_snippet`, `query_graph`, `get_graph_schema` |
| **tier-2-bounded** | local source reads | medium | line ranges, bounded file reads, `search_code` |
| **tier-3-expensive** | broad exploration | high | full-file reads, recursive search, wide logs |

## Default exploration limits
- Start at the lowest tier that can answer the current question.
- **Always check memory first**: `search_nodes` for past knowledge before rediscovering.
- **Always check graph next**: `search_graph` or `get_architecture` before grep or file reads.
- Prefer at most a few bounded reads before summarizing ambiguity.
- Escalate only when lower-tier evidence is insufficient.

## Escalation signals
Escalate when:
- the exact target is still unclear after graph query
- a bounded read reveals shared-core impact
- debugging needs additional local evidence
- the current tool output is insufficient to resolve the next question

## Downgrade signals
Downgrade when:
- the exact target narrows to a small local scope
- the ambiguity that justified broad exploration is resolved
- a cheaper source of truth becomes available
- graph queries now answer the question directly

## Raw-artifact exceptions
Skip graph-first exploration when:
- the user gives an exact file or path
- the task depends on raw config, docs, logs, or user-provided source
- no graph index exists and creating one is not justified

## Anti-loop guard
Stop broadening context and narrow the question when:
- several reads still do not resolve the same uncertainty
- output volume rises without adding signal
- search results remain broad and non-discriminating
- the workflow is escalating out of habit instead of evidence

## Verification matrix (canonical)
This is the single canonical source for verification depth by `TaskClass`. `core-global-rules.md` and `runtime-routing.md` reference this section instead of restating it — do not duplicate this table elsewhere.

Use the lightest evidence that can prove the result:
- `trivial-local`: direct bounded readback.
- `scoped-change`: verify the target symbol and nearby flow — `trace_path` + readback.
- `shared-core-change`: verify impact-aware callers, paths, or dependent behavior — `detect_changes` + impact-aware `query_graph`.
- `investigation-debug`: verify both root cause and fix effect — cause-and-effect checks, hypothesis confirmation.
- `long-running-context-heavy`: verify the current subtask and preserve continuation state in memory.

## Relationship to other rules
- taxonomy: `task-routing.md`
- runtime orchestration: `runtime-routing.md`
- token discipline: `task-token-efficiency.md`
- graph-specific workflow: `nav-codebase-graph.md`
- memory policy: `memory-governance.md`
