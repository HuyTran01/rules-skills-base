# Code Quality

Use this file when reading, planning, or editing code.

## Required checks
- Identify the project structure before editing.
- Match the existing stack, file layout, and local conventions.
- Respect tests, build scripts, lint rules, and validation patterns when they exist.
- Avoid changing unrelated behavior.

## Planning guidance
Before editing:
- identify the likely files involved
- check whether the task is local or shared-core
- verify whether nearby tests or validation paths exist

## Editing guidance
- Prefer minimal, targeted changes.
- Keep names, style, and architecture consistent with the surrounding code.
- If the impact surface grows, re-route with `runtime-routing.md`.

## Escalate when unclear
If repository conventions or ownership are unclear, gather bounded evidence before making structural changes.
