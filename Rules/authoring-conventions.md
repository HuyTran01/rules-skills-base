# Authoring Conventions

Use these conventions to reduce drift and duplication when adding or editing rules and skills.

## Rule authoring
- Keep one canonical file per concern:
  - runtime orchestration: `runtime-routing.md`
  - taxonomy: `task-routing.md`
  - MCP intent mapping and selection: `runtime-routing.md`
  - conflict handling: `conflict-resolution.md`
  - global order: `core-global-rules.md`
  - memory policy: `memory-governance.md`
  - human-readable memory mirror: `memory-markdown-mirror.md`
- Do not duplicate full policy text from a canonical rule into another rule or a skill.
- Every new or updated rule should state:
  - scope
  - activation conditions
  - deactivation or downgrade conditions
  - how to re-check when task shape changes

## Skill authoring
- Each `skills/<folder>/SKILL` file should contain:
  1. metadata
  2. when to activate
  3. a short operating flow
  4. delegated canonical rules
- Skills should orchestrate; they should not restate canonical policy in full.
- Do not store raw logs, ad hoc experiments, or temporary notes in a skill.

## Anti-duplication checks
Before finalizing a new rule or skill:
- check whether the concern already exists in `Rules/`
- do not duplicate:
  - taxonomy
  - precedence
  - MCP intent mapping
  - retrieval budgets
  - memory safety policy
- if more detail is needed, add a short operating flow instead of copying background policy

## Cross-reference hygiene
- Every updated file must reference real filenames.
- Update `core-global-rules.md` when ownership, routing, or source-of-truth mapping changes.
- Update `runtime-routing.md` when the decision flow changes.
- Keep filenames, headings, and terms consistent across the repo.

## Validation discipline
- Re-read at least one related source-of-truth file before finalizing a change.
- Keep validator output clean at the error level.
- Change logic only with impact evidence.

## Change safety
- In this docs-first repo, prefer this order:
  `core-global-rules.md` or plan note -> canonical rule -> delegate rule -> skill
- Stop broad exploration once a canonical file answers the question.
- Prefer minimal edits with clean diffs.

## Formatting
- Use short headings and short lists.
- Avoid vague language.
- State activation conditions explicitly for exceptions or risks.
- Keep the tone direct and operational.
