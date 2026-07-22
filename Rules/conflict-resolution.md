# Conflict Resolution

Use this file when multiple rules apply or when workflow signals overlap.

## Purpose
Resolve conflicts consistently without re-deriving the workflow from scratch each time.

## Canonical precedence
Follow `core-global-rules.md` for the ordered rule stack.

If two rules do not truly conflict, apply both in the lowest-context way that still resolves the task.

## Tie-break principles

### 1. Cheapest sufficient path wins
- Use the lightest workflow that can answer the current question.
- Do not escalate tools or retrieval without a concrete signal.

### 2. Runtime shape beats static preference
- The current `TaskClass` matters more than a default project bias.
- Do not downgrade rigor when the task has clearly become shared-core or debugging-heavy.

### 3. Safety beats speed
- For memory, verification, indexing, and shared-core impact, prefer explicit safety and verifiability over speed.

### 4. Exact user evidence beats generic heuristics
- Exact files, exact errors, exact constraints, and user-provided artifacts beat general routing preferences.
- Do not force graph-first or MCP-first workflows when the raw artifact already answers the question.

### 5. Verified fact beats speculative routing
- When new evidence settles ambiguity, downgrade the heavier workflow.
- Do not keep reasoning, graph, or memory workflows active without a current reason.

## Common conflict patterns

### Profile says local, task class says shared-core
- Follow `runtime-routing.md` and `task-routing.md`.
- Use profile only as a bias.
- Keep impact-aware verification.

### Memory might help, but the fact is not stable
- Follow `memory-governance.md`.
- Do not store unstable or speculative memory.

### MCP might help, but bounded local tools already answer the question
- Follow `task-token-efficiency.md`.
- Do not add MCP without clear value.

### Graph-first instinct vs exact local artifact
- Exact local artifacts win.
- Use bounded file tools first.

### Retrieval budget is tight, but uncertainty remains
- Follow `task-retrieval-budget.md`.
- Summarize the remaining ambiguity and narrow the next question before reading more.

### Multiple MCPs could help, but adding all is expensive
- Start with the one that most directly answers the current question.
- Add a second MCP only when the first produces evidence that requires it.
- Order: memory (fastest lookup, ~50 tokens) → codebase-memory (graph query, ~100 tokens) → sequential-thinking (reasoning cost).

### sequential-thinking feels useful, but the decision is simple
- Use sequential-thinking only when there are genuinely multiple plausible paths or hypotheses.
- For binary or obvious decisions, skip it — reasoning overhead is not justified.

### Memory has old data that might conflict with current state
- Current source code and user evidence always beat stored memory.
- Update or delete stale memory observations when contradiction is found.
- Follow `memory-governance.md` for cleanup policy.

## Escalation rule
If precedence and tie-breaks still do not resolve the conflict:
1. identify the current `TaskClass`
2. identify the next concrete question that must be answered
3. choose the cheapest tool or rule that can answer that question
4. ask one specific user question only if external information is still required

## Anti-patterns
- citing many rules to justify broad exploration
- keeping heavy MCP workflows active out of inertia
- writing memory because it might be useful later
- skipping verification because the task looks simple
- explaining the full internal conflict process in the final response

## Output guidance
Do not mention internal rule conflicts in the final response unless that decision materially affected execution.
