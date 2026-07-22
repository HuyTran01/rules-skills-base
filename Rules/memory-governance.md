# Memory Governance

Use this file as the canonical policy for Knowledge Graph memory reads, writes, auto-learn triggers, and safety.

For the human-readable `memory/memory.md` mirror that should be kept in sync after every write described in this file, see `memory-markdown-mirror.md`.

The memory MCP uses a Knowledge Graph with **entities**, **relations**, and **observations**.

## Core concepts
- **Entity**: a named node with a type and observations (e.g., `{name: "ProjectX", entityType: "project", observations: ["Uses React 18", "Monorepo with Turborepo"]}`)
- **Relation**: a directed edge between entities (e.g., `{from: "ProjectX", to: "PostgreSQL", relationType: "uses_database"}`)
- **Observation**: an atomic fact attached to an entity (e.g., `"API follows REST conventions with /api/v1 prefix"`)

## Memory tools
| Tool | Purpose |
|---|---|
| `search_nodes` | Search entities by name, type, or observation content |
| `open_nodes` | Retrieve specific entities by name |
| `read_graph` | Read the entire knowledge graph |
| `create_entities` | Create new entity nodes |
| `create_relations` | Create directed relations between entities |
| `add_observations` | Add facts to existing entities |
| `delete_entities` | Remove entities and their relations |
| `delete_observations` | Remove specific observations |
| `delete_relations` | Remove specific relations |

## Entity taxonomy for coding context

Use these entity types consistently:

| entityType | When to create | Example entity name |
|---|---|---|
| `project` | One per repository/workspace | `my-webapp` |
| `tech_stack` | Major technology used | `React_18`, `PostgreSQL_15` |
| `convention` | Coding pattern or rule in the project | `naming_convention`, `api_pattern` |
| `architecture` | Structural decision or component | `auth_module`, `event_bus` |
| `decision` | Architectural or design decision | `chose_REST_over_GraphQL` |
| `bug_pattern` | Recurring bug type and its solution | `race_condition_in_auth` |
| `user_preference` | Confirmed user workflow preference | `user_prefers_dark_mode` |
| `file_group` | Important file cluster | `config_files`, `test_utils` |
| `continuity` | Cross-session work state for long-running tasks | `continuity_webapp_auth_refactor` |

## Entity naming convention
- Use `snake_case` with underscores.
- Be specific enough to be searchable: `react_component_pattern` not `pattern`.
- Include project context when multiple projects exist: `projectX_api_convention`.

## When to create entities vs add observations
- **Create entity**: when a new distinct concept appears (new project, technology, convention).
- **Add observation**: when adding a fact to an existing concept (new detail about a known convention).
- **Create relation**: when two entities have a meaningful connection.
- **Always check first**: `search_nodes` before creating to avoid duplicates.

## Auto-learn triggers
Save knowledge to memory after completing a task when any of these apply:

| Trigger | What to save | entityType |
|---|---|---|
| Discovered a project convention | Convention details as observations | `convention` |
| Made an architectural decision | Decision rationale and alternatives | `decision` |
| Found a bug and fixed it | Bug pattern, root cause, solution | `bug_pattern` |
| User confirmed a preference | Preference details | `user_preference` |
| Discovered tech stack details | Technology + version + configuration | `tech_stack` |
| Mapped a key code area | Module structure, entry points | `architecture` |

## Session start protocol
At the start of every task:
1. `search_nodes` with the project name or relevant keywords.
2. Review returned entities and observations for context.
3. Use recalled knowledge to skip rediscovery.

## Read-before-write policy
Before writing memory:
1. `search_nodes` to check if relevant entity exists.
2. If entity exists → `add_observations` instead of creating duplicate.
3. If entity has stale observations → `delete_observations` then `add_observations`.
4. Confirm the fact is stable enough to survive future sessions.

## What to store
- Stable project conventions and patterns.
- Confirmed architectural decisions with rationale.
- Bug patterns and their solutions.
- User preferences explicitly confirmed.
- Technology stack facts (versions, configurations).
- Cross-session continuity state for long-running tasks.

## What NOT to store
- Transient hypotheses or unconfirmed guesses.
- One-off task details with no reuse value.
- Unstable repository state.
- Secrets, credentials, or sensitive data.
- Raw logs or verbose tool output.
- Speculative debugging conclusions.
- Information cheap to re-read from source.

## Writing style for observations
- Keep entries compact — one fact per observation string.
- Store facts, not narration.
- Use neutral, searchable wording.
- Include enough specificity to be actionable without context.

## Cleanup
- Update observations when a fact becomes outdated.
- Delete entities that no longer apply (deprecated tech, removed modules).
- Prefer updating over re-creating.
- After any write or cleanup, resync the project's `memory/memory.md` mirror per `memory-markdown-mirror.md`.

## Continuity boundaries
Use continuity memory only when:
- the task is long-running
- the task is likely to resume later
- the local context would be expensive to rebuild

A continuity entity should store only:
- current objective (observation)
- confirmed findings (observations)
- files or areas touched (observations)
- next recommended step (observation)
