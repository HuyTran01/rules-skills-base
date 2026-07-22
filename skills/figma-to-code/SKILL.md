---
name: figma-to-code
description: Turn Figma design input into implementation guidance or code structure using a bounded, semantic workflow.
---

# Figma to Code

## When to activate
Use when the task includes a Figma URL, file key, node id, screenshot, or explicit design-to-code request.

## Delegates to
- `../../Rules/runtime-routing.md`
- `../../Rules/task-token-efficiency.md`
- `resources/layout-patterns.md`
- `resources/dom-structure.md`
- `resources/style-tokens.md`
- `resources/asset-detection.md`
- `resources/framework-adapters.md`

## Operating flow
1. Gather the exact design context and target framework.
2. Analyze layout and hierarchy before writing code.
3. Reconstruct semantic structure instead of copying pixel shapes directly.
4. Extract reusable style tokens and asset requirements.
5. Generate implementation that matches the requested framework and repo conventions.

## When not to over-generate
- If the design context is incomplete, stop at bounded implementation guidance and ask for the missing framework or target surface.
- If the task is only a layout review, return structure/tokens/asset guidance instead of full code.
- If the requested stack is unclear, avoid framework-specific code snippets until the target is known.

## Anti-patterns
- coding from visuals before understanding structure
- baking one-off values into reusable UI without justification
- duplicating reference content inside the skill
