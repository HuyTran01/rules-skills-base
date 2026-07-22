---
name: mcp-memory-continuity-resume
description: >-
  Preserve and restore session state via memory MCP entities. Enables seamless
  cross-session work continuation.
disabled: true
---

# MCP Memory Continuity Resume

## When to activate
Use when:
- A task is long-running and cannot complete in one session.
- Work is being paused and will resume later.
- The local context would be expensive to rebuild from scratch.
- A new session starts and the project has been worked on before.

## Delegates to
- `../../Rules/memory-governance.md` — entity taxonomy and safety
- `../../Rules/memory-markdown-mirror.md` — sync the human-readable mirror after preserving or cleaning up continuity state
- `../../Rules/template-plan-task-handoff.md` — handoff structure
- `../../Rules/task-response-compact.md`
- `../../Rules/skill-routing-matrix.md`

## Operating flow

### Session start: RESTORE
1. `search_nodes` with project name or "continuity".
2. If continuity entity found:
   a. `open_nodes` to load its full observations.
   b. Read: objective, confirmed findings, touched files, next step.
   c. Resume from the recorded next step — skip rediscovery.
3. If no continuity entity: start fresh, check for project/convention entities instead.

### Session end: PRESERVE
4. Evaluate: is the task complete?
   - YES → delete the continuity entity (or convert to permanent knowledge).
   - NO → save or update continuation state.
5. Create or update continuity entity:
   ```
   create_entities([{
     name: "continuity_<project>_<short_task_desc>",
     entityType: "continuity",
     observations: [
       "Objective: <current task goal>",
       "Status: <in-progress | blocked | paused>",
       "Findings: <confirmed facts, separated by semicolons>",
       "Files touched: <file1>, <file2>",
       "Next step: <specific action to take on resume>",
       "Blocker: <if any, describe>"
     ]
   }])
   ```
6. If the task discovered reusable knowledge → also save as permanent entities (convention, decision, etc.).

### On task completion: CLEAN UP
7. Delete the continuity entity: `delete_entities(["continuity_<project>_<short_task_desc>"])`.
8. Convert any valuable findings to permanent entities.
9. Resync the project's `memory/memory.md` mirror so the `Continuity (active)` section reflects the deletion.

## Notes
- Use the canonical handoff and cleanup structure from `template-plan-task-handoff.md` and `memory-governance.md`.
