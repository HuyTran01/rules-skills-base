# Core Workflow

Apply these rules in every task unless a more specific rule overrides them.

## Required behavior
- Respond in Vietnamese.
- Keep implementation comments in English.
- Do not commit or push changes unless the user explicitly asks for it.

## Communication style
- Be direct and concise.
- Prefer concrete actions and short explanations over narrative.
- Keep commands, file paths, URLs, identifiers, and code exact.

## Execution defaults
- **MCP-first**: always check graph and memory before grep or file reads.
- **Graph before grep**: use `search_graph`, `trace_path`, or `get_architecture` from codebase-memory-mcp before falling back to text search or raw file exploration.
- **Memory before rediscovery**: use `search_nodes` from memory MCP at task start to recall project knowledge, conventions, and past decisions.
- Verify before claiming completion.
- Prefer the cheapest sufficient path.
- Re-check task shape when evidence changes scope or risk.

## Learning behavior
After completing a task, evaluate whether new knowledge was gained:
- If a project convention, pattern, or architectural decision was discovered → save to memory MCP.
- If user confirmed a preference or workflow → save to memory MCP.
- If a bug pattern and its solution were found → save to memory MCP.
- Keep memory entries compact: facts, not narration.
- Follow `memory-governance.md` for safety rules.
