---
name: mcp-memory-markdown-mirror
description: >-
  Mirror Knowledge Graph memory writes into a human-readable memory/memory.md
  file inside the current project, so the user can audit project memory without
  opening the MCP JSON store.
disabled: true
---

# MCP Memory Markdown Mirror

## When to activate
Use right after any memory MCP write for the current project:
- `create_entities`, `add_observations`, `create_relations`
- `delete_entities`, `delete_observations`, `delete_relations`

Runs as a follow-up step after `mcp-memory-workflow` and `mcp-memory-continuity-resume` — not a replacement for either.

## Delegates to
- `../../Rules/memory-markdown-mirror.md` — canonical mirror format, sync strategy, and safety rules
- `../../Rules/memory-governance.md` — entity taxonomy and what not to store
- `../../Rules/task-token-efficiency.md`
- `../../Rules/skill-routing-matrix.md`

## Operating flow
1. After a memory write completes for the current project, gather current state scoped to that project:
   - `search_nodes` (or `read_graph` if the project's graph is small) filtered to entities belonging to the current project.
2. Group returned entities by `entityType`.
3. Render the mirror using the format in `memory-markdown-mirror.md`.
4. Write (overwrite) `<project_root>/memory/memory.md` with the rendered content.
   - Create the `memory/` folder first if it doesn't exist.
5. Do not narrate this step in the response unless the user asks about memory — it's silent housekeeping alongside the real task.

## Notes
- The canonical mirror format, sync strategy, and safety rules live in `memory-markdown-mirror.md`.
