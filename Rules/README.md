# Rules & Skills Base

An MCP-first rule system for AI coding agents. Designed to maximize agent intelligence through three MCP servers while minimizing token usage.

## MCP Server Requirements

| MCP Server | Role | Purpose | Repository |
|---|---|---|---|
| **codebase-memory-mcp** | The agent's **map** | Code intelligence graph (14 tools, 158 languages) so the agent navigates the codebase without getting lost and builds a deep structural understanding of it | [DeusData/codebase-memory-mcp](https://github.com/DeusData/codebase-memory-mcp) |
| **memory** | The agent's **memory** | Persistent knowledge graph (cross-session memory) so the agent always recalls key project facts and avoids context overflow from re-discovery | [modelcontextprotocol/servers/memory](https://github.com/modelcontextprotocol/servers/tree/main/src/memory) |
| **sequential-thinking** | The agent's **brain** | Structured reasoning with branching/revision for complex, multi-step tasks | [modelcontextprotocol/servers/sequentialthinking](https://github.com/modelcontextprotocol/servers/tree/main/src/sequentialthinking) |

## Reading Order

Please follow the canonical rule application and reading order defined in **[core-global-rules.md](core-global-rules.md)**.

The global rules are structured hierarchically:
- **Steering Core**: `core-workflow.md` (conventions) and `runtime-routing.md` (decision engine) represent the primary runtime steering.
- **Execution Rules**: Details about task classification, retrieval budget limits, token efficiency levels, memory governance, and graph navigation.
- **Context-Specific Rules**: Quality guardrails, verification templates, conflict resolution patterns, and project profile detection.

## Skills

| Skill | Purpose |
|---|---|
| [task-classification-router](../skills/task-classification-router/) | Classify task → choose MCP set → pick retrieval tier |
| [nav-token-aware-graph-navigation](../skills/nav-token-aware-graph-navigation/) | Navigate code via codebase-memory-mcp graph |
| [mcp-memory-workflow](../skills/mcp-memory-workflow/) | Read/write Knowledge Graph memory with auto-learn |
| [mcp-memory-continuity-resume](../skills/mcp-memory-continuity-resume/) | Cross-session work continuation via memory entities |
| [mcp-memory-markdown-mirror](../skills/mcp-memory-markdown-mirror/) | Mirror Knowledge Graph memory into a human-readable `memory/memory.md` per project |
| [sequential-thinking-reasoning](../skills/sequential-thinking-reasoning/) | Structured reasoning: debugging, decisions, planning |
| [figma-to-code](../skills/figma-to-code/) | Design-to-code conversion |

Skill activation and composition are defined in **[skill-routing-matrix.md](skill-routing-matrix.md)**.
All skills currently use `disabled: true` as an explicit opt-in/template marker; remove or change that field only when the target agent host supports and requires automatic activation.

## How It Works

```
Task arrives
    │
    ▼
┌─────────────────────┐
│ 1. CLASSIFY         │  task-routing.md
│    What TaskClass?   │
└─────────┬───────────┘
          │
          ▼
┌─────────────────────┐
│ 2. SELECT MCPs      │  runtime-routing.md
│    Which tools?      │
└─────────┬───────────┘
          │
          ▼
┌─────────────────────────────────────────┐
│ 3. RECALL                               │
│    memory: search_nodes(project)        │  memory MCP
│    graph: get_architecture / search     │  codebase-memory-mcp
└─────────┬───────────────────────────────┘
          │
          ▼
┌─────────────────────┐
│ 4. EXECUTE          │  Guided by graph + memory context
│    Cheapest path    │
└─────────┬───────────┘
          │
          ▼
┌─────────────────────┐
│ 5. VERIFY           │  Evidence per task class
└─────────┬───────────┘
          │
          ▼
┌─────────────────────┐
│ 6. LEARN            │  memory: save knowledge
│    Auto-learn       │  memory MCP + sync memory/memory.md
└─────────────────────┘
```

## Adding New Rules or Skills

See [authoring-conventions.md](authoring-conventions.md) for guidelines:
- One canonical file per concern.
- Skills delegate to rules, not copy policy.
- Check for duplicates before creating.
- Update `core-global-rules.md` when adding new rules.
