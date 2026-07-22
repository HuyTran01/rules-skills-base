# Rules & Skills Evaluation Set

This directory contains behavioral evaluation prompts for the rules/skills base.

## Purpose

The eval set checks whether an agent:

- activates the right skill for the task;
- selects the smallest sufficient MCP set;
- avoids redundant skills and MCP calls;
- preserves the relationship between canonical rules and orchestrating skills;
- follows memory safety, continuity, mirror-sync, graph-fallback, and reasoning boundaries.

This is a design-time eval set. It does not execute an agent by itself. Use it with an agent harness that can expose the repository rules and record the selected skills, MCP calls, tool arguments, and final response.

## Files

- `evals.json` — 30 prompts with expected behavior and scoring weights.

## Required run context

For each case, the runner should provide:

1. the rules/skills base as system/developer context;
2. a representative project context when the prompt refers to code, memory, or a Figma file;
3. MCP call tracing, including read/write operations;
4. the agent's final response and any proposed file changes.

If the harness cannot expose actual MCP calls, score the MCP assertions from the agent's explicit routing plan and mark the result as `inferred`, not `observed`.

## Scoring

Each case has weighted criteria:

- `trigger`: activates or avoids the expected skill;
- `routing`: selects the expected MCP posture and task class;
- `completeness`: includes required workflow steps;
- `restraint`: avoids unnecessary skills, MCPs, reads, or writes;
- `safety`: respects memory, continuity, mirror, and verification rules.

Score each criterion from `0` to `2`:

- `0` — wrong, missing, or unsafe;
- `1` — partially correct or insufficiently explicit;
- `2` — correct and complete.

The weighted case score is normalized to 100. A recommended initial gate is:

- overall average: at least 85;
- no safety case below 80;
- no negative-trigger case with an unnecessary skill activation;
- no case with an unverified completion claim.

## Coverage map

| Area | Cases |
|---|---|
| Task classification and routing | `router-01` – `router-06` |
| Graph navigation and fallback | `graph-01` – `graph-04` |
| Memory workflow and safety | `memory-01` – `memory-05` |
| Continuity and mirror composition | `continuity-01` – `continuity-03` |
| Sequential-thinking boundaries | `reasoning-01` – `reasoning-04` |
| Figma domain activation | `figma-01` – `figma-04` |
| Cross-skill composition | `composition-01` – `composition-04` |
