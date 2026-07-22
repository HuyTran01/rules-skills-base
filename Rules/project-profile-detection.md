# Project Profile Detection

Use this file to infer a repository profile that can bias tool choice, likely file locations, and verification style.

A project profile is a bias layer only.
It never overrides a higher-risk task shape or exact user evidence.

## Common profiles

### Docs or policy repository
Signals:
- many markdown files
- routing or conventions content
- little or no executable code

Bias:
- prefer bounded file reads and text search
- focus on structure, consistency, and cross-references
- browser verification is usually unnecessary

### Application or service repository
Signals:
- source directories, manifests, tests, runtime assets

Bias:
- inspect project structure before editing
- verification may require tests, commands, or runtime checks
- graph navigation may matter for shared-core work

### Frontend or design-heavy repository
Signals:
- component trees, styling systems, assets, design references

Bias:
- UI verification may matter
- design or browser tooling may become useful
- exact visual changes still start with bounded reads

### Mixed or monorepo workspace
Signals:
- multiple packages, services, apps, or toolchains

Bias:
- identify the active subproject early
- avoid broad repository exploration before narrowing
- shared-core and graph-aware workflows become more likely

### Hook / registry / event-driven project
Signals:
- dynamic dispatch by string name instead of direct static calls: hook or action systems (e.g. WordPress `add_action()`/`do_action()`/`apply_filters()`), event emitters, pub-sub channels, plugin/registry patterns
- framework-specific markers when present (e.g. WordPress: `wp-content/`, `wp-config.php`, `functions.php`, a `style.css` with a Theme/Plugin header, `themes`/`plugins`/`mu-plugins` directories) — treat these as one example of the pattern, not the only one

Bias:
- match the surrounding stack's coding standards and idioms once the specific framework is identified
- static analysis graph calls must be double-checked for dynamic registry/callback wiring (see `nav-codebase-graph.md` fallback rule) — a caller that looks unused via the graph may still be registered and invoked through a string-keyed hook/event name
- verification should consider registration-time vs dispatch-time behavior, not just static call sites

## How to apply profile bias
Use profile to bias:
- likely file locations
- likely verification methods
- whether browser verification is worth it
- whether graph-first navigation is justified

Do not use profile to:
- override `TaskClass`
- override exact user constraints
- reduce verification rigor for shared-core or debug work
