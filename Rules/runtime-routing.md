# Runtime Routing

Use this file as the canonical runtime orchestrator for task classification, MCP selection, retrieval tier, response mode, re-routing, and verification posture.

## Runtime decision flow
1. Classify task shape → Step 1
2. Select MCP set → Step 2
3. Choose retrieval tier → Step 3
4. Apply project profile bias → Step 4
5. Execute with re-routing → Step 5
6. Choose response mode → Step 6
7. Set verification posture → Step 7
8. Save knowledge → Step 8

## Step 1 — Classify task shape
Classify with `task-routing.md`.

If scope changes during execution, update the task class immediately.

## Step 2 — Select MCP set
Use the default MCP set from `task-routing.md` for the current TaskClass. Adjust based on:

### MCP selection decision tree
```
Is the question about code structure, callers, impact, or architecture?
  YES → codebase-memory-mcp (search_graph, trace_path, get_architecture, query_graph)
  NO  ↓

Is the problem complex, multi-hypothesis, or needs structured reasoning?
  YES → sequential-thinking (sequential_thinking with branching/revision)
  NO  ↓

Was this information discovered before, or is it a cross-session fact?
  YES → memory MCP (search_nodes, open_nodes)
  NO  ↓

Use standard local tools (file reads, grep, commands).
```

### MCP combination patterns
- **Code understanding task**: codebase-memory → get evidence → sequential-thinking → reason about it
- **Debug task**: memory → recall past bugs → codebase-memory → trace the issue → sequential-thinking → form hypotheses → memory → save solution
- **Architecture decision**: codebase-memory → get_architecture → sequential-thinking → reason about options → memory → save decision via manage_adr or memory entity
- **Resume work**: memory → search_nodes for context → codebase-memory → detect_changes → continue

### MCP re-route triggers
Re-check MCP selection when:
- scope expands or shifts
- the task reaches shared logic or many callers
- two or more plausible hypotheses appear
- framework or version-specific behavior matters
- the task becomes long-running or likely to resume
- several bounded steps fail to reduce ambiguity
- local exploration is becoming broad

### MCP downgrade triggers
Drop or reduce MCP usage when:
- ambiguity is resolved
- an exact path or local target becomes clear
- local tools are now sufficient
- the MCP no longer lowers cost or uncertainty

## Step 3 — Choose retrieval tier
Default starting points (MCP-aware):

| TaskClass | Start tier | Escalation |
|---|---|---|
| `trivial-local` | `tier-1-cheap` | rarely needed |
| `scoped-change` | `tier-0-graph` → `tier-1-cheap` | when target narrows |
| `shared-core-change` | `tier-0-graph` | escalate through `task-retrieval-budget.md` |
| `investigation-debug` | `tier-0-graph` + memory | plus bounded logs/tests if needed |
| `long-running-context-heavy` | cheapest tier per subtask | preserve continuity |

`tier-0-graph` = codebase-memory-mcp queries (sub-ms, ~100 tokens).

Escalate only through `task-retrieval-budget.md`.

## Step 4 — Apply project profile bias
Use `project-profile-detection.md` as a bias layer only.
It does not override a higher-risk task shape.

## Step 5 — Execute with re-routing
Re-run routing when:
- scope expands or contracts
- ambiguity rises or falls
- the target narrows to an exact path
- impact surface changes
- a useful MCP tool becomes relevant or unnecessary

## Step 6 — Choose response mode
Use `task-response-compact.md`:
- `compact-default`
- `detailed-on-demand`
- `verification-evidence`

## Step 7 — Set verification posture
Do not conclude before collecting evidence that matches the current task class.

Use the canonical verification matrix in `task-retrieval-budget.md#verification-matrix-canonical` — do not restate it here.

## Step 8 — Save knowledge
After completing verification, evaluate whether knowledge should be saved:
- New convention or pattern discovered → memory MCP entity/observation
- Architectural decision made → memory MCP or manage_adr
- Bug pattern solved → memory MCP observation
- Follow `memory-governance.md` for safety.
- After any memory write, resync `memory/memory.md` per `memory-markdown-mirror.md`.

## Conflict handling
For precedence, follow `core-global-rules.md`.
If ambiguity remains, follow `conflict-resolution.md`.

## Quick reference card
1. Classify → `task-routing.md`
2. Select MCPs → decision tree above
3. Pick retrieval tier → `task-retrieval-budget.md`
4. Re-route when task shape changes
5. Verify according to task class
6. Respond with shortest sufficient mode
7. Save valuable knowledge to memory

### Default tie-breaks
- Exact user evidence beats generic heuristics.
- Runtime task shape beats static preference.
- Cheapest sufficient path beats broader exploration.
- Verified fact beats speculative routing.
- MCP graph query beats equivalent grep chain.
