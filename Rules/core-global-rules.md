# Global Rules

Apply these files in order unless a more specific instruction overrides them.

If two rules do not truly conflict, apply both in the lowest-context way that still resolves the task.

## Application order
1. `core-workflow.md` - language, communication, commit behavior, and MCP-first defaults.
2. `runtime-routing.md` - runtime orchestration: task classification, MCP selection, retrieval tier, response mode, re-routing, and verification posture.
3. `task-routing.md` - shared taxonomy, escalation semantics, and default MCP sets per task class.
4. `project-profile-detection.md` - repository profile bias.
5. `task-retrieval-budget.md` - MCP-aware retrieval tiers, escalation gates, and stop conditions.
6. `task-token-efficiency.md` - MCP-first cheapest-sufficient execution and context control.
7. `memory-governance.md` - Knowledge Graph memory reads, writes, auto-learn triggers, and continuity safety.
8. `memory-markdown-mirror.md` - human-readable `memory/memory.md` mirror of memory MCP writes, per project.
9. `skill-routing-matrix.md` - composition map between TaskClass, skills, canonical rules, and MCPs.
10. Context-specific rules:
   - `code-quality.md`
   - `nav-codebase-graph.md`
   - `template-plan-task-handoff.md`
   - `conflict-resolution.md`
11. `task-response-compact.md` - response shaping and completion evidence.
12. `authoring-conventions.md` - rule and skill maintenance.

## MCP trinity
Three MCP servers power this system. The agent must treat them as primary tools, not optional add-ons.

| MCP server | Role | When to use |
|---|---|---|
| **codebase-memory-mcp** | The agent's **map** — code intelligence graph | Code discovery, impact analysis, architecture understanding, callers/callees — so the agent never gets lost in the codebase |
| **memory** (Knowledge Graph) | The agent's **memory** — persistent cross-session recall | Project knowledge, user preferences, conventions, patterns, decisions — so the agent doesn't rediscover the same facts and doesn't overflow context |
| **sequential-thinking** | The agent's **brain** — structured reasoning | Debugging, architectural decisions, multi-hypothesis analysis, complex planning |

## Shared taxonomy
- `TaskClass`: `trivial-local`, `scoped-change`, `shared-core-change`, `investigation-debug`, `long-running-context-heavy`
- `RetrievalTier`: `tier-0-graph`, `tier-1-cheap`, `tier-2-bounded`, `tier-3-expensive`
- `ContextPressureLevel`: `low`, `medium`, `high`, `critical`
- `ResponseMode`: `compact-default`, `detailed-on-demand`, `verification-evidence`

## Skill catalog
- `task-classification-router` — classify task shape, choose MCP set, and pick retrieval tier
- `nav-token-aware-graph-navigation` — navigate code relationships via codebase-memory-mcp
- `mcp-memory-workflow` — read/write Knowledge Graph memory with auto-learn protocol
- `mcp-memory-continuity-resume` — preserve or restore session state via memory entities
- `mcp-memory-markdown-mirror` — mirror memory MCP writes into a human-readable `memory/memory.md` per project
- `sequential-thinking-reasoning` — structured reasoning with branching and revision
- `figma-to-code` — convert design input into implementation guidance

## Source of truth by concern
- Core behavior: `core-workflow.md`
- Global precedence: this file
- Runtime orchestration: `runtime-routing.md`
- Taxonomy: `task-routing.md`
- Retrieval control: `task-retrieval-budget.md`
- Token discipline: `task-token-efficiency.md`
- Project profile bias: `project-profile-detection.md`
- Durable memory: `memory-governance.md`
- Human-readable memory mirror: `memory-markdown-mirror.md`
- Graph navigation: `nav-codebase-graph.md`
- Response shaping: `task-response-compact.md`
- Plans and handoffs: `template-plan-task-handoff.md`
- Conflict handling: `conflict-resolution.md`
- Authoring: `authoring-conventions.md`
- Skill composition: `skill-routing-matrix.md`

## Design principles
- Keep one source of truth per concern.
- Prefer delegation over repeated policy text.
- Keep verification proportional to task risk.
- Re-route when evidence changes task shape.
- Favor the cheapest sufficient path over broad exploration.
- Use MCP tools before falling back to raw file operations.
- Learn and remember — agent improves across sessions.

## Fast routing checklist
1. Classify the current `TaskClass` in `task-routing.md`.
2. Apply runtime orchestration in `runtime-routing.md` (includes MCP selection).
3. Add project-profile bias from `project-profile-detection.md`.
4. Stay within `task-retrieval-budget.md` and `task-token-efficiency.md`.
5. Format output with `task-response-compact.md`.
6. Save valuable knowledge via `memory-governance.md`, then sync `memory-markdown-mirror.md`.

## Verification matrix by task class
Canonical table lives in `task-retrieval-budget.md#verification-matrix-canonical`. This file does not restate it.

## Maintenance flow
1. Update the canonical source-of-truth file first.
2. Update skills only if the decision flow changed.
3. Check `authoring-conventions.md` before adding a new rule or skill.
