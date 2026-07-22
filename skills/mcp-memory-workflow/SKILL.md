---
name: mcp-memory-workflow
description: >-
  Read and write Knowledge Graph memory with auto-learn protocol. Agent
  remembers project knowledge across sessions.
disabled: true
---

# MCP Memory Workflow

## When to activate
Use when:
- Starting a task in a project (search for existing knowledge first).
- Stable conventions, patterns, or decisions are discovered.
- A bug is solved and the pattern should be remembered.
- User confirms a preference or workflow.
- Cross-session continuity may reduce repeated work.

## Delegates to
- `../../Rules/memory-governance.md` — entity taxonomy, naming, safety, and auto-learn triggers
- `../../Rules/memory-markdown-mirror.md` — sync the human-readable mirror after any write in this flow
- `../../Rules/task-token-efficiency.md`
- `../../Rules/conflict-resolution.md`
- `../../Rules/skill-routing-matrix.md`

## Operating flow

### At task start: RECALL
1. `search_nodes` with project name or task keywords.
2. Review returned entities and observations.
3. Use recalled knowledge to skip rediscovery and inform approach.

### During task: OBSERVE
4. Note new facts as they emerge:
   - Project conventions, naming patterns, file structure
   - Technology details (versions, configs)
   - Architectural patterns
   - User preferences

### After task: LEARN
5. Evaluate what was discovered against auto-learn triggers in `memory-governance.md`.
6. For each learnable fact:
   a. `search_nodes` — does this entity already exist?
   b. YES → `add_observations` to the existing entity.
   c. NO → `create_entities` with proper entityType from taxonomy.
7. If entities should be connected: `create_relations`.
8. Sync the project's `memory/memory.md` mirror — see `mcp-memory-markdown-mirror` and `Rules/memory-markdown-mirror.md`.

## Entity taxonomy reference

Please refer to the canonical entity taxonomy table defined in **[memory-governance.md](../../Rules/memory-governance.md#entity-taxonomy-for-coding-context)**. Do not define a duplicate taxonomy table here.

## Example: full auto-learn cycle

```
Task: "Fix the auth middleware to handle expired tokens"

1. RECALL:
   search_nodes("auth") → finds "auth_module" entity
   Observations: "JWT-based", "Tokens expire in 1h"
   → Agent knows the tech context without re-reading files

2. EXECUTE task using recalled context...

3. LEARN:
   - Discovered: refresh token rotation pattern
   - search_nodes("auth_module") → entity exists
   - add_observations("auth_module", ["Refresh token rotation implemented", "Refresh tokens expire in 7d"])
   - Discovered: user prefers error responses in Vietnamese
   - create_entities([{name: "user_error_locale", entityType: "user_preference", observations: ["Error messages should be in Vietnamese"]}])
```

## Cleanup protocol
- When a fact is contradicted by new evidence: `delete_observations` + `add_observations`.
- When an entity is no longer relevant: `delete_entities`.
- When a relation changes: `delete_relations` + `create_relations`.

## Anti-patterns
- Writing speculative or unconfirmed memory.
- Storing raw logs, tool output, or verbose text.
- Creating duplicate entities without checking first.
- Storing secrets or credentials.
- Writing memory for one-off tasks with no reuse value.
- Forgetting to recall memory at task start.
