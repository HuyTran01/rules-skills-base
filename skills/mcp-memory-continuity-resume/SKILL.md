---
name: mcp-memory-continuity-resume
description: Preserve and restore session state via memory MCP entities. Enables seamless cross-session work continuation.
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

## Entity linking
Connect continuity entities to project entities for cross-reference:
```
create_relations([{
  from: "continuity_my_webapp_auth_refactor",
  to: "my_webapp",
  relationType: "continues_work_on"
}])
```

## Example: pause and resume cycle

```
SESSION 1 (paused):
  - Working on: auth middleware refactor
  - Confirmed: 3 callers found, 2 need updates
  - Next: update UserController.authenticate()
  
  → create_entities([{
      name: "continuity_webapp_auth_refactor",
      entityType: "continuity",
      observations: [
        "Objective: Refactor auth middleware for JWT refresh",
        "Status: in-progress",
        "Findings: 3 callers - UserController, AdminController, ApiGateway; 2 need updates",
        "Files touched: src/middleware/auth.ts, src/controllers/UserController.ts",
        "Next step: Update UserController.authenticate() to handle refresh token rotation"
      ]
    }])

SESSION 2 (resumed):
  → search_nodes("continuity_webapp") → finds entity
  → open_nodes(["continuity_webapp_auth_refactor"])
  → Agent knows exactly where to continue without re-reading files
  → After completion: delete_entities(["continuity_webapp_auth_refactor"])
```

## Anti-patterns
- Storing full file contents or raw logs in continuity.
- Creating continuity for short, disposable tasks.
- Mixing speculative ideas with confirmed findings.
- Forgetting to clean up continuity entities after task completion.
- Not linking continuity to project entities.
