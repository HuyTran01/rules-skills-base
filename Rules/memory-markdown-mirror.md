# Memory Markdown Mirror

Use this file as the canonical policy for mirroring Knowledge Graph memory (MCP `memory` server) into a human-readable Markdown file inside the project being worked on.

## Purpose
The `memory` MCP persists to its own JSON store, which is not convenient for a human to open and audit. This rule defines a parallel, human-readable mirror so the user can review project memory at a glance without inspecting the MCP JSON store directly.

## Mirror location
- Path: `<project_root>/memory/memory.md`
- One mirror file per project. `project_root` is the same project boundary used for entity naming in `memory-governance.md`.
- Create the `memory/` folder and `memory.md` file if they do not exist yet, the first time a relevant entity is written for that project.

## When to sync
Regenerate the mirror any time memory MCP is written for the current project:
- after `create_entities`
- after `add_observations`
- after `create_relations`
- after `delete_entities`, `delete_observations`, or `delete_relations`

Do not sync on read-only calls (`search_nodes`, `open_nodes`, `read_graph`).

## Sync strategy: regenerate, don't append
- Re-render the full mirror file from the current state of entities/relations that belong to the current project.
- Do not append lines incrementally — incremental append drifts from the actual graph state over multiple sessions (stale entries, no delete reflection).
- Scope: only entities that belong to the current project (linked to the project entity by relation, or name-prefixed per the `memory-governance.md` naming convention). Do not dump the entire cross-project Knowledge Graph into one project's mirror.

## Mirror format
```markdown
# Project Memory: <project_name>

_Auto-generated mirror of the MCP memory Knowledge Graph. Edits here are not synced back to the graph — treat this file as read-only, human-facing._

## Tech Stack
- <observation>

## Conventions
- **<entity_name>**: <observation>; <observation>

## Architecture
- **<entity_name>**: <observation>

## Decisions
- **<entity_name>**: <observation>

## Bug Patterns
- **<entity_name>**: <observation>

## User Preferences
- <observation>

## Continuity (active)
- **<entity_name>**: Status: <...> / Next step: <...>

_Last synced: <ISO date>_
```
- Group by `entityType` from the taxonomy in `memory-governance.md`.
- Under each group, one heading per entity (bold entity name) with its observations as a compact list.
- Skip empty sections instead of printing empty headers.
- Include a `Last synced` timestamp line at the end so the user can tell if the mirror is stale.

## What NOT to mirror
Follow the same restrictions as `memory-governance.md`:
- no secrets or credentials
- no raw logs or verbose tool output
- no speculative or unconfirmed facts

## Read-only for the agent
Treat `memory/memory.md` as a **generated artifact**, not a source of truth:
- Never read `memory/memory.md` back into memory MCP as new input data.
- If the user manually edits `memory/memory.md`, do not treat those edits as authoritative — the Knowledge Graph (`search_nodes` / `open_nodes` / `read_graph`) remains the source of truth. If the user wants to change memory content, do it through the memory MCP, then resync the mirror.
- Manual edits to the mirror will be overwritten on the next sync; this is stated in the file header (see format above).

## Continuity entities
Continuity entities (see `memory-governance.md` and `template-plan-task-handoff.md`) are also mirrored, under a `## Continuity (active)` section, so the user can see at a glance what long-running task state exists for the project. Remove the section (or the specific entity block) once the continuity entity is deleted after task completion.

## Relationship to other rules
- entity taxonomy, safety, auto-learn triggers: `memory-governance.md`
- continuity structure: `template-plan-task-handoff.md`
- response shaping (do not dump the mirror content into chat unless asked): `task-response-compact.md`

## Anti-patterns
- Appending instead of regenerating (causes drift between the mirror and the actual graph).
- Mirroring the whole cross-project graph into a single project's file.
- Treating the markdown file as writable memory input.
- Skipping the sync after a delete operation (leaves stale entries visible to the user).
- Writing secrets or raw logs into the mirror.
