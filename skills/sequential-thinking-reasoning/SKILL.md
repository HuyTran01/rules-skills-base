---
name: sequential-thinking-reasoning
description: >-
  Structured reasoning via sequential-thinking MCP. Use for debugging,
  architectural decisions, multi-hypothesis analysis, and complex planning.
disabled: true
---

# Sequential Thinking Reasoning

## When to activate
Use when:
- Debugging with multiple plausible causes.
- Making architectural decisions with trade-offs.
- Planning multi-step changes that need structured analysis.
- Evaluating multiple approaches before committing.
- A decision has non-obvious consequences.
- Complex refactoring affecting many components.

## When NOT to activate
- Trivial yes/no decisions.
- Tasks where the solution is already clear.
- Simple code changes with no ambiguity.
- When the overhead of reasoning exceeds the value of the answer.

## Delegates to
- `../../Rules/runtime-routing.md` — MCP selection decision tree
- `../../Rules/task-routing.md` — task class determines when reasoning is justified
- `../../Rules/conflict-resolution.md` — "sequential-thinking feels useful but the decision is simple"
- `../../Rules/nav-codebase-graph.md` — gather graph evidence before reasoning
- `../../Rules/skill-routing-matrix.md`

## Tool reference

The sequential-thinking MCP has one tool: `sequential_thinking`.

| Parameter | Type | Purpose |
|---|---|---|
| `thought` | string | The current thinking step content |
| `nextThoughtNeeded` | boolean | Whether another step is needed |
| `thoughtNumber` | integer | Current step number |
| `totalThoughts` | integer | Estimated total steps (adjustable) |
| `isRevision` | boolean | Whether this revises a previous step |
| `revisesThought` | integer | Which step is being reconsidered |
| `branchFromThought` | integer | Branching point step number |
| `branchId` | string | Branch identifier |
| `needsMoreThoughts` | boolean | If more steps are needed than estimated |

## Operating flow

### Pattern 1: Linear reasoning (most common)
For straightforward multi-step analysis:

```
Thought 1/4: Define the problem clearly
Thought 2/4: Gather and list evidence
Thought 3/4: Analyze evidence and form conclusion
Thought 4/4: Verify conclusion and propose action
```

### Pattern 2: Hypothesis testing (debugging)
For investigating bugs or unexpected behavior:

```
Thought 1/5: State the symptom and constraints
Thought 2/5: List 2-3 plausible hypotheses
Thought 3/5: For each hypothesis, identify the cheapest test
Thought 4/5: Evaluate test results (revise if wrong)
Thought 5/5: Confirm root cause and propose fix
```

Use `isRevision: true` + `revisesThought: N` when a hypothesis is disproven.

### Pattern 3: Decision branching (architecture)
For evaluating trade-offs between approaches:

```
Thought 1/6: State the decision and constraints
Thought 2/6: Describe approach A (pros, cons, cost)
Thought 3/6: Describe approach B (pros, cons, cost) [branchFromThought: 1]
Thought 4/6: Compare approaches against constraints
Thought 5/6: Select approach with rationale
Thought 6/6: Define implementation plan for chosen approach
```

Use `branchFromThought` and `branchId` to explore alternatives.

### Pattern 4: Impact analysis (shared-core changes)
For understanding the blast radius of a change:

```
Thought 1/4: Identify the change scope
Thought 2/4: Map direct dependencies (from codebase-memory graph)
Thought 3/4: Assess risk for each affected area
Thought 4/4: Prioritize verification steps by risk
```

## Integration with other MCPs

### codebase-memory → sequential-thinking
Gather evidence first, then reason about it:
1. `search_graph` / `trace_path` → collect structural facts
2. `sequential_thinking` → analyze the evidence

### sequential-thinking → memory
Save the conclusion for future use:
1. `sequential_thinking` → reach a decision
2. `create_entities` / `add_observations` → persist the decision

### Full pipeline (debugging example)
1. memory: `search_nodes("checkout bug")` → recall past issues
2. codebase-memory: `trace_path("checkout")` → map the code flow
3. sequential-thinking: form hypotheses based on evidence
4. codebase-memory: `get_code_snippet` → verify hypothesis
5. sequential-thinking: revise if needed → confirm root cause
6. memory: `add_observations` → save the bug pattern

## Adjusting totalThoughts
- Start with a conservative estimate (4-6 steps).
- Set `needsMoreThoughts: true` if the analysis needs more depth.
- Reduce `totalThoughts` if the answer becomes clear early.
- Avoid over-thinking: if the conclusion is obvious by step 2, set `nextThoughtNeeded: false`.

## Anti-patterns
- Using sequential-thinking for trivial decisions.
- Starting reasoning without first gathering evidence from graph or memory.
- Not using revision when a hypothesis is disproven (continuing linearly instead).
- Not saving valuable conclusions to memory MCP.
- Over-estimating totalThoughts and producing unnecessary steps.
- Using sequential-thinking when a simple list of pros/cons would suffice.
